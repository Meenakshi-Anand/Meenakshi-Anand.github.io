## RSpec testing using Stubs, Mocks and Spies

RSpec is a testing tool written in Ruby. It is most frequently used testing library for Ruby production applications. In general while testing we can categorize the operations we do into 3 phases

1. Arrange: It consists of the preparations to start testing like initializing or instantiating objects, setting up mocks and stubs.
2. Act: Simulating the behaviour of the object by calling the appropriate methods on it.
3. Assert: To expect actions and methods used in Act phase to produce the desired resuts and compare the results

In this post, I am going discuss few ways to organize the data in the arrange phase.
Stubs, Mocks and Spies. What are these? Test Double

## Test Double 

A test double is a simplefied version of an object that allows us to define fake methods and their return values. 
For example we have two classes `User` and `Greeting` and the User can wish Greeting multiple times and the logic is implemented with this code. 
``` ruby
  class User
    def wish(name,greeting)
        greeting.birthday(name)
    end
  end

  class Greeting
    def birthday(name) 
      "Happy Birthday #{name}"
    end 
  end

```
Test for the method wish without double will look like 
``` ruby
  describe 'wish' do
    before do 
      @user =  User.create
      @greeting = Greeting.create
    end 
    it "returns the Happy birthday message do
      expect(@user.wish("Harry",@greeting)).to eq("Happy Birthday Harry")
    end
  end

```
One of the main pitfalls of testing the method in this ways is we are not testing the method in isolation. 
Here we are creating a new instance of `Greeting` to test our method wish, which is not the responsibility of the method. 
While testing the method wish all we need to look for is 
1.It takes in a name of type string and an object with the method birthday
