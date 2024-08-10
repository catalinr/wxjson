# CMake makefile for wxJSON samples.

add_executable(wxjson_test)

target_sources(wxjson_test PRIVATE
    main.cpp
    test1.cpp
    test3.cpp
    test4.cpp
    test5.cpp
    test6.cpp
    test7.cpp
    test8.cpp
    test9.cpp
    test10.cpp
    test11.cpp
    test12.cpp
    test13.cpp
    test14.cpp
    test15.cpp
)

target_link_libraries(wxjson_test PRIVATE wxJSON::wxJSON ${wxWidgets_LIBRARIES})
