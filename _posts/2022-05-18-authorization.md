---
layout: post 
title: "#16 - Authorization; What can you do?"
categories: misc
---
# Authorization; What can you do?

----

## Where are we at?

* We know how to create user
* We know how to login i.e. authenticate our users

----

## Agenda for today

* Recap: Authentication
* Live coding: Swagger set up and Authorization

----

### [Set up the assignment](https://classroom.github.com/a/x_XT-vwW)

Please follow [this link](https://classroom.github.com/a/x_XT-vwW) and accept the asignment. This serves as the base source code to the the tasks for this session. Do the tasks and push your changes to github.

### Run the application

```
mvn clean package
docker-compose build
docker-compose up
```

---

Alternatively

```
docker run --publish 5432:5432 --env POSTGRES_PASSWORD=mysecretpassword --detach postgres
+ run in IntelliJ IDEA
```

----

## Set up Swagger

### What is Swagger?

Swagger is an open source set of rules, specifications and tools for developing and describing RESTful APIs. The Swagger
framework allows developers to create interactive, machine and human-readable API documentation.

Swagger's open-source tooling usage can be broken up into different use cases: development, interaction with APIs, and
documentation.

#### Developing APIs

When creating APIs, Swagger tooling may be used to automatically generate an Open API document based on the code itself.
This embeds the API description in the source code of a project and is informally called code-first or bottom-up API
development.

Alternatively, using Swagger Codegen, developers can decouple the source code from the Open API document, and generate
client and server code directly from the design. This makes it possible to defer the coding aspect.

#### Interacting with APIs

Using the Swagger Codegen project, end users generate client SDKs directly from the OpenAPI document, reducing the need
for human-generated client code. As of August 2017, the Swagger Codegen project supported over 50 different languages
and formats for client SDK generation.

#### Documenting APIs

When described by an OpenAPI document, Swagger open-source tooling may be used to interact directly with the API through
the Swagger UI. This project allows connections directly to live APIs through an interactive, HTML-based user interface.
Requests can be made directly from the UI and the options explored by the user of the interface.

### Swagger with Spring Boot and `springdoc-openapi`

`springdoc-openapi` java library helps to automate the generation of API documentation using spring boot projects.
`springdoc-openapi` works by examining an application at runtime to infer API semantics based on spring configurations,
class structure and various annotations.

> ## Task
> Integrate Spring Boot with `springdoc-openapi`
>
>For the integration between `spring-boot` and `swagger-ui`, add the library to the list of your project dependencies (No
> additional configuration is needed)
>
>   ```xml
> <dependency>
>    <groupId>org.springdoc</groupId>
>    <artifactId>springdoc-openapi-ui</artifactId>
>    <version>1.6.8</version>
> </dependency>
>   ```

This will automatically deploy swagger-ui to a spring-boot application:

- Documentation will be available in HTML format, using the official swagger-ui jars
- The Swagger UI page will then be available at http://server:port/context-path/swagger-ui.html and the OpenAPI
- description will be available at the following url for json format: http://server:port/context-path/v3/api-docs
  - server: The server name or IP
  - port: The server port
  - context-path: The context path of the application
- Documentation can be available in yaml format as well, on the following path : /v3/api-docs.yaml

> ## Task
> The path where the swagger UI and API docs are located could potentially conflict with other application
> paths, so we will configure it to be available in a special location with prefix `/swagger`
>
>Add the following lines to your `application.properties` to specify the path where the swagger is located
>
>```properties
>springdoc.swagger-ui.path=/swagger/ui
>springdoc.api-docs.path=/swagger/api-docs
>```

> ## Task
> Allow everyone to access the swagger documentation
>
>Add the following line to your `SecurityConfig` after the "create user" config
>
>```java
>.antMatchers("/swagger/**").permitAll() /// everybody can access swagger
>```

> ## Task
> Visit the swagger documentation at `http://localhost:8080/swagger/-ui` and test all the endpoints.

## What is Authorization

Simply put, Authorization is what you are allowed i.e. authorized to do within a service. Authorization is the process
of giving someone the ability to access a resource.

A good example is house ownership. The owner has full access rights to the property (the resource) but can grant other
people the right to access it. You say that the owner authorizes people to access it. This simple example allows us to
introduce a few concepts in the authorization context.

### Role-Based Access Control (RBAC) and Authorization

> This is a theoretical definition of permissions and roles. Not to be confused with how Spring Security implementation.

RBAC treats authorization as **permissions** associated with **roles** and not directly with users. A role is nothing
but a collection of permissions. For example, imagine that you work as a department manager in an organization. In this
situation, you should have permissions that reflect your role, for example, the ability to approve vacation requests and
expense requests, assign tasks, and so on. To grant these permissions, a system manager would first create a role
called "Manager" (or similar). Then, they would assign these permissions to this role and would associate you with the "
Manager" role. Of course, other users that need the same set of permissions can be associated with that role.

The advantage of using RBAC is that managing authorization privileges becomes easier because system managers can deal
with users and permissions in bulk instead of having to deal with them one by one.

In our URL shortening system, we can define the following permissions

- users.create
- users.delete
- users.read
- users.all.read
- short-links.delete
- short-links.create
- short-links.read
- short-links.all.read

And we can define the following roles

- ANONYMOUS
  - users.create
- USER
  - short-links.create
  - short-links.read
- ADMIN
  - users.create
  - users.delete
  - users.read
  - users.all.read
  - short-links.delete
  - short-links.create
  - short-links.read
  - short-links.all.read

### Spring Expression-Based Access Control (Authority, Roles & Permissions)

> This is the practical implementation of permissions and roles by Spring Security

#### Granted Authority

Think of a GrantedAuthority as being a "permission" or a "right". Those "permissions" are (normally) expressed as
strings (with the getAuthority() method). Those strings let you identify the permissions and let your voters decide if
they grant access to something.

You can grant different GrantedAuthorities (permissions) to users by putting them into the security context. You
normally do that by implementing your own UserDetailsService that returns a UserDetails implementation that returns the
needed GrantedAuthorities.

#### Role

Roles (as they are used in many examples) are just "permissions" with a naming convention that says that a role is a
GrantedAuthority that starts with the prefix ROLE_. There's nothing more. A role is just a GrantedAuthority - a "
permission" - a "right".

But still: a role is just an authority with a special `ROLE_` prefix.

So in Spring security 3

- `@PreAuthorize("hasRole('ROLE_XYZ')")` is the same as
- `@PreAuthorize("hasAuthority('ROLE_XYZ')")`

and in Spring security 4

- `@PreAuthorize("hasRole('XYZ')")` is the same as
- `@PreAuthorize("hasAuthority('ROLE_XYZ')")`.

We shall see the purpose of `@PreAuthorize` in the next section

> ## Task
> Include granted authorities in your user model/entity
>
>Add the following property to `com.redi.demo.repository.model.User`. Don't forget to update the constructor(s)
>
>```java
>@ElementCollection(targetClass = String.class)
>private Collection<String> authorities;
>```
>
>`UserService#createUser` will now be broken. Fix by passing a single item list with `ROLE_USER` as
> the `authorities` i.e.
>
>```java
>final User user=new User(userRegistration.email,userRegistration.name,passwordEncoder.encode(userRegistration.password),List.of("ROLE_USER"));
>```
>This will ensure that anyone who signs up has the role `USER`
>
>And finally we must implement the `getAuthorities` method in our User class to ensure that the application security
> context is aware of the authorities/roles of the authenticated user. Replace the existing method with the following:
>```java
>@Override
>public Collection<? extends GrantedAuthority> getAuthorities() {
>    Collection<SimpleGrantedAuthority> authorities = new ArrayList<>();
>
>    for (String grantedAuthority : this.authorities) {
>        authorities.add(new SimpleGrantedAuthority(grantedAuthority));
>    }
>    
>    return authorities;
>}
>```

> ## Task
> Restart the server with a clean database and test all the endpoints again via swagger ui to ensure nothing is broken

#### Permissions

The most granular and flexible access control management by Spring Security. This is different from the theoretical
definition of permissions as defined in the RBAC section.

`hasPermission()` expressions are delegated to an instance of `PermissionEvaluator`. It is intended to bridge between
the expression system and Spring Security’s ACL system, allowing you to specify authorization constraints on domain
objects, based on abstract permissions. The interface has two methods:

```java
public interface PermissionEvaluator extends AopInfrastructureBean {

    /**
     * @param authentication represents the user in question. Should not be null.
     * @param targetDomainObject the domain object for which permissions should be
     * checked. May be null in which case implementations should return false, as the null
     * condition can be checked explicitly in the expression.
     * @param permission a representation of the permission object as supplied by the
     * expression system. Not null.
     * @return true if the permission is granted, false otherwise
     */
    boolean hasPermission(Authentication authentication, Object targetDomainObject, Object permission);

    /**
     * Alternative method for evaluating a permission where only the identifier of the
     * target object is available, rather than the target instance itself.
     * @param authentication represents the user in question. Should not be null.
     * @param targetId the identifier for the object instance (usually a Long)
     * @param targetType a String representing the target's type (usually a Java
     * classname). Not null.
     * @param permission a representation of the permission object as supplied by the
     * expression system. Not null.
     * @return true if the permission is granted, false otherwise
     */
    boolean hasPermission(Authentication authentication, Serializable targetId, String targetType, Object permission);

}
```

which map directly to the available versions of the expression, with the exception that the first argument (the
`Authentication` object) is not supplied. The first is used in situations where the domain object, to which access is
being controlled, is already loaded. Then expression will return true if the current user has the given permission for
that object. The second version is used in cases where the object is not loaded, but its identifier is known. An
abstract "type" specifier for the domain object is also required, allowing the correct ACL permissions to be loaded.
This has traditionally been the Java class of the object, but does not have to be as long as it is consistent with how
the permissions are loaded.

To use `hasPermission()` expressions, you have to explicitly configure a `PermissionEvaluator` in your application
context. The `PermissionEvaluator` allows you to implement a dynamic and granular permission evaluation.

> We shall see how `hasPermission()` can be used in the following section.

#### Expression-Based Access Control

Spring Security uses Spring EL (Expression Language) for expression support
and [you should look at how that works](https://docs.spring.io/spring-security/site/docs/5.3.x/reference/html5/#el-access)
if you are interested in understanding the topic in more depth. Expressions are evaluated with a "root object" as part
of the evaluation context. Spring Security uses specific classes for web and method security as the root object, in
order to provide built-in expressions and access to values such as the current principal.

Any method can be annotated and protected with an Expression-Based Access Control.

- `hasRole(String role)` Returns true if the current principal has the specified role.
- `hasAnyRole(String... roles)` Returns true if the current principal has any of the supplied roles (given as a
  comma-separated list of strings).
- `hasAuthority(String authority)`
- `hasAnyAuthority(String... authorities)`
- `hasPermission(Object target, Object permission)`
- `hasPermission(Object targetId, String targetType, Object permission)`
- `isAuthenticated()`
- `permitAll()`

[See more here ](https://docs.spring.io/spring-security/site/docs/5.3.x/reference/html5/#el-common-built-in)

#### Access Control using `@PreAuthorize`

The most useful annotation is `@PreAuthorize` which decides whether a method can actually be invoked or not. For example

```java
@PreAuthorize("hasRole('SHORT_LINKS_CREATE')")
public void createShortLink(CreateShortLinkRequest request);
@PreAuthorize("hasAuthority('ROLE_SHORT_LINKS_CREATE')") // same as above
public void createShortLink(CreateShortLinkRequest request);
@PreAuthorize("hasAuthority('SHORT_LINKS_CREATE')")
public void createShortLink(CreateShortLinkRequest request); 
```

which means that access will only be allowed for users with the role "SHORT_LINKS_CREATE". Obviously the same thing could easily be
achieved using a traditional configuration and a simple configuration attribute for the required role. But what about:

```java
@PreAuthorize("hasPermission(hasPermission(#key, 'short-links', 'read')")
public void expandShortLink(String key);
```

Here we’re actually using a method argument as part of the expression to decide whether the current user has the
"short-links.read" permission for the given `ShortLink`. The built-in `hasPermission()` expression is linked into the
Spring Security ACL module through the application context, as we’ll see below. You can access any of the method
arguments by name as expression variables.

> ## Task
> New rule is that only users with authority (or permission) `short-links.create` can create short links,
> and only users with authority (or permission) `short-links.read` can expand short links
>- We annotate `ShortLinksService#createShortLink` with `@PreAuthorize("hasAuthority('short-links.create')")`
>- We annotate `ShortLinksService#expandShortLink` with `@PreAuthorize("hasAuthority('short-links.read')")`
>
> We are annotating the `ShortLinksService` only to demonstrate that any bean method can be protected with these 
> annotations. We could in theory achieve the same thing by annotating the controller methods.
> 
> 
> This can also be achieved by configuring the `HttpSecurity` in `SecurityConfig` as follows
> - `.antMatchers(HttpMethod.POST, "/links").hasAuthority("short-links.create")`
> - `.antMatchers(HttpMethod.GET, "/{key}").hasAuthority("short-links.read")`
>
>Restart the server and test this via swagger by using a user session.
>
>Observe that the user cannot create or access short links. This is because we have not granted the user any permission.
> To fix that we should update the `grantedAuthorities` of the user during signup in `UserService#createUser`
>
>```java
>final User user=new User(userRegistration.email,userRegistration.name,passwordEncoder.encode(userRegistration.password),
>                       List.of("ROLE_USER", "short-links.create", "short-links.read"));
>```

In the task above, we have used Spring's granted authorities as permissions, observe that there is no `ROLE` prefix and
they are not upper case.

> ## Task
> Configure the roles and permission mapping dynamically.
> Spring does not have a way of having roles map to permissions as we have defined in RBAC section above
>
> To do that we must configure our role to permission mapping in `UserService`.
>
> Add the following static variables and method to `UserService`
>```java
>private static final Set<String> ALL_PERMISSIONS = Set.of(
>        "users.create",
>        "users.delete",
>        "users.read",
>        "users.all.read",
>        "short-links.delete",
>        "short-links.create",
>        "short-links.read",
>        "short-links.all.read"
>);
>
>private static final Map<String, Collection<String>> ROLES = Map.ofEntries(
>        Map.entry("ROLE_ADMIN", ALL_PERMISSIONS),
>        Map.entry("ROLE_USER", List.of(
>                "short-links.create",
>                "short-links.read"
>        )));
>
>private static final Map<String, Collection<String>> USER_ROLES =
>        Map.ofEntries(Map.entry("redi@redi.com", List.of("ROLE_ADMIN")));
>
>private static final String DEFAULT_ROLE = "ROLE_USER";
>
>/**
> * Return all the permissions allowed for the user identified by the provided email
> */
> 
    /**
     * Return all the authorities (permissions) allowed for the user identified by the provided email
     */
    private static Collection<String> findAuthorities(String email) {
        // Get all the roles for this user
        // {ROLE_A, ROLE_B}
        final Collection<String> roles = USER_ROLES.getOrDefault(email, List.of(DEFAULT_ROLE));

        // Get all the permissions for the user with roles
        // {a_1, a_2, b_1, b_2} because ROLE_A -> {a_1, 1_2} and ROLE_B -> {b_1, b_2}
        final Set<String> permissions = roles.stream()
                .map(ROLES::get)
                .flatMap(Collection::stream)
                .collect(Collectors.toSet());

        // Add all roles and permissions together and return as authorities
        final Collection<String> authorities = new HashSet<>();

        authorities.addAll(roles);
        authorities.addAll(permissions);

        return authorities;
    }

> 
    /**
     * Return all the authorities (permissions) allowed for the user identified by the provided email
     */
    private static Collection<String> findAuthorities(String email) {
        // Get all the roles for this user
        // {ROLE_A, ROLE_B}
        final Collection<String> roles = USER_ROLES.getOrDefault(email, List.of(DEFAULT_ROLE));

        // Get all the permissions for the user with roles
        // {a_1, a_2, b_1, b_2} because ROLE_A -> {a_1, 1_2} and ROLE_B -> {b_1, b_2}
        final Set<String> permissions = roles.stream()
                .map(ROLES::get)
                .flatMap(Collection::stream)
                .collect(Collectors.toSet());

        // Add all roles and permissions together and return as authorities
        final Collection<String> authorities = new HashSet<>();

        authorities.addAll(roles);
        authorities.addAll(permissions);

        return authorities;
    }

> 
    /**
     * Return all the authorities (permissions) allowed for the user identified by the provided email
     */
    private static Collection<String> findAuthorities(String email) {
        // Get all the roles for this user
        // {ROLE_A, ROLE_B}
        final Collection<String> roles = USER_ROLES.getOrDefault(email, List.of(DEFAULT_ROLE));

        // Get all the permissions for the user with roles
        // {a_1, a_2, b_1, b_2} because ROLE_A -> {a_1, 1_2} and ROLE_B -> {b_1, b_2}
        final Set<String> permissions = roles.stream()
                .map(ROLES::get)
                .flatMap(Collection::stream)
                .collect(Collectors.toSet());

        // Add all roles and permissions together and return as authorities
        final Collection<String> authorities = new HashSet<>();

        authorities.addAll(roles);
        authorities.addAll(permissions);

        return authorities;
    }

> 
>    /**
>     * Return all the authorities (permissions) allowed for the user identified by the provided email
>     */
>    private static Collection<String> findAuthorities(String email) {
>        // Get all the roles for this user
>        // {ROLE_A, ROLE_B}
>        final Collection<String> roles = USER_ROLES.getOrDefault(email, List.of(DEFAULT_ROLE));
>
>        // Get all the permissions for the user with roles
>        // {a_1, a_2, b_1, b_2} because ROLE_A -> {a_1, 1_2} and ROLE_B -> {b_1, b_2}
>        final Set<String> permissions = roles.stream()
>                .map(ROLES::get)
>                .flatMap(Collection::stream)
>                .collect(Collectors.toSet());
>
>        // Add all roles and permissions together and return as authorities
>        final Collection<String> authorities = new HashSet<>();
>
>        authorities.addAll(roles);
>        authorities.addAll(permissions);
>
>        return authorities;
>    }
>
>```
>
>And then we fix the created user in `UserService#createUser` as follows
> ```java
> final User user = new User(userRegistration.email, userRegistration.name,
>         passwordEncoder.encode(userRegistration.password), findAuthorities(userRegistration.email));
>```
>
> Now we can add roles and permissions dynamically whenever we want

> ## Task
> Another rule;
> - Users should only be able to read their own user details
> - Admins should be able to read any user details
    > To do that we protect `UserController#get` with the following annotation
> ```java
> @PreAuthorize("hasRole('ADMIN') || hasPermission(#email,'users','read')")
> ```
>
> In order for the `hasPermission` expression to work, we must configure a PermissionEvaluator component to check if
> the current user is indeed the same user being requested. Please add the following class in the package `com.redi.demo.services`
>
> ```java
> @Service
> public class PermissionEvaluatorService implements PermissionEvaluator {
>
>    @Override
>    public boolean hasPermission(Authentication authentication, Object targetDomainObject, Object permission) {
>        return false;
>    }
>
>    @Override
>    public boolean hasPermission(Authentication authentication, Serializable targetId, String targetType, Object permission) {
>        final User user = (User) authentication.getPrincipal();
>
>        if (effectivePermission(targetType, permission).equals("users.read")
>                && targetId.toString().equals(user.getEmail())) {
>            return true;
>        }
>        return false;
>    }
>
>    private static String effectivePermission(String targetType, Object permission) {
>        return format("%s.%s", targetType, permission);
>    }
>}
>```


> Bonus task: Add an endpoint to delete short urls and only allow admins or the owner of the short url to delete it.

## Links

- [Spring Security Reference](https://docs.spring.io/spring-security/site/docs/5.3.x/reference/html5/#el-access)
- [Spring role vs authority](https://stackoverflow.com/a/19542316)
- [Swagger on Wikipedia](https://en.wikipedia.org/wiki/Swagger_(software))
- [`springdoc-openapi`](https://springdoc.org/#Introduction)
- [Spring Security: Custom Permission Evaluator](https://blog.jdriven.com/2019/10/spring-security-custom-permission-evaluator/)
