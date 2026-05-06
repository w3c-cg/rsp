# RDF Messages Interoperability Tests

This document proposes interoperability tests for implementations that support RDF Message Logs for Turtle, TriG, N-Triples, and N-Quads.

Implementations MUST provide conformance tests for RDF Message Logs that verify message-level behavior.

An RDF Message Log parser MUST expose a message-level interface that emits one RDF Dataset per completed message.

A message is completed when a message delimiter or the end of the input is encountered. Empty messages MUST be preserved when they occur before the first triple or quad, or between two delimiters. A trailing delimiter after a non-empty message MUST finalize that message and MUST not by itself create an additional empty trailing message.

Blank node identifiers in RDF Message Logs MUST be scoped to the message in which they occur. Therefore, if the same blank node label appears in two different messages in the same input stream, the parser MUST produce two distinct blank nodes. This requirement applies even when the entire RDF Message Log is parsed from one continuous input string or stream by one parser instance.

Message delimiters are message-level syntax. They MUST only occur where the underlying RDF syntax permits directives or top-level statements, and MUST not occur inside an open graph block. A delimiter encountered inside a graph block MUST be a parse error.

Serializers should produce RDF Message Logs that round-trip to the same message sequence and the same quads per message. Conformance tests should not require a serializer to choose a specific delimiter spelling when more than one delimiter spelling is supported by the target syntax.


## Parsing Tests

### 1. Single Message Without Delimiter

Input:

```turtle
VERSION "1.2-messages"
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
```

### 2. Two Messages With `MESSAGE`

Input:

```turtle
VERSION "1.2-messages"
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
MESSAGE
<http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .

Message 2:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

### 3. Two Messages With `@message .`

Input:

```turtle
@version "1.2-messages" .
@prefix ex: <http://example.org/> .

ex:s1 ex:p ex:o1 .
@message .
ex:s2 ex:p ex:o2 .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .

Message 2:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

### 4. Empty First Message

Input:

```turtle
VERSION "1.2-messages"
MESSAGE
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
```

Expected messages:

```text
Message 1:
  empty

Message 2:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
```

### 5. Empty Message Between Non-Empty Messages

Input:

```turtle
VERSION "1.2-messages"
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
MESSAGE
MESSAGE
<http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .

Message 2:
  empty

Message 3:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

### 6. Final Delimiter Does Not Create Extra Empty Message

Input:

```turtle
VERSION "1.2-messages"
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
MESSAGE
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
```

### 7. N-Quads Messages Preserve Graph Names

Input:

```nquads
VERSION "1.2-messages"
<http://example.org/s1> <http://example.org/p> <http://example.org/o1> <http://example.org/g1> .
MESSAGE
<http://example.org/s2> <http://example.org/p> <http://example.org/o2> <http://example.org/g2> .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> <http://example.org/g1> .

Message 2:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> <http://example.org/g2> .
```

### 8. Message With Default Graph And Named Graph Quads

Input:

```trig
VERSION "1.2-messages"
PREFIX ex: <http://example.org/>

ex:s1 ex:p ex:o1 .
ex:g {
  ex:s2 ex:p ex:o2 .
  ex:s3 ex:p ex:o3 .
}
MESSAGE
ex:s4 ex:p ex:o4 .
```

Expected messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> <http://example.org/g> .
  <http://example.org/s3> <http://example.org/p> <http://example.org/o3> <http://example.org/g> .

Message 2:
  <http://example.org/s4> <http://example.org/p> <http://example.org/o4> .
```

### 9. Blank Node Labels Are Scoped Per Message

Input:

```turtle
VERSION "1.2-messages"
_:b0 <http://example.org/p> <http://example.org/o1> .
MESSAGE
_:b0 <http://example.org/p> <http://example.org/o2> .
```

Expected result:

```text
Message 1 contains one quad whose subject is a blank node.
Message 2 contains one quad whose subject is a blank node.
The two blank nodes must not be the same RDF term.
```

This test must be performed on one continuous input string or stream, not by creating two independent parser instances.

### 10. Repeated Prefixes Across Messages

Input:

```turtle
VERSION "1.2-messages"
PREFIX ex: <http://example.org/one/>
ex:s ex:p ex:o .
MESSAGE
PREFIX ex: <http://example.org/two/>
ex:s ex:p ex:o .
```

Expected messages:

```text
Message 1:
  <http://example.org/one/s> <http://example.org/one/p> <http://example.org/one/o> .

Message 2:
  <http://example.org/two/s> <http://example.org/two/p> <http://example.org/two/o> .
```

### 11. Message Boundary After A Graph Block

Input:

```trig
VERSION "1.2-messages"
<http://example.org/g> {
  <http://example.org/a> <http://example.org/b> <http://example.org/c> .
}
MESSAGE
<http://example.org/d> <http://example.org/e> <http://example.org/f> .
```

Expected messages:

```text
Message 1:
  <http://example.org/a> <http://example.org/b> <http://example.org/c> <http://example.org/g> .

Message 2:
  <http://example.org/d> <http://example.org/e> <http://example.org/f> .
```

## Serialization Tests

Given a sequence of RDF Messages, a serializer should write a document that can be parsed back into the same sequence of messages and quads.

Serializers may choose the concrete message delimiter supported by their syntax. These tests should not assert whether `@message .` or `MESSAGE` was used.

Round-trip input messages:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .

Message 2:
  empty

Message 3:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

Expected result after serializing and parsing the output:

```text
Message 1:
  <http://example.org/s1> <http://example.org/p> <http://example.org/o1> .

Message 2:
  empty

Message 3:
  <http://example.org/s2> <http://example.org/p> <http://example.org/o2> .
```

Round-trip input messages with named graph quads:

```text
Message 1:
  <http://example.org/a> <http://example.org/b> <http://example.org/c> <http://example.org/g> .

Message 2:
  <http://example.org/d> <http://example.org/e> <http://example.org/f> <http://example.org/g> .
```

Expected result after serializing and parsing the output:

```text
Message 1:
  <http://example.org/a> <http://example.org/b> <http://example.org/c> <http://example.org/g> .

Message 2:
  <http://example.org/d> <http://example.org/e> <http://example.org/f> <http://example.org/g> .
```

## Error Tests

### 1. Message Delimiter Without Message Support

If a parser is not in RDF Messages mode and encounters a message delimiter, it must reject the document.

Input:

```turtle
<http://example.org/s> <http://example.org/p> <http://example.org/o> .
MESSAGE
```

Expected result:

```text
Parse error.
```

### 2. `@message` Without Trailing Dot

Input:

```turtle
VERSION "1.2-messages"
<http://example.org/s> <http://example.org/p> <http://example.org/o> .
@message <http://example.org/invalid>
```

Expected result:

```text
Parse error.
```

### 3. Message Delimiter Inside An Open Graph Block

A message delimiter must not be accepted inside an open graph block. It must be rejected instead of being interpreted as splitting the graph block across two messages.

Input:

```trig
VERSION "1.2-messages"
<http://example.org/g> {
  <http://example.org/a> <http://example.org/b> <http://example.org/c> .
MESSAGE
  <http://example.org/d> <http://example.org/e> <http://example.org/f> .
}
```

Expected result:

```text
Parse error.
```

