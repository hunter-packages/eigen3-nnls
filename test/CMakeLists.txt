SET(NNLS_TEST_SOURCES main.cc nnls.c)
SET(NNLS_TEST_HEADERS)

ADD_EXECUTABLE(nnls_test ${NNLS_TEST_SOURCES})
target_link_libraries(nnls_test eigen3-nnls)
