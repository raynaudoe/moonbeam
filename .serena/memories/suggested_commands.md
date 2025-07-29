# Suggested Commands for Moonbeam Development

## Build Commands
- `cargo build --release` - Build the project in release mode
- `cargo build --features=fast-runtime` - Build with fast runtime for development
- `cargo check` - Check code compilation without building

## Test Commands
- `cargo test` - Run all tests
- `cargo test -p <pallet-name>` - Test specific pallet

## Formatting and Linting
- `cargo fmt` - Format code according to .rustfmt.toml
- `cargo clippy` - Run linter

## Docker Commands
- `docker run --network="host" moonbeamfoundation/moonbeam:v0.44.1 --dev` - Run dev node
- `docker run --network="host" moonbeamfoundation/moonbeam:v0.44.1 --chain alphanet` - Run testnet node

## Git Commands
- `git status` - Check current changes
- `git diff` - View unstaged changes
- `git add -A` - Stage all changes
- `git commit -m "message"` - Commit changes
- `gh pr create` - Create pull request

## System Commands (Linux)
- `ls -la` - List files with details
- `find . -name "*.rs"` - Find Rust files
- `grep -r "pattern" .` - Search for pattern in files
- `pwd` - Print working directory