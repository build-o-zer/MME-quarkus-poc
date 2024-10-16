# Questions et Réponses sur Quarkus et ses principaux scopes et annotations CDI

## Question 1 : Objectif et Mode d'utilisation des Scopes dans Quarkus : `@ApplicationScoped`, `@Dependent`, `@RequestScoped`, `@Inject`, `@Named`, `@Qualifier`, et `@Produces`

Voici une explication du but et de l'utilisation des scopes suivants sur Quarkus 

### Scopes dans Quarkus

- **`@ApplicationScoped`** :  
  Une instance unique du bean est créée pour l’application et partagée entre tous les points d’injection. L’instance est créée paresseusement, c’est-à-dire lorsqu’une méthode est invoquée sur le proxy client.

- **`@Dependent`** :  
  Il s’agit d’un pseudo-scope. Les instances ne sont pas partagées et chaque point d’injection génère une nouvelle instance du bean dépendant. Le cycle de vie du bean dépendant est lié au bean qui l’injecte : il sera créé et détruit en même temps que le bean qui l’injecte.

- **`@RequestScoped`** :  
  L’instance du bean est associée à la requête actuelle (généralement une requête HTTP). Une fois la requête terminée, le bean est détruit.

## Annotations dans Quarkus

- **`@Inject`** :  
  Indique au conteneur que le bean doit être injecté dans un autre bean. S’il n’y a pas de bean correspondant, la construction échoue.

- **`@Named`** :  
  Utilisée pour donner un nom à un bean, ce qui permet de le distinguer des autres beans du même type.

- **`@Qualifier`** :  
  Utilisé pour spécifier quel bean doit être injecté lorsqu’il existe plusieurs beans du même type. Permet aux développeurs de créer leurs propres qualificatifs personnalisés.

- **`@Produces`** :  
  Utilisé pour marquer une méthode ou un champ comme producteur d’un bean. Utile lorsque vous devez contrôler davantage l’instanciation d’un bean ou lors de l’intégration de bibliothèques tierces.

## Exemples d'utilisation 

- L’annotation **`@ApplicationScoped`** est utilisée pour les beans qui doivent être partagés dans toute l’application et dont une seule instance est nécessaire. Par exemple, un bean qui fournit une connexion à une base de données peut être annoté avec **`@ApplicationScoped`**

```
@ApplicationScoped
public class DatabaseConnection {
    // ...
}
 ```


- L’annotation **`@Dependent`** est utilisée pour les beans qui doivent être créés chaque fois qu’ils sont injectés. Par exemple, un bean qui représente un élément de données peut être annoté avec **`@Dependent`**
 ```
@Dependent
public class DataItem {
    // ...
}
 ```


- L’annotation **`@RequestScoped`** est utilisée pour les beans qui doivent être associés à une requête spécifique. Par exemple, un bean qui représente les données d’un formulaire peut être annoté avec **`@RequestScoped`**
 ```
@RequestScoped
public class FormData {
    // ...
}
 ```


- L’annotation **`@Inject`** est utilisée pour injecter un bean dans un autre bean. Par exemple, pour injecter le bean DatabaseConnection dans un autre bean, vous pouvez utiliser le code suivant:
 ```
@Inject
DatabaseConnection databaseConnection;

 ```


- Dans cet exemple, **`@Fast`** et **`@Slow`** sont des qualifiers qui permettent de choisir quelle implémentation du SpeedService injecter.
 ```
@ApplicationScoped
public class QualifierExample {

    @Inject
    @Fast
    private SpeedService fastService;

    @Inject
    @Slow
    private SpeedService slowService;

    public String getSpeedServicesInfo() {
        return "Fast: " + fastService.getSpeed() + ", Slow: " + slowService.getSpeed();
    }
}

// Define Qualifiers
import jakarta.inject.Qualifier;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
public @interface Fast {}

@Qualifier
@Retention(RetentionPolicy.RUNTIME)
public @interface Slow {}

// Fast and Slow Implementations
@ApplicationScoped
@Fast
public class FastService implements SpeedService {
    @Override
    public String getSpeed() {
        return "Fast Speed!";
    }
}

@ApplicationScoped
@Slow
public class SlowService implements SpeedService {
    @Override
    public String getSpeed() {
        return "Slow Speed!";
    }
}

 ```


- L’annotation **`@Produces`** est utilisée pour marquer une méthode ou un champ comme producteur d’un bean. Par exemple, vous pouvez utiliser le code suivant pour créer un bean qui produit un nombre aléatoire : 
 ```
@Produces
public int getRandomNumber() {
    return new Random().nextInt();
} 
 ```




## Question 2 : Donne moi l'équivalent de ces annotations et scopes Quarkus en SpringBoot

Voici l'équivalent des annotations Quarkus en Spring : 

1. **`@ApplicationScoped` (Quarkus)** :  
   - En **Spring**, l'équivalent est généralement **`@Singleton`** ou plus communément **`@Scope("singleton")`**. Par défaut, tous les beans Spring sont des singletons, donc pas besoin de cette annotation dans la plupart des cas.

2. **`@Dependent` (Quarkus)** :  
   - En **Spring**, l'équivalent serait **`@Scope("prototype")`**, ce qui signifie que chaque fois que le bean est injecté ou utilisé, une nouvelle instance est créée.

3. **`@RequestScoped` (Quarkus)** :  
   - En **Spring**, l'équivalent est **`@Scope(value = WebApplicationContext.SCOPE_REQUEST)`** ou simplement **`@RequestScope`** (dans les applications web).

4. **`@Inject` (Quarkus)** :  
   - En **Spring**, l'équivalent est **`@Autowired`**. Cependant, **`@Inject`** fonctionne aussi avec Spring car Spring prend en charge l'annotation **`@Inject`** de **Java CDI**.

5. **`@Named` (Quarkus)** :  
   - En **Spring**, l'équivalent est **`@Component`** ou **`@Service`** ou **`@Repository`** (selon le type de bean), mais si vous souhaitez spécifier un nom, vous pouvez utiliser **`@Component("myBeanName")`**. Si c'est un bean géré via CDI, **`@Named`** est également pris en charge en Spring.

6. **`@Qualifier` (Quarkus)** :  
   - En **Spring**, c'est directement **`@Qualifier`** aussi. Cette annotation est utilisée pour différencier plusieurs beans de même type.

7. **`@Produces` (Quarkus)** :  
   - En **Spring**, l'équivalent est **`@Bean`**. Cela indique que la méthode renverra un bean à gérer par le conteneur Spring.

Voici un tableau récapitulatif pour clarifier :

| Quarkus Annotation      | Spring Equivalent                |
|-------------------------|----------------------------------|
| `@ApplicationScoped`     | `@Scope("singleton")` (par défaut) |
| `@Dependent`             | `@Scope("prototype")`            |
| `@RequestScoped`         | `@RequestScope`                 |
| `@Inject`                | `@Autowired` / `@Inject`        |
| `@Named`                 | `@Component("beanName")` / `@Named` |
| `@Qualifier`             | `@Qualifier`                    |
| `@Produces`              | `@Bean`                         |
