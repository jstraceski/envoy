load("@envoy//bazel:envoy_build_system.bzl", "envoy_cc_library", "envoy_package", "envoy_select_enable_http3", "envoy_select_envoy_mobile_listener", "envoy_select_envoy_mobile_request_compression", "envoy_select_envoy_mobile_stats_reporting")

licenses(["notice"])  # Apache 2

envoy_package()

envoy_cc_library(
    name = "extension_registry",
    srcs = [
        "extension_registry.cc",
    ],
    hdrs = ["extension_registry.h"],
    repository = "@envoy",
    deps = [
        "extension_registry_platform_additions",
        "@envoy//source/common/http/matching:inputs_lib",
        "@envoy//source/common/network:socket_lib",
        "@envoy//source/common/router:upstream_codec_filter_lib",
        "@envoy//source/common/watchdog:abort_action_config",
        "@envoy//source/extensions/clusters/dynamic_forward_proxy:cluster",
        "@envoy//source/extensions/compression/brotli/decompressor:config",
        "@envoy//source/extensions/compression/gzip/decompressor:config",
        "@envoy//source/extensions/filters/http/alternate_protocols_cache:config",
        "@envoy//source/extensions/filters/http/buffer:config",
        "@envoy//source/extensions/filters/http/decompressor:config",
        "@envoy//source/extensions/filters/http/dynamic_forward_proxy:config",
        "@envoy//source/extensions/filters/http/router:config",
        "@envoy//source/extensions/filters/network/http_connection_manager:config",
        "@envoy//source/extensions/http/header_formatters/preserve_case:config",
        "@envoy//source/extensions/network/dns_resolver/getaddrinfo:config",
        "@envoy//source/extensions/path/match/uri_template:config",
        "@envoy//source/extensions/path/rewrite/uri_template:config",
        "@envoy//source/extensions/request_id/uuid:config",
        "@envoy//source/extensions/transport_sockets/http_11_proxy:upstream_config",
        "@envoy//source/extensions/transport_sockets/raw_buffer:config",
        "@envoy//source/extensions/transport_sockets/tls:config",
        "@envoy//source/extensions/transport_sockets/tls/cert_validator:cert_validator_lib",
        "@envoy//source/extensions/upstreams/http/generic:config",
        "@envoy_mobile//library/common/extensions/cert_validator/platform_bridge:config",
        "@envoy_mobile//library/common/extensions/filters/http/local_error:config",
        "@envoy_mobile//library/common/extensions/filters/http/network_configuration:config",
        "@envoy_mobile//library/common/extensions/filters/http/platform_bridge:config",
        "@envoy_mobile//library/common/extensions/filters/http/socket_tag:config",
        "@envoy_mobile//library/common/extensions/key_value/platform:config",
        "@envoy_mobile//library/common/extensions/listener_managers/api_listener_manager:api_listener_manager_lib",
        "@envoy_mobile//library/common/extensions/retry/options/network_configuration:config",
    ] + envoy_select_enable_http3(
        [
            "@envoy//source/common/quic:quic_transport_socket_factory_lib",
        ],
        "@envoy",
    ) + envoy_select_envoy_mobile_stats_reporting(
        [
            "@envoy//source/extensions/stat_sinks/metrics_service:config",
            "@envoy//source/extensions/stat_sinks/statsd:config",
            "@envoy//source/extensions/clusters/logical_dns:logical_dns_cluster_lib",
        ],
        "@envoy",
    ) + envoy_select_envoy_mobile_request_compression(
        [
            "@envoy//source/extensions/compression/brotli/compressor:config",
            "@envoy//source/extensions/compression/gzip/compressor:config",
            "@envoy//source/extensions/filters/http/compressor:config",
            "@envoy//source/extensions/filters/http/composite:config",
        ],
        "@envoy",
    ) + envoy_select_envoy_mobile_listener(
        [
            "@envoy//source/extensions/udp_packet_writer/default:config",
            "@envoy//source/extensions/listener_managers/listener_manager:listener_manager_lib",
            "@envoy//source/extensions/listener_managers/listener_manager:connection_handler_lib",
        ],
        "@envoy",
    ),
)

envoy_cc_library(
    name = "extension_registry_platform_additions",
    srcs = select({
        "@envoy//bazel:apple": ["extension_registry_apple.cc"],
        "//conditions:default": ["extension_registry_noop.cc"],
    }),
    hdrs = ["extension_registry_platform_additions.h"],
    repository = "@envoy",
    deps = select({
        "@envoy//bazel:apple": ["@envoy//source/extensions/network/dns_resolver/apple:config"],
        "//conditions:default": [],
    }),
)

# for java and C++ auto-register the test libraries.
envoy_cc_library(
    name = "test_extensions",
    srcs = [
        "test_extensions_autoregister.cc",
    ],
    repository = "@envoy",
    deps = [":test_extensions_no_autoregister"],
    alwayslink = 1,
)

# swift may compile out autoregistry functions, so registration will be performed manually.
envoy_cc_library(
    name = "test_extensions_no_autoregister",
    srcs = [
        "test_extensions.cc",
    ],
    hdrs = [
        "test_extensions.h",
    ],
    repository = "@envoy",
    # List of modules to compile into the test files. For JVM we always compile these into the .so all
    # the time, while for iOS we have to force register the extensions, so test files must pull in
    # the TestExtensions module and call register_test_extensions().
    deps = [
        "@envoy//source/extensions/clusters/static:static_cluster_lib",
        "@envoy//source/extensions/filters/http/buffer:config",
        "@envoy_mobile//library/common/extensions/filters/http/assertion:config",
        "@envoy_mobile//library/common/extensions/filters/http/route_cache_reset:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_accessor:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_event_tracker:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_kv_store:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_logger:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_read:config",
        "@envoy_mobile//library/common/extensions/filters/http/test_remote_response:config",
    ],
    alwayslink = 1,
)
