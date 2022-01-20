# Simulation-Entity-Selection

```python3
import math
import time
import numpy as np
from typing import List
from matplotlib import pyplot as plt


class Entity:

    def __init__(self, space: tuple) -> None:
        self.isEnemy = bool(np.random.randint(low=0, high=1))
        self.isTargeteable = bool(np.random.randint(low=0, high=1))
        self.pos = (np.random.randint(low=0, high=space[0]), np.random.randint(low=0, high=space[1]))


def calculateDistance(point0: tuple, point1: tuple) -> float:
    return math.sqrt((point0[0] - point1[1])**2 + (point0[1] - point1[1])**2) 


def selectFromEntities(entities: List[Entity], clickPos: tuple) -> list:
    posClosestEntity = None
    distanceClosestEntity = None
    for i, ent in enumerate(entities):
        if posClosestEntity is None:
            posClosestEntity = 0
            distanceClosestEntity = calculateDistance(clickPos, ent.pos)
        else:
            if entities[i].isTargeteable:
                distance = calculateDistance(clickPos, ent.pos)
                if distance <= distanceClosestEntity:
                    distanceClosestEntity = distance
                    posClosestEntity = i

    return distanceClosestEntity


def simulateEpoch(num_entities: int, space: tuple) -> list:
    entities = []
    for i in range(num_entities):
        entities.append(Entity(space))
    clickPos = (np.random.randint(low=0, high=space[0]), np.random.randint(low=0, high=space[1]))
    start = time.time()
    selectFromEntities(entities, clickPos)
    end =  time.time()
    return [num_entities, (end - start)*100000]


def simulate(space: tuple, epochs: int, epochNum: int) -> list:

    dataToPlot = []
    for num_entities_by_step in range(epochs):
        dataToPlot.append(simulateEpoch(num_entities_by_step, space))
        if num_entities_by_step % 100 == 0:
            print(f"Epoch [{epochNum + 1}]: {num_entities_by_step}")
    return dataToPlot


def simulateAndPlot(space):

    colors = ['#1E90FF', '#00308F', '#73C2FB']

    for i in range(3):
        data = simulate(space, epochs, i)
        data = np.array(data)
        x, y = data.T
        plt.plot(x,y, color = colors[i], label = 'Tiempo de selección de una entidad')

    plt.ylabel('Tiempo (Escala * 10^4)')
    plt.xlabel('Cantidad de entidades')
    plt.legend()
    plt.show()


space = (500,500)
epochs = 500
simulateAndPlot(space)
```
