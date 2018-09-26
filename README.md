### warden
---

https://github.com/wardencommunity/warden

```
env['warden'].authenticated?
env['warden'].authenticated?(:foo)
env['warden'].authenticated(:password)
env['warden'].authenticate!(:password)

env['warden'].authenticate(:password)
env['warden'].user

throw(:warden)
throw(:warden, :stuff => "foo")
```

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


