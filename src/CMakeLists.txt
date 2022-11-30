add_executable( delta
	delta.cpp
)

target_link_libraries( delta Boost_program_options_SHARED )
target_link_libraries( delta pthread )

set_target_properties( delta PROPERTIES RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR} )
