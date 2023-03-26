---
title: 【译】GPT-4能否“真正”编写代码？
date: 2023.03.19 17:49:20
cover: https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f131a456136e46c3a0821cfe8be2cdaf~tplv-k3u1fbpfcp-zoom-crop-mark:1512:1512:1512:851.awebp?

categories:
  - 开源大事件
tags:
  - 2022
---

## GPT-4能否“真正”编写代码？

> 原文链接：[【Can GPT-4 *Actually* Write Code?】](https://tylerglaiel.substack.com/p/can-gpt-4-actually-write-code) -- Tyler Glaiel

## 省流

- 笔者用了两个问题来测试GPT4: 小猫问题、新月问题。
- 著名的问题，或者已经有大量解决方案的问题，GPT-4 绝对可以写代码，它主要只是组装和混合它所见过的东西
- 尝试解决编程过程中遇到的以前未曾解决过问题或者新问题类型，它就完全失灵了。
- 此外，它喜欢“猜测”，如果将您引向错误的问题解决路径，这些猜测会浪费很多时间。

- ChatGPT不知道答案，在其训练集中没有此类示例，也无法在其模型中找到该答案。有用的做法是简单地说“我不知道是否存在这样的算法。” 但它过于自信，只是胡乱编造。


## 序
自从ChatGPT问世以来，我看到很多人发布了关于它编写代码的能力的帖子。人们已经发布了如何让它设计和实现数字拼图游戏（没有意识到这个游戏“发明”已经存在），他们如何让它克隆乒乓球，甚至我也用它编写了一些简单的Python实用程序脚本。 它非常有能力，是一个非常有用的工具。

但是，所有这些人们发布的例子中都有一个共同点。它们都是已经解决过的问题，或者对这些问题进行极小的修改。虽然说，公平地讲，很多编程工作实际上就是将现有解决方案粘合在一起，并将现有代码适应于您特定的用例，但编程中*困难* 的部分是解决以前没有被解决过的问题。

所以我决定在一个我几年前解决的特别困难的“算法”问题上进行测试。这个问题足够小和孤立，可以适应ChatGPT提示，但有足够的细微差别，让我觉得它可能会出错。

## 问题

让我们从描述实际的真实使用案例问题开始。在《Mewgenics》中，移动能力使用路径规划将猫从起点移动到目的地。

![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/09894a7e809a454fac8ee25b34f305f3~tplv-k3u1fbpfcp-watermark.image?)

猫有一个最大移动范围的属性（在这种情况下是6），而地块有一个代价（在这种情况下，基本地块为1，阻碍障碍物为9999）。我们还有水地块，通过它们需要花费2个代价。


![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0dbebf279273470bad414f63b279d24e~tplv-k3u1fbpfcp-watermark.image?)


到目前为止，我所描述的内容对于基于网格的路径规划来说都是很普通的。Dijkstra算法和A*算法都可以很好地处理不同成本的瓦片，并且仅需在一定距离后截断路径以处理最大移动范围问题。

当我们将火（和其他危险类型的方块）加入到混合中时，就会出现复杂性。与水方块不同，火方块在寻路时不需要额外花费代价，但是猫真的想避免通过火方块进行路径规划。这里展示了解决方法。还有很多更复杂的情况，如果有很多火砖，则应尽可能少地经过它们等等，并且所有这些都运作良好。


![cat.gif](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/780003bf945c4aafb46ef74d239093eb~tplv-k3u1fbpfcp-watermark.image?)

现在，为什么这是一个困难的问题并不完全明显。或者至少，为什么解决方案比简单修改启发式算法（如考虑火灾等“渴望成本”）的A*更加复杂也不是很清楚。这个问题与标准的A*路径规划非常微妙地不同，而解决方案虽然非常接近A*，但有一些非直觉性的变化对其影响很大。这是一个“难题”，回到2020年我第一次将火瓦片添加到游戏中时，这个路径规划问题花了几天时间才得以正确解决。主要复杂性在于“最大移动范围”的限制因素以及它如何与通过瓷砖的成本和用于避免危险瓷砖的“渴望成本”相互作用。您可以看到我发布的gif图像中，在尝试越过火灾区域并向前移动越远时路径会发生变化，最终无法在6步内绕过它，则必须穿过它来寻路。


而且除了简单的情况之外，它还有很多复杂性：

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/749711fac8274ac2b4eb2026e2e22028~tplv-k3u1fbpfcp-watermark.image?)


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a480c503b8e411796141e6792f6b3ab~tplv-k3u1fbpfcp-watermark.image?)

（14个总移动次数与同一级别的10个总移动次数）

A*和Dijkstra算法中有一种内置的假设，即如果从A到C的最短路径经过了方块B，则它也会沿着从A到B的最短路径。因此，如果您已经找到了通往B的最短路径，那么可以直接从那里开始继续前往C。这些算法依赖于这种效率，因为在从优先级队列中提取内容时可以跳过已经找到更短路径的方块，并且在重构路径时可以依靠每个方块存储其在寻路算法期间被访问的来源方块来实现。


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a821e7d484d24a4cb6acfca423d15c7a~tplv-k3u1fbpfcp-watermark.image?)

这是我的意思：这种情况在“标准”的寻路中不会发生！从A到B的最佳路径与从A到C的最佳路径不一致，尽管从A到C的路径包含了瓦片B。这使得事情变得复杂，并打破了现有寻路依赖的一些假设。

## 我的解决方案

所以，它是使用修改后的成本和启发式函数的A*或Dijkstra算法对吧？嗯...几乎是这样，但并不完全一样。你可以在下面看到代码（还有一些额外的东西来最小化路径中的拐弯）。你会注意到......它并不完全像dijkstra或A*，而且其中有很多东西并不明显为什么要这样做。实际上，我在2020年写了这篇文章，并已经从我的脑海中清除了它......甚至对我来说也不再明显为什么要这样做。为了准备这篇博客文章，我进行了双重检查，并尝试将其简化为修改后的A*算法，但每次简化都会添加大量寻路错误。所以就是现在这个模样。而导致此处思路的火车头大部分已经被抛弃于我的脑海之中，因为它已经能够正常工作了。

为什么路径被存储在添加到优先队列中的单元格内，而不是按照A*标准方式将节点存储为它们从哪个节点到达的？为什么在弹出队列时检查路径是否是最佳路径，而不是在推入队列时进行检查？这两者都与A*有相当大的偏差，但显然对于此工作来说绝对必要。

```cpp
    struct advanced_weighted_cell {
        iVec2D cell;
        int cost;
        int desire;
        int bends = -1;
        temppodvector<char> current_path;

        int path_value() const {
            return cost*100+bends;
        }

        std::weak_ordering operator <=>(const advanced_weighted_cell& rhs) const {
            if(desire != rhs.desire) return desire <=> rhs.desire;
            if(cost != rhs.cost) return cost <=> rhs.cost;
            if(bends != rhs.bends) return bends <=> rhs.bends;
            return std::weak_ordering::equivalent;
        }
    };

    podvector<iVec2D> TacticsGrid::advanced_pathfind(Character* source, iVec2D begin, iVec2D end, int max_range, bool sunken_only){
        max_range *= pathfind_cost_resolution;

        podvector<iVec2D> path;
        dynamic_grid<int> pathvalues(width, depth);
        for(auto& t:pathvalues) t = INT_MAX;

        if(!Debug.CheckAssert(pathvalues.in_bounds(begin)) || !Debug.CheckAssert(pathvalues.in_bounds(end))) {
            return path;
        }

        auto costs = pathfind_costs(source, sunken_only);

        std::priority_queue<advanced_weighted_cell, std::vector<advanced_weighted_cell>, std::greater<advanced_weighted_cell>> queue;

        queue.push({begin, 0, 0, 0});

        int total_checked = 0;

        while(!queue.empty()){
            auto tile = queue.top();
            queue.pop();

            if(tile.path_value() <= pathvalues[tile.cell]){
                pathvalues[tile.cell] = tile.path_value();

                if(tile.cell == end) {
                    iVec2D current = begin;
                    for(auto i : tile.current_path){
                        current += all_orientations[i];
                        path.push_back(current);
                    }

                    //Debug.DisplayVariable(total_checked);

                    return path;
                    break;
                }

                for(int i = 0; i<4; i++){
                    auto &v = all_orientations[i];
                    iVec2D t2 = v+tile.cell;

                    if(pathvalues.in_bounds(t2)){
                        advanced_weighted_cell next = {t2, 
                                                       tile.cost+costs[t2].enter_cost + costs[tile.cell].exit_cost,
                                                       tile.desire+(t2==end?0:costs[t2].desire_cost),// + iso_dist(t2-end), //heuristic is more efficient, but results in paths with non-optimal numbers of bends
                                                       tile.bends};

                        if(tile.current_path.size() > 0 && i != tile.current_path.back()) next.bends++;

                        if(next.cost <= max_range && next.path_value() <= pathvalues[t2]){
                            next.current_path.resize(tile.current_path.size()+1); //minimize reallocations
                            if(tile.current_path.size() > 0) {
                                std::memcpy(next.current_path.data(), tile.current_path.data(), tile.current_path.size()*sizeof(char));
                            }
                            next.current_path.back() = i;

                            queue.push(next);
                            total_checked++;
                        }
                    }
                }
            }
        }

        return path;
    }
```
无论如何，这就是问题的所有背景信息。如果你想真正感受到它有多么不平凡（或者如果你认为它只是一个带有修改启发式的A*算法），欢迎尝试自己解决它。（如果你要“建议”我改进方法，请先在上下文中实际实现和尝试一下，并知道我不会使用它，因为我的方法已经很好了，在成千上万个不同情况下进行了彻底测试，并且没有任何性能问题）

## 让我们看看ChatGPT表现如何

这篇文章的重点在于以下内容。实际的实验。 GPT-4（通过ChatGPT Plus）是否真正能够解决这个问题？ 我问了它：

> 编写一个函数来解决以下问题： 代理需要在一个10x10的网格中从当前位置到目标位置找到最佳路径。 他们有5个点的有限移动范围 普通瓷砖通过移动花费1个点，水瓷砖通过移动花费2个点。 火焰瓷砖通过移动花费1个点，但是即使这意味着走更长的路线到达目的地（只要路径仍然在其有限运动范围内），他们也应该避免穿过它们。

为了实验的目的，我明确省略了算法的细节，而是给出了需要解决的“问题”的描述。无论如何，这就是你开始处理问题的方式，而且我真正好奇是否可能有一个更明显或更简单的解决方案被我忽略了，所以我不想用实现细节来污染它思考问题。 我也没有说要使用哪种编程语言，因为我认为如果让计算机自己选择Python会比让它写C ++ 更好。毕竟我只关心实际算法。

ChatGPT 回复说，

> 你可以使用A*寻路算法来考虑成本和约束条件，找到最优路径。这是一个解决问题的Python函数：

```python
import heapq


class Tile:
    def __init__(self, x, y, tile_type):
        self.x = x
        self.y = y
        self.tile_type = tile_type

    def cost(self):
        if self.tile_type == "water":
            return 2
        elif self.tile_type == "fire":
            return 1
        else:
            return 1

    def is_fire(self):
        return self.tile_type == "fire"


def heuristic(a, b):
    return abs(a.x - b.x) + abs(a.y - b.y)


def a_star_search(grid, start, goal, max_movement):
    frontier = []
    heapq.heappush(frontier, (0, start))
    came_from = {start: None}
    cost_so_far = {start: 0}

    while frontier:
        current = heapq.heappop(frontier)[1]

        if current == goal:
            break

        for neighbor in get_neighbors(current, grid):
            new_cost = cost_so_far[current] + neighbor.cost()
            if new_cost <= max_movement and (neighbor not in cost_so_far or new_cost < cost_so_far[neighbor]) and not neighbor.is_fire():
                cost_so_far[neighbor] = new_cost
                priority = new_cost + heuristic(goal, neighbor)
                heapq.heappush(frontier, (priority, neighbor))
                came_from[neighbor] = current

    return came_from, cost_so_far


def get_neighbors(tile, grid):
    neighbors = []
    for dx, dy in ((-1, 0), (1, 0), (0, -1), (0, 1)):
        x, y = tile.x + dx, tile.y + dy
        if 0 <= x < len(grid) and 0 <= y < len(grid[0]):
            neighbors.append(grid[x][y])
    return neighbors


def reconstruct_path(came_from, start, goal):
    path = [goal]
    current = goal
    while current != start:
        current = came_from[current]
        path.append(current)
    return path[::-1]


def find_best_path(grid, start, goal, max_movement):
    came_from, cost_so_far = a_star_search(grid, start, goal, max_movement)
    if goal not in came_from:
        return None
    return reconstruct_path(came_from, start, goal)
```

它有点理解问题，但是它不是通过“避免”穿过火焰来解决问题，而是永远不会穿过火焰。

我继续问：

这段代码拒绝通过火焰，但如果必须在移动范围内到达目的地，则仍应通过火焰。

它道歉并修复了a_star_search函数，方法如下：

```python
def a_star_search(grid, start, goal, max_movement):
    frontier = []
    heapq.heappush(frontier, (0, start))
    came_from = {start: None}
    cost_so_far = {start: 0}

    while frontier:
        current = heapq.heappop(frontier)[1]

        if current == goal:
            break

        for neighbor in get_neighbors(current, grid):
            new_cost = cost_so_far[current] + neighbor.cost() + (1000 if neighbor.is_fire() else 0)
            if new_cost - (1000 if neighbor.is_fire() else 0) <= max_movement and (neighbor not in cost_so_far or new_cost < cost_so_far[neighbor]):
                cost_so_far[neighbor] = new_cost
                priority = new_cost + heuristic(goal, neighbor)
                heapq.heappush(frontier, (priority, neighbor))
                came_from[neighbor] = current

    return came_from, cost_so_far
```

再说一遍，还不太对。它没有区分通过火焰的惩罚成本和通过瓷砖的物理成本，因此它仍然不会穿过火焰（因为+1000的惩罚使得火焰瓷砖远超出你的移动范围）。

无论如何，我又反复尝试了几次，让它生成一个测试用例，并修复了在实际运行时出现的运行时错误。最终采用了这个方案，并将测试用例定制为一些简单易懂的失败情况。它在测试用例中混淆了x和y，但我不想因此与它争执，所以我只是处理好了（重新生成需要相当长时间）。

```python
import heapq

class Tile:
    def __init__(self, x, y, tile_type):
        self.x = x
        self.y = y
        self.tile_type = tile_type

    def cost(self):
        if self.tile_type == "water":
            return 2
        elif self.tile_type == "fire":
            return 1
        else:
            return 1

    def is_fire(self):
        return self.tile_type == "fire"

def heuristic(a, b):
    return abs(a.x - b.x) + abs(a.y - b.y)

def a_star_search(grid, start, goal, max_movement):
    frontier = []
    heapq.heappush(frontier, (0, 0, start))
    came_from = {start: None}
    cost_so_far = {start: 0}
    counter = 1

    while frontier:
        _, _, current = heapq.heappop(frontier)

        if current == goal:
            break

        for neighbor in get_neighbors(current, grid):
            new_cost = cost_so_far[current] + neighbor.cost()
            if new_cost <= max_movement and (neighbor not in cost_so_far or new_cost < cost_so_far[neighbor]):
                cost_so_far[neighbor] = new_cost
                priority = new_cost + heuristic(goal, neighbor) + (1000 if neighbor.is_fire() and not (current.is_fire() and neighbor == goal) else 0)
                heapq.heappush(frontier, (priority, counter, neighbor))
                came_from[neighbor] = current
                counter += 1

    return came_from, cost_so_far

def get_neighbors(tile, grid):
    neighbors = []
    for dx, dy in ((-1, 0), (1, 0), (0, -1), (0, 1)):
        x, y = tile.x + dx, tile.y + dy
        if 0 <= x < len(grid) and 0 <= y < len(grid[0]):
            neighbors.append(grid[x][y])
    return neighbors

def reconstruct_path(came_from, start, goal):
    path = [goal]
    current = goal
    while current != start:
        current = came_from[current]
        path.append(current)
    return path[::-1]

def find_best_path(grid, start, goal, max_movement):
    came_from, cost_so_far = a_star_search(grid, start, goal, max_movement)
    if goal not in came_from:
        return None
    return reconstruct_path(came_from, start, goal)



def create_tile_grid(letter_grid):
    tile_grid = []
    for x, row in enumerate(letter_grid):
        tile_row = []
        for y, tile_type in enumerate(row):
            if tile_type == "R":
                tile_row.append(Tile(x, y, "regular"))
            elif tile_type == "W":
                tile_row.append(Tile(x, y, "water"))
            elif tile_type == "F":
                tile_row.append(Tile(x, y, "fire"))
        tile_grid.append(tile_row)
    return tile_grid


def test_case(letter_grid, start_coords, goal_coords, max_movement):
    grid = create_tile_grid(letter_grid)
    start = grid[start_coords[0]][start_coords[1]]
    goal = grid[goal_coords[0]][goal_coords[1]]
    path = find_best_path(grid, start, goal, max_movement)

    if path:
        print("Path found:")
        for tile in path:
            print(f"({tile.x}, {tile.y}) -> {tile.tile_type}")
    else:
        print("No path found within the movement range.")


letter_grid = [
    ["R", "F", "F", "F", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
    ["R", "R", "R", "R", "R", "R", "R", "R", "R", "R"],
]

start_coords = (0, 0)
goal_coords = (0, 3)
max_movement = 20

test_case(letter_grid, start_coords, goal_coords, max_movement)
```
在给定的测试用例中，因为它以火砖结束，所以它忽略了尝试避开火砖。 

我要求它修复这个问题，但是它给了我一个解决方案，回到了完全无法通过火砖进行路径规划的状态。

我又要求它修复这个问题，结果又回到之前的状态。

在绕圈子几次之后，我决定就这样了。它接近成功了。它似乎理解了问题，但实际上无法正确地解决它。

这会在2020年帮助我吗？可能不会。我试图采用它的解决方案，并使用我的人类大脑将其修改为实际有效的东西，但是它所走的道路并不完全正确，因此无法挽救。再次强调，从基本A*到实际解决方案的修改并不明显，因此从“只是A*”开始并没有太多帮助。此外，GPT甚至不能真正意识到这个问题比“修改A*”更难，这一点洞察力原本可以在我最初编写该算法时节省时间。

无论如何，也许这不是适合GPT的问题。毕竟，A*算法是一种非常常见的算法，在其训练数据中肯定有成千上万甚至数百万个示例，因此它可能会受到限制，无论你多么努力推动和帮助它都可能不能偏离太远。

## 其他尝试

我用了几个其他“困难”的算法再次尝试，结果每次都差不多。它经常只会提出类似问题的解决方案，而忽略使您的问题与众不同的微妙之处，在几次修订后，它通常就会崩溃。 

在这种情况下（这是Mew解决反弹的方式），它基本上错过了问题的*所有*细节，比如说不能让两个移动物体同时移动到同一时间。这一个特别糟糕，在我意外地问GPT 3.5而不是GPT 4时，它比GPT 4更接近真正的解决方案。


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a0250e80d32485e88f9358f725f08e6~tplv-k3u1fbpfcp-watermark.image?)


这个……根本不接近。 当我无意中询问 GPT 3.5 时，它变得更加接近了。这实际上是一个“可行的解决方案，但存在一些错误和边缘情况”。它不能处理物体在链式移动中相互移动的循环，但是比起 GPT4 给出的绝对没有什么要好多了……奇怪…



![image.png](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/c609ed809a8642a5ab638d1ec636af7d~tplv-k3u1fbpfcp-watermark.image?)



![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0ec9828bd4254faea2ec8cccf0812d7a~tplv-k3u1fbpfcp-watermark.image?)

可能存在与其训练集中出现的类似问题。 很难想象真正独特的问题，它肯定没有见过任何类似的东西，至少是可以用一两段描述的。我们来试试一个奇怪构造的例子。让它创建一个在月牙形状之间进行碰撞检测算法。（我在快速搜索中找不到这个算法，并且它似乎有些非平凡，所以就让我们尝试吧）


![image.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/41df50357b244fcfa712b15423a3c747~tplv-k3u1fbpfcp-watermark.image?)



![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/062635f65a6c45b0880b12771901ae3d~tplv-k3u1fbpfcp-watermark.image?)


然后它编写了那个算法。值得称赞的是，如果你试图思考它为什么是错误的，这并不是显而易见的。但找到一个反例也不太难。


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/fef9904dc22748c098c3d8ed1f4c1e66~tplv-k3u1fbpfcp-watermark.image?)

我让它再试一次。


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2e91fa8f253644688a3c0b014cd84a15~tplv-k3u1fbpfcp-watermark.image?)


再次说明，实际上很难找到反例，但这里有一个失败了2.a（外圆相撞，每个内圆与另一个的外圆相撞但彼此之间不会碰撞，然而新月形状却没有发生碰撞）的例子：


![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a22cd3aa37644a0688b62b046edcd867~tplv-k3u1fbpfcp-watermark.image?)

我认为ChatGPT现在只是在胡说八道。它没有答案，也想不出一个，所以现在只是编造东西。而且...它真的很擅长编造东西！就像那个算法...它失败的情况非常微妙！你可以轻松地将该算法发布到书中，并让人们误以为实现有错误时会导致碰撞检测中存在漏洞，因为它肯定*听起来*像能解决这个问题的类型算法。

## 那么，GPT-4 真的能写代码吗？

鉴于算法的描述或者是已经有大量现成例子在网上的著名问题的描述，GPT-4 绝对可以写代码。它主要只是组装和混合它所见过的东西，但公平地说...很多编程就是这样。

然而，当尝试解决*实际问题*时，它完全失灵。这些是编程过程中可能遇到的以前未曾解决过的新问题类型。此外，它喜欢“猜测”，如果将您引向错误的问题解决路径，则这些猜测会浪费很多时间。

新月的例子在这里有点令人担忧。ChatGPT不知道答案，在其训练集中没有此类示例，也无法在其模型中找到该答案。有用的做法是简单地说“我不知道是否存在这样的算法。”但它过于自信，只是胡乱编造。尽管它在编写简单代码方面表现出奇怪的能力，但它与许多其他领域存在相同的问题，这一事实被掩盖了一些。

无论如何，用它自己的话说。


![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dffe9155704c4644b19296a834eb3714~tplv-k3u1fbpfcp-watermark.image?)

