#   BSD LICENSE
#
#   Copyright(c) 2010-2014 Intel Corporation. All rights reserved.
#   Copyright(c) 2014 6WIND S.A.
#   All rights reserved.
#
#   Redistribution and use in source and binary forms, with or without
#   modification, are permitted provided that the following conditions
#   are met:
#
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in
#       the documentation and/or other materials provided with the
#       distribution.
#     * Neither the name of Intel Corporation nor the names of its
#       contributors may be used to endorse or promote products derived
#       from this software without specific prior written permission.
#
#   THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
#   "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
#   LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
#   A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
#   OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
#   SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
#   LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
#   DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
#   THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
#   (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
#   OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

include $(RTE_SDK)/mk/rte.vars.mk

ifeq ($(NAPATECH3_PATH),)
$(error "Please define NAPATECH3_PATH environment variable")
endif

#
# library name
#
LIB = librte_pmd_ntacc.a

CFLAGS += -O3 -g
CFLAGS += $(WERROR_FLAGS)

# external library include paths
CFLAGS += -I$(NAPATECH3_PATH)/include
CFLAGS += -DNAPATECH3_LIB_PATH=\"$(NAPATECH3_PATH)/lib\"

ifeq ($(CONFIG_RTE_LIBRTE_PMD_NTACC_USE_SW_STAT),y)
CFLAGS += -DUSE_SW_STAT
endif

ifneq ($(CONFIG_RTE_LIBRTE_PMD_NTACC_COPY_OFFSET),n)
CFLAGS += -DCOPY_OFFSET0=$(CONFIG_RTE_LIBRTE_PMD_NTACC_OFFSET0)
endif

ifneq ($(CONFIG_RTE_LIBRTE_PMD_NTACC_USE_EXTERNAL_BUFFER),n)
CFLAGS += -DUSE_EXTERNAL_BUFFER
CFLAGS += -DALLOW_EXPERIMENTAL_API
endif

LDLIBS += -ldl -lpthread

EXPORT_MAP := rte_pmd_ntacc_version.map

LIBABIVER := 1

#
# all source are stored in SRCS-y
#
SRCS-$(CONFIG_RTE_LIBRTE_PMD_NTACC) += rte_eth_ntacc.c
SRCS-$(CONFIG_RTE_LIBRTE_PMD_NTACC) += filter_ntacc.c
SRCS-$(CONFIG_RTE_LIBRTE_PMD_NTACC) += nt_compat.c

#
# Export include files
#
SYMLINK-y-include +=

# this lib depends upon:
LDLIBS += -lrte_eal -lrte_mbuf -lrte_mempool -lrte_kvargs 
LDLIBS += -lrte_ethdev -lrte_bus_pci

include $(RTE_SDK)/mk/rte.lib.mk
