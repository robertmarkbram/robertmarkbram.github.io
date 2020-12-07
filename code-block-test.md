Java code block test.

```java
public void validate(final Conversation conversation) {
   validateBrand(conversation);
   validateClient(conversation);
   validateService(conversation);
   validateMessageType(conversation);
   validateDataBelongsTogether(conversation);

   log.info("Validated conversation: {} as\n{}", conversation.getLabel(), conversation);
}

```