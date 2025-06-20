# fluentcron

[![Latest Release](https://gitlab.com/thelabnyc/fluentcron/-/badges/release.svg)](https://gitlab.com/thelabnyc/fluentcron/-/releases)
[![pipeline status](https://gitlab.com/thelabnyc/fluentcron/badges/master/pipeline.svg)](https://gitlab.com/thelabnyc/fluentcron/-/commits/master)
[![coverage report](https://gitlab.com/thelabnyc/fluentcron/badges/master/coverage.svg)](https://gitlab.com/thelabnyc/fluentcron/-/commits/master)

A fluent interface for constructing crontab schedules in Python. Build readable, type-safe cron expressions without memorizing cron syntax.

## Features

- **Fluent API**: Chain methods to build schedules naturally
- **Type Safety**: Full type hints and validation with Python 3.13+
- **Zero Dependencies**: Uses only Python standard library
- **Immutable**: Schedule objects are immutable and hashable
- **Readable**: Self-documenting code that's easy to understand
- **Flexible**: Support for complex schedules and common presets

## Installation

```bash
pip install fluentcron
```

Requires Python 3.13 or higher.

## Quick Start

```python
from fluentcron import CronSchedule

# Daily at 5:00 AM
schedule = CronSchedule().daily().at(5, 0).to_str()
print(schedule)  # "0 5 * * *"

# Weekly on Monday at 5:00 AM
schedule = CronSchedule().weekly().on_monday().at(5, 0).to_str()
print(schedule)  # "0 5 * * 1"

# Every 30 minutes
schedule = CronSchedule().every_n_minutes(30).to_str()
print(schedule)  # "*/30 * * * *"

# Monthly on the 1st at 5:00 AM
schedule = str(CronSchedule().monthly().on_day(1).at(5, 0))
print(schedule)  # "0 5 1 * *"
```

## API Reference

### CronSchedule Class

The main class for building cron expressions using a fluent interface.

#### Time Methods

##### `at(hour, minute=0)`

Set the specific time to run.

```python
# Daily at 8:30 AM
CronSchedule().daily().at(8, 30)  # "30 8 * * *"

# Daily at midnight (minute defaults to 0)
CronSchedule().daily().at(0)      # "0 0 * * *"
```

- `hour`: 0-23 (required)
- `minute`: 0-59 (optional, defaults to 0)

#### Frequency Methods

##### `daily()`

Run every day.

```python
CronSchedule().daily().at(9)  # "0 9 * * *"
```

##### `weekly()`

Run weekly. Combine with weekday methods.

```python
CronSchedule().weekly().on_friday().at(17)  # "0 17 * * 5"
```

##### `monthly()`

Run monthly. Combine with `on_day()`.

```python
CronSchedule().monthly().on_day(15).at(12)  # "0 12 15 * *"
```

#### Interval Methods

##### `every_n_minutes(n)`

Run every N minutes.

```python
CronSchedule().every_n_minutes(15)  # "*/15 * * * *"
CronSchedule().every_n_minutes(1)   # "* * * * *"
```

- `n`: 1-59

##### `every_n_hours(n)`

Run every N hours.

```python
CronSchedule().every_n_hours(6)   # "* */6 * * *"
CronSchedule().every_n_hours(1)   # "* * * * *"
```

- `n`: 1-23

#### Weekday Methods

##### Named Weekday Methods

```python
CronSchedule().weekly().on_sunday().at(10)     # "0 10 * * 0"
CronSchedule().weekly().on_monday().at(10)     # "0 10 * * 1"
CronSchedule().weekly().on_tuesday().at(10)    # "0 10 * * 2"
CronSchedule().weekly().on_wednesday().at(10)  # "0 10 * * 3"
CronSchedule().weekly().on_thursday().at(10)   # "0 10 * * 4"
CronSchedule().weekly().on_friday().at(10)     # "0 10 * * 5"
CronSchedule().weekly().on_saturday().at(10)   # "0 10 * * 6"
```

##### `on_weekday(weekday)`

Set weekday using number or string.

```python
# Using numbers (0=Sunday, 1=Monday, ..., 6=Saturday)
CronSchedule().weekly().on_weekday(1).at(9)  # "0 9 * * 1"

# Using strings (case-insensitive)
CronSchedule().weekly().on_weekday("monday").at(9)    # "0 9 * * 1"
CronSchedule().weekly().on_weekday("FRIDAY").at(17)   # "0 17 * * 5"
CronSchedule().weekly().on_weekday("tue").at(14)      # "0 14 * * 2"
```

Supported string values:
- Full names: `"sunday"`, `"monday"`, `"tuesday"`, `"wednesday"`, `"thursday"`, `"friday"`, `"saturday"`
- Short names: `"sun"`, `"mon"`, `"tue"`, `"wed"`, `"thu"`, `"fri"`, `"sat"`
- Case-insensitive: `"MONDAY"`, `"Mon"`, `"MON"` all work

#### Day Methods

##### `on_day(day)`

Set the day of the month (1-31).

```python
CronSchedule().monthly().on_day(1).at(0)   # "0 0 1 * *"  - 1st of month
CronSchedule().monthly().on_day(15).at(12) # "0 12 15 * *" - 15th of month
```

#### Output Methods

##### `to_str()` / `str()`

Convert to cron expression string.

```python
schedule = CronSchedule().daily().at(9)
print(schedule.to_str())  # "0 9 * * *"
print(str(schedule))      # "0 9 * * *"
```

### Convenience Functions

For common schedules, use these shortcut functions that return strings directly:

```python
from fluentcron import daily_at, weekly_on, monthly_on_day, every_n_minutes, every_n_hours

# Quick shortcuts
daily_at(9)            # "0 9 * * *"
daily_at(8, 30)        # "30 8 * * *"
weekly_on("monday", 9) # "0 9 * * 1"
weekly_on(1, 9, 30)    # "30 9 * * 1"
monthly_on_day(1, 9)   # "0 9 1 * *"
every_n_minutes(15)    # "*/15 * * * *"
every_n_hours(6)       # "* */6 * * *"
```

### Common Schedules

Pre-built schedules for typical use cases:

```python
from fluentcron import CommonSchedules

# Use predefined common schedules
print(CommonSchedules.EVERY_MINUTE)         # "* * * * *"
print(CommonSchedules.EVERY_5_MINUTES)      # "*/5 * * * *"
print(CommonSchedules.EVERY_15_MINUTES)     # "*/15 * * * *"
print(CommonSchedules.EVERY_30_MINUTES)     # "*/30 * * * *"
print(CommonSchedules.EVERY_HOUR)           # "0 * * * *"
print(CommonSchedules.EVERY_2_HOURS)        # "0 */2 * * *"
print(CommonSchedules.EVERY_6_HOURS)        # "0 */6 * * *"
print(CommonSchedules.EVERY_12_HOURS)       # "0 */12 * * *"
print(CommonSchedules.DAILY_MIDNIGHT)       # "0 0 * * *"
print(CommonSchedules.DAILY_NOON)           # "0 12 * * *"
print(CommonSchedules.WEEKLY_SUNDAY_MIDNIGHT)  # "0 0 * * 0"
print(CommonSchedules.WEEKLY_MONDAY_MIDNIGHT)  # "0 0 * * 1"
print(CommonSchedules.MONTHLY_FIRST_MIDNIGHT)  # "0 0 1 * *"
print(CommonSchedules.YEARLY_JAN_FIRST)     # "0 0 1 1 *"
```

## Examples

### Basic Schedules

```python
from fluentcron import CronSchedule

# Every day at 6:00 AM
backup_schedule = CronSchedule().daily().at(6, 0)

# Every Monday at 9:00 AM
weekly_meeting = CronSchedule().weekly().on_monday().at(9, 0)

# 1st of every month at midnight
monthly_report = CronSchedule().monthly().on_day(1).at(0, 0)

# Every 15 minutes
health_check = CronSchedule().every_n_minutes(15)

# Every 4 hours
log_rotation = CronSchedule().every_n_hours(4)
```

### Business Hours Examples

```python
# Workday morning standup: Monday-Friday at 9:00 AM
# Note: For multiple weekdays, you'd need separate schedules
monday_standup = CronSchedule().weekly().on_monday().at(9, 0)
tuesday_standup = CronSchedule().weekly().on_tuesday().at(9, 0)
# ... etc for each day

# End of business day: Friday at 5:00 PM
eod_friday = CronSchedule().weekly().on_friday().at(17, 0)

# Weekly team lunch: Wednesday at 12:30 PM
team_lunch = CronSchedule().weekly().on_wednesday().at(12, 30)
```

### Maintenance Schedules

```python
# Database backup: Every day at 2:00 AM
db_backup = CronSchedule().daily().at(2, 0)

# Log cleanup: Sunday at 3:00 AM
log_cleanup = CronSchedule().weekly().on_sunday().at(3, 0)

# System updates: 1st Sunday of month at 4:00 AM
# (Note: This would be "0 4 1-7 * 0" in cron, requiring custom logic)
monthly_updates = CronSchedule().monthly().on_day(1).at(4, 0)  # Simplified version

# Certificate renewal check: 1st of each month at 1:00 AM
cert_check = CronSchedule().monthly().on_day(1).at(1, 0)
```

## Type Safety

This library provides full type safety with Python 3.13+ type hints:

```python
from fluentcron import CronSchedule, Hour, Minute, Weekday

# Type-safe parameters
hour: Hour = 9        # Valid: 0-23
minute: Minute = 30   # Valid: 0-59
weekday: Weekday = 1  # Valid: 0-6 or weekday strings

# This will show type errors in your IDE
schedule = CronSchedule().at(25, 0)     # Error: hour must be 0-23
schedule = CronSchedule().at(9, 65)     # Error: minute must be 0-59
schedule = CronSchedule().on_day(32)    # Error: day must be 1-31
```

## Advanced Usage

### Immutability & Hashability

`CronSchedule` objects are immutable and hashable, making them safe to use as dictionary keys or in sets:

```python
from fluentcron import CronSchedule

# Create schedules
daily_backup = CronSchedule().daily().at(2, 0)
weekly_report = CronSchedule().weekly().on_monday().at(9, 0)

# Use as dictionary keys
schedule_descriptions = {
    daily_backup: "Daily database backup",
    weekly_report: "Weekly status report",
}

# Use in sets
important_schedules = {daily_backup, weekly_report}

# Equality works as expected
same_schedule = CronSchedule().daily().at(2, 0)
assert daily_backup == same_schedule
```

### Serialization

Convert schedules to/from dictionaries for storage:

```python
schedule = CronSchedule().weekly().on_friday().at(17, 30)

# Convert to dictionary
schedule_dict = schedule._asdict()
# {'minute': '30', 'hour': '17', 'day': '*', 'month': '*', 'weekday': '5'}

# Recreate from dictionary
restored_schedule = CronSchedule(**schedule_dict)
assert schedule == restored_schedule
```

### Validation

The library validates inputs and provides helpful error messages:

```python
from fluentcron import CronSchedule

try:
    CronSchedule().at(25, 0)  # Invalid hour
except ValueError as e:
    print(e)  # "Hour must be between 0 and 23"

try:
    CronSchedule().on_weekday("invalid")  # Invalid weekday
except ValueError as e:
    print(e)  # "Invalid weekday name"
```

## Cron Expression Reference

For reference, cron expressions have 5 fields:
```
* * * * *
│ │ │ │ │
│ │ │ │ └─── Day of week (0-6, Sunday=0)
│ │ │ └───── Month (1-12)
│ │ └─────── Day of month (1-31)
│ └───────── Hour (0-23)
└─────────── Minute (0-59)
```

This library generates standard cron expressions compatible with most cron implementations.

## Contributing

Contributions are welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Add tests for new functionality
4. Ensure all tests pass: `tox`
5. Submit a pull request

### Development Setup

```bash
# Clone the repository
git clone https://gitlab.com/thelabnyc/fluentcron.git
cd fluentcron

# Install development dependencies
uv install

# Run tests
tox
```

## License

This project is licensed under the ISC License. See the [LICENSE](LICENSE) file for details.
