# Angular Ngrx State Management Pattern

> Getting familiar with NgRx Angular state management.

---

## Create model interface file

```javascript
export interface Tutorial {
  name: string;
  url: string;
}
```

---

## Create actions file

1.  import Action store and model interface

```javascript
import { Action } from '@ngrx/store';
import { Tutorial } from '../models/tutorial.model';
```

2. declare the action.types

```javascript
export const ADD_TUTORIAL = '[TUTORIAL] Add';
export const REMOVE_TUTORIAL = '[TUTORIAL] Remove';
```

3. Export Action functions

```javascript
export class AddTutorial implements Action {
  readonly type = ADD_TUTORIAL;

  constructor(public payload: Tutorial) {}
}

export class RemoveTutorial implements Action {
  readonly type = REMOVE_TUTORIAL;

  constructor(public payload: number) {}
}
```

4. Export Actions type which are the class

```javascript
export type Actions = AddTutorial | RemoveTutorial;
```

---

## Create Reducer file

1. import Action store, model interface and TutorialActions

```javascript
import { Action } from '@ngrx/store';
import { Tutorial } from '../models/tutorial.model';
import * as TutorialActions from '../actions/tutorial.actions';
```

2. Create an initialState
   > Reducer can have optional initial state

```javascript
const initialState: Tutorial = {
  name: 'Initial Tutorial',
  url: 'http://google.com'
};
```

3. Create reducer function
   > Reducer function accepts state and action params

```javascript
export function reducer(
  state: Tutorial[] = [initialState],
  action: TutorialActions.Actions
) {
  switch (action.type) {
    case TutorialActions.ADD_TUTORIAL:
      return [...state, action.payload];
    default:
      return state;
  }
}
```

`The Reducer function uses a switch statement that accepts an action.type to decides which action function to run, returning a modified state.`

---

## Create app state file

1. import model interface, same as above
2. export interface App State

```javascript
export interface AppState {
  readonly tutorial: Tutorial[];
}
```

`AppState can have multiple models`

---

## Connect store and reducer to root app

> app.module.ts

1. import StoreModule and reducer

```javascript
import { StoreModule } from '@ngrx/store';
import { reducer } from './reducers/tutorial.reducer.ts';
```

2. inject imports

```javascript
imports: [
  StoreModule.forRoot({
    tutorial: reducer
  })
];
```

`StoreModule is using store reducer name as tutorial`

---

## Create two components named read and create

> 1. ng g c read --skipTests
> 2. ng g c create --skipTests

## read.component.ts

> Demonstrate how to receive and read data from app state store

1. import ngrx Store, model interface, AppState, rxjs Observable

```javascript
import { Observable } from 'rxjs';
import { Store } from '@ngrx/store';
import { Tutorial } from '../models/tutorial.model';
import { AppState } from '../app.state';
```

2. Define a property of type Observable model interface array
3. inject dependency of store type AppState
4. in constructor assign property to access store of particular state

```javascript
export class ReadComponent implements OnInit {
  tutorials: Observable<Tutorial[]>;

  constructor(private store: Store<AppState>) {
    this.tutorials = store.select('tutorial');
  }

  delTutorial(index) {
    this.store.dispatch(new TutorialActions.RemoveTutorial(index));
  }

  ngOnInit() {}
}
```

5. add a component method to dispatch an RemoveTutorial action method, sending the index reference.

## read.component.html

```html
<div class="right" *ngIf="tutorials">
  <h3>Tutorials</h3>
  <ul>
    <li
      (click)="delTutorial(i)"
      *ngFor="let tutorial of tutorials | async; let i = index"
    >
      <a [href]="'//' + tutorial.url" target="_blank">{{ tutorial.name }}</a>
    </li>
  </ul>
</div>
```

> Read component uses structural directives to render div element, and a list of anchored tutorial names.

> li has click event that fires component method passing i as the index of the li, that comes from ngFor as a keyword.

> Always prepend your absolute external links with protocol or // shortcut for http:// OR https://

---

## create.component.ts

1. import ngrx Store, action methods, AppState, model interface

```javascript
import { Store } from '@ngrx/store';
import { AppState } from '../app.state';
import { Tutorial } from '../models/tutorial.model';
import * as TutorialActions from '../actions/tutorial.actions.ts';
```

2. inject Store dependency, same as above
3. add a component method to dispatch an AddTutorial action method, sending a name, and url params.

```javascript
addTutorial(name, url) {
    this.store.dispatch(
      new TutorialActions.AddTutorial(<Tutorial>{
        name: name,
        url: url
      })
    );
  }
```

## create.component.html

```html
<div class="left">
  <input type="text" placeholder="name" #name />
  <input type="text" placeholder="url" #url />
  <button (click)="addTutorial(name.value, url.value)">Add a Tutorial</button>
</div>
```

> Two input elements with template reference variable.

> Button with click event that fires component method sending the values of the referenced variable as params.

## Add components to app.component.ts
