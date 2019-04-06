Timestamp is a small utility library useful to
introduce compile-time settings into your CMake-built
program.

As the name suggest, this was originally used to get
a custom format of the compile time recorded as a string
into one of my programs (the standard C format is not
that nice looking, IMHO).


Usage:
-----
Copy or clone the entire timestamp directory as a 
subdirectory of your sources.

Then add to your CMakeLists.txt

	add_subdirectory(timestamp)

and link your program against the library:

	target_link_libraries( your_program timestamp)


The timestamp library's CMakeLists.txt will force 
a partial re-run of CMake every time you run your target
generator (e.g. 'make'), so that the recorded timestamp
(and other variables you might want to add) always reflect
the status at link time of your library.
