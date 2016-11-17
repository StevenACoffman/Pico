
## How to Initialize a Map in Java 8

'''java    
    public Map<Integer, String> toMap(Integer listId, String contentItemId) {
         return Collections.unmodifiableMap(Stream.of(
                new SimpleEntry<>(listId, contentItemId))
                .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue)));
    }
```

### Google Guava has a more readable solution.

```java
private static final Map<String, Comparator<ContentList>> COMPARATOR_MAP = ImmutableMap.of(
            "id", Comparator.comparing(ContentList::getId),
            "name", Comparator.comparing(ContentList::getName),
            "last_modified", Comparator.comparing(ContentList::getLastModified));

      if("ASC".equals(direction))
       {
           allListsForUser.sort(COMPARATOR_MAP.get(orderBy));
       } else {
           allListsForUser.sort(COMPARATOR_MAP.get(orderBy).reversed());
       }
       return allListsForUser;

```
