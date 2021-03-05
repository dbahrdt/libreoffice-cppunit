# This file is a merge of the ultimaker CMakeLists and 
# the patch from https://sourceforge.net/p/cppunit/patches/64/
# With some minor changes

cmake_minimum_required(VERSION 3.11)
project(cppunit)
set(CPPUNIT_PACKAGE_NAME cppunit CACHE STRING "Name of the package to build.")
set(CPPUNIT_MAJOR_VERSION 1 CACHE STRING "Major (first) version number.")
set(CPPUNIT_MINOR_VERSION 14 CACHE STRING "Minor (second) version number.")
set(CPPUNIT_PATCH_VERSION 2 CACHE STRING "Patch (third) version number.")
set(CPPUNIT_VERSION "${CPPUNIT_MAJOR_VERSION}.${CPPUNIT_MINOR_VERSION}.${CPPUNIT_PATCH_VERSION}")

# CppUnit version number
set(CPPUNIT_MAJOR_VERSION 1)
set(CPPUNIT_MINOR_VERSION 13)
set(CPPUNIT_MICRO_VERSION 2)
set(CPPUNIT_VERSION ${CPPUNIT_MAJOR_VERSION}.${CPPUNIT_MINOR_VERSION}.${CPPUNIT_MICRO_VERSION})
##

# Generates include/cppunit/config-auto.h
# This is originally done by autoconf

include(CheckIncludeFile)
include(CheckIncludeFileCXX)
include(CheckCXXSourceCompiles)
include(CheckCSourceCompiles)
include(CheckLibraryExists)
include(CheckFunctionExists)

#Not used == not seen in any *.h *.cpp files
#Not used FUNC_STRING_COMPARE_STRING_FIRST

check_include_file_cxx(sstream CPPUNIT_HAVE_SSTREAM)
check_include_file_cxx(strstream CPPUNIT_HAVE_STRSTREAM)
set (CMAKE_REQUIRED_DEFINITIONS -DHAVE_STRSTREAM=CPPUNIT_HAVE_STRSTREAM)



check_cxx_source_compiles(
"#ifdef HAVE_STRSTREAM
#	include <strstream>
#else
#	include <strstream.h>
#endif
int main() {
	std::ostrstream message;
	message << \"Hello\";
	return 0;
}" CPPUNIT_HAVE_CLASS_STRSTREAM)

check_include_file_cxx(cmath CPPUNIT_HAVE_CMATH)
#Not used, dld library is obsolete anyway HAVE_DLD
#Not used HAVE_DLERROR
check_include_file(dlfcn.h CPPUNIT_HAVE_DLFCN_H)

check_c_source_compiles(
"#include <math.h>
int main() {
	return finite(3);
}" CPPUNIT_HAVE_FINITE)

check_c_source_compiles(
"#include <math.h>
int main() {
	return _finite(3);
}" CPPUNIT_HAVE__FINITE)

check_include_file_cxx(cxxabi.h CPPUNIT_HAVE_GCC_ABI_DEMANGLE)
#Not used HAVE_INTTYPES_H

check_c_source_compiles(
"#include <math.h>
int main() {
	return isfinite(3);
}" CPPUNIT_HAVE_ISFINITE)

check_library_exists(dl dlopen "" CPPUNIT_HAVE_LIBDL)
#Not used HAVE_MEMORY_H

check_cxx_source_compiles(
"namespace Outer {
	namespace Inner {
		int i = 0;
	}
}
using namespace Outer::Inner;
int main() {
	return i;
}" CPPUNIT_HAVE_NAMESPACES)

check_cxx_source_compiles(
"#include <typeinfo>
class Base {
public:
	Base() {}
	virtual int f() { return 0; }
};
class Derived : public Base {
public:
	Derived() {}
	virtual int f() { return 1; }
};
int main() {
	Derived d;
	Base * ptr = &d;
	return typeid(*ptr) == typeid(Derived);
}" CPPUNIT_HAVE_RTTI)

check_library_exists(dl shl_load "" CPPUNIT_HAVE_SHL_LOAD)
#Not used HAVE_STDINT_H
#Not used HAVE_STDLIB_H
#Not used HAVE_STRINGS_H
#Not used HAVE_STRING_H
#Not used HAVE_SYS_STAT_H
#Not used HAVE_SYS_TYPES_H
#Not used HAVE_UNISTD_H
#Not used PACKAGE
#Not used PACKAGE_BUGREPORT
#Not used PACKAGE_NAME
#Not used PACKAGE_STRING
#Not used PACKAGE_TARNAME
#Not used PACKAGE_VERSION
#Not used STDC_HEADERS
check_include_file_cxx(typeinfo CPPUNIT_USE_TYPEINFO_NAME)
#CPPUNIT_VERSION ok

configure_file(config.h.cmake "${CMAKE_CURRENT_BINARY_DIR}/cppunit/config-auto.h")
#This is the one from ultimake which lacks a lot of configuration options
#configure_file("config-auto.h.in" "cppunit/config-auto.h")

set(Sources
    src/cppunit/AdditionalMessage.cpp
    src/cppunit/Asserter.cpp
    src/cppunit/BriefTestProgressListener.cpp
    src/cppunit/CompilerOutputter.cpp
    src/cppunit/DefaultProtector.cpp
    #src/cppunit/DllMain.cpp #Apparently this one isn't built.
    src/cppunit/DynamicLibraryManager.cpp
    src/cppunit/DynamicLibraryManagerException.cpp
    src/cppunit/Exception.cpp
    src/cppunit/Message.cpp
    src/cppunit/PlugInManager.cpp
    src/cppunit/PlugInParameters.cpp
    src/cppunit/Protector.cpp
    src/cppunit/ProtectorChain.cpp
    src/cppunit/RepeatedTest.cpp
    src/cppunit/ShlDynamicLibraryManager.cpp
    src/cppunit/SourceLine.cpp
    src/cppunit/StringTools.cpp
    src/cppunit/SynchronizedObject.cpp
    src/cppunit/Test.cpp
    src/cppunit/TestAssert.cpp
    src/cppunit/TestCase.cpp
    src/cppunit/TestCaseDecorator.cpp
    src/cppunit/TestComposite.cpp
    src/cppunit/TestDecorator.cpp
    src/cppunit/TestFactoryRegistry.cpp
    src/cppunit/TestFailure.cpp
    src/cppunit/TestLeaf.cpp
    src/cppunit/TestNamer.cpp
    src/cppunit/TestPath.cpp
    src/cppunit/TestPlugInDefaultImpl.cpp
    src/cppunit/TestResult.cpp
    src/cppunit/TestResultCollector.cpp
    src/cppunit/TestRunner.cpp
    src/cppunit/TestSetUp.cpp
    src/cppunit/TestSuccessListener.cpp
    src/cppunit/TestSuite.cpp
    src/cppunit/TestSuiteBuilderContext.cpp
    src/cppunit/TextOutputter.cpp
    src/cppunit/TextTestProgressListener.cpp
    src/cppunit/TextTestResult.cpp
    src/cppunit/TextTestRunner.cpp
    src/cppunit/TypeInfoHelper.cpp
    src/cppunit/UnixDynamicLibraryManager.cpp
    src/cppunit/Win32DynamicLibraryManager.cpp
    src/cppunit/XmlDocument.cpp
    src/cppunit/XmlElement.cpp
    src/cppunit/XmlOutputter.cpp
    src/cppunit/XmlOutputterHook.cpp
)
set(HeaderDirs
    ${CMAKE_CURRENT_SOURCE_DIR}/src/cppunit
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

add_library(${PROJECT_NAME} STATIC ${Sources})
target_include_directories(${PROJECT_NAME} PUBLIC ${CMAKE_CURRENT_BINARY_DIR} ${HeaderDirs})
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_11)
set_target_properties(${PROJECT_NAME} PROPERTIES
	VERSION ${CPPUNIT_VERSION}
	SOVERSION ${CPPUNIT_MAJOR_VERSION}
)
if (UNIX)
    #needs dl library so that linking to cppunit does not fail
    target_link_libraries(${PROJECT_NAME} dl)
endif()
