set(headers
    condat_fast_tv.h
    general.h
    johnsonRyanTV.h
    lapackFunctionsWrap.h
    LPopt.h
    TVmacros.h
    TVopt.h
    utils.h
    )

set(sources
    condat_fast_tv.cpp
    johnsonRyanTV.cpp
    lapackFunctionsWrap.cpp
    LPopt.cpp
    TV2Dopt.cpp
    TV2DWopt.cpp
    TVgenopt.cpp
    TVL1opt.cpp
    TVL1opt_hybridtautstring.cpp
    TVL1opt_kolmogorov.cpp
    TVL1opt_tautstring.cpp
    TVL1Wopt.cpp
    TVL2opt.cpp
    TVLPopt.cpp
    TVNDopt.cpp
    utils.cpp
    )

add_library(proxTV ${sources})
target_sources(
    proxTV
  PUBLIC
    FILE_SET HEADERS
    FILES
        ${headers}
)

set_target_properties(proxTV PROPERTIES WINDOWS_EXPORT_ALL_SYMBOLS 1)
target_compile_definitions(proxTV PUBLIC NOMATLAB)
if(proxTV_USE_LAPACK)
    target_compile_definitions(proxTV PUBLIC PROXTV_USE_LAPACK)
endif()

# Pthreads
target_link_libraries(proxTV PUBLIC
    ${CMAKE_THREAD_LIBS_INIT}
    )

# Lapacke, Lapack
if(proxTV_USE_LAPACK)
    target_include_directories(proxTV PUBLIC
    ${LAPACKE_INCLUDE_DIRS}
    )
    target_link_libraries(proxTV PUBLIC
        ${LAPACK_LIBRARIES}
        ${LAPACKE_LIBRARIES}
        )
else()
    target_link_libraries(proxTV PUBLIC ${proxTV_Eigen_LIBRARIES})
endif()

# OpenMP
if(OpenMP_CXX_FOUND AND NOT MSVC)
    # target_compile_definitions(proxTV PRIVATE _OPENMP)
    target_link_libraries(proxTV PRIVATE OpenMP::OpenMP_CXX)
endif()


