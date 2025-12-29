# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

hyprland-autoname-workspaces is a Rust application that automatically renames Hyprland workspaces with icons based on running applications. It integrates with the Hyprland compositor's IPC system to monitor window events and update workspace names in real-time.

## Common Development Commands

### Building
- `make build-dev` - Development build with feature flag and dependency updates
- `make build` - Release build with locked dependencies
- `cargo build` - Standard Rust build

### Testing
- `make test` - Run all tests with locked dependencies
- `cargo test` - Standard Rust test command
- `cargo test --test <test_name>` - Run specific test

### Linting and Formatting
- `make lint` - Run both formatter check and clippy linter
- `cargo fmt` - Format code
- `cargo clippy` - Run linter with warnings as errors

### Running
- `make run` - Run the application
- `cargo run -- -c path/to/config.toml` - Run with custom config

### Release Process
- `make release` - Create new release with version bump and git tag

## Architecture Overview

### Core Components

1. **Main Entry Point** (`src/main.rs`):
   - Initializes the application
   - Sets up Hyprland event monitoring
   - Manages the main event loop

2. **Renamer Module** (`src/renamer/`):
   - `mod.rs` - Core renaming logic and workspace management
   - `formatter.rs` - Handles formatting of workspace names with placeholders
   - `icon.rs` - Icon mapping and resolution logic
   - `macros.rs` - Helper macros for the module

3. **Config Module** (`src/config/`):
   - Handles TOML configuration parsing
   - Manages config file watching for auto-reload
   - Provides default configuration generation

4. **Params Module** (`src/params/`):
   - Command-line argument parsing using clap

### Key Design Patterns

1. **Event-Driven Architecture**: The application subscribes to Hyprland IPC events and reacts to window/workspace changes.

2. **Regex-Based Matching**: Window classes and titles are matched using regex patterns for flexible icon assignment.

3. **Configuration Hot-Reload**: File system watching enables configuration changes without restart.

4. **Placeholder System**: Flexible formatting using placeholders like `{icon}`, `{class}`, `{title}`, etc.

## Testing Approach

Tests are integrated directly in source files using Rust's built-in testing framework:
- Unit tests use `#[cfg(test)]` modules
- Test functions are marked with `#[test]`
- Key test locations: `src/renamer/mod.rs`, `src/config/mod.rs`, `src/renamer/formatter.rs`

## Configuration System

The application uses TOML configuration with these main sections:
- `[format]` - Display formatting options
- `[class]` - Application class to icon mappings
- `[title_in_class]` - Title-based icon mappings within specific classes
- `[exclude]` - Window exclusion rules
- `[workspaces_name]` - Custom workspace names

Default config location: `~/.config/hyprland-autoname-workspaces/config.toml`

## Dependencies

Key dependencies (from Cargo.toml):
- `hyprland` - Hyprland IPC integration
- `clap` - Command-line parsing
- `toml` & `serde` - Configuration handling
- `regex` - Pattern matching
- `notify` - File system watching

## Development Notes

1. The project is seeking maintainers (see README)
2. Use `--features dev` for development builds
3. The systemd service file enables automatic startup
4. Icon generation helper script available at `contrib/generate_icons.py`
5. All regex patterns support case-insensitive matching with `(?i)` flag