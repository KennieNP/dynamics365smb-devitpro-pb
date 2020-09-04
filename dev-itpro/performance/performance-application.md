---
title: "How Application Configurations Affect Performance"
ms.custom: na
ms.date: 04/01/2020
ms.reviewer: solsen
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: article
ms.service: "dynamics365-business-central"
author: KennieNP
---

# How Application Configurations Affect Performance

The topics in this section are tips and tricks on how to set up [!INCLUDE[prodshort](../developer/includes/prodshort.md)] for performance and also describe how in-product configurations affect the performance of [!INCLUDE[prodshort](../developer/includes/prodshort.md)].

## Un-install extensions that you do not use
The extensions you have installed can affect the overall system performance. So if you have installed an app from AppSource but later discovers that it doesn’t solve your business needs, then uninstall it. The same advice applies to the extension that come preinstalled on your environment. For example, do uninstall all migration extension once you have migrated data, or if you do not intend to migrate data.

## Run things in the background

It's often desirable to offload work from the user session to happen in the background. Examples are:

- [Schedule long running reports to run in background](/dynamics365/business-central/ui-work-report#ScheduleReport)
- Schedule jobs (for example posting) to run in background
- Enable background posting in areas where your business is using reservations and item tracking using serial and lot numbers
- Adjust item costs as a periodic background job. Don't adjust automatically. 

> [!TIP]  
> don't run job queues too frequently.

## Avoid locking

When the [!INCLUDE[prodshort](../developer/includes/prodshort.md)] database needs to have exclusive access to a table or a data row, it will issue a lock. If another session needs to access a locked resource, it needs to wait until the session holding the lock is finished with its work. There are a few places in the [!INCLUDE[prodshort](../developer/includes/prodshort.md)] application where you can reduce the risk of locking. 

### Use number series that allow gaps

Number series in [!INCLUDE[prodshort](../developer/includes/prodshort.md)] are shared resources that sometimes cause locking issues. Not all records that you create in [!INCLUDE[prodshort](../developer/includes/prodshort.md)] are financial transactions that must use sequential numbering. Customer cards, sales quotes, and warehouse activities are examples of records that are assigned a number from a number series. They aren't subject to financial auditing and can be deleted. For all such number series, consider using number series that allow gaps to avoid locking issues. For more information, see [Gaps in Number Series](/dynamics365/business-central/ui-create-number-series#gaps-in-number-series).

### Be cautious with the **Rename/Copy company** operations

The **Rename company** and **Copy company** operations are not intended to run while business transactions are being applied to [!INCLUDE[prodshort](../developer/includes/prodshort.md)]. First, the operations are likely to induce locks on the tables that data is copied from. These locks will block users from transacting in the company. Second, the operations use resources on the database, which can in turn cause resource starvation for users working in other companies.  

If you must do a **Rename/Copy company** operation, it's highly recommended to do it outside working hours. Turn off scheduled jobs to avoid locking issues.

## Periodic activities that maintain performance

Block inactive customers, vendors, or items to improve filtering and searching on document data entry

- [Block Customers](/dynamics365/business-central/receivables-how-block-customers)  
- [Block Vendors](/dynamics365/business-central/payables-how-block-vendors)  
- [Block Items from Sales or Purchasing](/dynamics365/business-central/inventory-how-block-items)  

## Performance effect of enabling integration on a table

There's a performance overhead involved in enabling integration on an entity such as **Customer** or **Contact**. Only enable integration if you intend to integrate with Dynamics 365 Sales. Enable it only on the required entities. 

For more information, see [Synchronizing Data in Business Central and Dynamics 365 Sales](/dynamics365/business-central/admin-synchronizing-business-central-and-sales). <!-- change with CDS integration in spring 2020 -->

## Functionality with known performance impact

These areas of the application are known to cause a performance impact and require extra testing with realistic data setup before they're rolled out. 

- [Security filtering mode](../security/security-filters.md#PerformanceImpact)  
- [Inventory Posting](/dynamics365/business-central/design-details-inventory-posting)  
- [Dimensions](/dynamics365/business-central/finance-dimensions)  
- [Dynamic Order tracking](/dynamics365/business-central/design-details-reservation-order-tracking-and-action-messaging)  
- [Automatic reservation](/dynamics365/business-central/design-details-reservation-order-tracking-and-action-messaging)  
- [Item tracking and Lot/SN Expiration dates](/dynamics365/business-central/inventory-how-work-item-tracking)  
- [Change log](/dynamics365/business-central/across-log-changes)  

## Manage the database access intent on reports, API pages, and queries

[!INCLUDE[prodshort](../developer/includes/prodshort.md)] supports the **Read Scale-Out** feature in Azure SQL Database and SQL Server to load-balance analytical workloads. **Read Scale-Out** is built in to [!INCLUDE[prodshort](../developer/includes/prodshort.md)] online, but it can also be enabled for on-premises.

**Read Scale-Out** applies to queries, reports, or API pages. With these objects, instead of sharing the primary, they can be set up to run against a read-only replica. This setup   essentially isolates them from the main read-write workload. This way, they won't affect the performance of business processes.

A drawback of reading from a replica is that it introduces a slight delay compared to reading from the primary database. **Read Scale-Out** is controlled by the [DataAccessControl property](../developer/properties/devenv-dataaccessintent-property.md) on objects. This property determines whether to use a replica if one is available. If this delay isn't acceptable for an object, you can overwrite the default database access intent from the UI. For more information, see [Managing Database Access Intent](/dynamics365/business-central/admin-data-access-intent).

## Don't do these things

Finally, make sure that you don't repeat these performance mistakes that we have seen cause massive performance issues for customers:

- Don't adjust cost item entries with a high frequency.
- Don't set up a change log for everything. For more information, see [Auditing Changes in Business Central](/dynamics365/business-central/across-log-changes).  
- Don't run job queues too frequently.
- Don't adjust item costs automatically if you have many item entries. Run in the background instead.  
- Don't postpone setting up global dimensions, because it can be a heavy operation when you have much data. Set up correct global dimensions to avoid changing them later on.
- Don't run the **Copy company** operation during business hours.


## See Also

[Performance Overview](performance-overview.md)  
[Performance Topics For Developers](performance-developer.md)  
[Performance tips for business users](performance-users.md)  
[Performance Online](performance-online.md)  
[Performance of On-Premises Installations](performance-onprem.md)  
[How to Work with a Performance Problem](performance-work-perf-problem.md)  
