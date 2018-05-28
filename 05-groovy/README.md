# Scripting Groovy

Spring Boot CLI permet d'écrire des scripts groovy démarrés en application Web.

Exemple de Script (fichier exempleApp.groovy) :


```groovy
@RestController
class BonjourController {
    @RequestMapping("/bonjour")
    String bonjour() {
        return "bonjour depuis Spring Boot"
    }
}
```

Démarrer l'application :

```
spring run exempleApp.groovy
```
