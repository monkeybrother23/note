## lambda

### ListToMap

```java
List<Object[]> list = new ArrayList<>();
list.add(new Object[]{"1", "NA"});
list.add(new Object[]{"2", "NT"});
Map<String, String> collect1 = list.stream().collect(
                Collectors.toMap(a -> String.valueOf(a[0]), a -> String.valueOf(a[1])));

Map<String, Object[]> collect3 = list.stream().collect(Collectors.toMap(a -> String.valueOf(a[0]), a -> a));
Map<String, Object[]> collect4 = list.stream().collect(Collectors.toMap(a -> String.valueOf(a[0]), Function.identity()));
```

#### duplicate

```java
Map<String, String> collect2 = list.stream().collect(
        Collectors.toMap(a -> String.valueOf(a[0]), a -> String.valueOf(a[1]), (key1, key2) -> key2));
```

### groupingBy

```java
Map<String, List<Object[]>> collect = list.stream().collect(
                Collectors.groupingBy(a -> String.valueOf(a[0]), Collectors.toList()));
```

### sort

```java
        List<String> list = new ArrayList<>();
        list.add("1");
        list.add("3");
        list.add(null);
        list.add("5");
        list.add("5");
        list.add("2");
        list.sort((a, b) -> {
            if (Objects.isNull(a)) {
                return 1;
            }
            if (Objects.isNull(b)) {
                return -1;
            }
            return Integer.parseInt(a) - Integer.parseInt(b);
        });
        // [1, 2, 3, 5, 5, null]
        System.out.println(list);
```

### filter

```java
        List<String> list = new ArrayList<>();
        list.add("5");
        list.add("5");
        list.add("2");
        List<String> collect = list.stream().filter(a -> Objects.equals(a,"5")).collect(Collectors.toList());
        //[5, 5]
        System.out.println(collect);
```

#### count

```java
        List<String> list = new ArrayList<>();
        list.add("5");
        list.add("5");
        list.add("2");
        if (list.stream().filter(a -> Objects.equals(a,"5")).count()>1){
            System.out.println("存在5");
        };
        if (list.stream().noneMatch(a -> Objects.equals(a, "3"))){
            System.out.println("不存在3");
        };
```

