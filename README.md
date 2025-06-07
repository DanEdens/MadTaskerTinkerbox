# Mad Tasker Tinkerbox Projects

A collection of automation projects for Android using [Tasker](https://tasker.joaoapps.com/). This is a [Madness Interactive](https://github.com/DanEdens/madness_interactive) project.

## 🔄 Automated Backup Management System

### Overview
An intelligent system for retrieving, processing, and organizing weekly Tasker configuration backups from Google Drive. This system ensures your Tasker projects are always synchronized, versioned, and properly organized.

### 🔧 Core Components

#### 1. **Google Drive Integration** (`backup_retriever.py`)
- **Google Drive API v3** integration for secure backup access
- **OAuth2 authentication** with service account support
- **Incremental sync** - only downloads new/modified backups
- **Folder structure mapping** to organize different backup types
- **Rate limiting** to respect Google Drive API quotas

#### 2. **Tasker Config Parser** (`config_processor.py`)
- **XML parsing** for `.prj.xml` and `.tsk.xml` files
- **Project separation** - extracts individual projects from backup bundles
- **Metadata extraction** - project names, descriptions, modification dates
- **Dependency mapping** - tracks inter-project references and shared profiles
- **Version comparison** - detects changes between backup versions

#### 3. **Backup Processor** (`backup_manager.py`)
- **Automatic splitting** of combined backup files into individual projects
- **Smart organization** into project-specific directories
- **Conflict resolution** when multiple versions exist
- **Backup validation** - ensures file integrity and completeness
- **Historical archiving** - maintains backup history with timestamps

#### 4. **Automation Scheduler** (`scheduler.py`)
- **Weekly sync scheduling** with configurable timing
- **Webhook integration** for immediate processing of new backups
- **Error handling and retry logic** for failed operations
- **Notification system** via MQTT/email for sync status
- **Dashboard integration** for monitoring backup health

### 📁 Proposed Directory Structure

```
projects/tasker/
├── tools/                          # Backup management tools
│   ├── backup_retriever.py        # Google Drive API integration
│   ├── config_processor.py        # Tasker XML parsing and processing
│   ├── backup_manager.py          # Main orchestration logic
│   ├── scheduler.py               # Automated sync scheduling
│   └── utils/
│       ├── auth_manager.py        # Google OAuth handling
│       ├── file_utils.py          # File operations and validation
│       └── notification.py       # Status notifications
├── config/                        # Configuration files
│   ├── google_drive_config.json  # Drive API settings
│   ├── backup_schedule.yaml       # Sync timing configuration
│   └── project_mapping.json      # Project organization rules
├── backups/                       # Local backup storage
│   ├── raw/                       # Unprocessed backup files
│   ├── processed/                 # Split and organized configs
│   └── archive/                   # Historical backup versions
├── logs/                          # Operation logs
└── requirements.txt               # Python dependencies
```

### 🔐 Security Considerations

#### Authentication & Authorization
- **Service Account** for server-to-server Google Drive access
- **Scoped permissions** - read-only access to specific backup folders
- **Credential encryption** using environment variables or key management
- **API key rotation** strategy for long-term security

#### Data Protection
- **Local encryption** for sensitive backup data at rest
- **Secure transmission** using HTTPS for all API calls
- **Access logging** for audit trail of backup operations
- **Backup verification** using checksums to detect tampering

### 🛠 Implementation Workflow

#### Phase 1: Google Drive Integration
```python
# Example: Basic Drive API setup
from google.oauth2 import service_account
from googleapiclient.discovery import build

class DriveBackupRetriever:
    def __init__(self, credentials_path, backup_folder_id):
        self.credentials = service_account.Credentials.from_service_account_file(
            credentials_path,
            scopes=['https://www.googleapis.com/auth/drive.readonly']
        )
        self.service = build('drive', 'v3', credentials=self.credentials)
        self.backup_folder_id = backup_folder_id
    
    async def get_weekly_backups(self, since_date=None):
        """Retrieve new backup files since last sync"""
        # Implementation for fetching new backup files
        pass
    
    async def download_backup(self, file_id, destination_path):
        """Download and verify backup file integrity"""
        # Implementation for secure file download
        pass
```

#### Phase 2: Config Processing
```python
# Example: Tasker XML processing
import xml.etree.ElementTree as ET
from pathlib import Path

class TaskerConfigProcessor:
    def __init__(self):
        self.project_parsers = {
            '.prj.xml': self._parse_project_file,
            '.tsk.xml': self._parse_task_file,
            '.prf.xml': self._parse_profile_file
        }
    
    def split_backup_bundle(self, backup_path):
        """Extract individual projects from combined backup"""
        # Parse backup file and extract individual components
        pass
    
    def extract_project_metadata(self, project_xml):
        """Extract project information and dependencies"""
        # Parse XML to get project details
        pass
```

#### Phase 3: Automation & Monitoring
```python
# Example: Scheduled backup processing
import schedule
import asyncio
from datetime import datetime

class BackupScheduler:
    def __init__(self, retriever, processor, notifier):
        self.retriever = retriever
        self.processor = processor
        self.notifier = notifier
    
    def setup_weekly_sync(self):
        """Schedule weekly backup synchronization"""
        schedule.every().sunday.at("02:00").do(self.sync_backups)
    
    async def sync_backups(self):
        """Main backup synchronization workflow"""
        try:
            # 1. Check for new backups
            new_backups = await self.retriever.get_weekly_backups()
            
            # 2. Process each backup
            for backup in new_backups:
                await self.processor.process_backup(backup)
            
            # 3. Update project directories
            await self.processor.organize_projects()
            
            # 4. Send success notification
            await self.notifier.send_success_notification()
            
        except Exception as e:
            await self.notifier.send_error_notification(str(e))
```

### 📊 Monitoring & Analytics

#### Backup Health Dashboard
- **Sync status** - last successful backup retrieval timestamp
- **Project statistics** - number of projects, recent changes
- **Error tracking** - failed sync attempts and resolution status
- **Storage metrics** - backup file sizes and storage usage
- **Version history** - timeline of project modifications

#### Integration Points
- **MQTT publishing** for real-time backup status updates
- **Omnispindle integration** for creating todos from backup issues
- **Webhook endpoints** for external system notifications
- **Discord/Slack integration** for team notifications

### 🔄 Advanced Features

#### Intelligent Project Organization
- **Auto-categorization** based on project content analysis
- **Dependency mapping** to show inter-project relationships
- **Unused profile detection** for cleanup suggestions
- **Performance analysis** to identify resource-heavy projects

#### Version Control Integration
- **Git integration** for automatic commits of project changes
- **Branch management** for different backup versions
- **Merge conflict resolution** for simultaneous project modifications
- **Release tagging** for stable project versions

#### Backup Analytics
- **Change detection** - identify what changed between backups
- **Usage patterns** - analyze which projects are most active
- **Performance metrics** - track project complexity over time
- **Backup integrity checks** - verify data consistency

### 📋 Configuration Examples

#### Google Drive API Setup
```json
{
  "google_drive": {
    "service_account_file": "/path/to/service-account.json",
    "backup_folder_id": "1234567890abcdef",
    "download_timeout": 300,
    "max_retries": 3
  },
  "processing": {
    "split_combined_backups": true,
    "organize_by_category": true,
    "preserve_timestamps": true,
    "backup_verification": true
  }
}
```

#### Backup Schedule Configuration
```yaml
schedule:
  weekly_sync:
    day: "sunday"
    time: "02:00"
    timezone: "UTC"
  
  quick_check:
    interval: "4h"
    enabled: true
  
notifications:
  mqtt:
    broker: "localhost"
    topic: "madness/tasker/backups"
  
  email:
    enabled: false
    smtp_server: "smtp.gmail.com"
```

### 🚀 Getting Started

1. **Setup Google Drive API credentials**
2. **Configure backup folder permissions**
3. **Install Python dependencies**: `pip install -r requirements.txt`
4. **Configure settings** in `config/` directory
5. **Run initial sync**: `python tools/backup_manager.py --initial-sync`
6. **Setup automated scheduling**: `python tools/scheduler.py --start`

This system will transform your Tasker backup management from manual to fully automated, ensuring your projects are always up-to-date and properly organized!

## Projects

### [Verbatex](Verbatex/README.md)
Voice assistant integration and voice command systems for Tasker.

### [RunedManifold](RunedManifold/README.md)
User interface components and control systems for Tasker.

### [PhilosophersAmpoule](PhilosophersAmpoule/README.md)
Medication tracking and health management system using Tasker.

### [Ludomancery](Ludomancery/README.md)
Tasker automation for mobile games, including Raid Shadow Legends and Nexus.

### [Fragmentarium](Fragmentarium/README.md)
Clipboard management and text snippet tools for Tasker.

### [EntropyVector](EntropyVector/README.md)
Core subsystems for Android device management and state monitoring via Tasker.

### [ContextOfficium](ContextOfficium/README.md)
Tasker project for context-aware automation based on location (Home/Office) detection.

### [AnathemaHexVault](AnathemaHexVault/README.md)
A collection of legacy Tasker projects and configurations that have been archived for reference.

## Usage

Navigate to each project's directory and import the .prj.xml files into Tasker to use the automation.
