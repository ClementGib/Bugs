# Bugs 🪲 

# Languages
[English](#english-)
&
[Français](#français-)



## English 🇬🇧

*Stack: JEE, Java8, Wildfly 20* (2022)

### 1: Siblings of @Timeout Object will rollback when it catch Exceptions: 
The problem happens when a `Runtime` exception is catch in a child Object of a transactional `@Timeout` Object.
An `EJB` that `catch` a `Runtime` exception without the annotation `@ApplicationException(rollback = true)` will automatically rollback. In this case  the `AbstractTimer` Object `extends` by the broker used the `@Timout` annotation, this annotation make the Object transactional and managed by EJB. The behavior will be affected and the broker will be automatically restart after the rollback. The solution is to set the Object Non-Transactional with `@TransactionAttribute(TransactionAttributeType.NEVER)` annotation.

```java
    public abstract class AbstractTimer {
    
        @Resource
        private TimerService timerService
        
        private Timer timer;
        
        @PostConstruct
        private void init() {
        timer = timerService.createCalendarTimer(SchedulerExpression, timerConfig);
        ...
        }
    
    
        @Timeout
        @TransactionAttribute(TransactionAttributeType.NEVER)
        public void callBack() {
        ...
        }
        
        protected abstract void doSomething();
    }

    @Startup
    @ApplicationScoped
    @Singleton
    @TransactionAttribute(TransactionAttributeType.NEVER)
    public class Scheduler extends AbstractTimer {
        @Override
        protected void doSomething() {
            ...
            try {
            ...
            } catch (IOExcetpion exception) {
            ...
            }
        }
    }
```

    
### 2: line endings with carriage return and line feed on different environments
When we develop on two distinct environments like Windows\Unix, there is differences. In the case of unit tests we expect to have the same behavior after processing. For processing or generating file the result can be different on Windows or Unix, Windows use the `carriage return` and `line feed` at the end of the line `CRLF` also called `\r\n` whereas Unix use just line feed `LF` also called `\n`.
The git repository will contain only one type and using both can be a problem.
To uniform the repository it needs to create the `.gitattributes` file to define attributs to the repository like the line ending for a type of file for example:

```.gitattributes
# Set the default behavior, in case people don't have core.autocrlf set.
* text=auto

# Explicitly declare text files you want to always be normalized and converted
# to native line endings on checkout.
*.c text
*.h text

# Declare files that will always have CRLF line endings on checkout.
*.sln text eol=crlf

# Declare file that will have CRLF ending
test.xml eol=crlf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```

## Français 🇫🇷

*Stack: JEE, Java8, Wildfly 20* (2022)

### 1: Un enfant d'une classe @Timeout va rollback quand il catch une Exception: 
Le problème survient lorsqu'une exception `Runtime` est catché dans l'objet enfant.
Un `EJB` qui va catcher une `Runtime` exception non annoté `@ApplicationException(rollback = true)` va rollback automatiquement. Le AbstractTimer dont hérite le broker exploite l'annotation `@Timout` qui est transactionel ce qui affecterait apparemment son comportement et le relancerait automatiquement après le rollback. La solution est de rendre l'Object Non-Transactional `@TransactionAttribute(TransactionAttributeType.NEVER)`.

```java
    public abstract class AbstractTimer {
    
        @Resource
        private TimerService timerService
        
        private Timer timer;
        
        @PostConstruct
        private void init() {
        timer = timerService.createCalendarTimer(SchedulerExpression, timerConfig);
        ...
        }
    
    
        @Timeout
        @TransactionAttribute(TransactionAttributeType.NEVER)
        public void callBack() {
        ...
        }
        
        protected abstract void doSomething();
    }

    @Startup
    @ApplicationScoped
    @Singleton
    @TransactionAttribute(TransactionAttributeType.NEVER)
    public class Scheduler extends AbstractTimer {
        @Override
        protected void doSomething() {
            ...
            try {
            ...
            } catch (IOExcetpion exception) {
            ...
            }
        }
    }
```
    
### 2: Fin de ligne et retour chariot et line feed sur des environnements différents
Quand on développe sur deux environnement distinct comme Windows\Unix il y a des différences, dans le cas des tests unitaires on s'attend à avoir le même résultat après le traitement. Pour le traitement ou la génération d'un fichier le resultat peut être différent sur Windows ou Unix, Windows utilise en fin de ligne les `retours chariots` et `line feed` `CRLF` appelé `\r\n` alors que sur Unix utilise juste `line feed` `LF` appelé `\n`.
Le repository git va contenir un unique type et l'utilisation des deux peut poser des problèmes.<br>
Pour uniformiser le repository il faut définir un fichier `.gitattributes` pour définir des attributs au repository git comme le type de retour chariot pour un format de fichiers:

```.gitattributes
# Set the default behavior, in case people don't have core.autocrlf set.
* text=auto

# Explicitly declare text files you want to always be normalized and converted
# to native line endings on checkout.
*.c text
*.h text

# Declare files that will always have CRLF line endings on checkout.
*.sln text eol=crlf

# Declare le fichier comme ayant toujours CRLF
test.xml eol=crlf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```
