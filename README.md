# WordPress Remote News Publisher

Automated news article generation and publishing to WordPress via SSH and WP-CLI.

## Overview

This OpenClaw skill enables automated news content creation and publication to WordPress websites hosted on remote servers. It leverages SSH with public key authentication for secure, passwordless connections, and WP-CLI for all WordPress operations on the server side.

### Key Features

- **Remote Execution**: All WP-CLI operations run on the remote server via SSH
- **Automated Topic Selection**: Round-robin rotation through configured topics
- **Journalistic Writing Style**: Senior journalist quality content with lede, body, and attribution
- **Image Pipeline**: Unsplash download → local optimization → SCP transfer → WordPress import
- **SEO Integration**: Yoast SEO meta description and focus keyword support
- **Cron Scheduling**: Automated execution on weekdays

## Quick Start

### Prerequisites

- SSH access to a remote server with WordPress installed
- WP-CLI installed on the remote server
- Python 3 with `requests` library
- ImageMagick (for image optimization)
- Unsplash API access key

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `WP_SSH_HOST` | Remote server hostname or IP | Yes |
| `WP_SSH_USER` | SSH username on remote server | Yes |
| `WP_SSH_KEY` | Absolute path to SSH private key | Yes |
| `WP_SSH_PORT` | SSH port (default: 22) | No |
| `WP_REMOTE_PATH` | Absolute path to WordPress installation | Yes |
| `WP_REMOTE_TMP` | Writable temp directory on remote | No |
| `WP_AUTHOR_ID` | WordPress author ID for posts | Yes |
| `UNSPLASH_ACCESS_KEY` | Unsplash API access key | Yes |

### SSH Key Setup

```bash
# 1. Generate a dedicated key pair (ed25519 recommended)
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_wp \
    -C "openclaw-wp-publisher" -N ""

# 2. Copy public key to remote server
ssh-copy-id -i ~/.ssh/id_ed25519_wp.pub -p 22 deploy@your-server.com

# 3. Verify passwordless connection
ssh -i ~/.ssh/id_ed25519_wp -o BatchMode=yes \
    deploy@your-server.com "wp --info"
```

## Project Structure

```
WordPress-Remote-News-Publisher/
├── README.md              # This file
├── SKILL.md               # OpenClaw skill definition
├── config.json            # Configuration file
└── scripts/
    ├── download_cover.py      # Download cover images from Unsplash
    ├── optimize_image.sh      # Optimize images for web
    ├── upload_media_remote.sh # Upload media via SCP
    └── publish_wp_remote.sh   # Publish articles to WordPress
```

## Configuration

Edit `config.json` to customize:

- **Topics**: Define news topics for rotation
- **Categories**: Map topics to WordPress categories
- **Article Settings**: Word count, title length, meta description limits
- **Image Settings**: Dimensions, quality, orientation

### Example Topics

- Artificial Intelligence
- Cybersecurity
- Tech Startups
- Cloud Computing
- Quantum Computing
- Electric Vehicles
- Fintech
- Space Tech

## Usage

### Manual Execution

```bash
# Run with a specific topic
/wordpress-remote-news-publisher "Artificial Intelligence"

# Run with automatic topic selection
/wordpress-remote-news-publisher
```

### Cron Scheduling

The skill supports automatic execution via cron:

```json
{
  "cron": {
    "jobs": [
      {
        "schedule": "0 10 * * 1-5",
        "prompt": "Execute wordpress-remote-news-publisher"
      }
    ]
  }
}
```

## Editorial Guidelines

Every article adheres to these rules:

1. **Write like a senior journalist**, not an AI assistant
2. **Use variable sentence length**: Alternate short periods with complex constructions
3. **Lead with a clear thesis** in the first paragraph (lede)
4. **Include attributed quotes** from real sources when available
5. **Avoid AI-sounding phrases**: Never use "it's important to note," "in the digital age," etc.
6. **Specific titles**: No clickbait. The title must contain the main fact

## Remote Server Requirements

| Component | Requirement |
|-----------|-------------|
| WP-CLI | Installed and in PATH |
| PHP | ≥ 7.4 (8.x recommended) |
| Python 3 | For JSON parsing |
| Permissions | SSH user writes to WordPress directory |

## License

MIT License - See LICENSE file for details

## Author

ClawHub Community

## Version

1.0.0
