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

```javascript
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

```javascript
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

```javascript
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

```javascript
Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Harvester2' );
```

### 1.5.让第二个采矿creep工作

第二个 creep 已经就绪了，但是它现在还不会动，所以我们需要将其添加进我们的程序。

想要给所有的 creep 都设置行为，只需要把整个脚本为新的 creep 复制一遍就好了，但是更好的做法是使用 `for` 循环来遍历 `Game.creeps` 中的所有 creep。

拓展您的程序，使其可以适用到所有 creep 上。

文档：

- JavaScript 参考：[ for...in loops](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...in)

```javascript
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

```javascript
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



## 教程2--升级控制器

在本教程部分中，我们将来介绍您房间中的重要战略目标：**房间控制器**（controller）。控制这个不可摧毁的小东西将允许您在房间中建造建筑。控制器的等级越高，允许建造的建筑就越多。

### 2.1.1.创建一个升级工人

您将需要一个新 creep 工作单位去升级您的控制器等级，让我们称其为 “Upgrader1”。在接下来的章节中我们将介绍如何自动创建 creep，但是现在让我们还是和之前一样在控制器里输入下面的命令。

孵化一个身体为 `[WORK,CARRY,MOVE]` 且名称为 `Upgrader1` 的 creep。

文档：

- [控制](https://screeps-cn.gitee.io/control.html)
- [Game.spawns](http://docs.screeps.com/api/#Game.spawns)
- [StructureSpawn.spawnCreep](http://docs.screeps.com/api/#StructureSpawn.spawnCreep)

`Game.spawns['Spawn1'].spawnCreep( [WORK, CARRY, MOVE], 'Upgrader1' );`



### 2.1.2.creep “Upgrader1” 将执行和 harvester 相同的任务，但是我们并不想让它这么做。我们需要一个不同的 creep 角色（role）。

### 2.2.用内存设置不同的creep角色

为此，我们需要利用每个 creep 都有的 `memory` 属性，该属性允许在 creep 的“内存”中写入自定义信息。这样，我们就可以给 creep 分配不同的角色。

您储存的所有内存信息可以通过全局对象 `内存` 访问。这两种方式您想用哪种都可以。

使用控制台将属性 `role='harvester'` 写入采集单位的内存，将 `role='upgrader'` 写入升级单位的内存。

文档：

- [Memory 对象](https://screeps-cn.gitee.io/global-objects.html#Memory-对象)
- [Creep.memory](http://docs.screeps.com/api/#Creep.memory)

```javascript
Game.creeps['Harvester1'].memory.role = 'harvester';
Game.creeps['Upgrader1'].memory.role = 'upgrader';
```

### 2.3.让采矿工人和升级工人工作

您可以在左侧的 creep 信息面板或者 “内存” 面板中查看您 creep 的内存。

现在，让我们来定义新 creep 的行为逻辑。两种 creep 都需要采集能量，但是角色为 `harvester` 的 creep 需要把能量带回到 spawn，而角色为 `upgrader` 的 creep 需要走到 controller 旁然后对其执行 `upgradeController` 方法（您可以通过 `Creep.room.controller` 属性获取到 creep 所在房间的 controller 对象）。

为此，我们需要创建一个名为 `role.upgrader` 的新模块。

创建名为 `role.upgrader` 的新模块，并写入您新 creep 的行为逻辑。

文档：

- [RoomObject.room](http://docs.screeps.com/api/#RoomObject.room)
- [Room.controller](http://docs.screeps.com/api/#Room.controller)
- [Creep.upgradeController](http://docs.screeps.com/api/#Creep.upgradeController)

```javascript
var roleUpgrader = {

    /** @param {Creep} creep **/
    run: function(creep) {
	    if(creep.store[RESOURCE_ENERGY] == 0) {
            var sources = creep.room.find(FIND_SOURCES);
            if(creep.harvest(sources[0]) == ERR_NOT_IN_RANGE) {
                creep.moveTo(sources[0]);
            }
        }
        else {
            if(creep.upgradeController(creep.room.controller) == ERR_NOT_IN_RANGE) {
                creep.moveTo(creep.room.controller);
            }
        }
	}
};

module.exports = roleUpgrader;
```

### 2.4.创建升级工人模块

在我们的 main 模块中，所有的 creep 都在扮演相同的角色。我们需要使用先前定义的 `Creep.memory.role` 属性区分它们的行为，注意不要忘记导入新模块哦。

将 `role.upgrader` 模块中的逻辑应用到拥有 `upgrader` 角色的 creep 身上并检查其表现。

```javascript
var roleHarvester = require('role.harvester');
var roleUpgrader = require('role.upgrader');

module.exports.loop = function () {

    for(var name in Game.creeps) {
        var creep = Game.creeps[name];
        if(creep.memory.role == 'harvester') {
            roleHarvester.run(creep);
        }
        if(creep.memory.role == 'upgrader') {
            roleUpgrader.run(creep);
        }
    }
}
```

干得好，您已经成功升级了您控制器的等级！

**重要：**如果您在 20,000 游戏 tick 内都没有升级您的控制器的话，它将会损失一个等级。当降至 0 级时，您将失去对房间的控制权，并且其他的玩家可以毫无代价的将其占领。请确保至少有一个 creep 定期执行 `upgradeController` 方法。

