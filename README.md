# Bugs 🪲 
# Languages
[English](#english-)
[Français](#français-)



## English 🇬🇧 🇺🇸
**Stack: JEE, Java8, Wildfly 20**
### Siblings of abstract timer @Timeout rollback when it catch Exceptions: 
The problem happend Le problème survient lorsqu'une exception Runtime est catché dans l'objet enfant (Scheduler).
Un EJB qui va catcher une Runtime exception non annoté `@ApplicationException(rollback = true)` va rollback automatiquement. Le AbstractTimer dont hérite le Scheduler exploite l'annotation `@Timout` qui est transactionel ce qui affecterait apparemment son comportement et le relancerait automatiquement après le rollback. Il faut le rendre "non-transactional"  `@TransactionAttribute(TransactionAttributeType.NEVER)`, pour ne plus avoir ce problème.

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

### Siblings of abstract timer @Timeout rollback when it catch Exceptions: 
Le problème survient lorsqu'une exception Runtime est catché dans l'objet enfant (Scheduler).
Un EJB qui va catcher une Runtime exception non annoté `@ApplicationException(rollback = true)` va rollback automatiquement. Le AbstractTimer dont hérite le Scheduler exploite l'annotation `@Timout` qui est transactionel ce qui affecterait apparemment son comportement et le relancerait automatiquement après le rollback. Il faut le rendre "non-transactional"  `@TransactionAttribute(TransactionAttributeType.NEVER)`, pour ne plus avoir ce problème.

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
