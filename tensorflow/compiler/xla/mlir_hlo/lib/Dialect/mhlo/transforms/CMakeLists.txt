#
# Copyright 2020 The TensorFlow Authors. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
include_directories(BEFORE
    ${CMAKE_CURRENT_BINARY_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR})

set(LLVM_TARGET_DEFINITIONS lower_complex_patterns.td)
mlir_tablegen(generated_lower_complex.inc -gen-rewriters)
add_public_tablegen_target(MLIRMhloLowerComplexIncGen)

set(LLVM_TARGET_DEFINITIONS legalize_to_standard_patterns.td)
mlir_tablegen(generated_legalize_to_standard.inc -gen-rewriters)
add_public_tablegen_target(MLIRMhloLegalizeToStandardIncGen)

set(LLVM_TARGET_DEFINITIONS chlo_legalize_to_hlo_patterns.td)
mlir_tablegen(generated_chlo_legalize_to_hlo.inc -gen-rewriters)
add_public_tablegen_target(MLIRChloLegalizeToHloIncGen)


add_mlir_library(MhloScatterUtils
  mhlo_scatter_gather_utils.cc

  DEPENDS
  MLIRhlo_opsIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
)

add_mlir_library(MhloPasses
  broadcast_propagation.cc
  collapse_elementwise_map.cc
  constraint_fusion_pass.cc
  convert_to_signless_pass.cc
  expand_hlo_tuples.cc
  group_reduction_dimensions.cc
  legalize_einsum_to_dot_general.cc
  legalize_gather_to_torch_index_select.cc
  legalize_shape_computations.cc
  legalize_trigonometric_to_approximation.cc
  lower_complex.cc
  lower_complex_patterns.td
  lower_general_dot.cc
  materialize_broadcasts.cc
  materialize_broadcasts_pass.cc
  merge_assuming_ops.cc
  mhlo_canonicalize_gather.cc
  mhlo_canonicalize_reduction.cc
  mhlo_canonicalize_scatter.cc
  mhlo_flatten_tuple.cc
  prepare_for_export.cc
  optimize_mhlo.cc
  optimize_mhlo_pass.cc
  rank_specialization.cc
  restrict_max_rank.cc
  shape_reification_pass.cc
  sink_constants_to_control_flow.cc
  sparse_rewriting.cc
  test_infer_shaped_type_pass.cc
  unfuse_batch_norm.cc
  unfuse_batch_norm_pass.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloLowerComplexIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  ChloOps
  MhloDialect
  MhloScatterUtils
  MhloTypeConversion
  MLIRIR
  MLIRMhloUtils
  MLIRPass
  MLIRTransformUtils
  StablehloBroadcastUtils
)

add_mlir_library(MhloToThloConversion
  legalize_mhlo_to_thlo.cc

  DEPENDS
  MLIRMhloPassIncGen
  MLIRGmlStTilingInterfaceIncGen
  THLODialect

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MhloTypeConversion
  THLODialect
  MLIRIR
  MLIRMhloUtils
  MLIRPass
  MLIRTransformUtils
)

add_mlir_library(MhloToLhloConversion
  hlo_legalize_to_lhlo.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRlhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  LmhloDialect
  MLIRArithDialect
  MLIRArithTransforms
  MLIRFuncTransforms
  MLIRIR
  MLIRPass
  MLIRMathDialect
  MLIRShapeOpsTransforms
  MLIRTransformUtils
  MLIRTransforms
)

add_mlir_library(MhloTypeConversion
  type_conversion.cc

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MLIRIR
  MLIRFuncDialect
  MLIRFuncTransforms
  MLIRTensorDialect
  StablehloOps
)

add_mlir_library(MhloToMemrefConversion
  hlo_legalize_to_memref.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MhloTypeConversion
  MLIRIR
  MLIRPass
  MLIRMathDialect
  MLIRTransforms
  MLIRTransformUtils
)

add_mlir_library(MhloToArithmeticConversion
  hlo_legalize_to_arithmetic.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MhloTypeConversion
  MLIRIR
  MLIRPass
  MLIRMathDialect
  MLIRTransforms
  MLIRTransformUtils
)

add_mlir_library(MhloToStandard
  legalize_control_flow.cc
  legalize_sort.cc
  legalize_to_standard.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRlhlo_opsIncGen
  MLIRMhloLegalizeToStandardIncGen
  MLIRMhloPassIncGen
  MhloTypeConversion

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MLIRIR
  MLIRPass
  MLIRTensorDialect
  MLIRTransformUtils
)

add_mlir_library(HloToLinalgUtils
  legalize_to_linalg_utils.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  LmhloDialect
  MhloDialect
  MhloTypeConversion
  MLIRBufferizationDialect
  MLIRComplexDialect
  MLIRIR
  MLIRLinalgUtils
  MLIRPass
  MLIRRewrite
  MLIRTransformUtils
)

add_mlir_library(ChloPasses
  chlo_legalize_to_hlo.cc
  chlo_legalize_to_hlo_pass.cc
  sparse_chlo_legalize_to_linalg.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRChloLegalizeToHloIncGen
  MLIRMhloPassIncGen
  MLIRLmhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  ChloOps
  HloToLinalgUtils
  MLIRComplexDialect
  MLIRIR
  MLIRPass
  MLIRRewrite
  MLIRTransformUtils
)

add_mlir_library(MhloToLinalg
  legalize_to_linalg.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  HloToLinalgUtils
  LmhloDialect
  MhloDialect
  MhloTypeConversion
  MLIRBufferizationDialect
  MLIRComplexDialect
  MLIRIR
  MLIRLinalgUtils
  MLIRPass
  MLIRRewrite
  MLIRTransformUtils
)

add_mlir_library(MhloShapeOpsToStandard
  hlo_legalize_shape_ops_to_standard.cc

  DEPENDS
  MLIRhlo_opsIncGen
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  LmhloDialect
  MhloDialect
  MhloTypeConversion
  MLIRComplexDialect
  MLIRIR
  MLIRPass
  MLIRRewrite
  MLIRTransformUtils
)

add_mlir_library(MhloToStablehlo
  hlo_legalize_to_stablehlo.cc
  hlo_legalize_to_stablehlo_pass.cc

  DEPENDS
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MhloTypeConversion
  MLIRIR
  MLIRPass
  MLIRSupport
  MLIRTransforms
  StablehloOps
)

add_mlir_library(StablehloToMhlo
  stablehlo_legalize_to_hlo.cc
  stablehlo_legalize_to_hlo_pass.cc

  DEPENDS
  MLIRMhloPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MhloDialect
  MhloTypeConversion
  MLIRIR
  MLIRPass
  MLIRSupport
  MLIRTransforms
  StablehloOps
)

add_library(AllMhloPasses INTERFACE)
target_link_libraries(AllMhloPasses INTERFACE
  ChloPasses
  MhloPasses
  MhloToLhloConversion
  MhloToArithmeticConversion
  MhloToMemrefConversion
  MhloToStandard
  HloToLinalgUtils
  MhloToLinalg
  MhloToThloConversion
  MhloShapeOpsToStandard
  MhloToStablehlo
  StablehloToMhlo
)

add_library(AllGmlStPasses INTERFACE)
target_link_libraries(AllGmlStPasses INTERFACE
  GmlStPasses
  GmlStTestPasses
  MLIRFuncDialect
  MLIRPass
)

add_library(AllThloPasses INTERFACE)
target_link_libraries(AllThloPasses INTERFACE
  ThloPasses
)
