# Jest integration

Async Storage module is tighly coupled with its `NativeModule` part - it needs a running React Native application to work properly. In order to use it in tests, you have to provide its separate implementation. Follow those steps to add a mocked `Async Storage` module.

## Using Async Storage mock

You can use one of two ways to provide mocked version of `AsyncStorage`:

### With __mocks__ directory

1. In your project root directory, create `__mocks__/@react-native-community` directory.
2. Inside that folder, create `async-storage.js` file.
3. Inside that file, export `Async Storage` mock.

```javascript
export default from '@react-native-community/async-storage/jest/async-storage-mock'
```

### With Jest setup file

1. In your Jest config (probably in `package.json`) add setup files location:

```json
"jest": {
  "setupFiles": ["./path/to/jestSetupFile.js"]
}
```

2. Inside your setup file, set up Async Storage mocking:

```javascript
import mockAsyncStorage from '@react-native-community/async-storage/jest/async-storage-mock';

jest.mock('@react-native-community/async-storage', () => mockAsyncStorage);
```
## Testing with mock

Each public method available from `Async Storage` is [a mock function](https://jestjs.io/docs/en/mock-functions), that you can test for certain condition, for example, if `.getItem` has been called with a specific arguments:

```javascript
it('checks if Async Storage is used', async () => {
  await asyncOperationOnAsyncStorage();

  expect(AsyncStorage.getItem).toBeCalledWith('myKey');
})
```

## Overriding Mock logic

You can override mock implementation, by replacing its inner functions:

```javascript
// somewhere in your configuration files
import AsyncStorageMock from '@react-native-community/async-storage/jest/async-storage-mock';

AsyncStorageMock.multiGet = jest.fn(([keys], callback) => {
  // do something here to retrieve data
  callback([]);
})

export default AsyncStorageMock;
```

You can [check its implementation](../jest/async-storage-mock.js) to get more insight into methods signatures.

## Troubleshooting

### **`SyntaxError: Unexpected token export` in async-storage/lib/index.js**

**Note:** In React Native 0.60+, all `@react-native-community` packages are transformed by default.

You need to point Jest to transform this package. You can do so, by adding Async Storage path to `transformIgnorePatterns` setting in Jest's configuration.

```json
"jest": {
  "transformIgnorePatterns": ["node_modules/(?!(@react-native-community/async-storage/lib))"]
}
```

Optionally, you can transform all packages in the `react-native-community` and `react-native` directories by specifying this pattern:

```json
"jest": {
  "transformIgnorePatterns": ["node_modules/(?!(@react-native-community|react-native)/)"]
}
```

Or you can expand it even further to transform all packages whose names begin with `react-native` by omitting the trailing `/` in the above pattern.