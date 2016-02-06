# dson
Domain Specific Object Notation

TL;DR
: Create a model using Algabraic Data Types (ADTs) then use the model to create a compliler for that domain. The compiler can then be plugged into tools like Atom or VisualStudio Code it enforce the domain constraints and edit time. The end result is a kind of "typed YAML".
: With these tools it should be possible to create systems that automate the REST CRUD interactions for domain models. In some ways this is a return of COBOL copybooks and 3270 terminals.  


---

The idea is this - use a typed functional language, or more specifically a language with Algabraic Data Types (ADT) to model a domain for example as described by Scott Wlaschin in [this series](http://fsharpforfunandprofit.com/posts/designing-with-types-intro/).

For example here is a simple model of a contact based on Scott's series. You can see a contact consists of a name a mandatory primary contact method and any number of secondary contact methods. The contact methods may be mailing addresses, emails or phone numbers.

```
type PersonalName =
    {
    FirstName: string;
    MiddleInitial: string option;
    LastName: string;
    }

type PhoneNumber = PhoneNumber of string

type EmailAddress = EmailAddress of string

type ZipCode = ZipCode of string
type StateCode = StateCode of string
type PostalAddress =
    {
    Address1: string;
    Address2: string;
    City: string;
    State: StateCode;
    Zip: ZipCode;
    }

type ContactMethod =
    | Email of EmailAddress
    | PostalAddress of PostalAddress
    | HomePhone of PhoneContactInfo
    | WorkPhone of PhoneContactInfo

type Contact =
    {
    Name: PersonalName;
    PrimaryContactMethod: ContactMethod;
    SecondaryContactMethods: ContactMethod list;
    }
```
This is valid f# code. Note how the type systems works with you to make it impossible to create an invalid contact. This is the notion of making "illegal states unrepresentable" coined by [Yaron Minsky](https://blogs.janestreet.com/effective-ml-revisited/).

Scott shows how to take this further in f# with types such as `string50` a string with maximum length of 50 characters etc. In the case of f# these additional constrains are implement via constructors. Other typed functional languages may do this differently, for example by using dependent types in Haskel.
