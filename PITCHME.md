# Hand Rolled Test Doubles in Swift

---

Because Swift doesn't provide introspection

We have to hand roll test doubles

---

- Fakes
- Stubs
- Mocks
- Spies

---

# Fakes 

- Web services
- Hardware devices
- System clock 

etc.

---

# Stubs

Simple implementations of an interface (protocol)

---

Returns preset results

---

# Mocks

Glorified stubs that record;

- Calls (boolean or a count)
- Arguments sent

---

Mock methods sometimes do their own assertions when called

(NOTE: We don't do this on Ford)

---

# Spies

Like a Mock that's been attached to the actual implementation

---

Let's write a swift stub

---

Let's write a swift mock

--- 

Let's write a swift spy

---

Writing test doubles by hand is instructive

--- 

Writing test doubles by hand is tedious

---

https://github.com/Brightify/Cuckoo
