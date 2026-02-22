# GitHub Bash Commands Guide for Developers

A comprehensive reference of essential BASH commands for interacting with GitHub repositories.

## Table of Contents

1. [Initial Setup](#initial-setup)
2. [Repository Operations](#repository-operations)
3. [Branch Management](#branch-management)
4. [Committing Changes](#committing-changes)
5. [Remote Operations](#remote-operations)
6. [Viewing History](#viewing-history)
7. [Stashing & Reverting](#stashing--reverting)
8. [Merging & Rebasing](#merging--rebasing)
9. [Tags](#tags)
10. [GitHub CLI (gh) Commands](#github-cli-gh-commands)
11. [SSH Key Management](#ssh-key-management)
12. [Advanced Workflows](#advanced-workflows)

---

## Initial Setup

### Configure Git User (Required)

```bash
# Set your name globally
git config --global user.name "Your Name"

# Set your email globally
git config --global user.email "your.email@example.com"

# Verify configuration
git config --global --list

# Configure for specific repository only (local)
cd /path/to/repo
git config user.name "Your Name"
git config user.email "your.email@example.com"
