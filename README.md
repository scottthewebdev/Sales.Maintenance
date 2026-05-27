# Sales.Maintenance
Sales.Maintenance.Services contains background and operational services for the Sales backend.

Approximate size

- Lines of code: ≈ 420 LOC (hosted services, workers, utility helpers, configuration models, and integration adapters). This is an estimate and may change as the project evolves.

Key features

- Hosted background workers for maintenance tasks:
  - DataArchiveWorker: moves old data to archive storage and creates export packages
  - RetentionPurgeWorker: enforces retention policies and purges expired records
  - IndexRebuildWorker: scheduled index rebuilds/optimization for search stores
  - CacheWarmupWorker: pre-populates caches after deployments or restarts
  - HealthCheckReporter: periodic health aggregation and reporting
- Scheduling abstraction to drive intervals either via CRON expressions or fixed intervals
- Resiliency patterns: graceful shutdown, retries, and structured logging
- Pluggable adapters for storage, archive providers, and notification sinks
- Integration hooks for telemetry and alerting

Services and components

- DataArchiveWorker: orchestrates archive operations using ArchiveService and IArchiveRepository
- RetentionPurgeWorker: scans retention metadata and issues delete/cleanup operations
- WorkerScheduler: common scheduling and lifecycle management for hosted services
- MaintenanceOptions: configuration POCO for worker scheduling and thresholds
- Adapters: IStorageAdapter, INotificationSink, IArchiveAdapter
- Utilities: time helpers, safe-run wrappers, and backoff helpers

Configuration

Typical configuration (appsettings.json):

- Maintenance:Workers:DataArchive:Interval - e.g., "00:30:00" or CRON
- Maintenance:Workers:RetentionPurge:Enabled - true/false
- Maintenance:Archive:ExportPath - filesystem or blob path for exported archives
- Maintenance:Retention:DefaultDays - retention threshold for items
- Logging and telemetry settings for monitoring worker runs

Running and building

Requirements: .NET 11 SDK

Build:
- dotnet build Sales.Maintenance.Services

Run as part of host app:
- Register hosted services in the host's DI in Program.cs:
  services.AddHostedService<DataArchiveWorker>();
  services.AddHostedService<RetentionPurgeWorker>();

Testing

- Add unit tests for worker orchestration and retention logic in a sibling test project
- Use dependency injection to provide lightweight in-memory adapters for fast tests

Operational notes

- Ensure workers run with appropriate permissions for archive/export storage
- Use feature flags or configuration toggles when enabling purge operations in production
- Monitor worker durations and failures via telemetry; consider rate-limiting to avoid IO bursts

Extensibility

- Add new workers for custom maintenance flows (data migration, schema backfill)
- Implement provider-specific archive adapters (Azure Blob, AWS S3, network file share)
- Integrate with orchestration platforms or Kubernetes CronJobs for additional operational control

Contributing

- Follow repository coding conventions and add tests for new behaviors. Prefer small, reviewable PRs.

License

- Refer to the repository license at the project root.
