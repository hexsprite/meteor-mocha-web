# meteor-mocha-web

meteor-mocha-web allows you to easily and safely run mocha tests within the Meteor framework.  This means you can write tests that use and examine Meteor collections.

Here's an example using CoffeeScript (or [check out the full Meteor project w/ tests](https://github.com/mad-eye/leaderboard-mocha))

```coffeescript
describe "Leaderboard", ->
  describe "givePoints", ->
    it "gives 5 points to the user", ->
      #create a player
      playerId = Players.insert {name: "TestUser1", score: 5}
      Session.set "selected_player", playerId
      givePoints()
      player = Players.findOne(playerId)
      chai.assert.equal 10, player.score
      Players.remove {name: "TestUser1"}
```

You're free to write your tests in JavaScript or CoffeeScript.  The [chai](http://chaijs.com/) assertion library is included in this package, but you're free to use a different assertion library.

Tests are only included when `METEOR_MOCHA_TEST_DIRS` is defined, so they will only exist on environments you specify. Any tests in `client` or `server` subdirectories are run exclusively on the client or server following the normal Meteor convention.

One can test templates with code like this
```javascript
describe("should display on the episodes template", function() {
  it("Title", function() {
    episode = Episode.create({title: 'Episode 15 - Google Ventures'})
    div = document.createElement("DIV");
    comp = UI.renderWithData(Template.episode, episode);
    UI.insert(comp, div)
    $(div).html().should.include('Episode 15 - Google Ventures')
    episode.destroy()
  });
});
```

## Setup

1. Install Meteorite if you haven't already `npm install -g meteorite`
2. Add the smart package to your project. `mrt add mocha-web`
3. Add `{{> mochaTestReport}}` to the template where you'd like to see your client test results.
4. Add `{{> serverTestReport}}` to the template where you'd like to your server test results.
4. When running `meteor`, specify where your tests live by setting `METEOR_MOCHA_TEST_DIRS`:
  ```
  $ METEOR_MOCHA_TEST_DIRS="path/to/project/tests:other/path/to/tests" mrt
  ```
5. Whenever you add a test file or change the `METEOR_MOCHA_TEST_DIRS` environment variable, you will need to `meteor rebuild-all` to rebuild the mocha-web package and bring in the correct test files.
6. Customize mocha options by [setting Meteor.public.mocha_setup_args](http://docs.meteor.com/#meteor_settings) (example below, this currently only works on the client-side)

```javascript
{
  "public": {
    "mocha_setup_args": {
      "ui": "tdd",
      "check-leaks": false,
      "globals": ["script*"]
    }
  }
}
```

## Optional Setup

### Assertion Libraries
We've included the [chai](http://chaijs.com/) assertion library, but you can include other assertion libraries as
well.  Chai is included under the `chai` variable.  If you include in your file

    assert = chai.assert
    
you can use assert as you normally would in Node.  (We did not do this automatically so that we didn't pollute the global namespace).

Note that since these tests are running client side, you don't have the normal access to `require`.


### Setting up PhantomJS/Mocha 

```bash
  npm install -g phantomjs
  npm install -g mocha-phantomjs
  mocha-phantomjs http://localhost:3000
```

## Questions?
This package is in its early stages, so please feel free to ask questions, raise issues, or submit pull requests.
