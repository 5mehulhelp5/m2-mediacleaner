# Changelog
All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.4] - 2024

### Added
- Automated orphaned media file cleanup system for `catalog/product` directory
- Console command `qoliber:media:clean` for manual media cleanup execution
- Dry-run mode (`--dry-run` flag) to preview deletable files without removing them
- Multi-store support via `--store_id` option (accepts multiple values like `--store_id=1 --store_id=2`)
- Database cross-reference check against `catalog_product_entity_media_gallery` table
- Two specialized cleaner implementations:
  - `ProductImage` - removes product images not referenced in media gallery table
  - `ProductImageCache` - optionally removes entire `catalog/product/cache` directory
- Configurable cache directory cleanup via admin system configuration
- Cron job automation for scheduled cleanup operations
- Dedicated cron group `qoliber_media_cleaner` for resource management
- ACL resource `Qoliber_MediaCleaner::config` for admin permission control
- Disk space recovery calculation showing:
  - Total files count
  - Total size in bytes and GB (formatted to 2 decimal places)
- Cleaner provider system using `CleanerInterface` for extensibility
- Store-specific cleaner execution with default store (ID=0) always included

### Technical Details
- Uses `RecursiveDirectoryIterator` and `RecursiveIteratorIterator` for file traversal
- Skips `/cache` and `/placeholder` subdirectories during scan
- Compares file paths against database values using `in_array()` strict mode
- `OutputInfo` model tracks cleaned files count and total size
- Implements `CleanerInterface` allowing third-party extensions to add custom cleaners
- `useOnlyInDefaultStore()` method ensures certain cleaners run only once (store ID 0)
- Database queries use `\Magento\Framework\DB\Select` for optimized media gallery lookups
- Follows Magento Filesystem API using `DirectoryList::MEDIA` constant
- Exception handling via `CleaningException` for error recovery
- Configuration stored in `etc/config.xml` and `etc/adminhtml/system.xml`
