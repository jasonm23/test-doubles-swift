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

[Let's write our Calculator](https://bl.ocks.org/jasonm23/e3624cb734678af1701882f501fb29d1)

---

`func add` can be provided by an Adder class

---

(sorry! needed a really simple example!)

---

[Let's specifiy this dependency in our CalculatorSpec](https://bl.ocks.org/jasonm23/2892e0bb43b2545c2fbfdee7bd405dd5)

---

[So we'll need an Adder stub...](https://bl.ocks.org/jasonm23/3900afe81f05fb0a6ac886026ab71473)

---

[Let's convert the Adder stub into a Mock](https://bl.ocks.org/jasonm23/c8614d73d7bb020b8840d20dd5db5c1b)

---

[Let's write our Adder ... Test first](https://bl.ocks.org/jasonm23/115de906730a32b04b224ff8b369ddb2)

---

[And pass that test with a real implementation of Adder](https://bl.ocks.org/jasonm23/7cfd45d68e61c68a1b464c7f66b709a8)

---

[Let's test our Calculator and Adder together in a _sociable_ test using a Spy...](https://bl.ocks.org/jasonm23/6436745510ae40228b3db10c70920349)

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
