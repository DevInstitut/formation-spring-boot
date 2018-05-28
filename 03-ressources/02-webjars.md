# WebJars

Site Web : http://www.webjars.org.

Objectif => gérer les dépendances vers les ressources statiques automatiquement publiées via le chemin **/webjars/xxx**.


Exemple de WebJars :
```xml
<dependency>
    <groupId>org.webjars</groupId>
    <artifactId>jquery</artifactId>
    <version>1.11.1</version>
</dependency>
```

Exemple d'inclusion dans une page HTML :

```html
<script src="webjars/jquery/1.11.1/jquery.js"></script>
```
