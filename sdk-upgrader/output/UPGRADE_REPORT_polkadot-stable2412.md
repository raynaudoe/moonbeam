# Polkadot SDK Upgrade Report - stable2412

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