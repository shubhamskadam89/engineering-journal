# Java Exception Handling --- Engineering Revision Notes

> Goal: Build an engineering mental model, not memorize definitions.

------------------------------------------------------------------------

# 1. What I should answer in an interview

## Exception

**Frame your answer**

-   What is it?
-   Why does Java need it?
-   What happens after it is thrown?

**Answer skeleton**

-   Represents an abnormal condition.
-   Is an object.
-   Carries message + type + stack trace (+ optional cause).
-   JVM propagates it until a matching handler is found.
-   Separates **business flow** from **error handling**.

``` mermaid
flowchart LR
A[Method] -->|throw| B(Exception Object)
B --> C{Handler Found?}
C -->|Yes| D[catch block]
C -->|No| E[JVM prints stack trace]
```

------------------------------------------------------------------------

# 2. Exception Hierarchy

``` mermaid
flowchart TD
Object
Object --> Throwable
Throwable --> Error
Throwable --> Exception
Exception --> RuntimeException
```

## Mental model

  Class              Think of it as
  ------------------ ------------------------------------
  Error              Runtime is in serious trouble
  Exception          Application can react
  RuntimeException   Programming/business/runtime issue

------------------------------------------------------------------------

# 3. Error vs Exception

## Answer should include

-   Recoverable?
-   Who usually fixes it?
-   Examples

  Error                           Exception
  ------------------------------- -----------------------------
  Usually unrecoverable           Usually recoverable
  JVM/resource/runtime problems   Application/domain failures
  Rarely caught                   Frequently handled

Examples

Error

-   OutOfMemoryError
-   StackOverflowError
-   VirtualMachineError

Exception

-   IOException
-   FileNotFoundException
-   SQLException

------------------------------------------------------------------------

# 4. Checked vs Unchecked

``` mermaid
flowchart LR
Checked --> Compiler
Unchecked --> Runtime
```

## Frame

Checked

-   Compiler forces handling
-   extends Exception
-   not RuntimeException

Unchecked

-   extends RuntimeException
-   compiler does not force handling

Examples

Checked

-   IOException
-   SQLException
-   ClassNotFoundException

Unchecked

-   NullPointerException
-   IllegalArgumentException
-   ArithmeticException

------------------------------------------------------------------------

# 5. throw vs throws

  throw       throws
  ----------- -------------------
  Action      Contract
  Statement   Method signature
  Runtime     Compiler enforced

``` java
throw new UserNotFoundException(id);
```

``` java
public User getUser(UUID id) throws IOException
```

**Memory trick**

> throw = doing

> throws = promising

------------------------------------------------------------------------

# 6. Exception Propagation

``` mermaid
flowchart TD
Main --> Service
Service --> Repository
Repository --> ReadFile

ReadFile -->|"throws IOException"| Repository
Repository --> Service
Service --> Main
Main --> JVM
```

## Stack Unwinding

When exception occurs

-   Current method stops immediately.
-   Remaining statements are skipped.
-   Stack frames are popped.
-   Caller gets opportunity to handle.
-   If nobody handles → JVM prints stack trace.

------------------------------------------------------------------------

# 7. finally

Execution order

``` mermaid
flowchart LR
Try --> Catch --> Finally
Try --> Finally
```

## Remember

✅ executes after return

✅ executes after exception

❌ may not execute

-   System.exit()
-   JVM crash
-   kill -9
-   Power failure

------------------------------------------------------------------------

# 8. Trick Question

``` java
try{
    return 10;
}
finally{
    return 20;
}
```

Answer

    20

Why?

finally executes before method exits.

**Avoid**

-   return inside finally
-   throw inside finally

They hide exceptions.

------------------------------------------------------------------------

# 9. Custom Exceptions

Bad

``` java
throw new RuntimeException("Not found");
```

Good

``` java
throw new OrderNotFoundException(orderId);
```

Why?

-   Better logs
-   Better debugging
-   Domain language
-   Better API responses

------------------------------------------------------------------------

# 10. Exception Hierarchy for Projects

``` mermaid
flowchart TD
RuntimeException
RuntimeException --> BusinessException
BusinessException --> ResourceNotFoundException
BusinessException --> ValidationException

ResourceNotFoundException --> UserNotFoundException
ResourceNotFoundException --> OrderNotFoundException
ResourceNotFoundException --> ProductNotFoundException

ValidationException --> InvalidCouponException
ValidationException --> EmailAlreadyExistsException
```

------------------------------------------------------------------------

# 11. Spring Boot Flow

``` mermaid
sequenceDiagram
Client->>Controller: GET /orders/10
Controller->>Service: getOrder()
Service->>Repository: findById()
Repository-->>Service: Optional.empty()
Service-->>Controller: throw OrderNotFoundException
Controller-->>ControllerAdvice: Exception
ControllerAdvice-->>Client: HTTP 404 + ApiError
```

------------------------------------------------------------------------

# 12. Global Exception Handler

``` java
@RestControllerAdvice
class GlobalExceptionHandler {

    @ExceptionHandler(OrderNotFoundException.class)
    ResponseEntity<ApiError> handle(...) {
    }
}
```

Purpose

-   One place
-   Consistent API
-   No duplicated try-catch

------------------------------------------------------------------------

# 13. Common Mistakes

❌ return null

✔ Optional or Exception

------------------------------------------------------------------------

❌ catch(Exception)

✔ catch specific exception

------------------------------------------------------------------------

❌ throw new RuntimeException()

✔ preserve cause

``` java
throw new RuntimeException("...", ex);
```

------------------------------------------------------------------------

❌ Generic RuntimeException

✔ Business specific exception

------------------------------------------------------------------------

❌ return inside finally

✔ Cleanup only

------------------------------------------------------------------------

# 14. Engineering Checklist

Before throwing an exception ask:

-   Is this exceptional?
-   Can caller recover?
-   Checked or unchecked?
-   Should I add business context?
-   Am I preserving original cause?
-   Will logs explain the failure?
-   Will API return correct HTTP status?

------------------------------------------------------------------------

# 15. Interview Traps

### Q1

Difference between throw and throws?

Keywords:

-   action
-   contract
-   runtime
-   compiler

------------------------------------------------------------------------

### Q2

Why not return null?

Keywords:

-   NPE
-   unclear intent
-   poor API

------------------------------------------------------------------------

### Q3

Why RuntimeException for business exceptions?

Keywords:

-   cleaner service
-   less boilerplate
-   ControllerAdvice

------------------------------------------------------------------------

### Q4

Does finally execute after return?

Yes.

Exception:

-   System.exit()
-   JVM crash

------------------------------------------------------------------------

### Q5

Why not catch Exception?

Keywords:

-   hides bugs
-   loses intent
-   broad scope

------------------------------------------------------------------------

### Q6

Repository or Service should throw?

Repository

-   Optional

Service

-   Business Exception

------------------------------------------------------------------------

# 16. Mental Models

## throw

> "I'm failing now."

## throws

> "Be prepared, I may fail."

## Checked Exception

> "Compiler wants acknowledgement."

## RuntimeException

> "Application chooses whether to handle."

## finally

> "Cleanup before leaving."

## ControllerAdvice

> "HTTP translator for exceptions."

------------------------------------------------------------------------

# Revision Score

✅ Exception hierarchy

✅ Error vs Exception

✅ Checked vs Unchecked

✅ throw vs throws

✅ Stack unwinding

✅ finally

✅ Custom exceptions

✅ Exception propagation

✅ Global exception handling

✅ Engineering exception design

**Next Revision:** Spring Bean Lifecycle & IoC Internals
