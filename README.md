#SIMPLE VALIDATOR#

This package is just a simple home-made validator. It will compare an input object with a specially formed criterion object.

Examples:

```javascript

var v = require('./simple_validators');//index

var myRequestBody = {
  id: "foobarbaz123456",
  name: "Snoop",
  email: "foobarbizbaz@foo.com"
};
var myCriteriaObject = {
  id: {//each key is assumed to be required
    type: 'string',
    length: 15,
    lengthMin: 2,
    lengthMax: 30,
    equals: "foobarbizbaz@foo.com" //works for all types
  },
  name: {
    type: 'string'//not every criterion has to be inserted.
  },
  email: {
    lengthMin: 5,
    type: 'string'//if this was another type such as 'array', this would invalidate, and return false
  }
};

v.validate(myRequestBody, myCriteriaObject);//true (or false if not valid)

```
The accepted criterion keys and values are:

  - length: number (0-n)
  - lengthMin: number (0-n)
  - lengthMax: number (0-n)
  - type: 'string', 'array', 'object', 'number', 'boolean', 'null', 'undefined'
  - equal: (anything)

Each top-level key in a criteria object is assumed to be required

###Exposed inner methods##

```javascript

//We can also use some of the inner methods used in the validate method.

//compare length:
v.length('1234', 4);//true
v.length('12345',2);//false
v.length([1,2,3,4,5], 5);//true
v.length([1,2,3,4], 10);//false

//check minimum length:
v.lengthMin('1234',2);//true
v.lengthMin('123',7);//false
v.lengthMin([1,2,3,4,5], 1);//true
v.lengthMin([1,2,3,4], 15);//false

//check maximum length:
v.lengthMax('1234',10);//true
v.lengthMax('123',2);//false
v.lengthMax([1,2,3,4,5], 20);//true
v.lengthMax([1,2,3,4], 3);//false

//check if object keys Match:
v.keysMatch({foo: "potato"},{foo: "carrot"});//true
v.keysMatch({foo: "potato"},{bar: "potato"});//false

//check if types Match:
v.typesMatch(24,'number');//true
v.typesMatch('i like turtles','string');//true
v.typesMatch(false,'boolean');//true
v.typesMatch([1,2,3,4], 'array');//true
v.typesMatch({foo: "potato"},'object');//true
v.typesMatch(null,'null');//true
v.typesMatch(undefined,'undefined');//true
v.typesMatch(12, 'string');//false

//see if two inputs are equal, including arbitrary depth objects
v.equal(24,24);//true
v.equal('i like turtles','i like turtles');//true
v.equal(false,false);//true
v.equal([1,2,3,4], [1,2,3,4]);//true
v.equal({foo: "potato"},{foo: "potato"});//true
v.equal(null,null);//true
v.equal(undefined,undefined);//true
v.equal(12, '12');//false

//returns a cleaned, valid criteria object
v.getCleanedCriteriaObject({
                              id: {
                                type: 'string',
                                someUnsupportedKey: 234
                              }
                            });
/* corrected object:
  {
    id: {
      type: 'string'
    }
  }
*/

```
