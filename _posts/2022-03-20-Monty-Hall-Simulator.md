# Monte Hall Problem Simulator

## Introduction
During the film '21' Ben, the main character (played by Jim Sturgess) 
describes the choices he would make if faced with the [Monty Hall problem](https://en.wikipedia.org/wiki/Monty_Hall_problem).

The explaination given by Ben is (to me) far from intuitive, so I thought I'd verify it using a simulation approach. As I am currently learning Python this  presented a good opportunity to practice my skills.

## So what is the Monty Hall problem?
The  **Monty Hall problem**  is a brain teaser, in the form of a  probability puzzle, loosely based on the American television game show  *Let's Make a Deal*  and named after its original host, [Monty Hall](https://en.wikipedia.org/wiki/Monty_Hall "Monty Hall"). The problem was originally posed (and solved) in a letter by  [Steve Selvin](https://en.wikipedia.org/wiki/Steve_Selvin "Steve Selvin")  to the  _[American Statistician](https://en.wikipedia.org/wiki/The_American_Statistician "The American Statistician")_  in 1975.[[1]](https://en.wikipedia.org/wiki/Monty_Hall_problem#cite_note-FOOTNOTESelvin1975a-1)[[2]](https://en.wikipedia.org/wiki/Monty_Hall_problem#cite_note-FOOTNOTESelvin1975b-2)  It became famous as a question from reader Craig F. Whitaker's letter quoted in  [Marilyn vos Savant](https://en.wikipedia.org/wiki/Marilyn_vos_Savant "Marilyn vos Savant")'s "Ask Marilyn" column in  _[Parade](https://en.wikipedia.org/wiki/Parade_(magazine) "Parade (magazine)")_  magazine in 1990:[[3]](https://en.wikipedia.org/wiki/Monty_Hall_problem#cite_note-FOOTNOTEvos_Savant1990a-3)

>Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?

Vos Savant's response was that the contestant should switch to the other door.[[3]](https://en.wikipedia.org/wiki/Monty_Hall_problem#cite_note-FOOTNOTEvos_Savant1990a-3)  Under the standard assumptions, the switching strategy has a  2/3  [probability](https://en.wikipedia.org/wiki/Probability "Probability")  of winning the car, while the strategy that remains with the initial choice has only a  1/3  probability.

## A Monte Carlos approach
I chose to check the validity of the solution using a Monte Carlos approach, whereby many versions (thousands or even millions) of the gameshow are simulated. The outcomes of each simulation are then used to assess the probability of the contestant winning when they decide to change their initial decision for which door the prize is behind. 

Since we will be running many simulations and storing the results I decided to the implement the solution using numpy due to it's ability to deal with python arrays efficiently.

## Let's get writing some code

### First things first
Firstly lets import the packages that we need. As mentioned above we import numpy for efficiently dealing with arrays. We also import the random package which will be used to simulate the choices made by both the contestants and Monty.

```python
import numpy as np
import random
```

Since Monte Carlo simulation involves generating random numbers, we'll want to ensure the results are repeatable. To do this we'll set the random number seed generator to a fixed value, which means the random numbers chosen by ```np.random.randint``` will be the same each time we run the code.
```python
np.random.seed(seed=42)
```


### How many simulations?
Next we'll set up some basic parameters for the simulation. Firstly we'll set the number of simulations we want to run.
```python
nsims = 1000000 # Set the number of simulations
```

Next we'll want to simulate the three doors that the contestant can choose from. We'll use a type of python data structure called a *set* to do this. The contents of a set are immutable (cannot be changed) so using this data structure makes sense as there will always be three doors and we'll always want to label them as '1', '2' and '3'.
```python 
doors_set = {1, 2, 3} # The set of doors in the game
```
<br>

Now we can start to simulate the gameshow. Let's start by choosing the door behind which the prize lies for each of our simulations. We do this using numpy's ```np.random.randint``` function which randomly generates an integer (whole number) between two numbers. In this case we're telling ```np.random.int``` to choose an integer between 1 and 3. The ```nsims``` part tells python to do this for the number of simulations we set above. The numbers chosen will be saved into a numpy array called ```prize```   
```python
prize = np.random.randint(1, 4, nsims) # The door that wins the prize
```
The ```print``` functions let's us look at the output of this statement to see which is the winning door in the first 10 of our simulations. We see that in the first simulation the wining door is 3, in the second simulation  its 1, in the third and fourth simulations its 3, etc.
```python
print(prize[1:10])
>>> [3 1 3 3 1 1 3 2 3 3]
```

We can use the same approach to simulate the initial guess made by the contestant.
```python
guess = np.random.randint(1, 4, nsims) # The door chosen by the contestant
```
```python
print(guess[0:10])
>>>[3 1 3 1 2 1 1 3 1 1]
```

Since we know the winning door and the initial guess made by the contestant we can now test to see if the contestant guessed correctly

```python
guess_correct = np.array(guess == prize) # Did contestant guess correctly first time true/false?
```

Printing the result of ```guess correct``` shows how the contestant guesses correctly in the first three simulations.
```python
print(guess_correct[0:10])
[ True  True  True False False  True False False False False]
```
   
   
 ## Yet To be completed
   
 
``` python
# Create an array containing the door Monty opens after the initial guess

montys_door = np.zeros(nsims, dtype = np.int32) # Set up empty array

for i in range(0, nsims):
    # If contestant guesses the correct door then Monty can open either of the other two doors
    if guess_correct[i] == True:
        # Select random door not chosen by the contestant
        montys_door[i] = random.choice(tuple(doors_set.difference({guess[i]})))
    # Else the contestant has not guessed correctly, so Monty must open the other non-winning door
    else:
        # Select the other non-winning door not chosen by the contestant
        montys_door[i] = tuple(doors_set.difference({guess[i], prize[i]}))[0]


# Create an array indicating whether the contestant changes the door they 
# choose after Monty opens a non-prize winning door, 
# 0 = contestant sticks with original choice
# 1 = contestant chooses the other door
contestant_changes_mind = np.random.randint(0, 2, nsims)

contestant_final_guess = guess.copy()

for i in range(0,nsims):
    if contestant_changes_mind[i] == 1:
        contestant_final_guess[i] = tuple(doors_set.difference({guess[i], montys_door[i]}))[0]
```
