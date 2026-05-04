---
description: Stream events from n8n to your logging tools.
contentType: howto
---

# Log streaming

/// info | Feature availability
Log Streaming is available on all Enterprise plans.
///

Log streaming allows you to send events from n8n to your own logging tools. This allows you to manage your n8n monitoring in your own alerting and logging processes.

## Set up log streaming

To use log streaming, you have to add a streaming destination.

1. Navigate to **Settings** > **Log Streaming**.
2. Select **Add new destination**.
3. Choose your destination type. n8n opens the **New Event Destination** modal.
4. In the **New Event Destination** modal, enter the configuration information for your event destination. These depend on the type of destination you're using.
5. Select **Events** to choose which events to stream.
6. Select **Save**.

/// note | Self-hosted users
If you self-host n8n, you can configure additional log streaming behavior using [Environment variables](/hosting/configuration/environment-variables/logs.md#log-streaming).
///

## Per-process event log files

n8n persists each emitted event to a local log file before forwarding it to streaming destinations. The file survives restarts and lets n8n re-emit events that weren't yet delivered.

By default, n8n writes the event log to `<n8n-user-folder>/n8nEventLog.log`, with a `-worker` or `-webhook-processor` suffix on those processes. When a single n8n process owns the file, this default works as expected.

/// warning | Shared writable filesystems
If multiple n8n processes share one writable volume, for example [queue mode](/hosting/scaling/queue-mode.md) workers backed by a shared persistent volume on NFS or EFS, they must not write to the same event log file. Concurrent appends from multiple processes can interleave or corrupt the file, leading to recovery failures and lost events.
///

To avoid this, set [`N8N_EVENTBUS_LOGWRITER_LOGFULLPATH`](/hosting/configuration/environment-variables/logs.md#log-streaming) on each process to a unique absolute path that ends in `.log`. n8n uses the configured path verbatim and doesn't append a process-type suffix, so your orchestrator owns uniqueness across processes.

The companion variable [`N8N_EVENTBUS_LOGWRITER_MAXTOTALMESSAGESPERFILE`](/hosting/configuration/environment-variables/logs.md#log-streaming) bounds how many lines n8n parses from a single event log file during recovery, so a corrupted file can't exhaust process memory.

Notes:

* Default behavior is unchanged when `N8N_EVENTBUS_LOGWRITER_LOGFULLPATH` isn't set.
* When the variable is set, n8n doesn't auto-suffix the path. Each process must receive its own value.
* If a shared `n8nEventLog-worker.log` file already exists from a previous deployment, quarantine it manually before opting in. n8n doesn't auto-delete legacy files.

## Events

The following events are available. You can choose which events to stream in **Settings** > **Log Streaming** > **Events**.

* Workflow
	* Started
	* Success
	* Failed
	* Cancelled
* Node executions
	* Started
	* Finished
* Audit
	* User login success
	* User login failed
	* User signed up
	* User updated
	* User deleted
	* User invited
	* User invitation accepted
	* User re-invited
	* User email failed
	* User reset requested
	* User reset
	* User credentials created
	* User credentials shared
	* User credentials updated
	* User credentials deleted
	* User API created
	* User API deleted
	* User MFA enabled
	* User MFA disabled
	* User execution deleted
	* Execution data revealed
	* Execution data reveal failed
	* Package installed
	* Package updated
	* Package deleted
	* Workflow created
	* Workflow deleted
	* Workflow updated
	* Workflow archived
	* Workflow unarchived
	* Workflow activated
	* Workflow deactivated
	* Workflow version updated
    * Workflow executed
	* Workflow waiting
	* Workflow resumed
	* Variable created
	* Variable updated
	* Variable deleted
	* External secrets provider settings saved
	* External secrets provider reloaded
	* Personal publishing restricted enabled
	* Personal publishing restricted disabled
	* Personal sharing restricted enabled
	* Personal sharing restricted disabled
	* 2FA enforcement enabled
	* 2FA enforcement disabled
* Worker
	* Started
	* Stopped
* AI node logs
	* Memory get messages
	* Memory added message
	* Output parser parsed
	* Retriever get relevant documents
	* Embeddings embedded document
	* Embeddings embedded query
	* Document processed
	* Text splitter split
	* Tool called
	* Vector store searched
	* LLM generated
	* LLM error
	* Vector store populated
	* Vector store updated
* Runner
	* Task requested
	* Response received
* Queue
	* Job enqueued
	* Job dequeued
	* Job completed
	* Job failed
	* Job stalled

## Destinations

n8n supports three destination types:

* A syslog server
* A generic webhook
* A Sentry client
