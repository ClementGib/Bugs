# Bugs


### sub Object of AbstractTimer that rollback when it catch Exceptions: 

Le problème survient lorsqu'une exception Runtime est catché dans Scheduler.
Un EJB qui va catcher une Runtime exception non annoté @ApplicationException(rollback = true) va rollback automatiquement. Le AbstractTimer dont hérite le Scheduler exploite l'annotation @Timout qui est transactionel ce qui affecterait apparemment son comportement et le relancerait automatiquement après le rollback. Il faut le rendre "non-transactional"  `@TransactionAttribute(TransactionAttributeType.NEVER)`, pour ne plus avoir ce problème.

```java
    public abstract class AbstractTimer {
        @PostConstruct
        private void init() {
        ...
        }
    
    
        @Timeout
        @TransactionAttribute(TransactionAttributeType.NEVER)
        public void callBack() {
        ...
        }
        
        protected abstract void doSomething();
    }

    
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

