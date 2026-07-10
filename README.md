# Style Guide

---

EN [English](./README.md) | RU [Русский](./README.ru.md)

---

## Table of Contents

- [HTML](#html)
- [ACCESSIBILITY](#accessibility)
- [SCSS](#scss)
- [TS](#ts)
- [TESTING](#testing)

---

## HTML


- Repeating Elements
    - Extract into separate components:
        - If it is an independent element, such as a form, table, card, etc.
        - If it is a large block of code that repeats in multiple places.
    - Use `ng-template`:
        - If it is a small block of code that repeats in multiple places but is not an independent element.

---

- If multiple tags are located inside a single parent tag, each of these tags must be separated by an empty line. Single-line tags do not need to be separated.

Incorrect:

```html
<div class="flex flex-row mt-3">
    <div class="flex flex-row align-self-stretch w-full wrapper-client-base">
        <app-dashboard-client-base [clients]="clientGroups"/>
    </div>
    <div class="flex flex-row w-full align-self-stretch">
        <app-site-visitors-table [clients]="clientGroups"/>
        <app-available-communications-canals-table [data]="communicationCanalsData"/>
    </div>
</div>
<div class="flex flex-row mt-3">
    <div class="flex flex-row align-self-stretch w-full wrapper-client-base">
        <app-dashboard-client-base [clients]="clientGroups"/>
    </div>
</div>
```

Correct:

```html
<div class="flex flex-row mt-3">
    <div class="flex flex-row align-self-stretch w-full wrapper-client-base">
        <app-dashboard-client-base [clients]="clientGroups"/>
    </div>

    <div class="flex flex-row w-full align-self-stretch">
        <app-site-visitors-table [clients]="clientGroups"/>
        <app-available-communications-canals-table [data]="communicationCanalsData"/>
    </div>
</div>

<div class="flex flex-row mt-3">
    <div class="flex flex-row align-self-stretch w-full wrapper-client-base">
        <app-dashboard-client-base [clients]="clientGroups"/>
    </div>
</div>
```
---

- If a tag has more than one attribute, each attribute should be placed on a separate line.

Incorrect:

```html
<app-site-visitors-table class="flex flex-row w-full align-self-stretch" [clients]="clientGroups"/>
```

Correct:

```html
<app-site-visitors-table
    class="flex flex-row w-full align-self-stretch"
    [clients]="clientGroups"
/>
```

---

- Attribute Order:
    - Template references (`#ref`)
    - Structural directives (`*ngIf, *ngFor`)
    - Class (`class`)
    - Input parameters (`[prop]`)
    - Two-way bindings (`[(ngModel)]`)
    - Event handlers (`(event)`)

```angular2html
<div
    #templateRef
    *ngIf="isVisible"
    class="dynamic-class"
    [title]="'Dynamic Title'"
    [attr.data-custom]="customAttribute"
    [(ngModel)]="userInput"
    (input)="onInputChange($event)"
>
    <a [routerLink]="['/home']">Home</a>
</div>
```

---

- Element nesting must be cascading.

Incorrect:

```html
  <app-site-visitors-table [clients]="clientGroups"><span>Hello</span></app-site-visitors-table>
```

Correct:

```html
<app-site-visitors-table [clients]="clientGroups">
    <span>Hello</span>
</app-site-visitors-table>
```

---

- Use `ng-container` for conditional rendering when the creation of a tag is solely due to the need for the `ngIf` directive, and not for layout purposes.

Correct:

```angular2html
<ng-container *ngIf="isShow">
    <div class="flex flex-row align-items-center gap-3">
        ...
    </div>
</ng-container>
```

Incorrect (div is needed only for ngIf):

```angular2html
<div *ngIf="isShow">
    <div class="flex flex-row align-items-center gap-3">
        ...
    </div>
</div>
```

Correct (div is also used for styling):

```angular2html
<div
    *ngIf="isShow"
    class="flex flex-row align-items-center gap-3"
>
    <div class="flex flex-row align-items-center gap-3">
        ...
    </div>

    <div class="flex flex-row align-items-center gap-3">
        ...
    </div>
</div>
```

Correct:

```angular2html
<my-component
    *ngIf="isShow"
    [data]="data"
/>
```
Incorrect (div is used only for ngIf):

```angular2html
<div *ngIf="isShow">
    <my-component [data]="data"/>
</div>
```

---

- Use self-closing tags for components without content.

Incorrect:

```angular2html
<my-component></my-component>
```

Correct:

```angular2html
<my-component/>
```

---

- Use `trackBy` with `*ngFor` for dynamic collections.

This allows Angular to update only changed elements instead of re-rendering the entire list, improving performance.

```angular2html
<div>
    <ul>
        <li *ngFor="let item of collection; trackBy: trackByFn">{{item.id}}</li>
    </ul>
</div>
```

```typescript
interface TestItem {
    id: number;
}

public trackByFn (index: number, item: TestItem): number {
    return item.id;
}
```

---

## ACCESSIBILITY

- Use semantic and interactive elements correctly.
  - Use appropriate HTML tags for their intended purpose:
    - `<button>` - for buttons
    - `<nav>` -  for navigation
    - `<main>, <article>, <section>` -  for structuring content
  - Do not make non-interactive elements (`<div>, <span>`) interactive via JavaScript.

  
Incorrect:
```html
<div class="btn" (click)="submit()">Отправить</div>
```

Correct:
```html
<button type="button" (click)="submit()">Отправить</button>
```

---

- All interactive elements must be keyboard accessible.

If a non-standard element is used, you must add:

- `tabindex="0"`
- `keydown` handling
- Appropriate `role`

---

- All images must have an `alt` attribute.

For decorative images, use:

```html
<img
    src="..."
    alt=""
/>
```

---

- All form elements must be associated with a `<label>`.

Incorrect:

```html
<input type="email">
```

Correct:

```html
<label for="email">
    Email
</label>

<input
    id="email"
    type="email"
/>
```

---

- Add `aria-label` to elements without text content.

Correct:

```html
<button
    type="button"
    aria-label="Close dialog"
>
    <app-icon-close/>
</button>
```

---

- Use `aria-expanded, aria-selected, aria-pressed, aria-hidden` only when the element's state cannot be determined natively.

---

- Do not remove the `outline` from elements receiving focus.

Incorrect:

```scss
button:focus {
    outline: none;
}
```

Correct:

```scss
button:focus-visible {
    outline: 2px solid var(--primary);
}
```

---

- Use `:focus-visible` instead of `:focus` to display focus state.

---

- Do not rely solely on color to convey information.

Errors, warnings, and success states must be accompanied by text or an icon.

---

- Use correct heading hierarchy (`h1 → h2 → h3`) without skipping levels.

---

- Use live regions (`aria-live`) only for truly important dynamic messages (e.g., notifications or form errors).

---

- Check text and interactive element contrast according to WCAG AA.

---

- Verify accessibility using tools:
    - In Browser: Use the Accessibility panel in Chrome DevTools or plugins like axe DevTools for manual checks of individual pages and components.
    - In Code/CI: Integrate automated checks, for example using [a11y-shiftleft-cli](https://github.com/olboyarshinova/a11y-shiftleft-cli), to find issues early and prevent regressions.

---

## SCSS


- Each class must be separated by an empty line.

---

- Use nesting no deeper than 3 levels.

---

- Avoid `!important`. If necessary, add a comment explaining why.

---

- Use mixins/functions from `src/styles/` instead of duplicating values.

---

- Use BEM naming for isolated components: `.card__title`, `.card--featured`.

---

## TS


- Access modifiers (`public, private, protected`) must be specified for all class fields and methods.

---

- Class Entity Order:
    - @Input
    - @Output
    - Other decorators (except those applied to class methods)
    - Public class fields
    - Private class fields
    - Constructor
    - Lifecycle Hooks
    - Class methods

---

- Constructor Injection Order:
    - Decorators
    - Public class fields
    - Private class fields

---

- Do not write logic directly in component lifecycle hooks; only call methods.

```typescript
public ngOnInit(): void {
    this.loadData();
}

private loadData(): void {
    this.loading = true;
    this.api.getUsers().subscribe(users => this.processUsers(users));
}

private processUsers(users: User[]): void {
    this.items = users.filter(u => u.active);
    this.stats.total = this.items.length;
}
```

---

- Do not use `any`.

```typescript
data: unknown; // if type is truly unknown
data: Record<string, string>; // for objects
data: MyInterface[]; // for arrays
```

---

- Always specify return types in methods.

```typescript
public getActiveUsers(): User[] {
    return this.users.filter(u => u.isActive);
}

public loadConfig(): Observable<AppConfig> {
    return this.http.get<AppConfig>('/api/config');
}

public resetForm(): void {
    this.form.reset();
}
```

---

- Do not mutate input parameters.

Incorrect:

```typescript
class Component {
    @Input()
    public data: string = '';

    public updateData(): void {
        this.data = 'new value';
    }
}
```

Correct:

```typescript
class Component {
    @Input()
    public data: string = '';

    @Output()
    public dataChange = new EventEmitter<string>();

    public updateData(): void {
        this.dataChange.emit('new value');
    }
}
```

---

- Add `required: true` to all mandatory component input parameters (`@Input`).

Incorrect:

```typescript
@Input()
public data = '';
```

Correct:

```typescript
@Input({required: true})
public data = '';
```

---

- Use `ChangeDetectionStrategy.OnPush` by default.

It reduces unnecessary change detection cycles and improves application performance.

Use `Default` only if absolutely necessary and the reason is clearly understood.

For manual updates, use `ChangeDetectorRef.markForCheck()`.

```typescript
@Component({
    ...
    changeDetection: ChangeDetectionStrategy.OnPush,
})
```

---

- If a function performs two or more actions, split its functionality.

A function should perform one task. If its name contains "And" or "With", it violates Single Responsibility Principle.

Incorrect:

```typescript
public handleUser(userId: string): void {
    const user = fetchUser(userId);
    user.name = user.name.trim().toLowerCase();
    user.age = user.age > 0 ? user.age : 0;
    this.saveUser(user);
    this.sendNotification(user.email);
}
```

Correct:

```typescript
public handleUser(userId: string): void {
    const user = this.getUser(userId);
    this.saveUser(user);
    this.sendNotification(user.email);
}

public getUser(userId: string): User {
    const user = this.fetchUser(userId);
    return this.transformUser(user);
}

public fetchUser(userId: string): User {
    // Code to fetch data from DB
}

public transformUser(user: User): User {
    user.name = user.name.trim().toLowerCase();
    user.age = user.age > 0 ? user.age : 0;
    return user;
}
```

---

- Variable names must be clear and descriptive.
    - PascalCase for classes, interfaces, types.
    - camelCase for variables and functions.
    - Naming patterns:
        - Value: noun (`cat`, `dog`)
        - Array: plural noun (`cats`, `dogs`)
        - Function: verb (`deletePage`, `update`)
        - Class: capitalized noun (`Cat`, `Dog`)
        - Constant: UPPER_SNAKE_CASE (`MAXIMUM_FOOD`, `LIMIT_X`)
        - Handler: describes the triggering event, typically with prefix `on` or suffix Handler (`onButtonClick`, `formSendHandler`)
    - Avoid words like "data" and "info".
        - Names should be obvious. Generic terms make variable meaning unclear and require additional context.

        - Examples:
            - `userName` instead of `userData`;
            - `employeeList` instead of `employeeData`;
            - `productDetails` instead of `productInfo`.

---

- DRY (Don’t Repeat Yourself): Do not duplicate code.

---

- Clean Code: Do not leave unused variables, functions, or commented-out code.

Old code can always be found in version history.

---

- Comments: Use /** ... */ for multi-line comments and // for single-line comments. 

---

- Prefer self-documenting code over comments. Avoid describing obvious things.

---

- A function should not accept more than 3 parameters.

The more parameters passed, the harder it is to maintain correct order and refactor. Using a typed object solves this problem.

Incorrect:

```typescript
public onAppInit(): void {
    this.processUser(1, "John", 35, undefined, true);
}

public processUser(id: number, name: string, age: number, hasCar?: boolean, hasBarcode?: boolean) {
    ...
}
```

Correct:

```typescript
type UserInfo = {
    readonly id: number,
    readonly name: string,
    age: number,
    hasCar?: boolean,
    hasBarcode?: boolean,
}

public onAppInit(): void {
    this.processUser({
        id: 1,
        name: 'John',
        age: 35,
        hasBarcode: true,
    });
}

public processUser(user: UserInfo) {
    ...
}
```

---

- Do not use negative conditions.

Incorrect:

```typescript
const isEmailNotVerified = (email) => {
    ...
}

if (!isEmailNotVerified(email)) {
    ...
}

if (isVerified === true) {
    ...
}
```

Correct:

```typescript
const isEmailVerified = (email) => {
    ...
}

if (isEmailVerified(email)) {
    ...
}

if (isVerified) {
    ...
}
```

---

- Use `@HostListener` instead of `document.addEventListener` + `document.removeEventListener`.

The decorator handles subscription/unsubscription automatically, reducing code and preventing memory leaks.

Incorrect:

```typescript
// Необходимо подписаться
public ngOnInit(): void {
    document.addEventListener('click', this.onCustomContentClick);
}

// И не забыть отписаться, иначе получим утечку памяти
public ngOnDestroy(): void {
    document.removeEventListener('click', this.onCustomContentClick);
}
```

Correct:

```typescript
// HostListener делает всё сам
@HostListener('click', ['$event'])
public onCustomContentClick(event: MouseEvent): void {
    console.log(event.type);
}
```

---

- Use template literals (backticks) instead of string concatenation with `+`.

---

- Do not write return with an if condition on the same line.

This can reduce readability.

Incorrect:

```typescript
public checkValue(value: number): string {
    if (value > 10) return 'Greater than 10';
    return '10 or less';
}
```

Correct:

```typescript
public checkValue(value: number): string {
    if (value > 10) {
        return 'Greater than 10';
    } else {
        return '10 or less';
    }
}
```

---

- When importing multiple values from a single module, use multi-line syntax.

Incorrect:

```typescript
import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
```

Correct:

```typescript
import {
    longNameA,
    longNameB,
    longNameC,
    longNameD,
    longNameE,
} from 'path';
```

---

- Do not add blank lines at the beginning or end of code blocks.

Incorrect:

```typescript
function logFoo() {
    console.log(foo);

}

if (baz) {
    console.log(qux);
} else {
    console.log(foo);
}

```

Correct:

```typescript
function logFoo() {
    console.log(foo);
}

if (baz) {
    console.log(qux);
} else {
    console.log(foo);
}
```

---

- Add trailing commas.

This approach makes git diffs cleaner.

Правильно:

```typescript
const hero = {
    firstName: 'Florence',
    lastName: 'Nightingale',
};
```

---

## TESTING


- Test Naming: `it('should [expected behavior] when [condition]')`

---

- Async Testing: Use `fakeAsync/tick` for asynchronous tests.

---

- Mocking: Mock services via `TestBed.overrideProvider`.

---

- Coverage: Minimum 80% for business logic.

---

- E2E: Use `data-testid` attributes instead of relying on CSS classes.
