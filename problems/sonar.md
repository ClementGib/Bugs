Sonar can create a smell "use original value instead" when using `valueOf(map.getKey().hashcode())` to avoid smell you can: 
`key += entry.getValue().hashcode() & Integer.MAX_VALUE`