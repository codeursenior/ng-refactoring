# NgRefactoring

A repository for practicing refactoring based on the reference book "Refactoring" by Martin Fowler in the context of an Angular application.

## Inital setup

1. Run `npm install` to install dependencies.
2. Run `ng serve --open`.

The webapp will be automatically opened in your browser at `http://localhost:4200/`. 

> 🐱‍👤 The application will automatically reload if you change any of the source files.


## Useful resources
- Catalog of code smells : https://refactoring.guru/fr/refactoring/smells 
- Catalog of refactorings with explication : https://refactoring.guru/fr/refactoring/catalog
- Catalog of refactorings with before/after code exemple : https://refactoring.com/catalog/

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.


## Pro tip

Run `ng test` to execute the unit tests.
Start build solid suite of unit tests before start refactoring !

## Let's start refactoring this codebase !

> All *Refactoring* are in italic. You can find them in the catalogue.

### Refactoring #1 : Decomposing the `statement` method
Look at the main method `statement` in `app.component.ts`.
When refactoring a long function like this, I mentally try to identify points that separate differents part of the overall behavior.
The first chunk that leaps to my eye is the `switch` statement in the middle of the method `statement`.

As I look at this chunck, I conclude that it's calculating the charge for one performance.
That conclusion is a piece of insight about the code...

> **But this understanding is in my head, a notoriously volatile form of storage.**
> 
> **I need to persist it by moving it from my head back into the code itself. 🐱‍👤**

Maybe we can start by *Extract Function* of the `switch` statement in a new method `amountFor(perf, play)` ? 

- [ ] Apply refactoring *Extract Function*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

### Refactoring #2 : Rename variable in `amountFor` method
Apply refactoring *Rename Variable* on variable `thisAmount` by `result`. 
The name of the method already contains the return value information. 
So, I don't need to force my clients to understand how is method implemented. They just need to understand its purpose.
So we can reduce the internal processing by returning a `result` variable for the `amountFor` method.

- [ ] Apply refactoring *Rename Variable*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

### Refactoring #3 : Removing the `play` variable
As I consider the parameters to `amountFor`, I look to see where they come from. 
`aPerformance` comes from the loop variable, so naturally changes with each iteration through the loop.

But `play` is computed from the performance, **so there no need to pass it in a parameter at all**.

I can just recalculate it within `amountFor`. 
When I'm breaking down a long function, I like to get rid ot variables like `play`, because temporary variables create a lot of locally scoped names that complicate extractions. 

The refactoring we can use here is *Replace Temp with Query*. 

First, create and use the new method `playFor` : 

```ts
playFor(performance) {
  return plays[performance.playId];
}
```

- [ ] Apply refactoring *Replace Temp with Query*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Then you can apply refactoring *Inline Variable* on constant `play`. 

- [ ] Apply refactoring *Inline Variable*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Now we can remove the parameter `play` in method `amountFor`, by applying the refactoring *Change Function Declarations*.

- [ ] Apply refactoring *Change Function Declaration*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Now, this last refactoring alarms some programmers...

Previously, the code to look up the play was executed once in each loop iteration.
Now ? It's executed thrice. 

But it is much easier to improve performance of a well-factored code base. 

The great benefice of removing local variables is that it makes it much easier to do extractions, since there is less local scope to deal with. Indeed, usually I'll take out local variables before I do extractions. 

Now that I'm done with the arguments to `amountFor`, I look back at where it's called.
It's being used to set a temporary variable that's not updated again, so we can apply `Inline Variable`.

```ts
totalAmount => amountFor(perf)
```


### Refactoring #4 : Extracting Volume Credits

Now I get the benefits from removing the `play` variable as it makes it easier to extract the volume credits
calculation by removing one of the locally scoped variables. 

I still have to deal with the other two. Again, `perf` is easy to pass in, but `volumeCredits` is a bi more tricky as it is an accumulator updated in each pass of the loop. So my best bet is to initialize a shadow of 
it inside the extracted function and return it. 

```ts
volumeCreditsFor(perf) {
  let volumeCredits = 0;
  volumeCredits += Math.max(perf.audience - 30, 0);
  if("comedy" === playFor(perf).type) volumeCredits += Math.floor(perf.audience / 5);
  return volumeCredits
}
```

- [ ] Apply refactoring *Extract Function*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

With our new method `volumeCreditsFor`, we can remove comment which has become useless.

- [ ] Apply refactoring *Remove Comment*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Now we can also rename parameter `perf` into `performance` for internal use in mehtod `volumeCreditsFor`.

- [ ] Apply refactoring *Rename Variable*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.


### Refactoring #5 : Removing the `format` Variable
Let's look at the main `statement` method again.

As suggested before, temporary variables can be a problem.

They are only useful within their own routine, and therefore they encourage long, complex routines.
Our next move, then, is to replace some of them. 

The easiest one is `format`.

This is a case of assigning 

### Refactoring #6 : Removing Total Volume Credits



### Refactoring #7 : How to keep improve the internal structure of our codebase ? (Make it fast)

Now the code has move from "Make it work" to Make it right. It's more easy to update...

But we can increase SPEED of our code base from now. It can be valuable if a lot mainteanbility or flexibility is need on this part of our code...

=> 3 suggestions...
