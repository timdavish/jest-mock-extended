# jest-mock-extended
Type safe mocking extensions for Jest

# Installation
```bash
npm install jest-mock-extended --save-dev
```
or
```bash
yarn add jest-mock-extended --dev
```

# Example

```ts
import { mock } from 'jest-mock-extended';

interface PartyProvider {
   getPartyType: () => string;
   getSongs: (type: string) => string[]
   start: (type: string) => void;
}

describe('Party Tests', () => {
   test('Mock out an interface', () => {
       const mock = mock<PartyProvider>();
       mock.start('disco party');
       
       expect(mock.start).toHaveBeenCalledWith('disco party');
   });
   
   
   test('mock out a return type', () => {
       const mock = mock<FunProvider>();
       mock.getPartyType.mockReturnValue('west coast party');
       
       expect(mock.getPartyType()).toBe('west coast party');
   });
});
```

# calledWith() Extension

```jest-mock-extended``` allows for invocation matching expectations. Types of arguments, even when using matchers are type checked.

```ts
const provider = mock<PartyProvider>();
provider.getSongs.calledWith('disco party').mockReturnValue(['Dance the night away', 'Stayin Alive']);
expect(provider.getSongs('disco party')).toEqual(['Dance the night away', 'Stayin Alive']);

// Matchers
provider.getSongs.calledWith(any()).mockReturnValue(['Saw her standing there']);
provider.getSongs.calledWith(anyString()).mockReturnValue(['Saw her standing there']);

```
You can also use calledWith() on its own to create a jest.fn() with the calledWith extension:

```ts
 const fn = calledWithFn();
 fn.calledWith(1, 2).mockReturnValue(3);
```

## Available Matchers


| Matcher               | Description                                                           |
|-----------------------|-----------------------------------------------------------------------|
|any()                  | Matches any arg of any type.                                          |
|anyBoolean()           | Matches any boolean (true or false)                                   |
|anyString()            | Matches any string including empty string                             |
|anyNumber()            | Matches any number that is not NaN                                    |
|anyFunction()          | Matches any function                                                  |
|anyObject()            | Matches any object (typeof m === 'object') and is not null            |
|anyArray()             | Matches any array                                                     |
|anyMap()               | Matches any Map                                                       |
|anySet()               | Matches any Set                                                       |
|isA(class)             | e.g isA(DiscoPartyProvider)                                           |
|includes('value')      | Checks if value is in the argument array                              |
|containsKey('key')     |  Checks if the key exists in the object                               |
|containsValue('value') | Checks if the value exists in an object                               |
|has('value')           | checks if the value exists in a Set                                   |
|notNull()              | value !== null                                                        |
|notUndefined()         | value !== undefined                                                   |
|notEmpty()             | value !== undefined && value !== null && value !== ''                 |

## Writing a custom Matcher

Custom matchers can be written using a MatcherCreator
```ts
import { MatcherCreator, Matcher } from 'jest-mock-extended';

// expectedValue is optional
export const myMatcher: MatcherCreator<MyType> = (expectedValue) => new Matcher((actualValue) => {
    return (expectedValue === actualValue && actualValue.isSpecial);
});
```

By default, the expected value and actual value are the same type. In the case where you need to type the expectedValue 
differently than the actual value, you can use the optional 2 generic parameter:

```ts
import { MatcherCreator, Matcher } from 'jest-mock-extended';

// expectedValue is optional
export const myMatcher: MatcherCreator<string[], string> = (expectedValue) => new Matcher((actualValue) => {
    return (actualValue.includes(expectedValue));
});
```
