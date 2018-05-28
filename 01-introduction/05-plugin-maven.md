# Plugin Maven


```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>${spring-boot.version}</version>
</plugin>
```

## Tâches

La tâche **repackage** permet de générer un jar exécutable qui démarre un serveur::

```
mvn spring-boot:repackage
```

La tâche run démarre l'application

```
mvn spring-boot:run
```