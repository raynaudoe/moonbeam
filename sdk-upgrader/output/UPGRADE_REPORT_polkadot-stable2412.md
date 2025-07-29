# Polkadot SDK Upgrade Report - stable2412

## Error Group Fixes

### Error Group: ethereum_imports
✓ Fixed ethereum imports (confidence: 0.9)
- Error group: final_ethereum_imports
- Fixed compilation errors related to ethereum imports in macro impl_evm_runner_precompile_or_eth_xcm
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Changed `use ethereum::{H160, H256, U256};` to `use sp_core::{H160, H256, U256};`
- The macro was trying to import types from ethereum crate which is not available in runtime context
- Runtime files consistently use sp_core for these primitive types
- Status: COMPLETED
- All 6 errors fixed

## Error Group Fixes

### Error Group: primitive_types_missing_import
✓ Fixed primitive_types import (confidence: 0.9)
- Error group: primitive_types_missing_import
- Fixed compilation errors related to missing primitive_types import in runtime files
- Added `use sp_core as primitive_types;` alias to:
  - runtime/moonbase/src/xcm_config.rs
  - runtime/moonbeam/src/xcm_config.rs
  - runtime/moonriver/src/xcm_config.rs
- The macro `moonbeam_runtime_common::impl_evm_runner_precompile_or_eth_xcm!()` uses primitive_types::{H160, H256, U256}
- Since the runtime files use sp_core for these types, an alias was needed to satisfy the macro's import
- Status: COMPLETED
- All 3 errors fixed

## Error Group Fixes

### Error Group: h160_type_mismatch
✓ Fixed H160 type mismatch (confidence: 1.0)
- Error group: h160_type_mismatch
- Fixed compilation errors related to H160 type mismatch between sp_core and primitive_types
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Changed `use sp_core::{H160, H256, U256};` to `use primitive_types::{H160, H256, U256};`
- The issue was caused by ethereum crate v0.15.0 expecting primitive_types::H160 while we were providing sp_core::H160
- Direct type import fix resolved all 3 errors
- Status: COMPLETED
- All 3 errors fixed

## Error Group Fixes

### Error Group: evm_runner_authorization_list
✓ Fixed AuthorizationListItem (confidence: 0.95)
- Error group: evm_runner_authorization_list
- Fixed compilation errors related to AuthorizationListItem type mismatch
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Added import `use fp_ethereum::AuthorizationListItem;`
  - Changed authorization_list parameter type from tuple `Vec<(U256, H160, U256, Option<H160>)>` to `Vec<AuthorizationListItem>` in methods: call, create, create2, create_force_address
  - Note: The `validate` method still uses the tuple type as the trait hasn't been updated yet
- Fix was based on existing handbook entry with refinements for the validate method
- Status: COMPLETED
- All 12 errors fixed

## Error Group Fixes

✓ Fixed H160_H256_duplicate (confidence: 0.9)
✓ Fixed authorization_list_revert (confidence: 0.9)
- Error group: error_group_h160_h256_duplicate 
- Fixed compilation errors related to duplicate H160 and H256 imports

### Error Group: final_h160_mismatch
✓ Fixed Final H160 type conflict (confidence: 0.95)
- Error group: final_h160_mismatch
- Fixed compilation errors related to H160 type conflict between macro and runtime aliases
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Removed the `sp_core as ethereum_types` alias
  - Changed imports to use `primitive_types::{H160, H256, U256}` directly
- Removed conflicting `sp_core as primitive_types` alias from runtime files:
  - runtime/moonbase/src/xcm_config.rs
  - runtime/moonbeam/src/xcm_config.rs
  - runtime/moonriver/src/xcm_config.rs
- The issue was that both the macro and runtime files were creating different aliases for sp_core
- Status: COMPLETED
- All 3 errors fixed
- Removed duplicate `use sp_core::{H160, H256};` import from:
  - runtime/moonbase/src/xcm_config.rs
  - runtime/moonbeam/src/xcm_config.rs
  - runtime/moonriver/src/xcm_config.rs
- The macro `moonbeam_runtime_common::impl_evm_runner_precompile_or_eth_xcm!()` already imports H160 and H256
- Fix based on existing Weight_duplicate pattern
- Status: COMPLETED
- All 6 errors fixed (3 for H160, 3 for H256)

## Error Group Fixes

✓ Fixed Weight_duplicate (confidence: 0.9)
✓ Fixed h160_mismatch_ethereum (confidence: 0.85)
- Error group: error_group_h160_mismatch_ethereum
- Fixed compilation errors related to h160_mismatch_ethereum
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Changed AuthorizationListItem to tuple type `(U256, H160, U256, Option<H160>)`
  - Added primitive_types import as ethereum_types
  - Converted sp_core::H160 to primitive_types::H160 in TransactionAction::Call
- The issue was caused by different versions of primitive_types crate (0.12.2 vs 0.13.1)
- Status: COMPLETED
- All 3 errors fixed

✓ Fixed release_proposal_moonriver (confidence: 0.9)

### Error Group: primitive_types_import
✓ Fixed primitive_types_import (confidence: 0.9)
- Error group: error_group_primitive_types_import
- Fixed compilation errors related to primitive_types_import
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Changed `use primitive_types as ethereum_types;` to `use sp_core as ethereum_types;`
- Fix was based on existing handbook entry indicating types migrated to sp_core in SDK
- Status: COMPLETED
- All 3 errors fixed

### Error Group: h256_u256_methods
✓ Fixed U256/H256 method signatures (confidence: 0.9)
- Error group: h256_u256_methods
- Fixed compilation errors related to primitive types method signature changes
- Updated runtime/common/src/impl_xcm_evm_runner.rs:
  - Changed `H160::from(target.0)` to just `target` in TransactionAction::Call
- Updated runtime/common/src/apis.rs:
  - Changed `index.to_big_endian(&mut tmp)` to `let tmp = index.to_big_endian()`
- These changes align with primitive_types 0.13 where methods now return arrays directly instead of taking mutable references
- Based on Scout PR-5886
- Status: COMPLETED
- All 6 errors fixed


### Error Group: runtime_u256_h160_imports
✓ Fixed U256/H160 imports (confidence: 0.85)
- Error group: runtime_u256_h160_imports
- Fixed compilation errors related to missing U256 imports
- Updated runtime/moonbase/src/xcm_config.rs, runtime/moonriver/src/xcm_config.rs, runtime/moonbeam/src/xcm_config.rs:
  - Added U256 to existing sp_core import: `use sp_core::{ConstU32, H160, H256, U256};`
- The macro impl_evm_runner_precompile_or_eth_xcm requires U256 and H160 types in scope
- Status: COMPLETED
- All 9 errors fixed
