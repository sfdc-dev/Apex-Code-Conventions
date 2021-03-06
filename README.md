# APEX CODE CONVENTIONS

## 1. Class naming

* CamelCase starting from uppercase letter only.
* Singular nouns combinations only.
* Keep classes named using the full name of the object they primarily affect and postfix with class type.
  Class types:
  * Batch
  * Controller
  * Extension
  * Schedule
  * WebService
  * Exception
  * Interface
  * Test
  * TriggerHandler
* Test class names are the exact copies of tested class names postfixed with “Test”.
* Controller class names are the exact copies of its pages names postfixed with “Controller”.

|Bad :(                     |Good!                       |
|---------------------------|----------------------------|
|HomePageCtrl               |HomePageController          |
|OppTriggerHandler          |OpportunityTriggerHandler   |
|PopulateOrders_Batch       |OrderPopulationBatch        |
|TestAccountsWebServices    |AccountWebServiceTest       |

## 2. Variable naming

* CamelCase starting from lowercase letter only. Exceptions are:
  * Loop variables (postfix with “_i”);
  * Custom Object fields (start with capital letter, split words with underscores).
* No abbreviations. No acronyms.
* Always include full class name in instance variables.
* Postfix collections with collection type. Use singular noun forms.
* Use “To” between key and value descriptions in map names (unnecessary for standard maps from object primary key Id to object).
* Use clear, meaningful names.

|Bad :(                     |Good!                       |
|---------------------------|----------------------------|
|InsertAccount              |accountToInsert             |
|test_opp1                  |firstTestOpportunity        |
|PopulateOrders_Batch       |OrderPopulationBatch        |
|TestAccountsWebServices    |AccountWebServiceTest       |
|opportunitiesToUpdate      |opportunityToUpdateList     |
|fieldsMapping              |jsonFieldToInternalFieldMap |
|opportunitiesMap           |triggeredOpportunityMap     |
|uniqueData                 |orderSet                    |

## 3. Method naming

* CamelCase starting from lowercase letter only.
* Prefer using verbs in method names.
* Leave “get” and “set” prefixes for getters and setters only. Use “obtain” and “specify” instead.
* Postfix method names returning non-primitive types with corresponding class name.

|Bad :(                     |Good!                       |
|---------------------------|----------------------------|
|getOpportunities           | obtainWonOpportunityList   |

## 4. Constants naming

* Constants (i.e. variables defined as static final) should be in uppercase with words separated by underscores:

```java
public static final String PAYPAL_LOGIN_URL = 'https://login.paypal.com/';
```

## 5. SOQL and SOSL queries

* SOQL and SOSL keywords should be uppercase.
* Fields to retrieve should be on separate lines, with the separating comma on the same line as the field, unless there are just a few fields being retrieved. Then it is acceptable to have the query on a single line (see convention 7.1, about line length).

```java
List<Contact> contacts = [SELECT Id, Name FROM Contact WHERE AccountId IN :accountIdSet];
```

In this example the line takes 89 characters, which is below the limit imposed by convention 7.1 (line length).

```SQL
SELECT  Id,
        FirstName,
        LastName,
        Phone,
        Email,
        CustomField__c,
        AnotherField__c
FROM Contact
WHERE Name != NULL
```

In this situation the query alone would take more than 100 characters, excluding whitespace and variable declaration. To comply with convention 7.1 we format it to one field and condition per line. This makes it easier to edit which fields and conditions are being used on this query.

When using on Apex, prefer this syntax:

```java
List<Contact> contactsWithSpecificLastName = [
    SELECT  Id,
            FirstName,
            LastName,
            Email,
            Phone,
            CustomField__c,
            AnotherField__c
    FROM Contact
    WHERE LastName = :specifiedLastName
];

```

## 6. Comments

All methods must have a comment. Depending on the number of lines of code within a method comment as much as possible. Note that someone else will be maintaining the code. Use Javadoc commenting style (<http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html>) which can then be consumed by ApexDocs (<https://gitlab.com/StevenWCox/sfapexdoc>) to generate documentation easily.

``` java
// Classes should be commented as follows:
/**
* Description of class
* 1.0 Name DD/MM/YYYY Description
*
* @author yourname
* @date 02/08/2013
*
*/
public class SomeClass {
    // Methods should be commented as follows:
    /**
    * Descriptionoffunction
    * @author YourName
    * @date 11/27/2012
    * @param param1: Description of param1
    * @param param2: Description of param2
    * @see AnotherClass
    * @return void
    */
    public static void someFunction(
        String param1,
        String param2
    ){
        // Method body goes here..
    }
}
```

## 7. Indentation

Four spaces should be used as the unit of indentation. The exact construction of the indentation should be the space.

### 7.1 Line Length

Avoid lines longer than 100 characters, for consistency with IDEs and text editors. If a line exceeds 100 characters, it should be broken into two or more lines, depending on the situation (see below).

### 7.2 Wrapping Lines

When an expression will not fit on a single line, break it according to these general principles:

* Break after a comma.
* Break before an operator.
* Prefer higher-level breaks to lower-level breaks.
* Align the new line with the beginning of the expression at the same level on the previous line.
* If the above rules lead to confusing code or to code that is squished up against the right margin, just indent 8 spaces (2 tabs) instead.

#### Examples

##### 7.2.1 Method Signatures

```java
private class SomeClass {
    public static void doSomethingReallyComplicated(Integer sumOfSomething, Decimal anotherSum,
            String nameOfThatThing) {
        Object someObject = SomeOtherClass.getObject();
        // ...
    }
}
```

##### 7.2.2 Instance Declarations

The method line above has 95 characters (counting the 4 whitespaces before `public`), and it applies the first principle of breaking after a comma. Note that by using an extra indentation on the signature on the lower line we avoid the confusion of where the scope actually starts.

```java
public with sharing class SomeClass {
    // ...
    public void someMethod () {
        AnotherClass.AClassChildren bigVariableName
            = new AnotherClass.AClassChildren(this.someVariable);
    }
}
```

The instance declaration on line 3 would use 105 characters, but with the principle of breaing before an operator it will use 65 characters on the lower line.

##### 7.2.3.1 Conditionals - Bail before if possible

When declaring IF statements, start with the conditions that can skip the process, or bail before running unecessary code. 

```java
// DON'T DO THIS
if (condition1 && condition2) {
    doSomething02();
}
if (condition1 && condition3) {
    doSomething03();
}
if (!condition1) {
    continue;
}
```

```java
//DO THIS
if (!condition1) continue; // If condition1 is required for the rest of the steps

// The code will only get here if condition1 is true:
if (condition2) doSomething02();
if (condition3) doSomething03();

```

##### 7.2.3.2 Conditionals - Formatting

```java
// DON'T DO THIS
if ((condition1 && condition2)
    || (condition3 && condition4)
    || !(condition5 && condition6)) {
    doSomething();
}
```

```java
//DO THIS
if ((condition1 && condition2)
        || (condition3 && condition4)
        || !(condition5 && condition6)) {
    doSomething();
}
```

Bad wraps like the one on the first example make easy to miss the part where the method's signature ends and when the scope actually begins. Good wraps help to make those clearer, and not so easy to miss.

##### 7.2.4 Ternary Expressions

```java
// if it fits on the 100 character limit:
result = (aLongExpression) ? something : anotherThing;

// if it doesn't fit the 100 character limit use this:
result = (aVeryLongExtremelyLongExpression) ? something
                                            : anotherThing;

// or this:
result = (aVeryLongExtremelyLongExpression)
         ? something
         : anotherThing;

```

As exemplified above, there are three ways to format ternary expressions.

## 8. White Space

Blank lines improve readability by setting off sections of code that are logically related.

Two blank lines should be used in the following:

* Between sections of a source file
* Between class and interface definitions

One blank line should be used in the following:

* Between methods
* Between the local variables in a method and its first statement
* Before a block or a single line comment

### 8.2 Blank Spaces

Blank spaces should be used in the following circumstances:

* A keyword followed by a parenthesis should be separated by a single space.

```java
while(true){ // WRONG
    ...
}

while(true) { // ACCEPTABLE
    ...
}

while (true) { // CORRECT
    ...
}
```

* A blank space should appear after commas in argument lists.

```java
method(Object anObjectArg,Integer anIntegerArg){ // WRONG
    ...
}

method(Object anObjectArg, Integer anIntegerArg) { // CORRECT
    ...
}
```

* Expressions inside a `for` statement:

```java
for (Integer i=0;i<10;i++) { // WRONG
    ...
}

for(Integer i = 0; i < 10; i++) { // ACCEPTABLE
    ...
}

for (Integer i = 0; i < 10; i++) { // CORRECT
    ...
}
```

* Object casts:

```java
ObjectA objInstance=(ObjectA)anotherType; // WRONG

ObjectA objInstance = (ObjectA)anotherType; // ACCEPTABLE

ObjectA objInstance = (ObjectA) anotherType; // CORRECT
```

## 9. Error Messages

It's quite common to have situations where an error message will be displayed to your users. Situations like that can be frustrating to the user, so we need to follow best practices of UX. Always remember: 


[“A product should be usable enough to handle the user’s actions gracefully.”](https://www.designprinciplesftw.com/collections/10-usability-heuristics-for-user-interface-design)

We need to make sure we're handling these actions and errors properly, because it will impact  the experience that product is providing to its users. Below mentioned are few tips that when followed, error messages can also provide a pleasant experience to the user.

### Be Clear And Not Ambiguous

* Write error message in clear and simple language. User should be able to understand the problem while reading an error message.

*Bad*: _"The metadata doesn't contain the required component."_

*Good*: _"We couldn't find the [name_of_the_component] component."_

### Be Short and meaningful

* Error message should contain necessary information. Most of the time user is not willing to read a long story.
* Be concise and write a short description that is meaningful for user and gives him a clear idea of the problem and how to resolve it.
* Avoid using redundant words and do not over communicate the problem.

*Bad*: _"The name you used for the Account was not found when querying for accounts in order to validate the test, so please change the Account name."_

*Good*: _"We couldn't find the Account 'AccountTest' Please check the name and try again."_

### Don't use technical jargon

* Most of the users are not interested in technical details of the problem occurred. If a message contains technical terms or jargons, the user gets confused.

*Bad*: _"Error when reading the raw response from the HTTP method to perform on the REST API service URI when reading the asynchronous tests classes body"_

*Good*: _"We couldn't find the class 'AsynchronousRollupTest' "_

### Be Humble, don't blame the user

* A good error message is humble. It conveys the issues gracefully to its user without blaming them for their actions.
* The user can perform an incorrect action again and again. But the design’s responsibility is to inform him about his mistakes in a good way.

*Bad*: _"*You* forgot to create the Opportunity records!"_

*Good*: _"We couldn't find the Opportunity records. Please check if the they were created and try again."_

### Give directions to the user

* A good error message has three parts: problem identification, cause details if helpful, and a suggestion to lead the user to the solution.  
* Whenever an error occurs, user wants to fix it as soon as possible. The error message should have enough information for user that guides him how to get out of the erroneous situation.

*Bad*: _"Account object has some missing field."_

*Good*: _"We couldn't find the field 'Summary' in the object Account."_

### Be specific and relevant

* The message should contain relevant information so that user can relate specified location and options easily.
* Point out the exact location of problem — where user should go and and what steps are needed to follow to resolve it.

*Bad*: _"Something went wrong with the territories"_

*Good*: _"We couldn't find the required Territory Assignment. Please review the requirements and check if the assignment was created correctly."_

*Final considerations*: 

Please, do not use *Uppercase* text. BECAUSE IT MAY SOUND LIKE YOU'RE SCREAMING! 😉

Also, there are some situations where *humor* might be nice, but please keep it lighthearted and don't overuse it. 

