cmake_minimum_required(VERSION 2.8)

include(CryptoNoteWallet.cmake)

project(${CN_PROJECT_NAME})

execute_process(COMMAND git log -1 --pretty=format:%h
WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
OUTPUT_VARIABLE GIT_REVISION)

set(PEBEX_LIB pebex)
set(BOOST_INCLUDEDIR E:/Project/PEBEX/boost_1_55_0)

include_directories(${CMAKE_BINARY_DIR}
    src
    pebex/external
    pebex/include
    pebex/src)

find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5OpenGL REQUIRED)
find_package(OpenGL REQUIRED)

set(STATIC ON)
set(Boost_USE_STATIC_LIBS ON)
set(CMAKE_EXE_LINKER_FLAGS "-static -release")
set(CMAKE_BUILD_TYPE Release)
foreach(flag_var
        CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
        CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
    STRING (REGEX REPLACE "/RTC[^ ]*" "" ${flag_var} "${${flag_var}}")
endforeach(flag_var)

add_definitions( -DBOOST_ALL_NO_LIB )

if(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
else(WIN32)
  set(Boost_USE_STATIC_RUNTIME ON)
endif(WIN32)

find_package(Boost 1.55 REQUIRED COMPONENTS date_time filesystem program_options regex serialization system thread chrono)
if ((${Boost_MAJOR_VERSION} EQUAL 1) AND (${Boost_MINOR_VERSION} EQUAL 54))
	message(SEND_ERROR "Boost version 1.54 is unsupported, more details are available here http://goo.gl/RrCFmA")
endif ()

include_directories(${Boost_INCLUDE_DIRS})
link_directories(${Boost_LIBRARY_DIRS})

set(VERSION "")
configure_file("pebex/src/version.h.in" "version.h")
configure_file("src/CryptoNoteWalletConfig.h.in" "CryptoNoteWalletConfig.h")

add_definitions(-DGIT_REVISION=\"${GIT_REVISION}\")

set(CMAKE_AUTOMOC ON)

set(STATIC ${MSVC} CACHE BOOL "Link libraries statically")

if(MSVC)
  add_definitions("/bigobj /MP /W3 /GS- /D_CRT_SECURE_NO_WARNINGS /wd4996 /wd4345 /D_WIN32_WINNT=0x0600 /DWIN32_LEAN_AND_MEAN /DGTEST_HAS_TR1_TUPLE=0 /D_VARIADIC_MAX=8 /D__SSE4_1__")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /STACK:10485760")
  if(STATIC)
    foreach(VAR CMAKE_C_FLAGS_DEBUG CMAKE_CXX_FLAGS_DEBUG CMAKE_C_FLAGS_RELWITHDEBINFO CMAKE_CXX_FLAGS_RELWITHDEBINFO CMAKE_C_FLAGS_RELEASE CMAKE_CXX_FLAGS_RELEASE)
      string(REPLACE "/MD" "/MT" ${VAR} "${${VAR}}")
    endforeach()

    #foreach(VAR CMAKE_C_FLAGS_DEBUG CMAKE_CXX_FLAGS_DEBUG CMAKE_C_FLAGS_RELWITHDEBINFO CMAKE_CXX_FLAGS_RELWITHDEBINFO CMAKE_C_FLAGS_RELEASE CMAKE_CXX_FLAGS_RELEASE)
      #string(REPLACE "/MTd" "/MT" ${VAR} "${${VAR}}")
    #endforeach()
  endif()
endif()

set(PEBEX_SOURCES
  pebex/external/miniupnpc/connecthostport.c
  pebex/external/miniupnpc/igd_desc_parse.c
  pebex/external/miniupnpc/minisoap.c
  pebex/external/miniupnpc/miniupnpc.c
  pebex/external/miniupnpc/miniwget.c
  pebex/external/miniupnpc/minixml.c
  pebex/external/miniupnpc/portlistingparse.c
  pebex/external/miniupnpc/receivedata.c
  pebex/external/miniupnpc/upnpcommands.c
  pebex/external/miniupnpc/upnpreplyparse.c
  pebex/src/Common/Base58.cpp
  pebex/src/Common/CommandLine.cpp
  pebex/src/Common/Util.cpp
  pebex/src/Common/StringTools.cpp
  pebex/src/Common/JsonValue.cpp
  pebex/src/Common/ConsoleTools.cpp
  pebex/src/Common/MemoryInputStream.cpp
  pebex/src/Common/PathTools.cpp
  pebex/src/Common/StdInputStream.cpp
  pebex/src/Common/StdOutputStream.cpp
  pebex/src/Common/StreamTools.cpp
  pebex/src/Common/StringOutputStream.cpp
  pebex/src/Common/StringView.cpp
  pebex/src/Common/VectorOutputStream.cpp
  pebex/src/crypto/blake256.c
  pebex/src/crypto/chacha8.c
  pebex/src/crypto/crypto-ops-data.c
  pebex/src/crypto/crypto-ops.c
  pebex/src/crypto/crypto.cpp
  pebex/src/crypto/groestl.c
  pebex/src/crypto/hash-extra-blake.c
  pebex/src/crypto/hash-extra-groestl.c
  pebex/src/crypto/hash-extra-jh.c
  pebex/src/crypto/hash-extra-skein.c
  pebex/src/crypto/hash.c
  pebex/src/crypto/jh.c
  pebex/src/crypto/keccak.c
  pebex/src/crypto/oaes_lib.c
  pebex/src/crypto/random.c
  pebex/src/crypto/skein.c
  pebex/src/crypto/slow-hash.c
  pebex/src/crypto/slow-hash.cpp
  pebex/src/crypto/tree-hash.c
  pebex/src/CryptoNoteCore/BlockchainIndices.cpp
  pebex/src/CryptoNoteCore/BlockchainMessages.cpp
  pebex/src/CryptoNoteCore/BlockIndex.cpp
  pebex/src/CryptoNoteCore/CoreConfig.cpp
  pebex/src/CryptoNoteCore/CryptoNoteBasic.cpp
  pebex/src/CryptoNoteCore/CryptoNoteTools.cpp
  pebex/src/CryptoNoteCore/Currency.cpp
  pebex/src/CryptoNoteCore/MinerConfig.cpp
  pebex/src/CryptoNoteCore/Transaction.cpp
  pebex/src/CryptoNoteCore/Account.cpp
  pebex/src/CryptoNoteCore/Blockchain.cpp
  pebex/src/CryptoNoteCore/Checkpoints.cpp
  pebex/src/CryptoNoteCore/CryptoNoteBasicImpl.cpp
  pebex/src/CryptoNoteCore/Core.cpp
  pebex/src/CryptoNoteCore/CryptoNoteFormatUtils.cpp
  pebex/src/CryptoNoteCore/CryptoNoteSerialization.cpp
  pebex/src/CryptoNoteCore/Difficulty.cpp
  pebex/src/CryptoNoteCore/IBlock.cpp
  pebex/src/CryptoNoteCore/Miner.cpp
  pebex/src/CryptoNoteCore/TransactionExtra.cpp
  pebex/src/CryptoNoteCore/TransactionPool.cpp
  pebex/src/CryptoNoteCore/TransactionPrefixImpl.cpp
  pebex/src/CryptoNoteCore/TransactionUtils.cpp
  pebex/src/InProcessNode/InProcessNode.cpp
  pebex/src/InProcessNode/InProcessNodeErrors.cpp
  pebex/src/NodeRpcProxy/NodeErrors.cpp
  pebex/src/NodeRpcProxy/NodeRpcProxy.cpp
  pebex/src/P2p/NetNodeConfig.cpp
  pebex/src/Serialization/BinaryInputStreamSerializer.cpp
  pebex/src/Serialization/BinaryOutputStreamSerializer.cpp
  pebex/src/Serialization/JsonInputValueSerializer.cpp
  pebex/src/Serialization/JsonOutputStreamSerializer.cpp
  pebex/src/Serialization/KVBinaryInputStreamSerializer.cpp
  pebex/src/Serialization/KVBinaryOutputStreamSerializer.cpp
  pebex/src/Serialization/SerializationOverloads.cpp
  pebex/src/Transfers/BlockchainSynchronizer.cpp
  pebex/src/Transfers/SynchronizationState.cpp
  pebex/src/Transfers/TransfersConsumer.cpp
  pebex/src/Transfers/TransfersContainer.cpp
  pebex/src/Transfers/TransfersSubscription.cpp
  pebex/src/Transfers/TransfersSynchronizer.cpp
  pebex/src/Wallet/LegacyKeysImporter.cpp
  pebex/src/Wallet/WalletAsyncContextCounter.cpp
  pebex/src/Wallet/WalletErrors.cpp
  pebex/src/WalletLegacy/KeysStorage.cpp
  pebex/src/WalletLegacy/WalletLegacy.cpp
  pebex/src/WalletLegacy/WalletHelper.cpp
  pebex/src/WalletLegacy/WalletLegacySerializer.cpp
  pebex/src/WalletLegacy/WalletLegacySerialization.cpp
  pebex/src/WalletLegacy/WalletTransactionSender.cpp
  pebex/src/WalletLegacy/WalletUnconfirmedTransactions.cpp
  pebex/src/WalletLegacy/WalletUserTransactionsCache.cpp
  pebex/src/System/ContextGroup.cpp
  pebex/src/System/Event.cpp
  pebex/src/System/EventLock.cpp
  pebex/src/System/InterruptedException.cpp
  pebex/src/System/Ipv4Address.cpp
  pebex/src/System/TcpStream.cpp
  pebex/src/HTTP/HttpRequest.cpp
  pebex/src/HTTP/HttpParser.cpp
  pebex/src/HTTP/HttpParserErrorCodes.cpp
  pebex/src/HTTP/HttpResponse.cpp
  pebex/src/Rpc/HttpClient.cpp
  pebex/src/Rpc/JsonRpc.cpp
  pebex/src/P2p/NetNode.cpp
  pebex/src/P2p/LevinProtocol.cpp
  pebex/src/P2p/NetNodeConfig.cpp
  pebex/src/P2p/PeerListManager.cpp
  pebex/src/CryptoNoteProtocol/CryptoNoteProtocolHandler.cpp
  pebex/src/Logging/ILogger.cpp
  pebex/src/Logging/LoggerGroup.cpp
  pebex/src/Logging/CommonLogger.cpp
  pebex/src/Logging/LoggerManager.cpp
  pebex/src/Logging/FileLogger.cpp
  pebex/src/Logging/StreamLogger.cpp
  pebex/src/Logging/ConsoleLogger.cpp
  pebex/src/Logging/LoggerMessage.cpp
  pebex/src/Logging/LoggerRef.cpp
  pebex/src/BlockchainExplorer/BlockchainExplorer.cpp
  pebex/src/BlockchainExplorer/BlockchainExplorerDataBuilder.cpp
  pebex/src/BlockchainExplorer/BlockchainExplorerErrors.cpp
)

file(GLOB_RECURSE SOURCES src/*.cpp)
file(GLOB_RECURSE HEADERS src/*.h)
file(GLOB_RECURSE FORMS src/gui/ui/*.ui)

set(QRC src/resources.qrc)

qt5_wrap_ui(UIS ${FORMS})
qt5_add_resources(RCC ${QRC})


if (WIN32)
  if (NOT MSVC)
	message(FATAL_ERROR "Only MSVC is supported on this platform")
  endif ()
  add_definitions(/D_CRT_SECURE_NO_WARNINGS /D_WIN32_WINNT=0x0600 /DSTATICLIB)
  include_directories(pebex/src/platform/msc)

  set(PLATFORM_DIR Windows)
  set(BUILD_PLATFORM WIN32)
  set(BUILD_RESOURCES src/cryptonotewallet.rc)
  set(QTMAIN Qt5::WinMain)

elseif (UNIX)
  set(PEBEX_SOURCES ${PEBEX_SOURCES} pebex/external/miniupnpc/minissdpc.c)
  if (APPLE)
    enable_language(ASM)
    file(GLOB_RECURSE OBJC_SOURCES src/*.mm)
    set(SOURCES ${SOURCES} ${OBJC_SOURCES})
    set(PLATFORM_DIR OSX)
    set(MACOSX_BUNDLE_INFO_STRING "PEBEX GUI wallet")
    set(MACOSX_BUNDLE_LONG_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_NAME PebexWallet)
    set(MACOSX_BUNDLE_SHORT_VERSION_STRING "${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")
    set(MACOSX_BUNDLE_BUNDLE_VERSION "$${VERSION_VERSION}.${VERSION_MINOR}.${VERSION_PATCH}")

    find_package(Qt5PrintSupport REQUIRED)

    include_directories(/usr/include/malloc)

    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11 -stdlib=libc++")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=c11 -maes -D_DARWIN_C_SOURCE")
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -framework Cocoa -framework OpenGL -framework CoreFoundation -framework Carbon -framework IOKit -L/usr/lib")

    set(MACOSX_BUNDLE_ICON_FILE cryptonote.icns)
    set(APPLICATION_ICON src/images/cryptonote.icns)
    set_source_files_properties(${APPLICATION_ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set(BUILD_PLATFORM MACOSX_BUNDLE)
    set(BUILD_RESOURCES ${APPLICATION_ICON})
	
    GET_TARGET_PROPERTY(QT_LIB_DIR "${Qt5Widgets_LIBRARIES}" LOCATION)
    GET_FILENAME_COMPONENT(QT_LIB_DIR "${QT_LIB_DIR}" PATH)
  else(APPLE)
    set(PLATFORM_DIR Linux)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes -std=c++11")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes -std=c11")
  endif (APPLE)
endif ()

include_directories(pebex/src/Platform/${PLATFORM_DIR})
file(GLOB PLATFORM_SOURCES pebex/src/Platform/${PLATFORM_DIR}/System/*)
set(PEBEX_SOURCES ${PEBEX_SOURCES} ${PLATFORM_SOURCES})

add_library(${PEBEX_LIB} STATIC ${PEBEX_SOURCES})
set_target_properties(${PEBEX_LIB} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PEBEX_LIB} ${Boost_LIBRARIES})

add_executable(${PROJECT_NAME} ${BUILD_PLATFORM} ${BUILD_RESOURCES} ${SOURCES} ${HEADERS} ${UIS} ${RCC})
set_target_properties(${PROJECT_NAME} PROPERTIES COMPILE_DEFINITIONS _GNU_SOURCE)
target_link_libraries(${PROJECT_NAME} ${Boost_LIBRARIES} ${QTMAIN} ${PEBEX_LIB})

if (APPLE)
  qt5_use_modules(${PROJECT_NAME} PrintSupport)
elseif (UNIX)
  target_link_libraries(${PROJECT_NAME} -lpthread)
elseif (WIN32)
  target_link_libraries(${PROJECT_NAME} Imm32 Iphlpapi Winmm)
endif (APPLE)

qt5_use_modules(${PROJECT_NAME} Widgets Gui)

set(QT_LIBS
  "libGLESv2\;libGLESv2d"
  "libEGL\;libEGLd"
  "poly2tri\;poly2trid"
  "preprocessor\;preprocessord"
  "qtfreetype\;qtfreetyped"
  "qtharfbuzzng\;qtharfbuzzngd"
  "qtpcre\;qtpcred"
  "translator\;translatord"
  "Qt5PlatformSupport\;Qt5PlatformSupportd"
)

set(D3D_LIBS
  "d3d9"
  "dxguid"
)


foreach(LIBPAIR ${QT_LIBS})
  foreach(LIB ${LIBPAIR})
    add_library(${LIB} STATIC IMPORTED)
    set_target_properties(${LIB} PROPERTIES IMPORTED_LOCATION C:/Qt/5.6.3/qtbase/lib/${LIB}.lib)
  endforeach()
  list (GET LIBPAIR 0 RELEASE)
  list (GET LIBPAIR 1 DEBUG)
  target_link_libraries(${PROJECT_NAME} PUBLIC optimized ${RELEASE} debug ${DEBUG})
endforeach()

add_library(qwindows STATIC IMPORTED)
set_target_properties(qwindows PROPERTIES IMPORTED_LOCATION C:/Qt/5.6.3/qtbase/plugins/platforms/qwindows.lib)
add_library(qwindowsd STATIC IMPORTED)
set_target_properties("qwindowsd" PROPERTIES IMPORTED_LOCATION C:/Qt/5.6.3/qtbase/plugins/platforms/qwindowsd.lib)
target_link_libraries(${PROJECT_NAME} PUBLIC optimized qwindows debug qwindowsd)

foreach(LIB ${D3D_LIBS})
  add_library(${LIB} STATIC IMPORTED)
  set_target_properties(${LIB} PROPERTIES IMPORTED_LOCATION "C:/Program Files (x86)/Microsoft DirectX SDK (June 2010)/Lib/x64/${LIB}.lib")
  target_link_libraries(${PROJECT_NAME} ${LIB})
endforeach()