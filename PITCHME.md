# Hand Rolled Test Doubles in Swift

---

Because Swift doesn't provide introspection

We have to hand roll test doubles

---

Test Doubles

- Fakes
- Stubs
- Mocks
- Spies

---

# Fakes 

- Web services
- Hardware devices
- System clock 

We won't look at these today!
---

# Stubs

Simple implementations of an interface (protocol)

---

Returns preset results

---

# Mocks

Glorified stubs:

- Record Calls (boolean or a count)
- Record Arguments sent

---

Mock methods sometimes do their own assertions when called

(NOTE: We don't do this on Ford)

---

Stubs and Mocks allow us to _**Tell don't Ask**_

---

# Spies

Like a Mock that's been attached to the actual implementation

---

Careful when using Spies...!

---

Only use for a sociable test

---

Mock or Stub for a unit test

---

Let's write a swift stub

```swift
extension APTimelinePresenter {
    func moreFeatures(_ string: String) -> String {
        return "String"
    }
}
```

Let's write a swift mock

--- 

Let's write a swift spy

---

Writing test doubles by hand is instructive

--- 

Writing test doubles by hand is tedious

---

https://github.com/Brightify/Cuckoo
