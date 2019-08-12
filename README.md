# AngularNgrxStore

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

### 1. Read component, will demonstrate how to receive and read data from app state store

1. import ngrx Store, model interface, app State, rxjs Observable

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

  ngOnInit() {}
}
```
