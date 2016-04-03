# dson
Domain Specific Object Notation

TL;DR Create a model using Algebraic Data Types (ADTs) then use the model to create a compiler for that domain. The compiler can then be plugged into tools like Atom or VisualStudio Code it enforce the domain constraints and edit time. The end result is a kind of "typed YAML".

With these tools it should be possible to create systems that automate the REST CRUD interactions for domain models. In some ways this is a "Back to the Future" idea a return of COBOL copybooks, record editors and 3270 terminals.  Or perhaps as a kind of "typed YAML" and there are efforts out there to do that. But this proposal is different because it is not adding another "schema language" but rather building the schema from the implementation language.

---

# Domain Modeling

We start with the idea of using a typed functional language, or more specifically a language with Algebraic Data Types (ADT) to model a domain for example as described by Scott Wlaschin in [this series](http://fsharpforfunandprofit.com/posts/designing-with-types-intro/).

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
* since it results in text provides great flexibility in terms of how it is subsequently transmitted, stored, and processed
* makes it easy to create an off-line editing experience
* amenable to automation, from a domain model it should be possible to automate the construction of the appropriate compiler to be plugged into a code editor

Hopefully the result will be a compiler directed domain language editing experience.

# Initial Plan (implementation sketch)

1. Use FParsec to hand build a parser for the contact model above. This is to get familiar with parser combinators and FParsec specifically.
2. Plug the resulting parser into VisualStudio Code
3. Re-evaluate to determine if all this is a good idea.
4. Investigate how to build the parser from the f# code using the meta-information in the parse tree or perhaps the compiled f# assemblies themselves.

# Where could this be useful

This technique will not replace well designed web applications for broad audiences. But that leaves a lot of opportunity. Two big ones are:

**Configuration Files** - these typically have strict syntax and semantics but are often not validated at edit time. Most developers are probably familiar with editing an Apache conf. file and getting an error on startup.

**Application Control Information** - many application are a bit like ice bergs, the UI for the primary group of users is quite small, but there is a large UI needed to maintain various bits of control and configuration data. This hidden UI is ofter quite large and poorly implemented and a drain on resources that could be better utilized.

**Applications for Power Users** - It is not true that domain experts can't use sophisticated or terse tools. Well designed text based tools for use but domain experts are sometimes preferred. Especially if that means more function faster and fewer bugs.

#Been there done that?

If all this has a "Back to the Future" feel to it you may be right. Consider for a moment COBOL copybooks and 3270 terminals. Working together these allowed programmers to define record types including nested types and variant records. But critically there was also support for building record editors with not much more than just the type definitions. It looks primitive to our eye but it is very functional and quick to build. Hopefully we can do better today.

The proposal is also similar to various "form definition" languages available today. For example PDF forms. But those then to have ad-hoc languages and the output is not a semantically correct text document.

Finally you could think of this idea as a kind of "typed YAML" and there are efforts out there to do that. This proposal is different because it is not adding another "schema language" but rather building the schema from the implementation language.
-
