# How to fund speeding up CPython

In order to fund the development work on speeding CPython by a factor of five, something like $2M will be required.
This seems like a sizeable sum, but compared to the amount of money spent on Python development and 
the cost of running Python applications, it is quite a modest sum.

The money should be split, roughly 50/50, into the development budget and into paying for ongoing maintenance.

## Funding each stage individually

Since each stage of [the plan](./plan.md) brings benefit regardless of whether the subsequent stages occur,
it makes sense to fund each stage seperately. Each stage should cost about $500k.

## Why would anyone pay for this?

Speeding up CPython by 50% will cut the hardware required to run Python programs by 33%.
Speeding up CPython also reduces the time to run tests and improves developer productivity by some 
small, but useful amount.

Paying any sum up to the annual savings is an obvious win; the payment is a one off, but the savings are ongoing.

## Payment on delivery

In order to minimize the risk to the funding organizations, payment should be upon delivery.
Unless the promised speedup is delivered, no one should have to pay.

## The PSF

The PSF seems to be the obvious organization to coordinate funding.
By subcontracting the development, risk to the PSF can be minimized.
Failure to deliver might be a bit embarassing, but the financial cost should be negligible.


### Community not corporate control

Much open source language development is funded by large corporations.
Java and JVM development is funded by Oracle. PHP and HHVM development is funded by Facebook.
V8 and node development is funded by Google.

However, relying on a large corporation means that the needs of the community come second to that corporation. Python has always been a community driven language; that should continue.

Of course, large corporations should contibute financially, but ultimate control must be retained by the PSF and core developers.


## Risks

### Failure to deliver by contractor

Complete failure to deliver anything is highly unlikely,
but failure to deliver exactly 50% speedup is very likely.

To minimize the risk of failure,
a range of expected speedups should be considered a success.
A range from 40% to 60% would seem reasonable, but might vary from stage to stage.
The range considered success would need to be fixed before any funding is agreed.
Payment should be proportional to the speed-up delivered.

### Failure to find a suitable contractor

Failure to find a suitable contractor would not cause any loss to any party, but would be a disappointment.

For stage 1, this is not a risk as I can already deliver stage 1.

### Failure to gather sufficient funding

The biggest risk for the whole project is that funding cannot be raised.

However, I belive there is a desire in the Python community as a whole
for Python performance to be improved.

The number of developers and companies using Python is enormous.
With sufficient will, funding will be found.
