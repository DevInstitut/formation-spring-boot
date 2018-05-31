# TP Web Api

## Application Backend Gestion Personnel

* Créer un fork du projet `sgp-api`.
* Créer une application Spring Boot _sgp-webapi_ (sans Spring Security) que vous sauvegardez un dépôt Github : _sirh-gestion-personnel-webapi_.

## Ressource Départements

* Créer l'API Web _GET /api/departements_ retourne la liste des départements au format JSON.

## Ressource Collaborateurs

* Créer les API Web suivantes :
 * `GET /api/collaborateurs`  : retourne la liste des collaborateurs au format JSON.
 * `GET /api/collaborateurs?departement=[ID_DEPARTEMENT]`: retourne la liste des collaborateurs au format JSON dont le département a l'identifiant _ID_DEPARTEMENT_.
 * `GET /api/collaborateurs/[MATRICULE]` : retourne les informations d'un collaborateur.
 * `PUT /api/collaborateurs/[MATRICULE]` : modifie un collaborateur (données envoyées au format JSON).
 * `GET /api/collaborateurs/[MATRICULE]/banque` : récupère les coordonnées bancaires d'un collaborateur (nom de la banque, iban et bic)
 * `PUT /api/collaborateurs/[MATRICULE]/banque` : modifie uniquement les coordonnées bancaires d'un collaborateur.

* Tester via Postman que les API fonctionnent.

## Déployer l'application sur Heroku

* Créer une branche `production`.

* Modifier la partie `<build>` du pom.xml.

```xml
<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			
			<!-- RUBRIQUE A AJOUTER -->
			<plugin>
				<groupId>com.heroku.sdk</groupId>
				<artifactId>heroku-maven-plugin</artifactId>
				<configuration>
					<processTypes>
						<web>java $JAVA_OPTS -cp target/classes:target/dependency/* Main</web>
					</processTypes>
				</configuration>
			</plugin>
		</plugins>
</build>
```

* Ajouter la dépendance vers `PostgreSQL` .

```xml
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

* Modifier le fichier `application.properties` comme suit :



```properties
spring.jpa.show-sql=true
spring.jpa.generate-ddl=true

# pour un bug de déploiement avec PostgreSQL chez Heroku
spring.jpa.properties.hibernate.temp.use_jdbc_metadata_defaults = false
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQL9Dialect
```


* Déployer la branche de production.

* Mettre à jour la pull request avec le lien vers l'application.