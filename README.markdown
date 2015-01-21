[![DevOps By Rultor.com](http://www.rultor.com/b/mikehoward/use_tinymce)](http://www.rultor.com/p/mikehoward/use_tinymce)

[![Gem Version](https://badge.fury.io/rb/use_tinymce.svg)](http://badge.fury.io/rb/use_tinymce)

UseTinyMCE
==========

**UseTinyMCE** is yet another (as if we needed another) hack for including
TinyMCE in a Rails 3.0, 3.1, 3.2, 4.0, 4.1, or 4.2 app.

**NOTE:** Rails 3.1.0 broke version 0.0.8. The current version has been tested
on Rails 4.0.0. It *should* work on 3.0, 3.1 and 3.2 because "nothing there has been changed". Let me know if this is not true.

This gem differs from the others that I looked at on rubygems in that:

1. It works with Rails [3.0](#rails_30_integration), [3.1 & 3.2](#rails_31_integration) and [4.0, 4.1, & 4.2](#rails_40_integration) details below
2. It's minimal. It does not provide any juicy Rails style configuration for
TinyMCE. You just use the config stuff which comes with TinyMCE - in javascript.
3. I've moved on to Ruby 2.0, so I'm now depending on others to find and patch bugs in 1.9.x
and 1.8.x. For example, version 0.0.12 contains a patch by github://janne to correct the syntax of the rakefile
so that it works with the Regexp engine in 1.8.x

## Rails Integration

It's almost the same in all cases. See [Step By Step](#step_by_step) below.

**Rails 3.0 Note:** Rails 3.0 defaults to `prototype` for javascript, so `use_tinymce`
supports both *raw* TinyMCE (which works well with Prototype) and the *jquery* version,
which works with 'jquery'. It also supports both *simple* and *advanced* editor
installations for the *raw* type.

**Rails 3.1, 3.2, 4.0, 4.1, 4.2 Note:** TinyMCE doesn't play well with the asset pipeline, so it is installed
in public. This has to do with the way TinyMCE is architected: it is plugin based
and scatters javascript, css, and html all through it's plugin directory. It manages it's
own loading.

Also, since **3.1** Rails defaults to jQuery for javascript, so `use_tinymce` does not support
installing the *raw* version of TinyMCE.

## Selectively adding TinyMCE to selected text areas

TinyMCE supports selectively using TinyMCE in or not in different textareas within
the same page .

To do this, proceed as above and for `textarea` elements that you do *not* want to
use TinyMCE in, add the class '`no-tinymce`'.

Read the comments in '`use_tinymce-gem-location/lib/tasks/use_tinymce.rake`' for details.
Or read the TinyMCE code.

## Step by Step

**Note:** you should *not* have to 'require use_tinymce` anyplace. If you do, please let me know.

1. You will need to install *TinyMCE* in your /public/javascript directory.
Do that by running one of the rake tasks below. They are all in the `use_tinymce`
namespace and depend on the version of Rails you are using. See below for details.

2. Add `use_tinymce args` to all the controllers for views containing **textarea** fields
in which you want to run *TinyMCE*. `args` should be:

  * `:all` - to enable *TinyMCE* for all actions
  * `:foo, :bar` - to enable *TinyMCE* only for views rendered by `foo` and `bar` actions
  [usually this is something like `use_tinymce :new, :edit`].

3. Add `<%= use_tinymce_link %>` to the `HEAD` section of you application layout - for *at least*
all pages which should use *TinyMCE*

4. (Optional) Edit `/public/javascript/use_tinymce_init.js` to customize your *TinyMCE* feature
set.

That's it.

### Rake Tasks

Rails 3.0:

* `rake use_tinymce:install_jquery` - if you are using jQuery (Rails 3.1 default)
* `rake use_tinymce:install_simple` - if you are using Prototype (Rails 3.0 default)
* `rake use_tinymce:install_advanced` - if you are using Prototype (Rails 3.0 default)
* `rake use_tinymce:uninstall` - removes all trace of TinyMCE & the initialization file from
your `/public/javascripts` directory. **WARNING:** this wipes out any customizations you have
done to the `init_tinymce.js` file. **WARNING:** `uninstall` is *always* run prior to
any of the `install` tasks.

If you don't need much and aren't familiar with *TinyMCE*, then `rake use_tinymce:install_simple`
should be fine. The *advanced* version configures *TinyMCE* with many more features. Go to
the ["TinyMCE website for details"](http://tinymce.moxiecode.com/)

Rails 3.1, 3.2, 4.0, 4.1, 4.2

* `rake use_tinymce:install` - installs the jQuery version of TinyMCE
* `rake use_tinymce:uninstall` - removes all trace of TinyMCE & the initialization file from
your `/public/javascripts` directory.  **WARNING:** this wipes out any customizations you have
done to the `init_tinymce.js` file. **WARNING:** `uninstall` is *always* run prior to
the `install` task.

This creates an `advanced` editor. See the *TinyMCE* website for configuration information:
["TinyMCE website for details"](http://tinymce.moxiecode.com/)


## Supplied Methods

Detailed info on the methods in this Gem.

As usual, read the source for the definitive, up-to-date truth.

### `use_tinymce(*actions)`

Which is added to ApplicationController as a Class Method.

`*actions` is 0 or more method names or the distinguished symbol `:all`. You put
this someplace in your controller if you want any of your controller actions
to render a page which uses TinyMCE

### `use_tinymce_link`

Which renders either an empty string or the links required to pull
in TinyMCE and `use_tinymce_init.js`. It makes it's decision by looking at the
value of `params[:action]`

### `use_tinymce?(action)`
Which you can use if you want or need finer control, use `use_tinymce?`
and write your own conditional.

`use_tinymce(action)` is a helper method you add to the cruft which creates
the `head` element of your page. It returns `true` if the symbol `action` was set
by a previous call to `use_tinymce` [or if you included `use_tinymce :all` in your controller]
`action` will usually come from `params[:action]`, so it's easier to use
`use_tinymce_link` which already does that.

## Rake Tasks:

**NOTE** `assets` here refers to the `assets` directory in the `use_tinymce`
gem - **not** the Rails 3.1, 3.2, 4.0, 4.1, 4.2 asset pipeline directory.

This initialization scripts were copied literally from the TinyMCE website
["For Dummies" page](http://tinymce.moxiecode.com/wiki.php/%22For_Dummies%22)
- that is: *http://tinymce.moxiecode.com/wiki.php/%22For_Dummies%22*

### Rails 3.0

**NOTE** Rails 3.0 support is no longer maintained by me. I will accept patches, but will not test them.

Prototype Versions:

* `rake use_tinymce:install_advanced` copies `use_tinymce/assets/use_tinymce_init_advanced.js`
and the contents of `use_tinymce/tinymce_no_jquery` -
which provides all the full blown features.

* `rake use_tinymce:install_simple` copies `use_tinymce/assets/use_tinymce_init_simple.js`
and the contents of `use_tinymce/tinymce_no_jquery` -
copies the bare bones version.

The jQuery version was copied from an example script and then slightly modified
[see the code comments].

* `rake use_tinymce:install_jquery` copies `assets/use_tinymce_init_jquery.js`
and the contents of `use_tinymce/tinymce_jquery` -
which attaches an 'advanced' TinyMCE configuration to textareas.

Maintenance: if you want to get rid of `use_tinymce`, then:

* `rake use_tinymce:uninstall` removes use_tinymce_init.js and the TinyMCE
directory from your applications `public/javascripts`

**Note:** `rake use_tinymce::uninstall` is called automatically when running any
`rake use_tinymce::install_*` to insure you get a clean installation. This will also
*clean* out any customization you have done.

### Rails 3.1, 3.2, 4.0, 4.1, 4.2

**NOTE** I no longer support this gem for Rails 3.1 and 3.2. I will accept patches, but will not test them.

`use_tinymce` only supports installing the jQuery version of TinyMCE on Rails 3.1, 3.2, 4.0, 4.1, 4.2.

There are two Rake tasks:

* rake use_tinymce:install - copies `assets/use_tinymce_init_jquery.js`
and the contents of `use_tinymce/tinymce_jquery` -
which attaches an 'advanced' TinyMCE configuration to textareas.
* `rake use_tinymce:uninstall` removes use_tinymce_init.js and the TinyMCE
directory from your applications `public/javascripts`

**Note:** `rake use_tinymce::uninstall` is called automatically when running
`rake use_tinymce::install` to insure you get a clean installation. This will also
*clean* out any customization you have done.

## Relocating *TinyMCE* or Something Else

This gem is pretty simple, so if you want to change the location and/or names of some files,
go ahead and hack it.

There was a time when I would have added features to allow this, but not any more. It ususally
turned out to be more trouble than it was worth.

So, if you want to do it, have a blast - but please don't send me the patches.

## Upgrading *TinyMCE*

`use_tinymce` ships with two versions of *TinyMCE* which I downloaded from the Moxiecode
website. You *should* be able to upgrade your *TinyMCE* if you want by doing the exact
same thing and then unpacking the zip file in `/public/javascript`

   version 3.4.2 of *TinyMCE* - used for Rails 3.0, 3.1 and 3.2 (it was working, so why change?)
   version 4.0.2 for Rails 4.0.0 - I decided to add this when adding support for Rails 4.0

**WARNING:** Rails 3.1 uses sprockets which has a problem compiling non-Unicoded characters.
the advanced *TinyMCE* contains some directly encoded characters in the *spellchecker_word_separator_chars*
variable in the spellchecking plugin. I've modified the code to convert them to Unicode \U...
representations. If you upgrade *TinyMCE* and run with Rails 3.1 + you will probably have
to do the same.

Alternately, you can poke around your system and find the gem and unpack the zip file
into the `use_tinymce` gem's `/assets` folder, re-run the rake tasks and . . .
**WARNING** if you do this, you'll overwrite your `use_tinymce_init.js` file and lose
all your special customizations.

Alternately (2), you can send me a note and I *might* upgrade the *TinyMCE* in the gem.
Then you'd need to do a `bundle update use_tinymce`, run the rake task, re-create your
special customizations, etc.

It's probably easier to grab the code from ["Moxiecode"](http://tinymce.moxiecode.com/download/download.php)
