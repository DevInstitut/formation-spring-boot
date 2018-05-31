# TP Démo Spring Boot

Spring est le leader des frameworks et outils Java pour réaliser des applications d'entreprise.

Spring apporte un écosystème riche et surtout flexible. 
La flexibilité a un coût : les multiples configurations nécessaires pour prendre des décisions.

Spring a initié le projet _Spring Boot_ comme un framework qui prend quelques décisions initiales pour vous grâce au principe de l'_autoconfiguration_ : Spring Boot détecte la présence d'une librairie dans le classpath et s'autoconfigure.

Pour démarrer un projet Spring rapidement, le site http://start.spring.io est pratique.

Afin d'illustrer la perspective de productivité avec l'écosystème Spring, nous allons faire un tour d'horizon des possibilités.


## Initialisation du projet

### Génération du projet

* Rendez-vous sur le site http://start.spring.io.

* Générer un projet avec les informations suivantes :

 * Group : dev
 * Artifact : exemple-spring-boot
 * Dependencies : _Web_, _JPA_, _H2_, _Devtools_, _MySQL_.

* Décompresser l'archive dans le répertoire de votre choix.

### Projet Github

* Créer un dépôt Github : _exemple-spring-boot_.

* Envoyer vos sources vers Github :

```
cd exemple-spring-boot
git init
git add .
git commit -m "init Spring Boot"
git remote add origin https://github.com/VOTRE_NOM_UTILISATEUR/exemple-spring-boot.git
git pull origin master
git push origin master
```

###  STS

* Importer le projet sous STS.

* Démarrer l'application via la classe _ExempleSpringBootApplication_ (clic droit > _Run As_ > _Spring Boot App_).

* Arrêter l'application.

* Démarrer l'application via la tâche Maven :

```
mvn spring-boot:run
```

* Arrêter l'application.

* Renommer le package _dev.exemplespringboot_ en _dev.exemple_.



## Premier service avec Spring Boot

* Créer une entité _dev.exemple.entite_ :

```java
@Entity
@Table(name = "EXEMPLE")
public class Exemple {

@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
@Column(name = "ID")
private Integer id;

@Column(name = "CHAMP_1")
private String champ1;

@Column(name = "CHAMP_2")
private String champ2;

...

}
```

* Créer un repository _dev.exemple.repository.ExempleRepository_ :

```java
public interface ExempleRepository extends JpaRepository<Exemple, Integer>{

}
```

* Créer un contrôleur _dev.exemple.controller.ExempleApiController_ :

```java
@RestController
@RequestMapping("/exemples")
public class ExempleApiController {

@Autowired private ExempleRepository exempleRepo;

@GetMapping
public List<Exemple> listerexemples() {
    return this.exempleRepo.findAll();
}

}
```

* Configurer le fichier _src/main/resources/application.properties_ :

[source, properties]
```
# datasource
spring.datasource.url = jdbc:h2:mem:testdb
spring.datasource.driver-class-name = org.h2.Driver
spring.datasource.username = sa
spring.datasource.password =
spring.jpa.generate-ddl=true
```

* Démarrer l'application.

* Connectez-vous à la console _H2_ : http://localhost:8080/h2-console/ avec les informations valorisées dans le fichier _src/main/resources/application.properties_.

* Ajouter des données à la table _EXEMPLE_.

* Tester le service développé via l'adresse : http://localhost:8080/exemples. Vérifier que les données s'affichent.

* Créer un jar exécutable via Maven :

```
cd exemple-spring-boot
mvn clean package -Dmaven.test.skip
```

* Démarrer l'application en ligne de commande :

```
java -jar target/exemple-spring-boot-0.0.1-SNAPSHOT.jar
```

* Arrêter l'application.




## Templating & WebJar

###  Templating
Spring Boot supporte plusieurs moteurs de templating :
* Freemarker
* Groovy
* Thymeleaf
* Mustache
* JSP (avec quelques limitations)

Les templates se trouvent par défaut dans le répertoire _src/main/resources/templates_.

Dû à des limitations de Tomcat, le templating JSP ne fonctionne pas avec un JAR exécutable. Il faut un WAR (qui peut-être aussi exécutable).

Faisons à présent les modifications nécessaires pour utiliser JSP comme moteur de templating.

* Modifier le fichier _pom.xml_ comme suit :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
...
<packaging>war</packaging> <1>
....
<dependencies>
    ...
    <!-- Dépendances support de JSP & JSTL --> <2>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>jstl</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-jasper</artifactId>
        <scope>provided</scope>
    </dependency>
</dependencies>
...
</project>	
```
<1> Modifier le type de packaging de _jar_ en _war_.
<2> Ajouter les 3 dépendances.

* Compléter le fichier _src/main/resources/application.properties_ :

[source, properties]
```
# ...
# mvc
spring.mvc.view.prefix = /WEB-INF/views/
spring.mvc.view.suffix = .jsp
```

* Ajouter un contrôleur _dev.exemple.controller.AccueilController_ :

```java
@Controller
@RequestMapping("/accueil")
public class AccueilController {

@Autowired private ExempleRepository exempleRepo;

@GetMapping
public ModelAndView afficherAccueil() {
    ModelAndView mv = new ModelAndView();
    mv.setViewName("accueil"); <1>
    mv.addObject("listeExemples", exempleRepo.findAll()); <2>
    return mv;
}

}
```
<1> Nom logique de la vue.
<2> Liste d'exemples dans le modèle Spring MVC.

* Ajouter un fichier _src/main/webapp/WEB-INF/views/accueil.jsp_ :

```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Exemple</title>
</head>
<body>
<table class="table">
    <thead>
        <tr>
            <th>Id</th>
            <th>Champ 1</th>
            <th>Champ 2</th>
        </tr>
    </thead>
    <tbody>
        <c:forEach items="${listeExemples}" var="ex">
            <tr>
                <td>${ex.id}</td>
                <td>${ex.champ1}</td>
                <td>${ex.champ2}</td>
            </tr>
        </c:forEach>
    </tbody>
</table>

</body>
</html>
```

* Démarrer l'application.

* Ajouter des données via la console _H2_.

* Vérifier l'affichage de la page d'accueil : http://localhost:8080/accueil.

###  WebJars

Spring Boot est pré-configuré pour gérer les _WebJars_. Il s'agit de librairies/frameworks UI (comme Twitter Bootstrap, JQuery, Angular, ...) packagés sous la forme d'un fichier Jar.

Vous souhaitez vous un aperçu de l'écosystème ? Visitez le site :http://www.webjars.org/. Cherchez y la bibliothèque _Bootstrap_.

Nous allons utiliser un WebJar pour Twitter Bootstap.

* Ajouter la dépendance suivante :

```xml
<dependency>
<groupId>org.webjars</groupId>
<artifactId>bootstrap</artifactId>
<version>3.3.7</version>
<scope>runtime</scope>
</dependency>
```

* Compléter la page _accueil.jsp_ pour ajouter le lien vers Bootstrap.


```html
<link rel="stylesheet" href="webjars/bootstrap/3.3.7/css/bootstrap.css" />
```

* Vérifier que Bootstrap est bien pris en compte.

* Générer un WAR exécutable via Maven :

```
cd exemple-spring-boot
mvn clean package -Dmaven.test.skip
```

* Démarrer l'application en ligne de commande :

```
java -jar target/exemple-spring-boot-0.0.1-SNAPSHOT.war
```

* Vérifier que la page _accueil_ s'affiche.



## Spring Security

Spring Security s'intègre bien dans un projet Spring Boot avec un minimum de configuration.

Faisons un exemple.

* Ajouter une dépendance vers le Starter Spring Security :

```xml
<!-- Spring Security -->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

* Ajouter au projet la classe de configuration _dev.exemple.WebSecurityConfig_ :

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests().anyRequest().authenticated().and().formLogin();
}

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication().withUser("user").password("password").roles("USER");
}

}
```

* Tester l'application. Vérifier que l'accès à la page d'accueil est soumis à authentification.



## Spring Boot CLI

###  Installation

_Spring Boot CLI_ est un outil en ligne de commande permettant de rapidement prototyper une application Spring.

Il permet d'exécuter des scripts _Groovy_.

* Télécharger et décompresser l'archive : http://repo.spring.io/release/org/springframework/boot/spring-boot-cli/1.5.3.RELEASE/spring-boot-cli-1.5.3.RELEASE-bin.zip

* Ajouter le répertoire décompresser dans la variable _PATH_.

* Vérifier que vous avez accès à la commande _spring_ :

```
spring --version
```

###  Exemple de service REST

* Créer un fichier _cotisations.groovy_ avec le contenu suivant :

```java
@RestController
class CotisationsController {

  @RequestMapping("/")
  String lister() {
      "Les cotisations arrivent bientôt"
  }

}
```

* Exécuter le fichier :

```
spring run cotisations.groovy
```

Pour changer le port d'exécution : 

```
spring run cotisations.groovy -- --server.port=9000
```

* Accéder à la racine de l'application via le navigateur. Vérifier que la chaîne de caractères "Les cotisations arrivent bientôt" s'affichent.

###  Accès à une base de données

* Compléter le fichier _cotisations.groovy_ comme suit :

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

@Configuration
@Grab(group='mysql', module='mysql-connector-java', version='5.1.41')
class DataSourceConfig {
@Bean
public DataSource dataSource() {
    DriverManagerDataSource dataSource = new DriverManagerDataSource();
    dataSource.setDriverClassName("com.mysql.jdbc.Driver");
    dataSource.setUrl("jdbc:mysql://localhost:3306/sirh-paie?useSSL=false");
    dataSource.setUsername("sirh-paie");
    dataSource.setPassword("sirh-paie");
    return dataSource;
}
}

class Cotisation {
def id
def code
def libelle
def tauxSalarial
def tauxPatronal
}

@RestController
class CotisationsController {

@Autowired JdbcTemplate jdbcTemplate;

RowMapper<Cotisation> rowMapper = {rs,num -> return new Cotisation(
    id:rs.getInt("ID"), 
    libelle:rs.getString("LIBELLE"),
    code:rs.getString("CODE"),
    tauxSalarial:rs.getString("TAUX_SALARIAL"),
    tauxPatronal:rs.getString("TAUX_PATRONAL")
)}
  
  @RequestMapping("/")
  List<Cotisation> lister() {
    return this.jdbcTemplate.query("select * from COTISATION", rowMapper)
  }

}
```

* Démarrer lapplication

```
spring run cotisations.groovy
```

* Vérifier que vous accédez à la liste des cotisations en base de données.

* Séparer le code en 3 fichiers :

```
entites.groovy		<1>
datasource.groovy 	<2>
cotisations.groovy	<3>
```
<1> la classe Cotisation
<2> la classe DataSourceConfig (avec les imports)
<3> la classe CotisationsController

* Démarrer l'application comme suit :

```
spring run *.groovy
```

* Vérifier que vous accédez à la liste des cotisations en base de données.

###  Packaging

* Générer un JAR exécutable :

```
spring jar cotisations-web-api.jar *.groovy
```

Par défaut, le packaging :

* inclut : _public/**, resources/**, static/**, templates/**, META-INF/**, *_
* exclut : _.*, repository/**, build/**, target/**, **/*.jar, **/*.groovy_.


* Démarrer le serveur :

```
java -jar cotisations-web-api.jar
```

* Vérifier que vous accédez à la liste des cotisations en base de données.

###  Initialiser un projet
Spring Boot CLI permet de générer un projet Spring Boot via l'outil : https://start.spring.io/.

* Générer un projet :

```
spring init --dependencies=web,data-jpa test-spring-cli
```

* Pour avoir accès à la liste des configurations possibles :

```
spring init --list
```
