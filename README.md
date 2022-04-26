# Bugs


### sub Object of AbstractTimer that rollback when it catch Exceptions: 

Le problème survient lorsqu'une exception Runtime est catché dans Scheduler.
Un EJB qui va catcher une Runtime exception non annoté @ApplicationException(rollback = true) va rollback automatiquement. Le AbstractTimer dont hérite le Scheduler exploite l'annotation @Timout qui est transactionel ce qui affecterait apparemment son comportement et le relancerait automatiquement après le rollback.

```java
    @Timeout
    @TransactionAttribute(TransactionAttributeType.NEVER)
    public void do() {
    ...
    }
    
    public class Scheduler extends AbstractTimer {
    ...
    try {
    ...
    } catch (IOExcetpion exception) {
    ...
    }
    }
```

