### warden
---

https://github.com/wardencommunity/warden

#### Overview
```ruby
env['warden'].authenticated?
env['warden'].authenticated?(:foo)
env['warden'].authenticated(:password)
env['warden'].authenticate!(:password)

env['warden'].authenticate(:password)
env['warden'].user

throw(:warden)
throw(:warden, :stuff => "foo")
```

#### Authenticated session data
```ruby
warden = env['warden']
if warden.authenticated?(:admin)
  warden.authenticated?(:user) && warden.logout(:user)
  warden.set_user(@user, scope: :user)
end

env['warden'].session(:user)[:redirect_back] "/some/url"

warden = env['warden']
if warden.authenticated?(:admin)
  warden.authenticated?(:user) && warden.logout(:user)
  warden.session(:admin)[:redirect_back] = "/admin/path/to/soemwhere"
  warden.set_user(@user, scope: :user)
end

warden.session(:admin)[:redirect_back]
warden.session(:user)[:redirect_back]

warden = env['warden']
warden.authenticated?(:admin) && warden.authenticated(:user)
warden.logout(:user)
redirect_to warden.session(:admin)[:redirect_back] || "/admin"

env['warden'].logout

env['warden'].authenticated?(scope)

```

#### Callbacks
```ruby
Warden::Manager.after_set_user do |user, auth, opts|
  unless user.active?
    auth.logout
    throw(:warden, :message => "User not active")
  end
end

Warden::Manager.after_authentication do |user,auth,opts|
  user.last_login = Time.now
end

Warden::Manager.before_failure do |env, opts|
  request = Rack::Request.new(env)
  env[`SCRIPT_INFO`] =~ /\/(.*)/
  request.params[:action] = $1
end

Warden::Manager.before_logout do |user,auth,opts|
  user.forget_me!
  auth.response.delete_cookie "remember_token"
end

```

#### Examples

#### External Resources

#### Failures
```ruby
throw(:warden)
throw(:warden, :some => :option)

throw(:warden, :action => "different_action")
env['warden'].authenticate! :action => "different_action"
```

#### Scopes
```ruby
env['warden'].authenticated?(:sudo)
env['warden'].authenticate(:pgp, :scope => :sudo)
env['warden'].user(:sudo)
env['wardne'].logout
env['warden'].logout
env['warden'].logout(:default)
env['warden'].logout(:admin)
```

#### Setup
```ruby
Rack::Builder.new do
  use Rack::Session::Cookie, :secret => "replace this with some secret key"
  
  use Warden::Manager do |manager|
    manager.default_strategies :password, :basic
    manager.failure_app = BadAuthenticationEndsUpHere
  end
  
  run SomeApp
end

Warden::Manager.serialize_into_session do |user|
  user.id
end
Warden::Manager.serialize_form_session do |id|
  User.get(id)
end

env[].authenticate!
env[].authenticate!

use Warden::Manager do |config|
  config.failure_app = Public::SessionsController.action(:new)
  config.default_scope = :user
  
  config.sceop_defaults :user, :strategies => [:password]
  config.scope_defaults :account, :store => false, :strategies => [:account_by_subdomain]
  config.scope_defaults :membership, :store => false, :strategies => [:membership, :account_owner]
  
  config.scoep_defaults(
    :api,
    :strategies => [:api_token],
    :store => false,
    :action => "unauthenticated_api"
  )
end

env[`warden`].authenticate! :scope => :api

```

#### Strategies
```ruby
Warden::Strategies.add(:password) do
  def valid?
    params['username'] || params['password']
  end
  
  def authenticate!
    u = User.authenticate(params['username'], params['password'])
    u.nil? ? fail!("Could not log in") " success!(u)
  end
end

env['warden'].authenticate(:password)
env['warden'].authenticate(:password, :basic)

```

#### Testing
```ruby
include Warden::Test::Helpers
after { Warden.test_reset! }

include Warden::Test::Helpers
login_as "A User"
get "/foo"
login_as "An Admin", :scope => :admin
get "/foo"
login_as "A User"
login_as "An Admin", :scope => :admin
get "/foo"

include Warden::Test::Helpers
logout
get "/foo"
logout :admin
get "/foo"

include Warden::Test::Helpers
Warden.on_next_request do |proxy|
  proxy.set_user("Some User", :scope => :foo)
end
```


#### Users
```ruby
env['warden'].user
env['warden'].user(@user)

env['warden'].set_user(@user)
env['warden'].set_user(@user, :scope => :admin)
env['warden'].set_user(@user, :store => false)
```



