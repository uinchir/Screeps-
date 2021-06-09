## 教程1--游戏UI与游戏编程

### 1.1.1.欢迎来到 Screeps！

这个教程将帮助您一步步地了解这个游戏的基础概念。您可以稍后再进行这个教程，但是我们强烈建议您在开始真正的游戏前先来试试手。

Screeps 是一个为程序员们设计的游戏，如果您不知道如何编写 JavaScript 代码，来试试这个 [免费的交互式课程](https://codecademy.com/learn/javascript)

### 1.1.2.让我们开始吧！

这是一个被称为 “房间（room）” 的游戏窗口，在实际游戏中，房间会通过出口（exit）与其他房间相连，但是在模拟模式下，只有一个房间可以供您使用。

屏幕中心的这个小东西是您的第一个 Spawn，它是您的殖民地核心。

文档：

- [游戏世界](https://screeps-cn.gitee.io/introduction.html#游戏世界)

### 1.1.3创建一个采矿creep

您的 Spawn 可以通过 `spawnCreep` 方法创建名为 “creep” 的新单位。可以在 [本文档](https://screeps-cn.gitee.io/index.html) 中找到该方法的介绍。每个 creep 都有一个名字（name）和一定量的身体部件（body part），不同的身体部件会带来不同的能力。

您可以使用您 spawn 的名字来获取到它，就像这样：`Game.spawns['Spawn1']`。

创建一个身体部件为 `[WORK,CARRY,MOVE]` 并且名字叫做 `Harvester1`（这个名字对本教程来说非常重要！）的工人 creep。您可以自己在控制台中输入这些代码，或者复制 & 粘贴下面的提示。

文档：

- [您的殖民地](https://screeps-cn.gitee.io/introduction.html#属地（Colony）)
- [Creeps](https://screeps-cn.gitee.io/creeps.html)
- [游戏对象](https://screeps-cn.gitee.io/global-objects.html#Game-对象)
- [StructureSpawn.spawnCreep](http://docs.screeps.com/api/#StructureSpawn.spawnCreep)

```
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Harvester1' );
```

### 1.2.让creep去采矿

想让 creep 去采集能量，您需要使用下面 “文档” 小节中介绍的方法，这些指令每个游戏 tick 都会被执行。而 `harvest` 方法则需要在 creep 相邻的位置上有一个能量源。

您可以通过 creep 的名字来获取到它并对其下达命令，就像这样：`Game.creeps['Harvester1']`。把 `FIND_SOURCES` 常量作为参数传递给 `Room.find` 方法可以房间中的能量源。

通过在 “脚本” 面板中键入代码来让您的 creep 前去采集能量。

文档：

- [Game.creeps](http://docs.screeps.com/api/#Game.creeps)
- [RoomObject.room](http://docs.screeps.com/api/#RoomObject.room)
- [Room.find](http://docs.screeps.com/api/#Room.find)
- [Creep.moveTo](http://docs.screeps.com/api/#Creep.moveTo)
- [Creep.harvest](http://docs.screeps.com/api/#Creep.harvest)

```
module.exports.loop = function () {
    var creep = Game.creeps['Harvester1'];
    var sources = creep.room.find(FIND_SOURCES);
    if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
        creep.moveTo(sources[0]);
    }
}
```

### 1.3.让creep运矿回来

想要让 creep 把能量运送回 spawn，您需要使用 `Creep.transfer` 方法。但是请记住，这个方法只有在 creep 和 spawn 相邻的时候才能正确执行，所以需要让 creep 先走回来。

当您把 `.store.getFreeCapacity() > 0` 作为检查条件添加到代码里时，creep 应该就可以自己一步步的把能量搬运回 spawn 然后走回能量源。

拓展您的 creep 程序，使其可以将采集到的能量运送（transfer）回 spawn 中并重新开始工作。

文档：

- [Creep.transfer](https://screeps-cn.gitee.io/api/#Creep.transfer)
- [Creep.store](https://screeps-cn.gitee.io/api/#Creep.store)

```
module.exports.loop = function () {
    var creep = Game.creeps['Harvester1'];
    if(creep.store.getFreeCapacity() > 0) {
        var sources = creep.room.find(FIND_SOURCES);
        if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
            creep.moveTo(sources[0]);
        }
    }
    else {
        if( creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE ) {
            creep.moveTo(Game.spawns['Spawn1']);
        }
    }
}
```

### 1.4.再创建一个采矿creep

Nice！现在这个 creep 将会一直作为采集者（harvester）工作直到去世。请记住，几乎所有的 creep 都有 1500 游戏 tick 的生命周期，在此之后它就会 “老去” 然后死掉（这个设定在本教程中并不生效）。

让我们孵化新的 creep 来帮助第一个。这会消耗掉 200 点能量，所以您可能需要等到采集单位收集到足够的能量。`spawnCreep` 方法会返回错误码 `ERR_NOT_ENOUGH_ENERGY`（-6）直到您能量足够为止。

请记住：想要执行一次性的代码的话，直接在 “控制台” 面板中输入就可以了。

孵化第二个 creep，其身体部件为 `[WORK,CARRY,MOVE]` 并命名为 `Harvester2`。

文档：

- [StructureSpawn.spawnCreep](https://screeps-cn.gitee.io/api/#StructureSpawn.spawnCreep)

```
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Harvester2' );
```

### 1.5.让第二个采矿creep工作

第二个 creep 已经就绪了，但是它现在还不会动，所以我们需要将其添加进我们的程序。

想要给所有的 creep 都设置行为，只需要把整个脚本为新的 creep 复制一遍就好了，但是更好的做法是使用 `for` 循环来遍历 `Game.creeps` 中的所有 creep。

拓展您的程序，使其可以适用到所有 creep 上。

文档：

- JavaScript 参考：[ for...in loops](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)

```
module.exports.loop = function () {
    for(var name in Game.creeps) {
        var creep = Game.creeps[name];

        if(creep.store.getFreeCapacity() > 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE) {
                creep.moveTo(Game.spawns['Spawn1']);
            }
        }
    }
}
```

### 1.6.创建一个采矿工人模块

现在，让我们把工作单位的行为逻辑封装到一个单独的 *module* 里来改善我们的代码。使用模块功能创建一个名为 `role.harvester` 的模块，您可以在脚本编辑器的左侧找到它。然后在 `module.exports` 对象中定义一个 `run` 函数来存放 creep 的行为逻辑。

创建 `role.harvester` 模块。

文档：

- [使用模块组织代码](https://screeps-cn.gitee.io/modules.html)

```
var roleHarvester = {

    /** @param {Creep} creep **/
    run: function(creep) {
	    if(creep.store.getFreeCapacity() > 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.transfer(Game.spawns['Spawn1'], RESOURCE_ENERGY) == ERR_NOT_IN_RANGE) {
                creep.moveTo(Game.spawns['Spawn1']);
            }
        }
	}
};

module.exports = roleHarvester;
```

### 1.7.重写main函数代码

现在，您可以重写 main 模块的代码，只留下 loop 函数，并通过 `require('role.harvester')` 方法调用您的新模块。

将 `role.harvester` 模块引入到 main 模块中。

```javascript
var roleHarvester = require('role.harvester');

module.exports.loop = function () {

    for(var name in Game.creeps) {
        var creep = Game.creeps[name];
        roleHarvester.run(creep);
    }
}
```