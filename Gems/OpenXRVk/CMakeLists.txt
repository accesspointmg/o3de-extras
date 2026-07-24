#
# Copyright (c) Contributors to the Open 3D Engine Project.
# For complete copyright and license terms please see the LICENSE at the root of this distribution.
#
# SPDX-License-Identifier: Apache-2.0 OR MIT
#
#

set(gem_path ${CMAKE_CURRENT_LIST_DIR})
set(gem_json ${gem_path}/gem.json)

set(pal_3rdparty_dir ${CMAKE_CURRENT_LIST_DIR}/3rdParty/Platform/${PAL_PLATFORM_NAME})

include(${pal_3rdparty_dir}/BuiltInPackages_${PAL_PLATFORM_NAME_LOWERCASE}.cmake)

add_subdirectory(Code)
