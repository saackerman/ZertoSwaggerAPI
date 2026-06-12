# Zerto ZVM REST API Reference — v10.0 U6

## Overview

The Zerto RESTful API enables programmatic management of DR operations without the Zerto UI. All endpoints require Bearer token authentication via Keycloak.

**Base URL**: `https://<ZVM-HOST>:443/v1`  
**Auth**: POST to `/auth/realms/zerto/protocol/openid-connect/token` with `grant_type=password`, `client_id=zerto-client`  
**Format**: JSON (default), XML supported

---

## Authentication

### Get Bearer Token

```
POST /auth/realms/zerto/protocol/openid-connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=password&client_id=zerto-client&username=<user>&password=<pass>
```

**Response**: JSON with `access_token` field. Use as `Authorization: Bearer <token>` on all subsequent calls.

```powershell
$TokenBody = @{ grant_type='password'; client_id='zerto-client'; username=$User; password=$Pass }
$Token = (Invoke-RestMethod -Uri "https://$ZVM:443/auth/realms/zerto/protocol/openid-connect/token" -Method POST -Body $TokenBody -SkipCertificateCheck).access_token
$Headers = @{ Accept='application/json'; Authorization="Bearer $Token" }
```

---

## Sessions

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/zsspsessions` | List all ZSSP sessions |
| GET | `/v1/zsspsessions/{id}` | Get specific session |

---

## Alerts

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/alerts` | List alerts (filterable by date, VPG, level, entity) |
| GET | `/v1/alerts/{id}` | Get specific alert |
| POST | `/v1/alerts/{id}/dismiss` | Dismiss alert |
| POST | `/v1/alerts/{id}/undismiss` | Undismiss alert |
| GET | `/v1/alerts/levels` | List alert levels |
| GET | `/v1/alerts/entities` | List alert entity types |
| GET | `/v1/alerts/helpidentifiers` | List alert help IDs |

### Query Parameters (GET /v1/alerts)

| Param | Type | Description |
|-------|------|-------------|
| startDate | datetime | Filter start |
| endDate | datetime | Filter end |
| vpgIdentifier | string | Filter by VPG |
| siteIdentifier | string | Filter by site |
| level | string | Alert level |
| entity | string | Entity type |
| isDismissed | boolean | Dismissed status |

---

## Events

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/events` | List events (filterable) |
| GET | `/v1/events/{id}` | Get specific event |
| GET | `/v1/events/types` | List event types |
| GET | `/v1/events/entities` | List event entity types |
| GET | `/v1/events/categories` | List event categories |

### Query Parameters (GET /v1/events)

| Param | Type | Description |
|-------|------|-------------|
| startDate | datetime | Filter start |
| endDate | datetime | Filter end |
| vpgIdentifier | string | VPG filter |
| siteName | string | Site name |
| eventType | string | Event type |
| entityType | string | Entity type |
| category | string | Category |
| userName | string | User who triggered |

---

## Virtual Machines

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/vms` | List all protected VMs |
| GET | `/v1/vms/{vmId}` | Get specific VM details |
| GET | `/v1/vms/{vmId}/pointsInTime` | Get recovery checkpoints |
| GET | `/v1/vms/{vmId}/pointsInTime/stats` | Checkpoint statistics |
| POST | `/v1/vms/{vmId}/Restore` | Start VM restore from checkpoint |
| POST | `/v1/vms/{vmId}/RestoreCommit` | Commit a restore operation |
| POST | `/v1/vms/{vmId}/RestoreRollback` | Rollback a restore operation |
| GET | `/v1/statistics/vms` | VM replication statistics |

### Query Parameters (GET /v1/vms)

| Param | Type | Description |
|-------|------|-------------|
| vpgName | string | Filter by VPG name |

---

## VPGs (Virtual Protection Groups)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/vpgs` | List all VPGs |
| GET | `/v1/vpgs/{vpgId}` | Get VPG details |
| GET | `/v1/vpgs/{vpgId}/checkpoints` | List VPG checkpoints |
| GET | `/v1/vpgs/{vpgId}/checkpoints/stats` | Checkpoint statistics |
| POST | `/v1/vpgs/{vpgId}/FailoverTest` | Start failover test |
| POST | `/v1/vpgs/{vpgId}/FailoverTestStop` | Stop failover test |
| POST | `/v1/vpgs/{vpgId}/Failover` | Start live failover |
| POST | `/v1/vpgs/{vpgId}/FailoverCommit` | Commit failover |
| POST | `/v1/vpgs/{vpgId}/FailoverRollback` | Rollback failover |
| POST | `/v1/vpgs/{vpgId}/Move` | Start move operation |
| POST | `/v1/vpgs/{vpgId}/MoveCommit` | Commit move |
| POST | `/v1/vpgs/{vpgId}/moveRollback` | Rollback move |
| POST | `/v1/vpgs/{vpgId}/CloneStart` | Start clone |
| POST | `/v1/vpgs/{vpgId}/CloneAbort` | Abort clone |
| POST | `/v1/vpgs/{vpgId}/forcesync` | Force sync |
| POST | `/v1/vpgs/{vpgId}/pause` | Pause replication |
| POST | `/v1/vpgs/{vpgId}/resume` | Resume replication |

### VPG Lookup Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/vpgs/statuses` | List VPG status values |
| GET | `/v1/vpgs/substatuses` | List VPG sub-status values |
| GET | `/v1/vpgs/priorities` | List priority values |
| GET | `/v1/vpgs/entitytypes` | List entity types |
| GET | `/v1/vpgs/retentionpolicies` | List retention policies |
| GET | `/v1/vpgs/failovercommitpolicies` | List commit policies |
| GET | `/v1/vpgs/failovershutdownpolicies` | List shutdown policies |

---

## VPG Settings

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/v1/vpgSettings` | Create new VPG (returns settings ID) |
| GET | `/v1/vpgSettings/{id}` | Get VPG settings |
| PUT | `/v1/vpgSettings/{id}` | Update VPG settings |
| DELETE | `/v1/vpgSettings/{id}` | Delete VPG |
| POST | `/v1/vpgSettings/{id}/commit` | Commit VPG settings changes |
| GET | `/v1/vpgSettings/{id}/basic` | Get basic settings |
| GET | `/v1/vpgSettings/{id}/journal` | Get journal settings |
| GET | `/v1/vpgSettings/{id}/networks` | Get network settings |
| GET | `/v1/vpgSettings/{id}/recovery` | Get recovery settings |
| GET | `/v1/vpgSettings/{id}/vms` | List VMs in VPG settings |
| GET | `/v1/vpgSettings/{id}/vms/{vmId}` | Get VM settings |
| GET | `/v1/vpgSettings/{id}/vms/{vmId}/nics` | Get VM NIC settings |
| GET | `/v1/vpgSettings/{id}/vms/{vmId}/volumes` | Get VM volume settings |
| POST | `/v1/vpgSettings/copyVpgSettings` | Copy settings from existing VPG |

### Export/Import VPG Settings

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/v1/vpgSettings/exportSettings` | Export all VPG settings |
| GET | `/v1/vpgSettings/exportedSettings` | List exported settings files |
| POST | `/v1/vpgSettings/exportedSettings/{ts}` | Read exported settings by timestamp |
| GET | `/v1/vpgSettings/exportedSettings/{ts}/vpgsinfo` | List VPGs in exported file |
| POST | `/v1/vpgSettings/import` | Import VPG settings |

---

## VRAs (Virtual Replication Appliances)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/vras` | List all VRAs |
| GET | `/v1/vras/{vraId}` | Get specific VRA |
| POST | `/v1/vras/{vraId}/upgrade` | Upgrade VRA |
| GET | `/v1/vras/{vraId}/changerecoveryvra/potentials` | List potential recovery VRAs |
| GET | `/v1/vras/{vraId}/changerecoveryvra/recommendation` | Get recommended VRA |
| POST | `/v1/vras/{vraId}/changerecoveryvra/validate` | Validate VRA change |
| POST | `/v1/vras/{vraId}/changerecoveryvra/execute` | Execute VRA change |
| POST | `/v1/vras/cleanup` | Clean up stale VRAs |
| GET | `/v1/vras/clusters` | List VRA clusters |
| GET | `/v1/vras/clusters/{clusterId}` | Get cluster detail |
| GET | `/v1/vras/clusters/{clusterId}/settings` | Get cluster VRA settings |
| GET | `/v1/vras/ipconfigurationtypes` | List IP config types |
| GET | `/v1/vras/statuses` | List VRA statuses |

---

## Datastores

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/datastores` | List all datastores on current site |
| GET | `/v1/datastores/{id}` | Get specific datastore |

---

## Volumes

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/volumes` | List available volumes |

---

## Sites

### Local Site

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/localsite` | Get local site info |
| GET | `/v1/localsite/pairingstatuses` | List pairing status values |
| POST | `/v1/localsite/billing/sendUsage` | Send billing data |
| GET | `/v1/localsite/settings/loginBanner` | Get login banner |
| PUT | `/v1/localsite/settings/loginBanner` | Set login banner |

### Peer Sites

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/peersites` | List peer sites (filterable) |
| GET | `/v1/peersites/{siteId}` | Get peer site detail |
| POST | `/v1/peersites` | Pair to new site |
| DELETE | `/v1/peersites/{siteId}` | Unpair site |
| POST | `/v1/peersites/generatetoken` | Generate pairing token |
| GET | `/v1/peersites/pairingstatuses` | List status values |

### Virtualization Sites (Resources)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/virtualizationsites` | List all virtualization sites |
| GET | `/v1/virtualizationsites/{siteId}` | Get site detail |
| GET | `/v1/virtualizationsites/{siteId}/datastores` | Site datastores |
| GET | `/v1/virtualizationsites/{siteId}/datastoreclusters` | Datastore clusters |
| GET | `/v1/virtualizationsites/{siteId}/hosts` | Site hosts |
| GET | `/v1/virtualizationsites/{siteId}/hostclusters` | Host clusters |
| GET | `/v1/virtualizationsites/{siteId}/networks` | Site networks |
| GET | `/v1/virtualizationsites/{siteId}/folders` | Site folders |
| GET | `/v1/virtualizationsites/{siteId}/resourcepools` | Resource pools |
| GET | `/v1/virtualizationsites/{siteId}/repositories` | LTR repositories |
| GET | `/v1/virtualizationsites/{siteId}/vms` | VMs on site |
| GET | `/v1/virtualizationsites/{siteId}/devices` | Storage devices |

---

## Encryption Detection (Ransomware)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/encryptionDetection/suspected/vms` | VMs suspected of encryption |
| GET | `/v1/encryptionDetection/suspected/volumes` | Volumes suspected |
| GET | `/v1/encryptionDetection/suspected/vpgs` | VPGs suspected |
| GET | `/v1/encryptionDetection/metrics/vms` | VM encryption metrics |
| GET | `/v1/encryptionDetection/metrics/volumes` | Volume metrics |
| GET | `/v1/encryptionDetection/metrics/vpgs` | VPG metrics |
| POST | `/v1/encryptionDetection/dismissEvent` | Dismiss encryption event |
| GET | `/v1/encryptionDetection/state` | Get detection state |
| POST | `/v1/encryptionDetection/state` | Enable/disable detection |

---

## Extended Journal Copy (LTR)

### Catalog

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/ltr/catalog/vms` | List VMs in retention sets |
| GET | `/v1/ltr/catalog/fullretentionsets` | List full retention sets |
| GET | `/v1/ltr/catalog/fullretentionsets/{id}/incrementals` | Incremental sets |
| GET | `/v1/ltr/catalog/vms/{vmId}/retentionsets` | VM retention sets |
| GET | `/v1/ltr/catalog/vms/{vmId}/retentionsets/{setId}/settings` | VM original settings |
| POST | `/v1/ltr/catalog/deleteretentionsets` | Delete retention sets |

### Restore & Health

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/v1/ltr/restore/vm` | Restore VM from repository |
| GET | `/v1/ltr/health/vpgs` | LTR VPG health |
| POST | `/v1/ltr/vpgs/{vpgId}/retentionstart` | Start manual retention |
| POST | `/v1/ltr/vpgs/{vpgId}/retentionabort` | Abort retention |

### Repositories

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/ltr/repositories` | List all repositories |
| GET | `/v1/ltr/repositories/{id}` | Get repository by ID |
| POST | `/v1/ltr/repositories` | Create repository |
| PUT | `/v1/ltr/repositories/{id}` | Edit repository |
| DELETE | `/v1/ltr/repositories/{id}` | Delete repository |

---

## File-Level Restore (FLR)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/flrs` | List mounted volumes |
| POST | `/v1/flrs` | Mount disk (create FLR session) |
| GET | `/v1/flrs/{sessionId}` | Get mount session |
| DELETE | `/v1/flrs/{sessionId}` | Unmount disk |
| POST | `/v1/flrs/{sessionId}/browse` | Browse files/folders |
| POST | `/v1/flrs/{sessionId}/download` | Get download URL |

---

## Reports

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/reports/recovery` | Recovery operation reports |
| GET | `/v1/reports/recovery/{id}` | Specific recovery report |
| GET | `/v1/reports/resources` | VM resource reports |

---

## Recovery Scripts

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/recoveryScripts/output/files` | Output script files |
| GET | `/v1/recoveryScripts/output/folders` | Output folders |
| GET | `/v1/recoveryScripts/output/items` | Output items |
| GET | `/v1/recoveryScripts/repository/files` | Repository files |
| GET | `/v1/recoveryScripts/repository/folders` | Repository folders |
| GET | `/v1/recoveryScripts/repository/items` | Repository items |

---

## Tasks

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/tasks` | List tasks |
| GET | `/v1/tasks/{taskId}` | Get specific task |
| GET | `/v1/tasks/types` | List task types |

---

## Licensing

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/license` | Get license details |
| PUT | `/v1/license` | Add/update license |
| DELETE | `/v1/license` | Delete license |

---

## Service Profiles

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/serviceprofiles` | List service profiles |
| GET | `/v1/serviceprofiles/{id}` | Get specific profile |

---

## ZORGs (Zerto Organizations)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/zorgs` | List all ZORGs |
| GET | `/v1/zorgs/{zorgId}` | Get specific ZORG |

---

## Server Date-Time

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/v1/serverDateTime/serverDateTimeLocal` | ZVM local time |
| GET | `/v1/serverDateTime/serverDateTimeUtc` | ZVM UTC time |
| GET | `/v1/serverDateTime/dateTimeArgument` | Expected datetime format |

---

## PowerShell Quick Reference

```powershell
# Authenticate
$TokenBody = @{ grant_type='password'; client_id='zerto-client'; username='user'; password='pass' }
$Token = (Invoke-RestMethod -Uri "https://zvm.example.com:443/auth/realms/zerto/protocol/openid-connect/token" -Method POST -Body $TokenBody -SkipCertificateCheck).access_token
$H = @{ Accept='application/json'; Authorization="Bearer $Token" }

# List all VMs
Invoke-RestMethod -Uri 'https://zvm.example.com:443/v1/vms' -Headers $H -SkipCertificateCheck

# List all VPGs
Invoke-RestMethod -Uri 'https://zvm.example.com:443/v1/vpgs' -Headers $H -SkipCertificateCheck

# Get VMs in specific VPG
$Encoded = [System.Uri]::EscapeDataString('My VPG Name')
Invoke-RestMethod -Uri "https://zvm.example.com:443/v1/vms?vpgName=$Encoded" -Headers $H -SkipCertificateCheck

# Start failover test
Invoke-RestMethod -Uri "https://zvm.example.com:443/v1/vpgs/$VpgId/FailoverTest" -Method POST -Headers $H -Body $Body -ContentType 'application/json' -SkipCertificateCheck
```
