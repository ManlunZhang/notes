
# RL IIT

# Lec 50 - Hierarchical RL

Advantages:

 - temporal abstraction 
 - Transfer / Reusability
 - more powerful / Meaningful
 - state abstraction 
    - when I start breaking things down into these kinds of subproblems I can start focusing on things that are only needed for solving the subproblems.
    - solving one subproblem does not depend on other subproblems. 

# Lec 51 - Types of Optimality


![](https://raw.githubusercontent.com/mebusy/notes/master/imgs/iit_rl_51_types_of_optimality_room.png)


 - 1. hierarchically optimal
    - you have to search through policies that respect this hierarchy. 

The best way I can solve this problem of getting out of room 1 is the white policy. 

If I use the white policy in room1 and yellow policy in room2 , that is a valid solution. 

But overall among all policies that satisfy the hierarchy , that is not the optimal one in terms of overall cost.  The yellow policy in room 1 followed by the yellow policy in room2 gives the **hierarchically optimal**.  ( think about the dotted line has different distance to those 2 exits.  )

Following the white policy in room1 gives me a solution where the individual components in the hierarchy are optimal. But overall put together that is not the best possible solution. 

---

If I come up with a solution where the individual components solved optimally , and then the overal solution is suboptimally. Then I call that 

 - 2.  recursively optimal
    - given that all my sub problems are optimal , and the policies are frozen , what is the best can I do ?
    - I have to put together this optimal sub policies to find something that is best among those polices. So this is a more restricted policy.
 - hierarchically optimal is to say that I have to respect the hierarchy but I need not necessarily solve each component optimally , overall the problem should be optimally solved. 
 - recursively optimal is to say that not only should I respect the hierarchy but for each individual components in the sub hierarchy I should have an optimal solution. 

---

 - 3. Flat optimality 
    - does not respect any of the hierarchical restrictions 
    - it just looks at the most basic actions that are possible in this world , and tries to find what is optimal. 

---

 - so usually fat optimality gives you a more optimal solution than hierarchical optimality , which in turn gives you something better than recursive optimality
 - recursively optimal is great
    - it is more resuable. 
    - it might not be the best for any particular problem. but across different problems maybe this will be the better one on an average. 
    - so it is always a good idea to solve these things independently 
 - when do want to learn recursively optimal policies ?
    - I am solving the same problem multiple times , or my recursive optimal is really bad.  
    - it is possible that recursive optimal is bad. for instance, if I put a laser beam horizontally cross room2, so every time you touch the laser you get burned by the laser. Unfortunately still the white policies is recursively optimal
    
---

# Lec 52: Semi Markov Decision Process

## SMDP 

 - Actions have duration
    - MDP: s<sub>t</sub> --- a<sub>t</sub>--> s<sub>t+1</sub> ( with r<sub>t+1</sub> )
    - SMDP: s<sub>t</sub> --- a<sub>t</sub>--> ... s<sub>t+τ</sub> ( with r<sub>t+τ</sub> )    
        - --- a<sub>t+τ</sub>--> ... s<sub>t+τ+τ'</sub> ( with r<sub>t+τ+τ'</sub> )  
        - now I am not only interested in what the next state I'm going to land up in but also how long will it take me to get to the next state , because that is how much I will discount 
        - so optimality now would depend not only on where the action takes me but also how long it takes to get there. 
        - τ is the **holding time**. essentially the idea is that at time *t* I decide *a* is the action I'm goint to take, but I do not apply the action *a* xxx 没听清 
            - it could be a variety of reasons. I could actually be sensing the market at some point of time, and by the time I place my order at my xxxx 15 seconds later. 
            - that should be gap between the sensor input coming to you and you taking an action. 
        - you can understand as : I see s<sub>t</sub> , I pick a<sub>t</sub> immediately, but I wait for τ seconds before I apply it. So the decision is made before the waiting time. So the holding time is still a function of the decision. 

So it's SMDP in the sense that for the duration of the action I do not know how the system is evolving. So to determine when the next state is going to occur I really need to know how much time has elapsed since the last state was seen. Some amount of history dependence is there. So that is why it's called semi MDP. 

I know that τ seconds have elapsed before the next state happens, but how much of the τ seconds left ? 

The probability of s<sub>t+τ</sub> depends only on s<sub>t</sub>, but during this transition when s<sub>t+τ</sub> will actually show up it depends on how much time has elapsed. So it is only for this brief periods you have the time dependence. 

Ps. here , holding τ seconds then apply action ,  and taking action immediately how long does it take for the next state , both mean the same thing. 

--- 

## SMDP Definition

SMDP is defined as the tuple <S,A,P,R>

 - P: P(s',τ | s, a) 
 - R: E[ v | s,a,s',τ ]
    - reward could depend on the transition time , or the holding time.

---

## SMDP Q-Learning

 - Q(s<sub>t</sub>,a<sub>t</sub>) = Q(s<sub>t</sub>,a<sub>t</sub>) + α·[ r̄<sub>t+τ</sub> + γ<sup>τ</sup>·max<sub>a'</sub>Q(s'<sub>t</sub>,a') - Q(s<sub>t</sub>,a<sub>t</sub>) ]

The tricky thing here is I have hidden some stuff into this **r̄<sub>t+τ</sub>** , What is r̄<sub>t+τ</sub>  ?  It's the expected reward that I am going to get over the τ time steps. It is not the one instancely happened.  It could be variety of things. Normally that in the classical SMDP framework wo do not tell you how this r̄<sub>t+τ</sub> comes up. So you have some mechanism by which you can generate it. 

But we are more interested in is to actually think of 

 - r̄<sub>t+τ</sub> = r<sub>t+1</sub> + γ·r<sub>t+2</sub> + ... + γ<sup>τ-1</sup> ·r<sub>t+τ</sub> 

So notice that SMDP Q-Learning does not actually look into the structure Q(s<sub>t</sub>,a<sub>t</sub>) of a<sub>t</sub>  . It just assumes that this reward comes then I take action a<sub>t</sub>.  So it assumes that a<sub>t</sub> has been learnt already. So what is a<sub>t</sub> here in our room example ?  Goto room2 !  It is not a single simple action, this is what you mean by the `temporal abstraction`.


--- 

# Lec 53 :  Options

In some sense it is the simplest of the hierarchical frameworks.  The basic idea behind options is something very very simple. So I am going to take the solutions to these sub problems that we are defining , I will encapsulate them as a single action.  

## macro action

So the idea comes from the planning literature actually where people talk about something called **macro actions**. So in the planning community macro actions are essentially sequence of actions put together and then we talk about as a single action. In the RL space macro action is not a sequence of actions but **encapsulated policies**. 

How to specify a useful macro action (option) ?

 1. Initiation , Iₒ ⊆ S' 
    - a subset of the state space
    - I need something that tells me where the **iniitation** can happen 
 2. Policy , πₒ
    - I need a *policy* to use while the macro action is executing 
 3. Termination , βₒ: S'→ [0,1]
    - and then I need a termination. 
    - typically specified by β: S'→ [0,1]
        - β of s will be the probability that option will stop in state s.  
        - so fo many states the probability could be 1. That means on all those states the option will not continue. 
    - the probabilistic definition of options was actually introduced to for more of a mathematical convenience. In practice when you define this options you typically make the probability of termination 0 or 1. So 0 means it does not terminate, 1 means it terminates. 

So Option o=< Iₒ,πₒ,βₒ >


## Two kinds of options 

 - Markov option
    - πₒ depends only on current state
    - the nice thing about Markov options is I do not have to worry about where I started the option . So if I am in a particular state and I am executing that option then I will take the same action. 
 
 - Semi Markov option
    - πₒ depends on histroy since the option started











