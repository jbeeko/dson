# dson
Domain Specific Object Notation

TL;DR Create a model using Algebraic Data Types (ADTs) then use the model to create a compiler for that domain. The compiler can then be plugged into tools like Atom or VisualStudio Code it enforce the domain constraints and edit time. The end result is a kind of "typed YAML".

With these tools it should be possible to create systems that automate the REST CRUD interactions for domain models. In some ways this is a "Back to the Future" idea a return of COBOL copybooks, record editors and 3270 terminals.  

---

# Domain Modeling

We start with the idea of useing a typed functional language, or more specifically a language with Algabraic Data Types (ADT) to model a domain for example as described by Scott Wlaschin in [this series](http://fsharpforfunandprofit.com/posts/designing-with-types-intro/).

Here is a simple model of a contact based on Scott's series. You can see a contact consists of a name a mandatory primary contact method and any number of secondary contact methods. The contact methods may be mailing addresses, emails or phone numbers.

```
module Contact =
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
While not the complete story, this is valid f# code. Note how the type systems works with you to make it impossible to create an invalid contact. This is the notion of making "illegal states unrepresentable" coined by [Yaron Minsky](https://blogs.janestreet.com/effective-ml-revisited/). PersonName must have a first name and a last name, Middle initial is optional.

Scott shows how to take this further in f# with types such as `string50` a string with maximum length of 50 characters etc. Or `initial` an alphabetic string of length 1. In the case of f# these additional constrains are implement via constructors. Other typed functional languages may do this differently, for example by using dependent types in Haskel.

The Contact module could now be used to processing data collected by any front end technology. It will ensure only valid data is stored in the data base. Typically to ensure a reasonable user experience parallel validation rules are implemented on the front end.

# Domain Model as a Language

The domain model defined above can be used to specify a domain language or a Domain Specific Object Notation. A direct translation would be something like the following:
```
Contact:
    Name:
      FirstName: John
      LastName: Smith
    PrimaryContactMethod
      HomePhone: +1 604 999 1234
    SecondaryContactMethods
      WorkPhone: +1 604 345 4353
      Email: john@acmewidgets.com
```

A better version of the language might be something like this:
```
Contact:
    FirstName: John LastName: Smith
    HomePhone: +1 604 999 1234
    SecondaryContactMethods
      WorkPhone: +1 604 345 4353
      Email: john@acmewidgets.com
```

After all we know contacts can only have 3 children.
* a PersonName in the Name field
* a ContactMethod in the PrimaryContactMethod field
* a list of contact methods in the SecondaryContactMethods field

# Editing the Domain Language

Programmers get the best tools. Don't believer it? Consider how many content management tools are as functional as Git (everything versioned, branching, merging of branches, blame, distributed editing)? But back to the domain model. As a Web Application this would almost certainly be implemented as a set of forms with buttons to add items. Some validation may be at the field level and some on submit. If you are lucky error messages are good and all work is saved between submissions of invalid forms.

Compare that to todays best code editing experience with:
* Intellisense
* Great search
* Tool tips
* Code folding
* etc.

Now imagine creating not just a simple contact as above, but perhaps a contract defining
* the parties
* terms
* services
* etc.

What if the contract could be created by editing text guided using the latest in code editing techniques. I suggest that directed text editing may be:

* faster than using a web application
* provide a better overview of the model under construction
* provides a lot of flexibility in terms of tools
* since it results in text provides great flexibility in terms of how it is subsequently transmitted, stored, and processed.
* makes it easy to create an off-line editing experience

And... I hope that given a domain model all this can be automated.

# Sketch of an implementation

TBD


#Been there done that?

If all this has a "Back to the Future" feel to it you may be right. Consider for a moment COBOL copybooks and 3270 terminals. Working together these allowed programmers to define record types including nested types and variant records. But critically there was also support for building record editors with not much more than just the type definitions. It looks primitive to our eye but it is very functional and quick to build. Hopefully we can do better today.

The proposal is also similar to various "form definition" languages available today. For example PDF forms. But those then to have ad-hoc languages and the output is not a semantically correct text document.
