# Setup ClusterShell Action

This GitHub Action sets up a ClusterShell environment.

## Inputs

| Input | Description | Default |
|-------|-------------|---------|
| `cfgdir` | ClusterShell configuration directory | `$HOME/.config/clustershell` |
| `groupsource` | ClusterShell default groupsource | `example` |

## Usage

### Basic Usage

```yaml
- name: Setup ClusterShell
  uses: zckv/action-clustershell-setup@v1
```

### With Custom Configuration Directory

```yaml
- name: Setup ClusterShell
  uses: zckv/action-clustershell-setup@v1
  with:
    cfgdir: /opt/clustershell
```

### With Custom Group Source

```yaml
- name: Setup ClusterShell
  uses: zckv/action-clustershell-setup@v1
  with:
    groupsource: mygroups
```

### Complete Example Workflow

```yaml
name: Example with ClusterShell
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - name: Setup ClusterShell
        uses: zckv/action-clustershell-setup@v1
        with:
          groupsource: my_cluster
      - name: Create my_cluster group file
        shell: bash
        run: |
          cat <<EOF > "$HOME/.config/clustershell/groups.d/cluster.yaml"
          cluster:
              login: 'cluster_node[1-2]'
              compute: 'cluster_node[3-10]'
              all: '@login,@compute'
          EOF
      - name: Use ClusterShell
        run: |
          # View available group sources
          nodeset --groupsources
          
          # View all groups
          nodeset -L
```

## What Gets Installed

The action creates the following directory structure:

```
$cfgdir/
├── groups            # Empty file for compatibility
├── groups.conf       # Main ClusterShell configuration
└── groups.d/
    └── example.yaml  # Default group definitions (or specified groupsource)
```

### Default Example Groups

By default, the action creates the following example groups:

```yaml
example:
  login: 'node[1-2]'
  compute: 'node[3-10]'
  all: '@login,@compute'
```
