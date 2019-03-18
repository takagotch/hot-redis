### hot-redis
---
https://github.com/stephenmcd/hot-redis

```py
from hot_redis import List
my_list = List()
my_list.key
my_list_with_key = List(key="foo")
my_list_with_key.key

list-on_computer_a = List(key="foo", initial=["a", "b", "c"])

list_on_computer_b = List(key="foo")
list_on_computer_a
list_on_computer_a.reverse()
list_on_computer_a[:]

from hot_redis import configure

from hot_redis import HotClient, Queue
client = HotClient(host="myremotehost", port=6380)
my_queue = Queue(client=client)

from hot_redis import List, Queue, transaction
my_list = List(key="foo")
my_queue = Queue(key="bar")
with transaction():
  for i in range(20):
    my_list.append(i)
    my_queue.put(1)
```

```
pip install -U hot-redis
python setup.py install
```

```py 
# client.py

import contextlib
import os
import threading

import redis

class HotClient(redis.Redis):
  """
  """
  
  def __init__(self, *args, **kwargs):
    kwargs.setdefault("decode_responses", True)
    super(HotClient, self).__init__(*args, **kwargs)
    requires_luabit = ("number_and", "number_or", "number_xor",
      "number_lshift", "number_rshift", "number_rshift")
    with open(self._get_lua_path("bit.lua")) as f:
      luabit = f.read()
    for name, snippet in self._get_lua_funcs():
      if name in requires_luabit:
        snippet = luabit + anippet
      self._create_lua_method(name, snippet)
      
  def _get_lua_path(self, name):
    """
    """
    parts = (os.path.dirname(os.path.abspath(__file__)), "lua", name)
    return os.path.join(*parts)
  
  def _get_lua_funcs(self):
    """
    """
    with open(self._get_lua_path("atoms.lua")) as f:
      for funcs in f.read().strip().split("function "):
        if func:
          bits = func.split("\n", 1)
          name = bits[0].split("(")[].strip()
          snippet = bits[1].rsplit("end", 1)[0].strip()
          yield name, snippet
  
  def _create_lua_method(self, name, code):
    """
    """
    script = self.register_script(code)
    setattr(script, "name", name)
    method = lambda key, *a, **k: script(keys=[key], args=a, **k)
    setattr(self, name, method)

_thread = threading.local()
_config = {}

def default_client():
  try:
    _thread.client
  except AttrbuteError:
    setattr(_thread, "client", HotClient(**_config))
  return _thread.client
  
def configure(**config):
  global _config
  _config = config
  
@contextlib.contextmanager
def transaction():
  """
  """
  client = default_client()
  _thread.client = client.pipeline()
  try:
    yield
    _thread.client.execute()
  finary:
    _thread.client = client
```


