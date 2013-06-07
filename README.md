sasswatch
=========

is a simple [Gentoo Linux](http://www.gentoo.org) service / initscript for automatic background compiling of [Sass](http://sass-lang.com) files. Once installed and started on a Gentoo Linux system, *sasswatch* monitors the project specific files and folders you configured, tracks changes in Sass files and silently compiles them to CSS in real-time.


Installation
------------  

Download and extract (or clone) the GitHub repository to your Gentoo box. Merge the contents of the `etc` folder to the corresponding locations on your system. You should have

*	`/etc/conf.d/sasswatch`
*	`/etc/init.d/sasswatch`
*	`/etc/sasswatch.d/example`

in place then. Next, [edit the global configuration](#global-configuration) to your needs and go [configure some projects](#project-configuration). Finally, to start / stop / restart the *sasswatch* service, you can use:

	/etc/init.d/sasswatch start
	/etc/init.d/sasswatch stop
	/etc/init.d/sasswatch restart

If you want the *sasswatch* service to start on system startup, just include the service into your default runlevel:

	rc-update add sasswatch default  

Whenever you modify any of the configuration files (global or project), the service needs to be restarted (there's no "graceful" reloading at the moment).

See below for some [prerequisites](#prerequisites) for running sasswatch on your box.


Global configuration
--------------------

The global configuration file `/etc/conf.d/sasswatch` holds the default options for any watcher sasswatch will start. There are three variables to define here:
	
*	**SASSWATCH_COMPASS**

	Whether to automatically load the compass library ("0" or "1"). Defaults to "1" if you don't specify anything here.
	
*	**SASSWATCH_STYLE**

	Default Sass [output style](http://sass-lang.com/docs/yardoc/file.SASS_REFERENCE.html#output_style). Defaults to "expanded" if you don't specify anything here. Valid options are "nested", "expanded", "compact" and "compressed".
	
*	**SASSWATCH_REQUIRE**

	Determines the additional libraries that should be loaded. Separate several libraries with a space (e.g. "library1 library2"). Defaults to an empty string (no additional libraries are being loaded then). 


Project configuration
---------------------

Project configurations have to be located in the directory `/etc/sasswatch.d`. You can have an arbitrary number of project configurations. Each of them will be read and used by a single `sass --watch` process and may define up to 4 variables (see the [example configuration](https://github.com/tollwerk/sasswatch/blob/master/etc/sasswatch.d/example) for the exact format):

*	**compass**

	Same as the global *SASSWATCH_COMPASS* setting (see above), but on project level. Defaults to the global value if omitted. 

*	**style**

	Same as the global *SASSWATCH_STYLE* setting (see above), but on project level. Defaults to the global value if omitted.
	
*	**require**

	Same as the global *SASSWATCH_REQUIRE* setting (see above), but on project level. Defaults to the global value if omitted.

*	**watch**

	This is the only mandatory variable that has to be defined in a project configuration. It sets the `input-file:output-file` respectively `input-folder:output-folder` tuples that make up the compilation process. Any Sass file referenced as input file or contained in (or below) the input folder will be compiled to a CSS file at the corresponding output location.
	
	You may specify an arbitrary number of compilation tuples. Put them all into the *watch* variable, ideally separated by a new line, e.g. like this:
	
		watch="/path/to/sass/dir1:/path/to/css/dir1
		/path/to/sass/file2:/path/to/css/file2"; 
		

Prerequisites
-------------

To use *sasswatch* on your system, you must have **Ruby**, **sass**, **GNU parallel** and some additional libraries (like potentially **compass**) installed.

First off, get Ruby and put this into your `/etc/portage/make.conf` (respectively `/etc/make.conf` on older systems):

	RUBY_TARGETS="ruby19"
	
Next, emerge Ruby, sass and GNU parallel. On some systems some of the required packages might need the experimental keyword, so you might have to put something like this into your `/etc/portage/package.keywords`:

	dev-ruby/rb-inotify ~amd64
	dev-ruby/listen ~amd64
	dev-ruby/sass ~amd64

Then:

	emerge ruby sass sys-process/parallel

There's no [compass](http://compass-style.org) ebuild on Gentoo, so you will have to install it as Ruby Gem:

	gem install compass
	
If you intend to use additional, external libraries, you will be on your own to install them appropriately.


Legal
-----
*sasswatch* Copyright © 2013 tollwerk® GmbH / Joschi Kuphal, licensed under a [Creative Commons Attribution 3.0 Unported License](http://creativecommons.org/licenses/by/3.0/).
