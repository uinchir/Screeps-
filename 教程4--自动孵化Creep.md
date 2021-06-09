## 教程4--自动孵化Creep

### 4.1

到目前为止，我们都是通过在控制台中输入命令来手动创建新的 creep。我们并不推荐经常这么做，因为 Screeps 的主旨就是让您的殖民地实现自我控制。更好的做法是教会您这个房间中的 spawn 自己生产 creep。

这是一个相当复杂的问题，许多玩家会花费几个月的时间来完善和增强他们的自动孵化代码。但是先让我们从简单开始，来了解一些相关的基本原则。

### 4.2

您需要在老的 creep 因为寿命或其他原因死掉时孵化新的 creep。由于游戏中没有事件机制来报告特定 creep 的死亡。所以最简单的方式就是通过统计每种 creep 的数量，一旦其数量低于给定值，就开始孵化。

有很多种方法可以统计指定类型的 creep 数量。其中一种就是通过 `_.filter` 方法以及 creep 内存中的 role 字段对 `Game.creeps` 进行筛选。让我们尝试一下，并把 creep 的数量显示在控制台中。

把 `harvester` 角色的 creep 数量显示在控制台中。

文档：

- [Game.creeps](https://screeps-cn.gitee.io/api/#Game.creeps)
- [lodash.filter](https://lodash.com/docs#filter)

```
var roleHarvester = require('role.harvester');
var roleUpgrader = require('role.upgrader');

module.exports.loop = function () {

    var harvesters = _.filter(Game.creeps, (creep) => creep.memory.role == 'harvester');
    console.log('Harvesters: ' + harvesters.length);

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

### 4.3

假设我们最少需要维持两个采集单位（harvester），最简单的办法就是：每当我们发现它们的数量小于这个值时，就执行 `StructureSpawn.spawnCreep` 方法。您可能还没想好它们应该叫什么（这一步我们会自动给它们起名字），但是不要忘了给他们设置需要的角色（role）。

我们还会添加一些新的 `RoomVisual` 来显示当前正在孵化的 creep。

在您的 main 模块中添加 `StructureSpawn.spawnCreep` 相关逻辑。

文档：

- [StructureSpawn.spawnCreep](https://screeps-cn.gitee.io/api/#StructureSpawn.spawnCreep)
- [RoomVisual](https://screeps-cn.gitee.io/api/#RoomVisual)

```
var roleHarvester = require('role.harvester');
var roleUpgrader = require('role.upgrader');

module.exports.loop = function () {

    var harvesters = _.filter(Game.creeps, (creep) => creep.memory.role == 'harvester');
    console.log('Harvesters: ' + harvesters.length);

    if(harvesters.length < 2) {
        var newName = 'Harvester' + Game.time;
        console.log('Spawning new harvester: ' + newName);
        Game.spawns['Spawn1'].spawnCreep([WORK,CARRY,MOVE], newName, 
            {memory: {role: 'harvester'}});        
    }
    
    if(Game.spawns['Spawn1'].spawning) { 
        var spawningCreep = Game.creeps[Game.spawns['Spawn1'].spawning.name];
        Game.spawns['Spawn1'].room.visual.text(
            '🛠️' + spawningCreep.memory.role,
            Game.spawns['Spawn1'].pos.x + 1, 
            Game.spawns['Spawn1'].pos.y, 
            {align: 'left', opacity: 0.8});
    }

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

### 4.4

现在让我们模拟一下，当一个采集单位死掉了的情况。您可以在控制台中对指定 creep 执行 `suicide` 命令，或者直接在右侧的属性面板中点击 “自杀” 按钮。

让某个采集单位自杀。

文档：

- [Creep.suicide](https://screeps-cn.gitee.io/api/#Creep.suicide)

```
Game.creeps['Harvester1'].suicide()
```

### 4.5

还有一件事，由于死亡 creep 的内存我们之后可能会用到，所以它们并不会被自动清除。如果您每次都用随机名称去孵化新 creep 的话，内存可能会因此溢出，所以您需要在每个 tick 开始的时候将它们清除掉（creep 创建代码之前）。

添加清理内存的代码。

```
var roleHarvester = require('role.harvester');
var roleUpgrader = require('role.upgrader');

module.exports.loop = function () {

    for(var name in Memory.creeps) {
        if(!Game.creeps[name]) {
            delete Memory.creeps[name];
            console.log('Clearing non-existing creep memory:', name);
        }
    }

    var harvesters = _.filter(Game.creeps, (creep) => creep.memory.role == 'harvester');
    console.log('Harvesters: ' + harvesters.length);

    if(harvesters.length < 2) {
        var newName = 'Harvester' + Game.time;
        console.log('Spawning new harvester: ' + newName);
        Game.spawns['Spawn1'].spawnCreep([WORK,CARRY,MOVE], newName, 
            {memory: {role: 'harvester'}});
    }
    
    if(Game.spawns['Spawn1'].spawning) { 
        var spawningCreep = Game.creeps[Game.spawns['Spawn1'].spawning.name];
        Game.spawns['Spawn1'].room.visual.text(
            '🛠️' + spawningCreep.memory.role,
            Game.spawns['Spawn1'].pos.x + 1, 
            Game.spawns['Spawn1'].pos.y, 
            {align: 'left', opacity: 0.8});
    }

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

### 4.6

现在，死者的内存被回收掉了，这有助于帮助我们节省资源。

除了在老 creep 死掉之后再创建一个新的，还有其他的方法可以把 creep 的数量维持在期望值：`StructureSpawn.renewCreep` 方法。不过在本教程中 creep 的老化已经被禁用了，所以我们建议您自己尝试了解一下。

文档：

- [StructureSpawn.renewCreep](https://screeps-cn.gitee.io/api/#StructureSpawn.renewCreep)