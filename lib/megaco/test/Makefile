# 
# %CopyrightBegin%
# 
# Copyright Ericsson AB 1999-2009. All Rights Reserved.
# 
# The contents of this file are subject to the Erlang Public License,
# Version 1.1, (the "License"); you may not use this file except in
# compliance with the License. You should have received a copy of the
# Erlang Public License along with this software. If not, it can be
# retrieved online at http://www.erlang.org/.
# 
# Software distributed under the License is distributed on an "AS IS"
# basis, WITHOUT WARRANTY OF ANY KIND, either express or implied. See
# the License for the specific language governing rights and limitations
# under the License.
# 
# %CopyrightEnd%

include $(ERL_TOP)/make/target.mk
include $(ERL_TOP)/make/$(TARGET)/otp.mk


# ----------------------------------------------------
# Application version
# ----------------------------------------------------
include ../vsn.mk
VSN=$(MEGACO_VSN)


# ----------------------------------------------------
# Release directory specification
# ----------------------------------------------------
RELSYSDIR = $(RELEASE_PATH)/megaco_test

ifeq ($(findstring win32,$(TARGET)),win32)

MAKEFILE_SRC = Makefile.win32.src

else

MAKEFILE_SRC = Makefile.src

endif

ifeq ($(TT_DIR),)
TT_DIR = /tmp
endif

# ----------------------------------------------------
# Target Specs
# ----------------------------------------------------

include modules.mk

HRL_FILES = megaco_test_lib.hrl

ERL_FILES = $(MODULES:%=%.erl)

TARGET_FILES = $(MODULES:%=%.$(EMULATOR))

COVER_SPEC_FILE = megaco.cover

APP_CASES   = app appup

CODEC_CASES = codec1 codec2 codec3a codec3b codec3c

MISC_CASES  = tid sdp dm conf udp tcp ex timer flex

OP_CASES    = mess mib mreq pending trans actions load

ALL_CASES   = $(APP_CASES) $(CODEC_CASES) $(MISC_CASES) $(OP_CASES)


# ----------------------------------------------------
# FLAGS
# ----------------------------------------------------

include ../src/app/megaco.mk 

ifeq ($(USE_MEGACO_TEST_CODE),true)
ERL_COMPILE_FLAGS += -DMEGACO_TEST_CODE=mona_lisa_spelar_doom
endif

ifeq ($(USE_MEGACO_HIPE),true)
ERL_COMPILE_FLAGS += +native -Dmegaco_hipe_special=true
endif

ERL_COMPILE_FLAGS += \
	$(MEGACO_ERL_COMPILE_FLAGS)         \
	-pa $(ERL_TOP)/lib/test_server/ebin \
	-I$(ERL_TOP)/lib/test_server/include

ERL_PATH = -pa ../../megaco/examples/simple \
           -pa ../../megaco/ebin \
           -pa ../../et/ebin

ifndef SUITE
SUITE = megaco_SUITE
endif

ESTOP = -s init stop

ifeq ($(DONT_STOP),true)
MAYBE_ESTOP =
else
MAYBE_ESTOP = $(ESTOP)
endif

ETVIEW = -s et_viewer
ifeq ($(USE_ET_VIEWER),true)
MAYBE_ETVIEW =
else
MAYBE_ETVIEW = $(ETVIEW)
endif

ifeq ($(MERL),)
MERL = erl
endif

ARGS += -noshell

ifeq ($(DISABLE_TC_TIMEOUT),true)
ARGS += -megaco_test_timeout
endif


# ----------------------------------------------------
# Targets
# ----------------------------------------------------

tests debug opt: $(TARGET_FILES)

clean:
	rm -f $(TARGET_FILES) 
	rm -f errs core *~

docs:

info:
	@echo "ERL_COMPILE_FLAGS = $(ERL_COMPILE_FLAGS)"
	@echo "ERL  = $(ERL)"
	@echo "MERL = $(MERL)"
	@echo ""


# ----------------------------------------------------
# Special Targets
# ----------------------------------------------------

aall: make
	@echo "make sure epmd is new"
	@epmd -kill > /dev/null
	@echo "Running all app sub-suites separatelly"
	@for i in $(APP_CASES); do \
            echo "SUITE: $$i"; \
            clearmake -V $$i > $$i.log; \
        done
	echo "done"

call: make
	@echo "make sure epmd is new"
	@epmd -kill > /dev/null
	@echo "Running all codec sub-suites separatelly"
	@for i in $(CODEC_CASES); do \
            echo "SUITE: $$i"; \
            clearmake -V $$i > $$i.log; \
        done

mall: make
	@echo "make sure epmd is new"
	@epmd -kill > /dev/null
	@echo "Running all misc sub-suites separatelly"
	@for i in $(MISC_CASES); do \
            echo "SUITE: $$i"; \
            clearmake -V $$i > $$i.log; \
        done

oall: make
	@echo "make sure epmd is new"
	@epmd -kill > /dev/null
	@echo "Running all operation sub-suites separatelly"
	@for i in $(OP_CASES); do \
            echo "SUITE: $$i"; \
            clearmake -V $$i > $$i.log; \
        done

all: make
	@echo "make sure epmd is new"
	@epmd -kill > /dev/null
	@echo "Running all sub-suites separatelly"
	@for i in $(ALL_CASES); do \
            echo "SUITE: $$i"; \
            clearmake -V $$i > $$i.log; \
        done

make: targets

targets: $(TARGET_FILES)

test: make
	$(MERL) $(ARGS) -sname megaco_test $(ERL_PATH) \
            -s megaco_test_lib t $(SUITE) \
            $(MAYBE_ESTOP)

utest: make
	$(MERL) $(ARGS) -sname megaco_utest $(ERL_PATH) \
            $(MAYBE_ETVIEW) \
            -s megaco_test_lib t $(SUITE) \
            $(ESTOP)

ftest: make
	$(MERL) $(ARGS) -sname megaco_ftest $(ERL_PATH) \
            -s megaco_filter \
            -s megaco_test_lib t $(SUITE) \
            $(ESTOP)

decode_compact_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_compact $(ERL_PATH) \
            -s megaco_codec_v1_test profile_decode_compact_text_messages \
            $(ESTOP)

decode_compact_flex_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_compact_flex $(ERL_PATH) \
            -s megaco_codec_v1_test profile_decode_compact_flex_text_messages \
            $(ESTOP)

decode_compact_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_compact $(ERL_PATH) \
            -s megaco_codec_v2_test profile_decode_compact_text_messages \
            $(ESTOP)

decode_compact_flex_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_compact_flex $(ERL_PATH) \
            -s megaco_codec_v2_test profile_decode_compact_flex_text_messages \
            $(ESTOP)

decode_pretty_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_pretty $(ERL_PATH) \
            -s megaco_codec_v1_test profile_decode_pretty_text_messages \
            $(ESTOP)

decode_pretty_flex_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_pretty_flex $(ERL_PATH) \
            -s megaco_codec_v1_test profile_decode_pretty_flex_text_messages \
            $(ESTOP)

decode_pretty_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_pretty $(ERL_PATH) \
            -s megaco_codec_v2_test profile_decode_pretty_text_messages \
            $(ESTOP)

decode_pretty_flex_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_decode_pretty_flex $(ERL_PATH) \
            -s megaco_codec_v2_test profile_decode_pretty_flex_text_messages \
            $(ESTOP)

encode_compact_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_encode_compact $(ERL_PATH) \
            -s megaco_codec_v1_test profile_encode_compact_text_messages \
            $(ESTOP)

encode_compact_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_encode_compact $(ERL_PATH) \
            -s megaco_codec_v2_test profile_encode_compact_text_messages \
            $(ESTOP)

encode_pretty_prof1: make
	$(MERL) $(ARGS) -sname megaco_profile_encode_pretty $(ERL_PATH) \
            -s megaco_codec_v1_test profile_encode_pretty_text_messages \
            $(ESTOP)

encode_pretty_prof2: make
	$(MERL) $(ARGS) -sname megaco_profile_encode_pretty $(ERL_PATH) \
            -s megaco_codec_v2_test profile_encode_pretty_text_messages \
            $(ESTOP)


##########################

tickets: make
	$(MERL) $(ARGS) -sname megaco_tickets $(ERL_PATH) \
            -s megaco_test_lib tickets $(SUITE) \
            $(ESTOP)

app: make
	$(MERL) $(ARGS) -sname megaco_app $(ERL_PATH) \
            -s megaco_test_lib t megaco_app_test \
            $(ESTOP)

appup: make
	$(MERL) $(ARGS) -sname megaco_appup $(ERL_PATH) \
            -s megaco_test_lib t megaco_appup_test \
            $(ESTOP)

conf: make
	$(MERL) $(ARGS) -sname megaco_config $(ERL_PATH) \
            -s megaco_test_lib t megaco_config_test \
            $(ESTOP)


##########################

codec: make
	$(MERL) $(ARGS) -sname megaco_codec $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_test \
            $(MAYBE_ESTOP)

codec1: make
	$(MERL) $(ARGS) -sname megaco_codec1 $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_v1_test \
            $(MAYBE_ESTOP)

codec1_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec1_tickets $(ERL_PATH) \
            -s megaco_codec_v1_test tickets \
            $(ESTOP)

codec2: make
	$(MERL) $(ARGS) -sname megaco_codec2 $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_v2_test \
            $(MAYBE_ESTOP)

codec2_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec2_tickets $(ERL_PATH) \
            -s megaco_codec_v2_test tickets \
            $(ESTOP)

codec3a: make
	$(MERL) $(ARGS) -sname megaco_codec3a $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_prev3a_test \
            $(MAYBE_ESTOP)

codec3a_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec3a_tickets $(ERL_PATH) \
            -s megaco_codec_prev3a_test tickets \
            $(ESTOP)

codec3b: make
	$(MERL) $(ARGS) -sname megaco_codec3b $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_prev3b_test \
            $(MAYBE_ESTOP)

codec3b_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec3b_tickets $(ERL_PATH) \
            -s megaco_codec_prev3b_test tickets \
            $(ESTOP)

codec3c: make
	$(MERL) $(ARGS) -sname megaco_codec3c $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_prev3c_test \
            $(MAYBE_ESTOP)

codec3c_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec3c_tickets $(ERL_PATH) \
            -s megaco_codec_prev3c_test tickets \
            $(ESTOP)

codec3: make
	$(MERL) $(ARGS) -sname megaco_codec3 $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_v3_test \
            $(MAYBE_ESTOP)

codec3_tickets: make
	$(MERL) $(ARGS) -sname megaco_codec3_tickets $(ERL_PATH) \
            -s megaco_codec_v3_test tickets \
            $(ESTOP)

codecm: make
	$(MERL) $(ARGS) -sname megaco_codec1 $(ERL_PATH) \
            -s megaco_test_lib t megaco_codec_mini_test \
            $(MAYBE_ESTOP)


##########################

time1: make
	$(MERL) $(ARGS) -sname megaco_time1 $(ERL_PATH) \
            -run megaco_codec_v1_test tt $(TT_DIR) \
            $(ESTOP)

time2: make
	$(MERL) $(ARGS) -sname megaco_time2 $(ERL_PATH) \
            -run megaco_codec_v2_test tt $(TT_DIR) \
            $(ESTOP)

timeo1: make
	$(MERL) $(ARGS) -sname megaco_timeo1 $(ERL_PATH) \
            -run megaco_codec_v1_test tt_official $(TT_DIR) \
            $(ESTOP)

timeo2: make
	$(MERL) $(ARGS) -sname megaco_timeo2 $(ERL_PATH) \
            -run megaco_codec_v2_test tt_official $(TT_DIR) \
            $(ESTOP)

timeo3: make
	$(MERL) $(ARGS) -sname megaco_timeo3 $(ERL_PATH) \
            -run megaco_codec_v3_test tt_official $(TT_DIR) \
            $(ESTOP)

timet1: make
	$(MERL) $(ARGS) -sname megaco_timet1 $(ERL_PATH) \
            -run megaco_codec_v1_test tt_texts $(TT_DIR) \
            $(ESTOP)

timet2: make
	$(MERL) $(ARGS) -sname megaco_timet2 $(ERL_PATH) \
            -run megaco_codec_v2_test tt_texts $(TT_DIR) \
            $(ESTOP)

timet3: make
	$(MERL) $(ARGS) -sname megaco_timet3 $(ERL_PATH) \
            -run megaco_codec_v3_test tt_texts $(TT_DIR) \
            $(ESTOP)

timeb1: make
	$(MERL) $(ARGS) -sname megaco_timeb1 $(ERL_PATH) \
            -run megaco_codec_v1_test tt_bins $(TT_DIR) \
            $(ESTOP)

timeb2: make
	$(MERL) $(ARGS) -sname megaco_timeb2 $(ERL_PATH) \
            -run megaco_codec_v2_test tt_bins $(TT_DIR) \
            $(ESTOP)

timeb3: make
	$(MERL) $(ARGS) -sname megaco_timeb3 $(ERL_PATH) \
            -run megaco_codec_v3_test tt_bins $(TT_DIR) \
            $(ESTOP)


##########################

flex: make
	$(MERL) $(ARGS) -sname megaco_flex $(ERL_PATH) \
            -s megaco_test_lib t megaco_flex_test \
            $(ESTOP)

dm: make
	$(MERL) $(ARGS) -sname megaco_dm $(ERL_PATH) \
            -s megaco_test_lib t megaco_digit_map_test \
            $(ESTOP)

tid: make
	$(MERL) $(ARGS) -sname megaco_tid $(ERL_PATH) \
            -s megaco_test_lib t megaco_binary_term_id_test \
            $(ESTOP)

sdp: make
	$(MERL) $(ARGS) -sname megaco_sdp $(ERL_PATH) \
            -s megaco_test_lib t megaco_sdp_test \
            $(MAYBE_ESTOP)

actions: make
	$(MERL) $(ARGS) -sname megaco_actions $(ERL_PATH) \
            -s megaco_test_lib t megaco_actions_test \
            $(MAYBE_ESTOP)

mess: make
	$(MERL) $(ARGS) -sname megaco_mess $(ERL_PATH) \
            $(MAYBE_ETVIEW) \
            -s megaco_test_lib t megaco_mess_test \
            $(ESTOP)

trans: make
	$(MERL) $(ARGS) -sname megaco_trans $(ERL_PATH) \
            -s megaco_test_lib t megaco_trans_test \
            $(MAYBE_ESTOP)

mib: make
	$(MERL) $(ARGS) -sname megaco_mib $(ERL_PATH) \
            -s megaco_test_lib t megaco_mib_test \
            $(MAYBE_ESTOP)

mreq: make
	$(MERL) $(ARGS) -sname megaco_mreq $(ERL_PATH) \
            -s megaco_test_lib t megaco_mreq_test \
            $(MAYBE_ESTOP)

pending: make
	$(MERL) $(ARGS) -sname megaco_pending $(ERL_PATH) \
            -s megaco_test_lib t megaco_pending_limit_test \
            $(MAYBE_ESTOP)

pl: make
	$(MERL) $(ARGS) -sname megaco_pl $(ERL_PATH) \
            -s megaco_test_lib t megaco_pending_limit_test \
            $(MAYBE_ESTOP)

udp: make
	$(MERL) $(ARGS) -sname megaco_pl $(ERL_PATH) \
            -s megaco_test_lib t megaco_udp_test \
            $(MAYBE_ESTOP)

tcp: make
	$(MERL) $(ARGS) -sname megaco_pl $(ERL_PATH) \
            -s megaco_test_lib t megaco_tcp_test \
            $(MAYBE_ESTOP)

load: make
	$(MERL) $(ARGS) -sname megaco_load $(ERL_PATH) \
            -s megaco_test_lib t megaco_load_test \
            $(MAYBE_ESTOP)

ex: make
	$(MERL) $(ARGS) -sname megaco_ex $(ERL_PATH) \
            -s megaco_test_lib t megaco_examples_test \
            $(MAYBE_ESTOP)

segment: make
	$(MERL) $(ARGS) -sname megaco_segment $(ERL_PATH) \
            -s megaco_test_lib t megaco_segment_test \
            $(ESTOP)

timer: make
	$(MERL) $(ARGS) -sname megaco_timer $(ERL_PATH) \
            -s megaco_test_lib t megaco_timer_test \
            $(ESTOP)


###################

gnuplot_gif: make
	$(MERL) $(ARGS) -sname megaco_gnuplot_gif $(ERL_PATH) \
            -s megaco_call_flow_test gnuplot_gif \
            $(ESTOP)

display_v1: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_v1 $(ERL_PATH) \
            -s megaco_codec_v1_test display_text_messages \
            $(ESTOP)

generate_v1: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs_v1 $(ERL_PATH) \
            -s megaco_codec_v1_test generate_text_messages \
            $(ESTOP)

display_v2: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_v2 $(ERL_PATH) \
            -s megaco_codec_v2_test display_text_messages \
            $(ESTOP)

generate_v2: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs_v2 $(ERL_PATH) \
            -s megaco_codec_v2_test generate_text_messages \
            $(ESTOP)

display_prev3a: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_prev3a $(ERL_PATH) \
            -s megaco_codec_prev3a_test display_text_messages \
            $(ESTOP)

display_prev3b: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_prev3b $(ERL_PATH) \
            -s megaco_codec_prev3b_test display_text_messages \
            $(ESTOP)

generate_prev3b: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs_prev3b $(ERL_PATH) \
            -s megaco_codec_prev3b_test generate_text_messages \
            $(ESTOP)

display_prev3c: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_prev3c $(ERL_PATH) \
            -s megaco_codec_prev3c_test display_text_messages \
            $(ESTOP)

generate_prev3c: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs_prev3c $(ERL_PATH) \
            -s megaco_codec_prev3c_test generate_text_messages \
            $(ESTOP)

display_v3: make
	$(MERL) $(ARGS) -sname megaco_display_text_msgs_v3 $(ERL_PATH) \
            -s megaco_codec_v3_test display_text_messages \
            $(ESTOP)

generate_v3: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs_v3 $(ERL_PATH) \
            -s megaco_codec_v3_test generate_text_messages \
            $(ESTOP)

generate: make
	$(MERL) $(ARGS) -sname megaco_generate_text_msgs $(ERL_PATH) \
            -s megaco_codec_v1_test generate_text_messages \
            -s megaco_codec_v2_test generate_text_messages \
            -s megaco_codec_v3_test generate_text_messages \
            $(ESTOP)

node:
	$(MERL) -sname megaco $(ERL_PATH)


# ----------------------------------------------------
# Release Targets
# ---------------------------------------------------- 

include $(ERL_TOP)/make/otp_release_targets.mk

release_spec: 

release_docs_spec:

release_tests_spec: tests
	$(INSTALL_DIR)  $(RELSYSDIR)
	$(INSTALL_DATA) $(TEST_SPEC_FILE) $(COVER_SPEC_FILE) \
                        $(HRL_FILES) $(ERL_FILES) \
                        $(RELSYSDIR)
	$(INSTALL_DATA) $(TARGET_FILES) $(RELSYSDIR)
	chmod -f -R u+w $(RELSYSDIR)

