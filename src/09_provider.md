
# Provider / inject

* Provider / inject: Inject stores in components
* Uses context, but: doesn't break shouldComponentUpdate
* mobx-react package
* [Blog: How to safely use React context](https://medium.com/@mweststrate/how-to-safely-use-react-context-b7e343eff076#.1y806u7cy)

---

# Provider / inject

```javascript
class App extends React.Component {
  render() {
    return (
        <Provider color="red">
            <div>
                {this.props.messages.map((message) =>
                    <Message text={message.text} />
                )}
            </div>
        </Provider>
    )
  }
}
```

---

# Provider / inject

```javascript
@inject("color") @observer
class Button extends React.Component {
  render() {
    return (
      <button style={{background: this.props.color}}>
        {this.props.children}
      </button>
    )
  }
}
```