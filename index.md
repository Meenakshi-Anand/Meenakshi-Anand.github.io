## RSpec testing using Stubs, Mocks and Spies

Testing in layman terms is nothing but "checking that stuff works". In general while testing we can categorize the operations we do into 3 phases

1. Arrange: It consists of the preparations to start testing like initializing or instantiating objects, setting up mocks and stubs.
2. Act: Simulating the behaviour of the test-subject by calling the appropriate methods on it.
3. Assert: To expect the methods used in Act phase to produce the desired resuts and compare the results

This post is going to discuss about the arrange phase. The examples given in the post are written using RSpec,a testing tool written in Ruby. 

So what are we going to test. 
We have two classes `User` and `Food`.User is initialized with a `food` attribute and has a method `eat now`, which returns a string which says the food which the user wants to eat now. Food has a `fav_food` method returns a food item which is randomly picked.

``` ruby
  class User
    def initialize(food)
       @food = food
    end
    
    def eat_now
      "I want to eat '#{@food.fav_food}'"
    end    
  end
```
``` ruby
  class Food 
    def fav_food
      ['pizza','burger','fries'].sample
    end    
  end
```
We are going to test the `eat_now` method in the `User` class. To test `eat_now` we need a `Food` object with a `fav_food` method. What are the ways in which we do this? 

## Create Real Instance
Our test will look like :

``` ruby
 describe '#eat_now' do
  it "says which food to eat now"  do
    food =  Food.new
    user = User.new(food)
    puts user.eat_now, "here"
    expect(user.eat_now).to match(/I want to eat '(pizza|burger|fries)'/)
  end
end
```
One of the main pitfalls of testing the method in this ways is we are not testing our subject method in isolation. Our tests depend a lot on `Food` object, and hence we have to account for all possible states of the object in our tests. Secondly if we were to change the functionality of the `fav_food` method in the `Food` class , our tests will fail despite the tests for `eat_now` being correct.

So now, instead of creating the `Food` object , lets think of the minimum information we want from the `Food` object to test our subject method. The bare minimum we need is an object with a method fav_food returning a food item. These are called Test Doubles .

## Test Double
A test double is a simplefied version of an object that allows us to define fake methods and their return values.Stubs, mocks and spies are types of test double.  Lets see how to create stubs , mocks and spies in the same context.

## Stubs 
Stubs are objects with fake methods returning hardcoded responses. When we want to test the property of return value of a our subject method, we can use stubs to provide consistent values to other methods used in the subject method.To better understand lets see how to use them in our example .
``` ruby
  describe '#eat_now' do
    it "says which food to eat now"  do
      food = double(:food, fav_food: "pasta")
      user = User.new(@food)
      expect(@user.eat_now).to match('I want to eat pasta.')
    end
  end
```
Here, #double is an RSpec helper method that creates an object with certain properties that we specify. `:food` is the label we give for the object.  

This makes our test very clear , we have created an object which will always return "pasta".The functionality of the object never changes and our tests now only focus on our subject method. This also solves our previous problem of any functional changes in the actual `fav_food` method will not affect our tests. 

## Mocks 
Mocks are used when we want to verify a property of communication between two objects. For example if we were to add a special constraint that our `User` can have only one fav food item. 
``` ruby
  describe '#eat_now' do
    it "can have only one fav food item"  do
      # arrange
      food = double(:food) 
      # assert
      allow(food).to receive(fav_now).once
      #arrange 
      user = User.new
      # act
      user.eat_now
      user.eat_now
    end
  end
```
Here we are placing a message exception on our test double,ensuring that we call the `fav_food` method only once .

## Spies 
Spies are similar to mocks, but instead of placing message exceptions on test doubles , we specify what message it is allowed to receive. We are only saying how our test double is allowed to behave , nothing about the subject method results.
The double then records any messages it receives during Act phase. Finally, in the Assert phase, we can make assertions about the messages it recorded

``` ruby
  describe '#eat_now' do
    it "can have only one fav food item"   do
      # arrange
      food = double(:food,fav_food: "soup") 
      user = User.new
      
      # act
      user.eat_now
      user.eat_now
      
      # assert
      allow(food).to have_receive(fav_now).once
    end
  end
```
Spying is used when we want to verify the same communication property as Mocks,but having the assertions in the end.
