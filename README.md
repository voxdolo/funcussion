# Funcussion

More functional than your standard concussion!

## What is it?

Funcussion provides convenience methods on standard Objective-C objects that
help you write Objective-C in a functional style. Operations performed on a
collection always return a new collection. This is in stark opposition to the
mutable approach encouraged by a lot of Objective-C builtin analogs to the
methods provided here. Most common functional idioms for collecitons are
supported and there's a bit of sugar thrown in for good measure.

## NSArray Extensions

A bit of sugar to sweeten your common interactions with NSArray.

### firstObject

Returns the first object in the array; a shortcut for `[array objectAtIndex:0]`.

    -(id)firstObject;

_Example:_

    [@[@"Foo", @"Bar", @"Baz"]  firstObject]; // => @"Foo"

### flatten

Recursively flattens array's into a single-dimensional array. Objects are
inserted into the returned array as they're encountered.

    -(NSArray*)flatten;

_Example:_

    NSArray *simpleArray = @[@"one", @"two"];
    NSArray *nestedArray = @[@"microphone", @"check", simpleArray, simpleArray];
    [nestedArray flatten];
    // => @[@"microphone", @"check", @"one", @"two", @"one", @"two"];

## NSArray functional operations

Core methods for applying functional approaches to handling instances of
`NSArray`.

### each:

Iterate every element in an `NSArray` with a supplied function.

    -(void)each:(VoidArrayIteratorBlock)aBlock;

_Example:_

    [simpleArray each:^(id obj) {
      NSLog(@"element: %@",obj);
    }];


### eachWithIndex:

Iterate every element in an `NSArray` while yielding the element and an index to
the supplied function.

    -(void)eachWithIndex:(VoidArrayIteratorIndexedBlock)aBlock;

_Example:_

    [simpleArray eachWithIndex:^(id obj, NSUInteger index) {
      NSLog(@"element: %@ index: %d", obj, index);
    }];

### map:

Apply a function to every element of an `NSArray`. Returns a new array
containing the result of the applied functions.

    -(NSArray*)map:(ObjectArrayIteratorBlock)aBlock;

_Example:_

    [simpleArray map:^(id obj) {
      return [obj uppercaseString];
    }];
    // => @[@"ONE", @"TWO"]

### mapWithIndex:

Apply a function to every element of an `NSArray` while additionally yielding
an index to the function. Returns a new array containing the result of the
applied functions.

    -(NSArray*)mapWithIndex:(ObjectArrayIteratorIndexedBlock)aBlock;

_Example:_

    [simpleArray mapWithIndex:^(id obj, NSUInteger index) {
      return [[obj stringByAppendingFormat:@":%d", index] uppercaseString];
    }];
    // => @[@"ONE:0", @"TWO:1"]

### filter:

Applies the supplied function and returns a new `NSArray` containing all
elements the supplied function evaluates to true for.

    -(NSArray*)filter:(BoolArrayIteratorBlock)aBlock;

_Example:_

    [@[@0, @1, @"zero", @"one"] filter:^BOOL(id obj) {
      return [obj isKindOfClass:[NSNumber class]];
    }];
    // => @[@0,@1]

### reduceWithAccumulator:andBlock:

Applies the supplied function to reduce all values in the `NSArray` down to a
single value. Initialize it with an accumulator of the same type you will
reduce to.

    -(id)reduceWithAccumulator:(id)accumulator andBlock:(ObjectArrayAccumulatorBlock)aBlock;

_Example:_

    NSArray *numericArray = @[@22, @20];
    [numericArray reduceWithAccumulator:@0 andBlock:^id(id acc, id obj) {
      return [NSNumber numberWithInt:[acc intValue] + [obj intValue]];
    }]; // => @42

### reduceWithAccumulator:andIndexedBlock:

Applies the supplied function to reduce all values in the `NSArray` down to a
single value. Passes an index to the block. Initialize it with an accumulator
of the same type you will reduce to.

    -(id)reduceWithAccumulator:(id)accumulator andIndexedBlock:(ObjectArrayAccumulatorBlock)aBlock;

_Example:_

    NSArray *numericArray = @[@22, @20];
    [numericArray reduceWithAccumulator:@0 andIndexedBlock:^id(id acc, id obj, NSUInteger index) {
      return [NSNumber numberWithInt:[acc intValue] + [obj intValue] + index];
    }]; // => @43

### detect:

Returns the first element of the array that matches the BOOL function passed it.

    -(id)detect:(BoolArrayIteratorBlock)aBlock;

_Example:_

    NSArray *mixedArray = @[@0, @1, @"zero", @"one"];
    [mixedArray detect:^BOOL(id obj) {
      return [obj isKindOfClass:[NSString class]];
    }]; // => @"zero"

### every:

Applies the supplied BOOL function to every element in the array and returns a
BOOL value indicating whether the function is true for all elements.

    -(BOOL)every:(BoolArrayIteratorBlock)aBlock;

_Example:_

    [mixedArray every:^BOOL(id obj) {
      return [obj isKindOfClass:[NSString class]];
    }]; // => NO

    [mixedArray every:^BOOL(id obj) {
      return [obj isKindOfClass:[NSObject class]];
    }]; // => YES

### any:

Applies the supplied BOOL function to every element in the array and returns a
BOOL value indicating whether the function is true for any element.

    -(BOOL)any:(BoolArrayIteratorBlock)aBlock;

_Example:_

    [mixedArray any:^BOOL(id obj) {
      return [obj isKindOfClass:[NSString class]];
    }]; // => YES

    [mixedArray any:^BOOL(id obj) {
      return [obj isKindOfClass:[NSException class]];
    }]; // => NO

## NSDictionary functional operations

Core methods for applying functional approaches to handling instances of
`NSDictionary`.

### each:

Iterate every couplet in an `NSDictionary` with a supplied function.

    -(void)each:(VoidDictIteratorBlock)aBlock;

_Example:_

    NSDictionary *simpleDict = @{@1: @"one", @2: @"two"}
    [simpleDict each:^(id key, id value) {
      NSLog(@"couplet: %@ => %@", key, value);
    }];

### map:

Applies a function that returns an NSDictionary to every couplet of an
`NSDictionary`. Returns a new dictionary containing the result of the applied
functions.

    -(NSDictionary*)map:(DictionaryDictIteratorBlock)aBlock;

_Example:_

    [simpleDict map:^(id key, id value) {
      return [NSDictionary dictionaryWithObject:
              [key stringByAppendingFormat:@":%@",value]
                                         forKey:[key uppercaseString]];
    }];
    // => @{@"ONE": @"one:1", @"TWO": @"two:2"};

### mapValues:

Applies a function that returns an object to every couplet of an
`NSDictionary`. Returns a new dictionary containing the result of the applied
functions assigned as the value.

    -(NSDictionary*)mapValues:(ObjectDictIteratorBlock)aBlock;

_Example:_

    [simpleDict mapValues:^id(id key, id value) {
      return [key stringByAppendingFormat:@":%@",value]
    }];
    // => @{@"one": @"one:1", @"two": @"two:2"};

### mapToArray:

Applies a function that returns an object to every couplet of an
`NSDictionary`. Returns an array containing the result of the applied
functions.

    -(NSArray*)mapToArray:(ObjectDictIteratorBlock)aBlock;


_Example:_

    [simpleDict mapToArray:^id(id key, id value) {
      return [key stringByAppendingFormat:@":%@",value]
    }];
    // => @[@"one:1", @"two:2"];

### filter:

Applies the supplied function to every couplet of an `NSDictionary` and
returns a new `NSDictionary` containing all couplets the supplied function
evaluates to true for.

    -(NSDictionary*)filter:(BoolDictIteratorBlock)aBlock;

_Example:_

    __block NSPredicate *containPred = [NSPredicate predicateWithFormat:
                                        @"SELF contains[cd] %@", @"one"];
    [simpleDict filter:^BOOL(id key, id value) {
      return [containPred evaluateWithObject:key];
    }]; // => @{@"one": @"one:1"};

### reduceWithAccumulator:andBlock:

Applies the supplied function to reduce all couplets in the `NSDictionary`
down to a single value. Initialize it with an accumulator of the same type you
will reduce to.

    -(id)reduceWithAccumulator:(id)accumulator andBlock:(ObjectAcumulatorDictBlock)aBlock;

_Example:_

    [simpleDict reduceWithAccumulator:@0 andBlock:^id(id acc, id key, id value) {
      return [NSNumber numberWithInt:[acc intValue] + [value intValue]];
    }]; // => @3

### every:

Applies the supplied BOOL function to every element in an `NSDictionary` and
returns a BOOL value indicating whether the function is true for all elements.

    -(BOOL)every:(BoolDictIteratorBlock)aBlock;

_Example:_

    [simpleDict every:^BOOL(id key, id value) {
      return [key isKindOfClass:[NSString class]];
    }]; // => YES

    [simpleDict every:^BOOL(id key, id value) {
      return [value intValue] < 2;
    }]; // => NO

### any:

Applies the supplied BOOL function to every element in an `NSDictionary` and
returns a BOOL value indicating whether the function is true for any element.

    -(BOOL)any:(BoolDictIteratorBlock)aBlock;

_Example:_

    [simpleDict any:^BOOL(id key, id value) {
      return [key isKindOfClass:[NSNumber class]];
    }]; // => NO

    [simpleDict any:^BOOL(id key, id value) {
      return [value intValue] < 2;
    }]; // => YES

# Contributing

Contributions are welcome, just fork the project make your changes and submit
a pull request. Please ensure the SenTesting suite has coverage for any
additions you make and that it still passes after you make any modifications.

# License

As with all my software, Funcussion uses the [WTFPL](http://www.wtfpl.net/) so
you can just do what the fuck you want to with it.
