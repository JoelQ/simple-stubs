# Intro

When testing one object, you may find that the other objects it interacts with get in the way. For example, when testing a controller, it can be cumbersome to ensure the model is used correctly:

``` ruby
describe PostsController do
  describe "#index" do
    it "finds the 10 latest published posts" do
      10.times { |i| create(:post, :published, title: "published#{i}") }
      create(:post, published: false, title: "unpublished")

      get :index

      expect(assigns[:posts].map(&:title)).to match_array(%w(
        published0 published1 published2 published3 published4 published5
        published6 published7 published8 published9
      ))
    end
  end
end
```

In this example, the setup and verification phases become complex and hard to follow because we need to re-test the logic to find 10 published posts, even though this logic is already implemented and tested in the model layer.

We can use stubs to clean it up:

``` ruby
describe PostsController do
  describe "#index" do
    it "finds the 10 latest published posts" do
      posts = double("latest_published")
      allow(Post).to receive(:latest_published).and_return(posts)

      get :index

      expect(assigns[:posts]).to eq(posts)
    end
  end
end
```

The `posts` variable above is called a "stub," which is a kind of "test double."

You can create test doubles in RSpec by using the `double` method:

``` ruby
posts = double("latest_published")
```

This creates an object that stands in for an actual list of published posts; it's like a stunt double for your actual object. Using the `double` method is very similar to calling `Object.new`, but tests that use doubles will fail with much clearer error messages. The `"latest_published"` string above is simply a name which will be used in test failure messages.

You can stub out a method on any object (including a test double) by using `allow`:

``` ruby
allow(Post).to receive(:latest_published).and_return(posts)
```

This changes the `Post` class to return `posts` whenever `latest_published` is called. Stubbed methods will revert to their regular behavior after each test runs.

Using the `double` and `allow` methods, you can create simple stubs.

In this exercise, you'll use simple stubs to clean up some tests.

If you'd like to see a quick intro to test doubles, check out the related episode of [the Weekly Iteration](https://upcase.com/videos/stubs-mocks-spies-and-fakes).

## Setup

After running `bin/setup`, edit `spec/dashboard_spec.rb` and look for test logic which is duplicated from `spec/post_spec.rb`. Use `double` and `allow` to create a stub for posts and eliminate this duplication.
## Tips and Tricks


[Upcase Forum discussion](https://forum.upcase.com/t/test-doubles-simple-stubs/4609)
## Trail

[Trail home page](https://thoughtbot.com/upcase/test-doubles)
