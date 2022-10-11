# Bugs 🪲 
# Languages
[English](#english-)
[Français](#français-)



## English 🇬🇧 🇺🇸

*Stack: JEE, Java8, Wildfly 20*

### Siblings of @Timeout Object will rollback when it catch Exceptions: 
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


## Français 🇫🇷

*Stack: JEE, Java8, Wildfly 20*

### Un enfant d'une classe @Timeout va rollback quand il catch une Exception: 
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
