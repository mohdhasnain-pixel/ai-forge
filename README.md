# Soup Plugin System

A plugin and hook system for extensible trainer workflows.

## Attribution

This module is derived from the [Soup project](https://github.com/MakazhanAlpamys/Soup) by MakazhanAlpamys, originally licensed under the Apache License 2.0.

Original source: `src/soup_cli/plugins/__init__.py`

## Overview

This is a standalone plugin/hook system that provides:

- **Plugin Registration**: Register custom plugins with validation
- **Hook System**: Pre/post training hooks and step hooks
- **Template & Model Group Registration**: Register custom chat templates and model groups
- **Plugin Discovery**: Auto-discover and load plugins
- **Thread-Safe Operations**: Built-in locking for concurrent access

## Features

### Hook Points

The system supports four hook points in the trainer lifecycle:
- `pre_train`: Before training starts
- `post_train`: After training completes
- `pre_step`: Before each training step
- `post_step`: After each training step

### Plugin Registration

```python
from plugins import register_plugin

class MyPlugin:
    def pre_train(self, context):
        print("Training starting!")
        
register_plugin(
    name="my-plugin",
    version="1.0.0",
    plugin=MyPlugin(),
    description="My custom plugin",
    templates=["my-template"],
    model_groups=["my-models"]
)
```

### Plugin Discovery

Plugins automatically register themselves at module import time using `register_plugin()`.

### API Reference

#### `register_plugin()`
Register a new plugin.

**Parameters:**
- `name` (str): Kebab-case plugin name (1-40 chars, alphanumeric + hyphens)
- `version` (str): Semantic version (MAJOR.MINOR.PATCH)
- `plugin` (Any): Plugin object implementing hook methods
- `description` (str, optional): Plugin description (max 256 chars)
- `templates` (List[str], optional): Chat templates provided by plugin
- `model_groups` (List[str], optional): Model groups provided by plugin

**Returns:** `PluginSpec`

#### `list_plugins()`
Get all registered plugins as an immutable mapping.

**Returns:** `Mapping[str, PluginSpec]`

#### `get_plugin(name)`
Get a specific plugin by name.

**Parameters:**
- `name` (str): Plugin name

**Returns:** `PluginSpec | None`

#### `enable_plugin(name)` / `disable_plugin(name)`
Enable or disable a registered plugin.

**Parameters:**
- `name` (str): Plugin name

**Returns:** `bool` - True if state changed

#### `is_enabled(name)`
Check if a plugin is registered and enabled.

**Parameters:**
- `name` (str): Plugin name

**Returns:** `bool`

#### `discover_hooks(plugin)`
Find which hooks a plugin implements.

**Parameters:**
- `plugin` (Any): Plugin object

**Returns:** `Dict[str, Callable]` - Mapping of hook names to methods

#### `load_plugins()`
Auto-discover and load all plugins from a module.

**Returns:** `int` - Number of plugins loaded

#### `list_hook_names()`
Get all valid hook names.

**Returns:** `Tuple[str, ...]`

#### `clear_plugins()`
Remove all registered plugins (mainly for testing).

## Protocol

Plugins can implement this protocol:

```python
class BasePlugin(Protocol):
    def pre_train(self, context: Dict[str, Any]) -> None: ...
    def post_train(self, context: Dict[str, Any]) -> None: ...
    def pre_step(self, context: Dict[str, Any]) -> None: ...
    def post_step(self, context: Dict[str, Any]) -> None: ...
```

Plugins are not required to implement all hooks - implement only what you need.

## Requirements

- Python 3.10+
- No external dependencies (uses only standard library)

## License

Apache License 2.0

See LICENSE file for details.

## Derived Work

This is a derivative work based on the Soup project. All modifications maintain the original Apache 2.0 license.
