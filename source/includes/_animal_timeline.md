# Animal Timeline

Animal Timeline holds events concerning cows (*Animals*, as a general term for farm stock)
and herds (*Groups*, as a general term for collections of animals).

The events concerning these are as follows:

* Membership of an Animal in a Group for a period (start date, and possibly stop date).
* Lactation event for an Animal for a period (start date, and possibly stop date).
* Health event for an Animal (condition and condition category, treatment (product) and category, and withholding information).

For a given Group, two reports are available for the Animals that are in that Group over specific periods:

* Cows in Milk: For the current season and the previous season:
  * For each day of that period:
    * How many Animals are in the Group that day and how many of them are in milk.
* Herd Health: For the today and for the current season:
  * Total number of health events for that period.
  * Number of repeat offenders for that period (the number of Animals that have had more than one health event).
  * For each distinct condition category that arises in that period:
    * Details of the condition category.
    * How many health events have occurred.
    * The number of hours when Animals were not contributing milk to the vat.
    * The number of days when Animals were not contributing milk to the vat.

## Resource

A user (such as through Agrigate) is only able to access those two reports for herds that they have rights to access.
The following API shows what data is visible to such a user.

## Farm Performance API

### Group

Access to the reports for a Group are provided on the Group, as illustrated.
This follows the REST hypermedia microformat documented above.

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/group/1"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/"
        },
        {
            "rel": "cows-in-milk-report",
            "href": "http://localhost:3004/animal-timeline-service/group/1/cowsInMilkReport"
        },
        {
            "rel": "herd-health-report",
            "href": "http://localhost:3004/animal-timeline-service/group/1/herdHealthReport"
        }
    ],
    "animalGroupId": "5000000",
    "dateOfInitialOnFarmEvent": "2016-04-30T12:00:00+00:00",
    "_type": "Group"
}
```

### Herd Health Report

To request this report, a date is provided (in UTC format).
This date may be today, Or it could be in the past, in which case the "current" season and "today"
are relative to that time.
For example, if the date provided is 2 July 2014, the "current" season will be 1 June 2014 upto 1 June 2015, and
"today" will be 2 July 2014 up to (but not including) 3 July 2014.

The resulting report is provided in the network of data, with links between the various parts of the report.

Here's an example of the top-level of the report:

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/group/1"
        },
        {
            "rel": "periods",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period"
        },
        {
            "rel": "herd-health-docs",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/docs/report"
        }
    ],
    "dateOfReportCreation": "2017-01-20T03:58:23+00:00",
    "dimension": "periods",
    "completed": true,
    "_type": "Report",
    "_kind": "HerdHealthReport",
    "selection": {
        "selection": "Individual Herd",
        "animalGroupId": "5000000"
    }
}
```

Things to note about this:

+ *dateOfReportCreation* is the date-time of when the report was generated.
+ *dimension* specifies that the sub-parts of the report are periods.
+ *completed* is true once the report generation is complete.

Reports involving thousands of animals may take several seconds to complete.

In the meantime, the client can display the data as it is created.

+ *_type* specifies that this representation is for a Report.
+ *_kind* gives the specific type of report.
+ *selection* specifies whether an individual herd was selected for the report
(this is the only option currently: benchmarking may later allow for herds within a selected circle to be handled together).
+ The *up* link refers to the group when the selection is for an individual herd.
+ The *periods* link is to a collection of sub-reports for each of the periods of the report.
+ The *herd-health-docs* link is to documentation about this report.

Here's an example of part of the report for a period, the current season:

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period/1"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period"
        },
        {
            "rel": "conditions",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period/1/conditionCategory"
        },
        {
            "rel": "herd-health-period-docs",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/docs/period"
        }
    ],
    "periodName": "Current Season",
    "dateRange": "2016-05-31T12:00:00+00:00/2017-05-31T12:00:00+00:00",
    "dimension": "conditions",
    "totalHealthEventsCount": 60,
    "repeatOffendersCount": 0,
    "_type": "Report",
    "_kind": "HerdHealthPeriodReport"

}
```

Things to note about this:

+ *dateRange* gives the range of dates for the period.
+ *totalHealthEventsCount* is the total number of health events (other than 'DRY COW THERAPIES') that occurred within that period
+ *repeatOffendersCount* is the number of animals that had more than one health event within the period.
+ The *up* link refers to the top-level of the report.
+ The *conditions* link refers to a collection of sub-reports related to each of the condition categories that arose in that period.
+ The *herd-health-period-docs* link is to documentation about this part of the report.

Here's an example of part of the report for a condition that occurred within a given period:

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period/1/conditionCategory/1"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/dB4yoV1brQ/period/1/conditionCategory"
        },
        {
            "rel": "herd-health-condition-docs",
            "href": "http://localhost:3004/animal-timeline-service/herdHealthReport/docs/conditionCategory"
        }
    ],
    "conditionCategory": "Masticillin Rtu Injection",
    "eventsCount": 20,
    "numberOfHoursOutOfVat": 200,
    "numberOfDaysOutOfVat": 20,
    "type_": "Report",
    "kind_": "ConditionCategoryReport"
}
```

Things to note about this:

+ *conditionCategory* gives the category name of a condition that occurred one or more times during the period.
+ *eventsCount* is the number of health events of that condition category that occurred within that period
+ *numberOfHoursOutOfVat* is the number of hours that an animal did not contribute milk due to the health event.
+ *numberOfDaysOutOfVat* is the number of days that an animal did not contribute milk due to the health event.

### Cows in Milk Report

To request this report, a date is provided (in UTC format), as with the herd health report above.
The "current" season and the "previous" season
are relative to the given date.
For example, if the date provided is 2 July 2014, the "current" season will be 1 June 2014 upto 1 June 2015, and the
"previous" season will be 1 June 2013 up to (but not including) 1 June 2014.

Here's an example of the top-level of this report:

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/group/1"
        },
        {
            "rel": "periods",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period"
        },
        {
            "rel": "cows-in-milk-docs",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/docs/report"
        }
    ],
    "dateOfReportCreation": "2017-01-23T03:09:36+00:00",
    "dimension": "periods",
    "completed": true,
    "processingTime": {
        "value": 647,
        "units": "milliseconds"
    },
    "_type": "Report",
    "_kind": "CowsInMilk",
    "selection": {
        "selection": "Individual Herd",
        "animalGroupId": "5000000"
    }
}
```

This is similar to the first report.

Here's an example of part of the report for a period, the current season:

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period/0"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period"
        },
        {
            "rel": "days",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period/0/day"
        },
        {
            "rel": "cows-in-milk-period-docs",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/docs/period"
        }
    ],
    "periodName": "Current Season",
    "dateOfReportCreation": "2017-01-23T03:09:36+00:00",
    "dateRange": "2016-05-31T12:00:00+00:00/2017-05-31T12:00:00+00:00",
    "dimension": "days",
    "_type": "Report",
    "_kind": "PeriodCowsReport"

}
```

And here's an example of a day within a period within the larger report (there is one for each day pf the period):

```json
{
    "links": [
        {
            "rel": "self",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period/0/day/0"
        },
        {
            "rel": "up",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/VPbyr3Jxjm/period/0/day"
        },
        {
            "rel": "cows-in-milk-day-docs",
            "href": "http://localhost:3004/animal-timeline-service/cowsInMilk/docs/day"
        }
    ],
    "totalCount": 200,
    "inMilkCount": 200,
    "type_": "Report",
    "kind_": "DailyCowsReport",
    "day": "2016-05-31T12:00:00+00:00"
}
```

Things to note about this:

+ *totalCount* is the number of cows in the herd on that day.
+ *inMilkCount* is the number of cows in the herd that are supplying milk on that day.
