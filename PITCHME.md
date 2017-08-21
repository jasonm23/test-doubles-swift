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

[Silly example time](https://bl.ocks.org/jasonm23/bf6cb763a8ebea9187ba3f4104a0e4c3)

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

--- 

example code

```swift

import Quick
import Nimble

public class CalculatorSpec: QuickSpec {
    override public func spec(){
        var subject: Calculator!

        describe("Calculator"){
            beforeEach {
                subject = Calculator()
            }

            it("adds numbers"){
                expect(adder.add(1,2)).to(equal(3))
            }
        }
    }
}

/// v1
public class Calculator: NSObject {
    func add(_ first: Double, _ second: Double) -> Double {
        return first + second
    }
}

// with injection
public class CalculatorSpec: QuickSpec {
    override public func spec(){
        var subject: Calculator!
        var adder: AdderStub!

        describe("Calculator"){
            beforeEach {
                let injector: BSInjector & BSBinder
                injector = Blindside.injector(withModules: []) as! BSInjector & BSBinder
                adder = AdderStub()
                injector.bind(AdderProtocol.self, toInstance: adder)
                subject = Calculator()
            }

            it("adds numbers"){
                expect(adder.add(1,2)).to(equal(3))
            }
        }
    }
}

public class AdderStub : AdderProtocol {
    func add(_ first: Double, _ second: Double) -> Double {
        return 3
    }
}

// Mock v1
public class AdderMock : AdderProtocol {
    var addWasCalled = false

    func add(_ first: Double, _ second: Double) -> Double {
        addWasCalled = true
        return 3
    }
}

// Mock v2
public class AdderMock : AdderProtocol {
    var addWasCalled = false

    var addWasCalledWithFirst = 0.0
    var addWasCalledWithSecond = 0.0

    func add(_ first: Double, _ second: Double) -> Double {
        addWasCalled = true
        addWasCalledWithFirst = first
        addWasCalledWithSecond = second
        return 3
    }
}


public class AdderSpy : Adder {
    var addWasCalled = false
    var addWasCalledWithFirst = 0.0
    var addWasCalledWithSecond = 0.0

    override func add(_ first: Double, _ second: Double) -> Double {
        addWasCalled = true
        addWasCalledWithFirst = first
        addWasCalledWithSecond = second
        return super.add(first, second)
    }
}

public class Adder : AdderProtocol {
    func add(_ first: Double, _ second: Double) -> Double {
        return first + second
    }
}

/// v1
// public class Calculator: NSObject {
//     func add(_ first: Double, _ second: Double) -> Double {
//         return first + second
//     }
// }

public class Calculator: NSObject {
    var adder: AdderProtocol! // <-- an adder strategy

    override public class func bsProperties() -> BSPropertySet {
        let propertySet = BSPropertySet.init(with: self,
                                             propertyNamesArray: [
                                                "adder" // <- let blindside provide via property injection
            ])
        return propertySet
    }

    func add(_ first: Double, _ second: Double) -> Double {
        return adder.add(first, second) // <- use the adder strategy
    }
}

// For speed, we'll pretend we already have an adder spec.

// Blindside is required, but we won't dig into the setup of blindside... (let's assume it's already configured)

```
