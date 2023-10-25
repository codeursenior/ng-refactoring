# Worksshop Refactoring - Pôle Web

A repository for practicing refactoring based on the reference book "Refactoring" by Martin Fowler in the context of an Angular application.

## Inital setup

1. Run `npm install` to install dependencies.
2. Run `ng serve --open`.

The webapp will be automatically opened in your browser at `http://localhost:4200/`. 

> 🐱‍👤 The application will automatically reload if you change any of the source files.

## Code scaffolding

You need to work in the `src/app/app.component.ts` folder.

You can also check the `app.component.html` display file.


## Useful resources
The following links will be useful to you throughout the workshop:

- Catalog of code smells : https://refactoring.guru/fr/refactoring/smells 
- Catalog of refactorings with explication : https://refactoring.guru/fr/refactoring/catalog
- Catalog of refactorings with before/after code exemple : https://refactoring.com/catalog/

## Let's start refactoring this codebase !

> All *Refactoring* are in italic. You can find them in the catalogue !

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

This is a case of assigning a function to a temp, which I prefer to replace with a declared function. 

So we extract function `format(aNumber)` :

```ts
format(aNumber) {
  return new Intl
    .NumberFormat("en-US", { style: "currency", currency: "USD", minimumFractionDigits: 2})
    .format(aNumber);
}
```

- [ ] Apply refactoring *Extract Function*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Now I'm not keen on the name - `"format"` doesn't really convey enough of what it's doing.

`"formatAsUSD"` would be a bit too long-winded since it's being used in a string template, particularly within this small scope.

I think the the fact that it's formatting a currency amount is the thing to hightlight here, so I pick a name that suggests that and apply *Change Function Declaration* to `usd`.

- [ ] Apply refactoring *Change Function Declaration*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.


### Refactoring #6 : Removing Total Volume Credits
Our next target variable is `volumeCredits`. 
This is a trickier case, as it's build up during the iterations of the loop. 

My first move, then, is to use refactoring *Split Loop* to seperate the accumulation of `volumeCredits` in method `statement`.

- [ ] Apply refactoring *Split Loop*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

With that done, we can use refactoring *Slide Statements* to move the declaration of the variable next to the loop. 

- [ ] Apply refactoring *Slide Statements*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Gathering together everything that updates the volumeCredits variable to makes it easier to do *Replace Temp with Query*.
As before, the first step is to apply *Extract Function* by createting a new method `totalVolumeCredits()` to do the overall calculation of the variable : 

```ts
totalVolumeCredits() {
  let volumeCredits = 0;
  for (let perf of invoice.performances) {
    volumeCredits += volumeCreditsFor(perf);
  }
  return volumeCredits;
}
```

- [ ] Apply refactoring *Extract Function*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Once everything is extracted, we can apply *Inline Variable* to remove temp variable `volumeCredits` : 

- [ ] Apply refactoring *Inline Variable*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

Then we can repeat the whole process to remove `totalAmount`.
We can apply the last 4 commit in the same order.

- [ ] Apply refactoring *Split Loop*, *Slide Statements*, *Extract function* and *Inline Variable*. 
- [ ] Verifiy than the code is always working.
- [ ] Commit this valuable refactoring.

### Refactoring #7 : How can we continue to improve the internal structure of our code base? (Make it fast)

Now the code has move from "Make it work" to "Make it right". It is much easier to evolve quickly.

But we can increase SPEED of our codebase from now, by accelerating the maintainability, flexibility and testability of our code.

Here are some suggestions if you still have time to continue refactoring your codebase:

1) Splitting the Phases of Calculation and Formatting.

2) Creating a Performance Calculator with Strategy Pattern.

3) Ensure that the code is battle unit-tested to enable future safe refactoring.
