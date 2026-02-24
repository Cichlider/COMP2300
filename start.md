Comment Your Code!
I can’t stress this enough, please comment your code. ARM comments can be styled using the following:

@ for a single line comment
// also for a single line comment
/* for
   a
   multi-line
   comment */
And while you can comment on every single line like so:

get_order_cost:
  str lr, [sp, -4]! @ Store the link register on the stack
  ldr r1, [r0]      @ Load the customer id
  ldr r2, [r0, 4]   @ Load the items in the order
  str r1, [sp, -4]! @ Store the customer id on the stack 
  str r2, [sp, -4]! @ Store the # of items in the order on the stack 
  @ ...
it’s not always the most helpful, or the best use of your time. Instead, you would be better suited to commenting your functions and segments of your code, and resorting to line by line comments only when what you are doing is particularly tricky or unintuitive.

Here is an example for a function preamble, you don’t have to follow this exact format, however the main idea of covering the following is something you should do when writing your code:

What the function does
What arguments it takes and where they are
What does it return and where is that
Are the flags set (if relevant)
@ Reads the led current output state based on a given row 
@ and column index. 
@ --parameters--
@ r0: row index (0-4 with 0 being the top row)
@ r1: column index (0-4 with 0 being the left most column)
@ --return--
@ r0: bit<0> contains led state (1 == On, 0 == Off)
@ flags set
read_led:
@ ...
Functionize Your Code!
Assembly programs can get very long, very quickly. This can lead to errors, bugs and just plain confusion. Something that you should always try and do is break your code down into logical blocks, or functions, that have a specific purpose.
This will make your life a lot lot lot easier when it comes to debugging your program, because you’ll be able to set breakpoints, use the debugger functions like ‘step over’ etc. and will be able to verify pieces of your code, without having to try and hold the whole program logic in your mind at once.

Follow Calling Convention!
If you’re writing a function, actually make it a function. This means following calling convention. It’s super important and has its own page.

Maintain Consistent Indentation!
I can’t tell you the amount of times that I have opened up a students code and seen something like this:

main:
nop
  adds r0, 1
  sub r1, 1
       mov r0, 2
       mov r1 , 3
mul r1, r1
@ ...
It just makes your code hard to read for no reason, instead try and maintain things on the same level, or indent blocks where it makes sense, like in a conditional or loop section

main:
  adds r0, 1
  sub r1, 1
  mov r0, 2
  mov r1, 3
  mul r1, r1
  @ ...

calc:
  mov r0, 0
  mov r1, 10
  calc_loop:
    add r0, 1
    @ ...
    cmp r0, r1
    blt calc_loop
  mov r0, r3
  bx lr
Keep Your Code Structure Logical
So what does this mean exactly? It is basically a combination of other rules and a general guideline for writing your programs. This relates to:

Functions
Calling Convention
etc.
What it is saying is that your code should should make logical sense:

it should be clear that your functions are following the calling convention
your code, and any functions called, should be being executed in the right locations, the right amount of times
For point 1, you can help this out by follwing a few rules:

Store necessary registers once at the start of a function and restore those same registers at the end of the function
If you do need to have a push / pop in the middle of a function, keep the push and pop on the same conditional area
This means that if you have a push, don’t put the corresponding pop after a conditional branch as it makes it a lot easier to lose track of what you’re doing with the stack
Always exit the function from the same location, even if you could have returned earlier, instead opt for a branch to an end_function label which performs the same clean up
Here is an example of a function / handler following the above rules

.global ExampleFnOrHandler
.type ExampleFnOrHandler, %function
ExampleFnOrHandler:
  push {r4 - r6, lr} @ Store necessary registers once at the start

  mov r5, 10
  subs r3, r5
  beq end_example

  @ don't have a push and a pop in different conditional areas, 
  @ make sure that after a push you will always execute the 
  @ corresponding pop
  push {r0} 
  mov r4, 1
  cmp r0, r4
  beq if_condition
  b else_condition

  if_condition:
    @ if code here
    mov r0, 1
    bl something
    b end_if
  else_condition:
    @ else code here
    mov r0, 2
    bl something_else
    b end_if
  end_if:
  pop {r0}

@ always have functions exit from the same spot, even if you could 
@ have exited earlier, this helps keep the stack consistent
end_example:
  pop {r4 - r6, lr} @ Restore those same registers at the end
  bx lr
.size ExampleFnOrHandler, .-ExampleFnOrHandler
If I were to take the exact same example, and instead do this:

.global ExampleFnOrHandler
.type ExampleFnOrHandler, %function
ExampleFnOrHandler:
  @ don't store necessary registers at the start

  push {r5}
  mov r5, 10
  subs r3, r5
  bne continue
  pop {r5}
  bx lr

continue:
  push {r4}
  mov r4, 1
  cmp r0, r4
  beq if_condition
  b else_condition
   
  if_condition:
    push {r0, lr}
    @ if code here
    mov r0, 1
    bl something
    b end_if
  else_condition:
    push {r0, lr}
    @ else code here
    mov r0, 2
    bl something_else
    b end_if
  end_if:
  pop {r0, lr}

end_example:
  pop {r5} 
  bx lr
.size ExampleFnOrHandler, .-ExampleFnOrHandler
Notice how its now a lot harder to track the fact that I am keeping the stack correct and saving registers as necessary? By following the previous example you will save yourself a load of headaches and make debugging your code a lot easier.