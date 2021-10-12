# lua-aoi

  基于[cfadmin](https://cfadmin.cn)实现的纯Lua版九宫格`Aoi`算法.
  
## API Introduce

  ```lua
  local aoi = require "lua-aoi"
  ```
  
  导入后必须先使用`Aoi`类来创建一个实例.

### 0. Aoi new

```lua
function Aoi:new({
    x = x or 65535,
    y = y or 65535,
    radius = radius or 100,
  })
end
```

  `x` - `X`轴的最大值, 起始值为`0`, 默认值为`65535`.

  `y` - `Y`轴的最大值, 起始值为`0`, 默认值为`65535`.

  `radius` - 指定半径大小, 默认值为`100`.

  使用`Aoi`类创建实例, 创建后的实例可用于下面的操作.

### 1. Aoi Enter

```lua
---comment @Player Enter
---@param uid  any      @UID
---@param x    integer  @Y Position
---@param y    integer  @X Position
---@param fast boolean  @don't care response.
function Aoi:enter(uid, x, y, fast) return { uid1, uid2, uid3 } end
```

  返回值为**进入**后, 需要通知的**单位数组**.
  
  如果指定`fast`为`true`, 那么将不会有返回值.

### 2. Aoi Move

```lua
---comment @Player Move
---@param uid any      @UID
---@param x   integer  @Y Position
---@param y   integer  @X Position
function Aoi:move(uid, x, y) return { uid1, uid2, uid3 } end
```

  返回值为**移动**后, 需要通知的**单位数组**.

### 3. Aoi Leave

```lua
---comment @Player Leave
---@param uid any      @UID
function Aoi:leave(uid) return { uid1, uid2, uid3 } end
```

  返回值为**离开**后, 需要通知的**单位数组**.

### 4. Aoi Around

```lua
---comment @Player Get all units around `uid`
---@param uid any      @UID
function Aoi:around(uid) return { uid1, uid2, uid3 } end
```

  返回值为指定`uid`周围需要通知的**单位数组**.

### 5. Aoi Aroundx

```lua
---comment @Player Get all units around `X` and `Y` position
---@param x   integer  @Y Position
---@param y   integer  @X Position
function Aoi:aroundx(x, y) return { uid1, uid2, uid3 } end
```
  返回值为指定`X`与`Y`位置周围需要通知的**单位数组**.
  
### 6. Aoi get_uid

```lua
---comment Get uid position.
---@param uid any   @UID
---@return table    @Position{ x = xxx, y = yyy }
function Aoi:get_uid(uid) return { x = y, y = x } end
```
  返回值为指定`uid`的`X`与`Y`值.

### 7. Aoi Count

```lua
---comment Get all units amount.
---@return integer
function Aoi:Count()
  return self.ucount
end
```
  返回值内部单位总数

## Test

```lua
local aoi = require "lua-aoi"

local sys = require "sys"
local now = sys.now

-- 地图大小
local max_x, max_y = 5000, 5000
-- 地图内的人数
local max_humen = 1000
-- 指定半径范围
local radius = 100

local Amap = aoi:new {
  x = max_x,
  y = max_y,
  radius = radius,
}

for i = 1, max_humen do
  -- 指定人数进入到随机的位置
  Amap:enter("user-" .. i, math.random(0, max_x), math.random(0, max_y), true)
end

-- 启动每隔0.5秒触发一次的周期定时器
require "cf".at(0.5, function ()
  local ret = {}
  local uid =  "user-" .. math.random(1, max_humen)
  local s = now()
  -- -- 玩家移动后需要通知的人
  -- ret = Amap:move(uid, x, y)
  -- -- 玩家离开后需要通知的人
  -- ret = Amap:leave(uid)
  -- -- 根据指定UID, 获取其周边有多少人
  -- ret = Amap:around(uid)
  -- -- 根据指定位置, 获取周边有多少人
  -- local x, y = math.random(0, max_x), math.random(0, max_y)
  -- ret = Amap:aroundx(x, y)
  local e = now()
  local position = Amap:get_uid(uid)
  print(string.format("uid为: %s(%d, %d), 数量为: %d, 耗时为: %.4f秒", uid, position.x, position.y, #ret, e - s))
end)
```

## Advice

  * `uid`为`unique ID`的缩写, 是用来代指`Aoi`结构内部**唯一ID**而不是`User ID`.
  * 支持`integer/string`类型的`uid`值, 但建议自行构造成: `player::pid`、`npc::nid`、`creep::cid`等.
  * 数组下标查表是非常高效的, 所以一般是不会有性能问题的. 但请尽可能将聚集度设计的更松散, 避免大量单位聚集在格子内.
  * 可自行根据示例代码测试不同大小、范围、人数等等情况下的效率.
