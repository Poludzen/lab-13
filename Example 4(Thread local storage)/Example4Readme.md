Thread Local Storage
Sometimes you need to create a variable, like a global one, but which only one thread sees. Other threads also see the variable, but for them it has its own local value. To do this, they came up with Thread Local Storage, or TLS. Among other things, TLS can be used to significantly speed up the generation of pseudorandom numbers
