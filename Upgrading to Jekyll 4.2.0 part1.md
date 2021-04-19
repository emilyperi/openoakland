## Upgrading to Jekyll 4.2.0 (Part I)

###### Setting up docker to run website locally

1. Install docker desktop application https://www.docker.com/get-started

2. Clone the github respository 

3. Open a terminal app and change to the repository directory

   Here is is a look at the contents of my clone of the repository

   ```bash
   emily$ ls
   Dockerfile		Gemfile.lock		Makefile		_config.yml		src
   Gemfile			LICENSE			README.md		docker-compose.yml
   ```

   We have two docker related configuration files, the Dockerfile and the docker-compose.yml

   The Dockerfile looks like this

   ```dockerfile
   FROM ruby:2.7
   
   COPY . /code
   WORKDIR /code
   RUN make setup
   
   CMD ["bash"]
   ```

   The format consistst of `INSTRUCTION arguments`

   * `FROM imagename` -- tells us which parent "image" we are using, in this case ruby 2.7. A docker image is sort of like the blue print for the container.

   * `COPY from to` -- copies the contents of a directory to another directory in the container
   * `WORKDIR directory` -- sets the working directory for the container
   * `RUN command` -- executes command when building the image
   * `CMD ["executable", "param1", "param2"]` -- this will run when the container is up

   Docker Compose is typically used for creating and running multi-container Docker applications. Although we are only using one container, we can still use Docker Compose to specify and run our container. The docker-compose.yaml file looks like

   ```yaml
   version: "2.0"
   services:
     site:
       build: .
       volumes:
         - ".:/code"
       ports:
         - "4000:4000"
       command: ["/usr/local/bin/bundle", "exec", "jekyll", "serve", "-H", "0.0.0.0"]
   ```

   

   * `version:` (DEPRECATED) specifies the version of Docker Compose, the language we are using
   * `services:` (REQUIRED) defines which services will be performed by the containers
     * In our application, there is only one service `site` made from one container, while in other applications, there may also be a databse etc.
   * `build:` (OPTIONAL) the directory where the we keep the `Dockerfile`, this can also 
   * `volumes:` defines a mapping between a host directory a container directory with `VOLUME:CONTAINER_PATH`
     * `VOLUME` is either a local (host) path, or a volume name. 
     * Allows changes made inside to container to persist even after removing the docker container
   * `ports:` exposes (defines which port) container ports and defines a mapping  `HOST:CONTAINER` 
     * Can also specify the protocol, such as `"6060:6060/udp"` or use a range of ports
   * `command:` overrides the the default command declared by the container image 
     * We are telling the container to run `bundle exec jekyll serve -H 0.0.0.0` which runs the website locally

   

4. Building and running the image with docker compose

   ```
   emily$ docker compose up --build
   ```

   (the output may look different)

   ```
   [+] Building 160.8s (9/9) FINISHED                                                     
    => [internal] load build definition from Dockerfile                              0.0s
    => => transferring dockerfile: 31B                                               0.0s
    => [internal] load .dockerignore                                                 0.0s
    => => transferring context: 2B                                                   0.0s
    => [internal] load metadata for docker.io/library/ruby:2.7                       3.0s
    => [internal] load build context                                                 0.2s
    => => transferring context: 250.33kB                                             0.2s
    => [1/4] FROM docker.io/library/ruby:2.7@sha256:e57b8922ec57a9116cdd8295c041a6  99.7s
    => => resolve docker.io/library/ruby:2.7@sha256:e57b8922ec57a9116cdd8295c041a6c  0.0s
    => => sha256:48ec805ebed1f1ca215167aee137f7cdecf0a0d0a4fd491361 2.00kB / 2.00kB  0.0s
    => => sha256:44718e6d535d365250316b02459f98a1b0fa490158cc53057 7.83MB / 7.83MB  23.2s
    => => sha256:efe9738af0cb2184ee8f3fb3dcb130455385aa428a27d14e 10.00MB / 10.00MB  9.0s
    => => sha256:e57b8922ec57a9116cdd8295c041a6cbf84ce7a8baf5e521e8 1.86kB / 1.86kB  0.0s
    => => sha256:5c2c0fe1800ad3d84d2d44763ecdd93174e44b1f2db06eb6ca 7.23kB / 7.23kB  0.0s
    => => sha256:bd8f6a7501ccbe80b95c82519ed6fd4f7236a41e0ae59ba 50.43MB / 50.43MB  48.9s
    => => sha256:f37aabde37b87d272286df45e6a3145b0884b72e07e657b 51.84MB / 51.84MB  40.3s
    => => sha256:3923d444ed0552ce73ef51fa235f1b45edafdec096abd 192.35MB / 192.35MB  85.4s
    => => sha256:6d245082de987bb6168e91693b43d7e0a7de48a26f500e42acc30 199B / 199B  40.9s
    => => sha256:8d567cf39ee06982ab7d75bff6f56520724047baf48386c 22.91MB / 22.91MB  53.1s
    => => sha256:34f0d57c91e21580983daf5cf060488dc08d5185f046df3ed50a5 176B / 176B  49.5s
    => => extracting sha256:bd8f6a7501ccbe80b95c82519ed6fd4f7236a41e0ae59ba4a8df76  10.6s
    => => extracting sha256:44718e6d535d365250316b02459f98a1b0fa490158cc53057d18858  0.6s
    => => extracting sha256:efe9738af0cb2184ee8f3fb3dcb130455385aa428a27d14e1e07a55  0.8s
    => => extracting sha256:f37aabde37b87d272286df45e6a3145b0884b72e07e657bf1a2a1e  10.8s
    => => extracting sha256:3923d444ed0552ce73ef51fa235f1b45edafdec096abda6abab710  12.2s
    => => extracting sha256:6d245082de987bb6168e91693b43d7e0a7de48a26f500e42acc30ee  0.0s
    => => extracting sha256:8d567cf39ee06982ab7d75bff6f56520724047baf48386c13c9fb08  1.0s
    => => extracting sha256:34f0d57c91e21580983daf5cf060488dc08d5185f046df3ed50a52f  0.0s
    => [2/4] COPY . /code                                                            0.6s
    => [3/4] WORKDIR /code                                                           0.0s
    => [4/4] RUN make setup                                                         56.2s
    => exporting to image                                                            1.1s
    => => exporting layers                                                           1.0s
    => => writing image sha256:90b4714428ca5c080939bbcc370e8a726afb5d50d231c2a30a77  0.0s
    => => naming to docker.io/library/openoaklandorg_site                            0.0s
   
   Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
   [+] Running 1/1
    ⠿ Container openoaklandorg_site_1  Created                                       0.1s
   Attaching to site_1
   site_1  | Configuration file: /code/_config.yml
   site_1  |             Source: src
   site_1  |        Destination: /code/_site
   site_1  |  Incremental build: disabled. Enable with --incremental
   site_1  |       Generating... 
   site_1  | /usr/local/bundle/gems/jekyll-3.8.6/lib/jekyll/convertible.rb:41: warning: Using the last argument as keyword parameters is deprecated
   site_1  | /usr/local/bundle/gems/jekyll-3.8.6/lib/jekyll/convertible.rb:41: warning: Using the last argument as keyword parameters is deprecated
   site_1  | /usr/local/bundle/gems/jekyll-3.8.6/lib/jekyll/convertible.rb:41: warning: Using the last argument as keyword parameters is deprecated
   ...
   site_1  |  Auto-regeneration: enabled for 'src'
   site_1  |     Server address: http://0.0.0.0:4000/
   site_1  |   Server running... press ctrl-c to stop.
   ```

   At the bottom we can see that the website is running at http://0.0.0.0:4000/ and that it will continue runnin until we press ctrl-c to stop it.

   In a new terminal window (you can also run the above with the flag -d for detached mode), we can verify that the conatiner is up and running

   ```bash
   emily$ docker ps
   CONTAINER ID   IMAGE                 COMMAND                  CREATED         STATUS         PORTS                    NAMES
   aab6989b85b0   openoaklandorg_site   "/usr/local/bin/bund…"   7 minutes ago   Up 7 minutes   0.0.0.0:4000->4000/tcp   openoaklandorg_site_1
   ```

   In this example, the container id is aab6989b85b0

5. We can run an interactive shell with the container

   ```
   emily$ docker exec -it aab6989b85b0 /bin/bash
   ```

   ```
   root@aab6989b85b0:/code# ls
   Dockerfile  Gemfile.lock  Makefile   _config.yml  docker-compose.yml
   Gemfile     LICENSE	  README.md  _site	  src
   ```

   Now instead of `emily$` we see `root@aab6989b85b0:/code#`.  In order to better organize our container, we put all of our website files inside the directory `\code`. We actually made this decision back in the Dockerfile with the line `WORKDIR \code`. The line `COPY . \code` in the Dockerfile copied all of the all of the files from the host directory `.` to `\code` in the container.

   

   ###### Gemfile and Gemfile.lock

   A gem is a mini ruby program that performs some specific funciton. We indicate all of the gems our project requires in our Gemfile. As you can see in the file comments, "this is where you manage which Jekyll version to run"

   Examining the Gemfile `root@aab6989b85b0:/code# cat Gemfile`

   ```
   source "https://rubygems.org"
   
   # Hello! This is where you manage which Jekyll version is used to run.
   # When you want to use a different version, change it below, save the
   # file and run `bundle install`. Run Jekyll with `bundle exec`, like so:
   #
   #     bundle exec jekyll serve
   #
   # This will help ensure the proper Jekyll version is running.
   # Happy Jekylling!
   gem "jekyll", "~> 3.8.5"
   gem 'jekyll-seo-tag'
   gem 'jekyll-redirect-from'
   gem 'hash-joiner'
   
   # If you want to use GitHub Pages, remove the "gem "jekyll"" above and
   # uncomment the line below. To upgrade, run `bundle update github-pages`.
   # gem "github-pages", group: :jekyll_plugins
   
   # If you have any plugins, put them here!
   group :jekyll_plugins do
     gem "jekyll-feed", "~> 0.6"
   end
   
   # Windows does not include zoneinfo files, so bundle the tzinfo-data gem
   gem "tzinfo-data", platforms: [:mingw, :mswin, :x64_mingw, :jruby]
   
   # Performance-booster for watching directories on Windows
   gem "wdm", "~> 0.1.0" if Gem.win_platform?
   
   
   gem "html-proofer", "~> 3.10"
   ```

   * `source LOCATION` defines the addres of the rubygems to download, typically rubygems.org

   * `gem "name", "version"` indicates the name and version for each gem to be included

   * `group :name do`  lumps the following gems into a group, so that they can be referenced later

     

   The Gemfile.lock is an *auto-generated* file that locks gems used in the application at a specific verison or range of versions. We should **not** change this manually.

   

   ```
   root@aab6989b85b0:/code# cat Gemfile.lock
   GEM
     remote: https://rubygems.org/
     specs:
       activesupport (5.2.4.3)
         concurrent-ruby (~> 1.0, >= 1.0.2)
         i18n (>= 0.7, < 2)
         minitest (~> 5.1)
         tzinfo (~> 1.1)
       addressable (2.6.0)
         public_suffix (>= 2.0.2, < 4.0)
       colorator (1.1.0)
       concurrent-ruby (1.1.6)
       em-websocket (0.5.1)
         eventmachine (>= 0.12.9)
         http_parser.rb (~> 0.6.0)
       ethon (0.12.0)
         ffi (>= 1.3.0)
       eventmachine (1.2.7)
       ffi (1.11.1)
       forwardable-extended (2.6.0)
       hash-joiner (0.0.7)
         safe_yaml
       html-proofer (3.11.1)
         activesupport (>= 4.2, < 6.0)
         addressable (~> 2.3)
         mercenary (~> 0.3.2)
         nokogiri (~> 1.9)
         parallel (~> 1.3)
         rainbow (~> 3.0)
         typhoeus (~> 1.3)
         yell (~> 2.0)
       http_parser.rb (0.6.0)
       i18n (0.9.5)
         concurrent-ruby (~> 1.0)
       jekyll (3.8.6)
         addressable (~> 2.4)
         colorator (~> 1.0)
         em-websocket (~> 0.5)
         i18n (~> 0.7)
         jekyll-sass-converter (~> 1.0)
         jekyll-watch (~> 2.0)
         kramdown (~> 1.14)
         liquid (~> 4.0)
         mercenary (~> 0.3.3)
         pathutil (~> 0.9)
         rouge (>= 1.7, < 4)
         safe_yaml (~> 1.0)
       jekyll-feed (0.12.1)
         jekyll (>= 3.7, < 5.0)
       jekyll-redirect-from (0.15.0)
         jekyll (>= 3.3, < 5.0)
       jekyll-sass-converter (1.5.2)
         sass (~> 3.4)
       jekyll-seo-tag (2.6.1)
         jekyll (>= 3.3, < 5.0)
       jekyll-watch (2.2.1)
         listen (~> 3.0)
       kramdown (1.17.0)
       liquid (4.0.3)
       listen (3.1.5)
         rb-fsevent (~> 0.9, >= 0.9.4)
         rb-inotify (~> 0.9, >= 0.9.7)
         ruby_dep (~> 1.2)
       mercenary (0.3.6)
       mini_portile2 (2.5.0)
       minitest (5.14.1)
       nokogiri (1.11.1)
         mini_portile2 (~> 2.5.0)
         racc (~> 1.4)
       parallel (1.17.0)
       pathutil (0.16.2)
         forwardable-extended (~> 2.6)
       public_suffix (3.1.1)
       racc (1.5.2)
       rainbow (3.0.0)
       rb-fsevent (0.10.3)
       rb-inotify (0.10.0)
         ffi (~> 1.0)
       rouge (3.9.0)
       ruby_dep (1.5.0)
       safe_yaml (1.0.5)
       sass (3.7.4)
         sass-listen (~> 4.0.0)
       sass-listen (4.0.0)
         rb-fsevent (~> 0.9, >= 0.9.4)
         rb-inotify (~> 0.9, >= 0.9.7)
       thread_safe (0.3.6)
       typhoeus (1.3.1)
         ethon (>= 0.9.0)
       tzinfo (1.2.7)
         thread_safe (~> 0.1)
       yell (2.2.0)
   
   PLATFORMS
     ruby
   
   DEPENDENCIES
     hash-joiner
     html-proofer (~> 3.10)
     jekyll (~> 3.8.5)
     jekyll-feed (~> 0.6)
     jekyll-redirect-from
     jekyll-seo-tag
     tzinfo-data
   
   BUNDLED WITH
      2.0.2
   
   ```

   The symbol `~>` indicates that the gem version can be within a range. 

   * ~> x.y.z is equivallent to >= x.y.z, < x.(y+1)
   * ~> x.y is equivalent to >= x.y, < (x+1)
   * ~> x just means >= x

   

   ###### bundle 

   bundle manages our gems. First, we should revert our changes to the Gemfile (`git stash`) and rebuild and run the docker container (see above). Then we can look at the possible `bundle` commands:

   ```
   root@881a5ccdde74:/code# bundle --help
   Bundler commands:
     bundle add GEM VERSION         # Add gem to Gemfile and run bundle install
     bundle binstubs GEM [OPTIONS]  # Install the binstubs of the listed gem
     bundle cache [OPTIONS]         # Locks and then caches all of the gems into vendor/cache
     bundle check [OPTIONS]         # Checks if the dependencies listed in Gemfile are satisfied by currently installed gems
     bundle config NAME [VALUE]     # Retrieve or set a configuration value
     bundle config get NAME         # Returns the value for the given key
     bundle config help [COMMAND]   # Describe subcommands or one specific subcommand
     bundle config list             # List out all configured settings
     bundle config set NAME VALUE   # Sets the given value for the given key
     bundle config unset NAME       # Unsets the value for the given key
     bundle console [GROUP]         # Opens an IRB session with the bundle pre-loaded
     bundle doctor [OPTIONS]        # Checks the bundle for common problems
     bundle env                     # Print information about the environment Bundler is running under
     bundle exec [OPTIONS]          # Run the command in context of the bundle
     bundle gem NAME [OPTIONS]      # Creates a skeleton for creating a rubygem
     bundle help [COMMAND]          # Describe available commands or one specific command
     bundle info GEM [OPTIONS]      # Show information for the given gem
     bundle init [OPTIONS]          # Generates a Gemfile into the current working directory
     bundle install [OPTIONS]       # Install the current environment to the system
     bundle issue                   # Learn how to report an issue in Bundler
     bundle licenses                # Prints the license of all gems in the bundle
     bundle list                    # List all gems in the bundle
     bundle lock                    # Creates a lockfile without installing
     bundle open GEM                # Opens the source directory of the given bundled gem
     bundle outdated GEM [OPTIONS]  # List installed gems with newer versions available
     bundle platform [OPTIONS]      # Displays platform compatibility information
     bundle plugin                  # Manage the bundler plugins
     bundle plugin help [COMMAND]   # Describe subcommands or one specific subcommand
     bundle plugin install PLUGINS  # Install the plugin from the source
     bundle plugin list             # List the installed plugins and available commands
     bundle pristine [GEMS...]      # Restores installed gems to pristine condition
     bundle remove [GEM [GEM ...]]  # Removes gems from the Gemfile
     bundle show GEM [OPTIONS]      # Shows all gems that are part of the bundle, or the path to a given gem
     bundle update [OPTIONS]        # Update the current environment
     bundle version                 # Prints the bundler's version information
   
   Options:
         [--no-color]                 # Disable colorization in output
     -r, [--retry=NUM]                # Specify the number of times you wish to attempt network commands
     -V, [--verbose], [--no-verbose]  # Enable verbose output mode
   ```

   

   Since we are trying to update jekyll, we might want to use `bundle update [OPTIONS]`.  We can get a more detailed look the options:

   ```
   root@881a5ccdde74:/code# bundler update --help
   Usage:
     bundler update [OPTIONS]
   
   Options:
         [--full-index=Fall back to using the single-file index of all gems], [--no-full-index]                                                     
         [--gemfile=Use the specified gemfile instead of Gemfile]                                                                                   
     -g, [--group=Update a specific group]                                                                                                          
     -j, [--jobs=Specify the number of jobs to run in parallel]                                                                                     
         [--local=Do not attempt to fetch gems remotely and use the gem cache instead], [--no-local]                                                
         [--quiet=Only output warnings and errors.], [--no-quiet]                                                                                   
         [--source=Update a specific source (and all gems associated with it)]                                                                      
     --force, [--redownload=Force downloading every gem.], [--no-redownload]                                                                        
         [--ruby=Update ruby specified in Gemfile.lock], [--no-ruby]                                                                                
         [--bundler=Update the locked version of bundler]                                                                                           
         [--patch=Prefer updating only to next patch version], [--no-patch]                                                                         
         [--minor=Prefer updating only to next minor version], [--no-minor]                                                                         
         [--major=Prefer updating to next major version (default)], [--no-major]                                                                    
         [--strict=Do not allow any gem to be updated past latest --patch | --minor | --major], [--no-strict]                                       
         [--conservative=Use bundle install conservative update behavior and do not allow shared dependencies to be updated.], [--no-conservative]  
         [--all=Update everything.], [--no-all]                                                                                                     
         [--no-color]                                                                                                                               # Disable colorization in output
     -r, [--retry=NUM]                                                                                                                              # Specify the number of times you wish to attempt network commands
     -V, [--verbose], [--no-verbose]                                                                                                                # Enable verbose output mode
   
   Description:
     Update will install the newest versions of the gems listed in the Gemfile. Use update when you have changed the Gemfile, or if
     you want to get the newest possible versions of the gems in the bundle.
   
   ```

   According to [A Guide to Update Gems with bundle update](https://medium.com/cedarcode/reduce-fear-of-bundle-update-with-this-4-step-process-e021e8808c48) by Gonzalo Rodriguez, we want have as change as little as possible per update in order reduce the likelyhood of breaking anything. The option `--conservative` will "not allow shared dependencies to be updated." That sounds like a good start.

   ```
   root@d16f9fcb34ff:/code# bundle update --conservative jekyll
   Fetching gem metadata from https://rubygems.org/..........
   Fetching gem metadata from https://rubygems.org/.
   Resolving dependencies...
   Using concurrent-ruby 1.1.6
   Using i18n 0.9.5
   Using minitest 5.14.1
   Using thread_safe 0.3.6
   Using tzinfo 1.2.7
   Using activesupport 5.2.4.3
   Using public_suffix 3.1.1
   Using addressable 2.6.0
   Using bundler 2.1.4
   Using colorator 1.1.0
   Using eventmachine 1.2.7
   Using http_parser.rb 0.6.0
   Fetching em-websocket 0.5.2 (was 0.5.1)
   Installing em-websocket 0.5.2 (was 0.5.1)
   Using ffi 1.11.1
   Using ethon 0.12.0
   Using forwardable-extended 2.6.0
   Using safe_yaml 1.0.5
   Using hash-joiner 0.0.7
   Using mercenary 0.3.6
   Using mini_portile2 2.5.0
   Using racc 1.5.2
   Using nokogiri 1.11.1 (x86_64-linux)
   Using parallel 1.17.0
   Using rainbow 3.0.0
   Using typhoeus 1.3.1
   Using yell 2.2.0
   Using html-proofer 3.11.1
   Fetching rb-fsevent 0.10.4 (was 0.10.3)
   Installing rb-fsevent 0.10.4 (was 0.10.3)
   Fetching rb-inotify 0.10.1 (was 0.10.0)
   Installing rb-inotify 0.10.1 (was 0.10.0)
   Using sass-listen 4.0.0
   Using sass 3.7.4
   Using jekyll-sass-converter 1.5.2
   Fetching listen 3.5.1 (was 3.1.5)
   Installing listen 3.5.1 (was 3.1.5)
   Using jekyll-watch 2.2.1
   Using kramdown 1.17.0
   Using liquid 4.0.3
   Using pathutil 0.16.2
   Fetching rouge 3.26.0 (was 3.9.0)
   Installing rouge 3.26.0 (was 3.9.0)
   Fetching jekyll 3.8.7 (was 3.8.6)
   Installing jekyll 3.8.7 (was 3.8.6)
   Using jekyll-feed 0.12.1
   Using jekyll-redirect-from 0.15.0
   Using jekyll-seo-tag 2.6.1
   Bundle updated!
   ```

   This seemed to update successfully, but looking closer we see `Installing jekyll 3.8.7 (was 3.8.6)`, so we did not manage to updagrade to our target. Looking at our Gemfile.lock again, towards the bottom we see:

   ```
   DEPENDENCIES
     hash-joiner
     html-proofer (~> 3.10)
     jekyll (~> 3.8.5)
     jekyll-feed (~> 0.6)
     jekyll-redirect-from
     jekyll-seo-tag
     tzinfo-data
   ```

   Because our Gemfile requests jekyll ~> 3.8.5, bundle will only make updates below 3.9. So what if we change this? Using vim (which I installed in the docker container using `apt-get install vim`), I edited the Gemfile to read `gem "jekyll", "~> 4.2.0"`. Then I re-ran the above command:

   ```
   root@d16f9fcb34ff:/code# bundle update --conservative jekyll
   Fetching gem metadata from https://rubygems.org/..........
   Fetching gem metadata from https://rubygems.org/.
   Resolving dependencies...
   Bundler could not find compatible versions for gem "i18n":
     In snapshot (Gemfile.lock):
       i18n (= 0.9.5)
   
     In Gemfile:
       html-proofer (~> 3.10) was resolved to 3.11.1, which depends on
         activesupport (>= 4.2, < 6.0) was resolved to 5.2.4.3, which depends on
           i18n (>= 0.7, < 2)
   
       jekyll (~> 4.2.0) was resolved to 4.2.0, which depends on
         i18n (~> 1.0)
   
   Running `bundle update` will rebuild your snapshot from scratch, using only
   the gems in your Gemfile, which may resolve the conflict.
   
   ```

   Unfortunately, this doesn't simply work. `bundle` gets stuck when trying to update `i18n`, since it is also used by `activesupport`, which is used by our current version of `html-proofer`.

   

   ###### Shared Dependencies

   First lets get some information on which gems are shared depdencies 

   Going back to the Gemfile.lock, we can see that our installation of `i18n` is locked at  `(~> 0.7)`. 

   However, in the message outputed above, we see that `jekyll (~> 4.2.0)` relies on `i18n (~> 1.0)`. Our current version of `html-proofer-3.11.1` uses `activesupport` and can use any version of `i18n` between `(>= 0.7, <2)`. Actually, *these are compatible!* Lets try to update both:

   ```
   root@f9c464e42334:/code# bundle update --conservative i18n jekyll
   Fetching gem metadata from https://rubygems.org/..........
   Fetching gem metadata from https://rubygems.org/.
   Resolving dependencies...
   Bundler could not find compatible versions for gem "mercenary":
     In snapshot (Gemfile.lock):
       mercenary (= 0.3.6)
   
     In Gemfile:
       html-proofer (~> 3.10) was resolved to 3.11.1, which depends on
         mercenary (~> 0.3.2)
   
       jekyll (~> 4.2.0) was resolved to 4.2.0, which depends on
         mercenary (~> 0.4.0)
   
   Running `bundle update` will rebuild your snapshot from scratch, using only
   the gems in your Gemfile, which may resolve the conflict.
   ```

   

   Now we run into another conflict, between `mercenary (~> 0.3.2)` required by `html-proofer` and  `mercenary (~> 0.4.0)` required by the newest version of `jekyll`.  This time not so easily resolved. 

   

   ###### Updating conflicting dependencies

   Looking at the [rubygems webpage for html-proofer](https://rubygems.org/gems/html-proofer) we can see that the most current version is 3.19.1 and requires `mercenary ~> 0.3`. In fact, any version of html-proofer beyond 3.12.0  requires this mercenary. **But upgrading to a newer html-proofer may cause parts of our website to break!** However, we can update to the next smallest patch by using --patch option. Additionally, we may want to update to the html-proofer, but we can do this little by little.

   

   ```
   root@f9c464e42334:/code# bundle update --patch  --conservative  html-proofer jekyll                                                                                     
   The dependency tzinfo-data (>= 0) will be unused by any of the platforms Bundler is installing for. Bundler is installing for ruby but the dependency is only for x86-mi
   ngw32, x86-mswin32, x64-mingw32, java. To add those platforms to the bundle, run `bundle lock --add-platform x86-mingw32 x86-mswin32 x64-mingw32 java`.                 
   Fetching gem metadata from https://rubygems.org/..........                                                                                                              
   Fetching gem metadata from https://rubygems.org/.                                                                                                                       
   Resolving dependencies...                                                                                                                                               
   Using public_suffix 3.1.1                                                                                                                                               
   Using addressable 2.6.0                                                                                                                                                 
   Using bundler 2.1.4                                                                                                                                                     
   Using colorator 1.1.0                                                                                                                                                   
   Using concurrent-ruby 1.1.6                                                                                                                                             
   Using eventmachine 1.2.7                                                                                                                                                
   Using http_parser.rb 0.6.0                                                                                                                                              
   Using em-websocket 0.5.2                                                                                                                                                
   Using ffi 1.11.1                                                                                                                                                        
   Using ethon 0.12.0                                                                                                                                                      
   Using forwardable-extended 2.6.0                                                                                                                                        
   Using safe_yaml 1.0.5                                                                                                                                                   
   Using hash-joiner 0.0.7                                                                                                                                                 
   Fetching mercenary 0.4.0 (was 0.3.6)                                                                                                                                    
   Installing mercenary 0.4.0 (was 0.3.6)                                                                                                                                  
   Using mini_portile2 2.5.0                                                                                                                                               
   Using racc 1.5.2                                                                                                                                                        
   Using nokogiri 1.11.1 (x86_64-linux)
   Using parallel 1.17.0
   Using rainbow 3.0.0
   Using typhoeus 1.3.1
   Using yell 2.2.0
   Fetching html-proofer 3.12.2 (was 3.11.1)
   Installing html-proofer 3.12.2 (was 3.11.1)
   Fetching i18n 1.0.1 (was 0.9.5)
   Installing i18n 1.0.1 (was 0.9.5)
   Fetching sassc 2.4.0
   Installing sassc 2.4.0 with native extensions
   Fetching jekyll-sass-converter 2.0.1 (was 1.5.2)
   Installing jekyll-sass-converter 2.0.1 (was 1.5.2)
   Using rb-fsevent 0.10.4
   Using rb-inotify 0.10.1
   Using listen 3.5.1
   Using jekyll-watch 2.2.1
   Fetching rexml 3.2.5
   Installing rexml 3.2.5
   Fetching kramdown 2.3.1 (was 1.17.0)
   Installing kramdown 2.3.1 (was 1.17.0)
   Fetching kramdown-parser-gfm 1.1.0
   Installing kramdown-parser-gfm 1.1.0
   Using liquid 4.0.3
   Using pathutil 0.16.2
   Using rouge 3.26.0
   Fetching unicode-display_width 1.7.0
   Installing unicode-display_width 1.7.0
   Fetching terminal-table 2.0.0
   Installing terminal-table 2.0.0
   Fetching jekyll 4.2.0 (was 3.8.7)
   Installing jekyll 4.2.0 (was 3.8.7)
   Using jekyll-feed 0.12.1
   Using jekyll-redirect-from 0.15.0
   Using jekyll-seo-tag 2.6.1
   Bundle updated!
   ```

   Great! Now if check our Gemfile.lock, we see that we have ` jekyll (4.2.0)` and `html-proofer (3.12.2)`

   The next step is to see how these changes impact our website source code.

   