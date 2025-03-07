---
title: 'Create a search service in the portal'
titleSuffix: Azure Cognitive Search
description: Learn how to set up an Azure Cognitive Search resource in the Azure portal. Choose resource groups, regions, and the SKU or pricing tier.

manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2022
---

# Create an Azure Cognitive Search service in the portal

[**Azure Cognitive Search**](search-what-is-azure-search.md) is an Azure resource used for adding a full text search experience to custom apps.

You can create search service using the [Azure portal](https://portal.azure.com/), which is covered in this article. You can also use [Azure PowerShell](search-manage-powershell.md), [Azure CLI](/cli/azure/search), the [Management REST API](/rest/api/searchmanagement/), an [Azure Resource Manager service template](https://azure.microsoft.com/resources/templates/azure-search-create/), or a [Bicep file](search-get-started-bicep.md).

[![Animated GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## Before you start

The following service properties are fixed for the lifetime of the service. Because they're fixed, consider the usage implications as you fill in each property:

+ Service name becomes part of the URL endpoint ([review tips for helpful service names](#name-the-service)).
+ [Tier](search-sku-tier.md) (Basic, Standard, and so forth) determines the underlying physical hardware and billing. Some features are tier-constrained.
+ [Service region](#choose-a-region) can determine the availability of certain scenarios. If you need high availability or [AI enrichment](cognitive-search-concept-intro.md), you'll need to create the resource in a region that provides the feature. 

## Subscribe (free or paid)

To try search for free, you have two options:

+ [Open a free Azure account](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) and use free credits to try out paid Azure services. After credits are used up, keep the account and continue to use free Azure services, such as Websites. Your credit card is never charged unless you explicitly change your settings and ask to be charged.

+ Alternatively, [activate Azure credits in a Visual Studio subscription](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). A Visual Studio subscription gives you credits every month you can use for paid Azure services. 

Paid (or billable) search becomes effective when you choose a billable tier (Basic or above) and create the resource.

## Find the Azure Cognitive Search offering

1. Sign in to the [Azure portal](https://portal.azure.com/).

1. Click the plus sign (**"+ Create Resource"**) in the top-left corner.

1. Use the search bar to find "Azure Cognitive Search" or navigate to the resource through **Web** > **Azure Cognitive Search**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Create a resource in the portal" border="true":::

## Choose a subscription

If you have more than one subscription, choose one for your search service. If you are implementing [double encryption](search-security-overview.md#double-encryption) or other features that depend on managed service identities, choose the same subscription as the one used for Azure Key Vault or other services for which managed identities are used.

## Set a resource group

A resource group is a container that holds related resources for your Azure solution. It's required for the search service. It's also useful for managing resources all-up, including costs. A resource group can consist of one service, or multiple services used together. For example, if you're using Azure Cognitive Search to index an Azure Cosmos DB database, you could make both services part of the same resource group for management purposes. 

If you aren't combining resources into a single group, or if existing resource groups are filled with resources used in unrelated solutions, create a new resource group just for your Azure Cognitive Search resource. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Create a new resource group" border="true":::

Over time, you can track current and projected costs all-up or you can view charges for individual resources. The following screenshot shows the kind of cost information you can expect to see when you combine multiple resources into one group.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Manage costs at the resource group level" border="true":::

> [!TIP]
> Resource groups simplify cleanup because deleting a group deletes all of the services within it. For prototype projects utilizing multiple services, putting all of them in the same resource group makes cleanup easier after the project is over.

## Name the service

In Instance Details, provide a service name in the **URL** field. The name is part of the URL endpoint against which API calls are issued: `https://your-service-name.search.windows.net`. For example, if you want the endpoint to be `https://myservice.search.windows.net`, you would enter `myservice`.

Service name requirements:

+ It must be unique within the search.windows.net namespace
+ It must be between 2 and 60 characters in length
+ You must use lowercase letters, digits, or dashes ("-")
+ Do not use dashes ("-") in the first 2 characters or as the last single character
+ You may not use consecutive dashes ("--") anywhere

> [!TIP]
> If you think you'll be using multiple services, we recommend including the region (or location) in the service name as a naming convention. Services within the same region can exchange data at no charge, so if Azure Cognitive Search is in West US, and you have other services also in West US, a name like `mysearchservice-westus` can save you a trip to the properties page when deciding how to combine or attach resources.

## Choose a region

Azure Cognitive Search is available in most regions, as listed in the [**Products available by region**](https://azure.microsoft.com/global-infrastructure/services/?products=search) page.

As a rule, if you're using multiple Azure services, putting all of them in the same region minimizes or voids bandwidth charges. There are no charges for outbound data when services are in the same region.

Two notable exceptions might lead to provisioning one or more search services in a separate region:

+ [Outbound connections from Cognitive Search to Azure Storage](search-indexer-securing-resources.md). You might want storage in a different region if you are enabling a firewall.

+ Business continuity and disaster recovery (BCDR) requirements dictate creating multiple search services in [regional pairs](../availability-zones/cross-region-replication-azure.md#azure-cross-region-replication-pairings-for-all-geographies). For example, if you are operating in North America, you might choose East US and West US, or North Central US and South Centra US, for each search service.

Some features are subject to regional availability. If you require any of following features, choose a region that provides them:

+ [AI enrichment](cognitive-search-concept-intro.md) requires Cognitive Services to be in the same physical region as Azure Cognitive Search. There are just a few regions that *don't* provide both. The [Products available by region](https://azure.microsoft.com/global-infrastructure/services/?products=search) page indicates a common regional presence by showing two stacked check marks. An unavailable combination has a missing check mark. The time piece icon indicates future availability.

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="Regional availability" border="true":::

+ Semantic search is [currently in preview in selected regions](https://azure.microsoft.com/global-infrastructure/services/?products=search), such as "Australia East" in the above screenshot. 

Other features that have regional constraints:

+ ["Availability Zones" in Scale for Performance](search-performance-optimization.md#availability-zones)
+ [Azure roles for data plane operations](search-security-rbac.md) (Azure public cloud only)

## Choose a tier

Azure Cognitive Search is currently offered in [multiple pricing tiers](https://azure.microsoft.com/pricing/details/search/): Free, Basic, Standard, or Storage Optimized. Each tier has its own [capacity and limits](search-limits-quotas-capacity.md). Also the tier you select may impact the availability of certain features. See [Feature availability by tier](search-sku-tier.md#feature-availability-by-tier) for guidance.

Basic and Standard are the most common choices for production workloads, but initially many customers start with the Free service for evaluation purposes. Among the billable tiers, key differences are partition size and speed, and limits on the number of objects you can create.

:::image type="content" source="media/search-create-service-portal/select-pricing-tier.png" alt-text="Screenshot of Select a pricing tier page" border="true":::

Remember, a pricing tier cannot be changed once the service is created. If you need a higher or lower tier, you will have to re-create the service.

## Create your service

After you've provided the necessary inputs, go ahead and create the service. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Review and create the service" border="true":::

Your service is deployed within minutes. You can monitor progress through Azure notifications. Consider pinning the service to your dashboard for easy access in the future.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitor and pin the service" border="true":::

## Get a key and URL endpoint

Unless you are using the portal, programmatic access to your new service requires that you provide the URL endpoint and an authenticated connection. [Azure role-based access control with Azure Active Directory](search-security-rbac.md) is in public preview. [Key-based authentication](search-security-api-keys.md) is the default. It's also the only generally available authentication methodology for inbound connections to a search service. 

1. On the **Overview** page, locate and copy the URL endpoint on the right side of the page.

1. On the **Keys** page, copy either one of the admin keys (they are equivalent). Admin API keys are required for creating, updating, and deleting objects on your service. In contrast, query keys provide read-access to index content.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Service overview page with URL endpoint" border="false":::

An endpoint and key are not needed for portal-based tasks. The portal is already linked to your Azure Cognitive Search resource with admin rights. For a portal walkthrough, start with [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md).

## Scale your service

After your service is provisioned, you can [scale it to meet your needs](search-limits-quotas-capacity.md). If you chose the Standard tier for your Azure Cognitive Search service, you can scale your service in two dimensions: replicas and partitions. For the Basic tier, you can only add replicas. If you provisioned the free service, scale is not available.

***Partitions*** allow your service to store and search through more documents.

***Replicas*** allow your service to handle a higher load of search queries.

Adding resources increases your monthly bill. The [pricing calculator](https://azure.microsoft.com/pricing/calculator/) can help you understand the billing ramifications of adding resources. Remember that you can adjust resources based on load. For example, you might increase resources to create a full initial index, and then reduce resources later to a level more appropriate for incremental indexing.

> [!Important]
> A service must have [2 replicas for read-only SLA and 3 replicas for read/write SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Go to your search service page in the Azure portal.
1. In the left-navigation pane, select **Settings** > **Scale**.
1. Use the slidebar to add resources of either type.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Add capacity through replicas and partitions" border="true":::

## When to add a second service

Most customers use just one service provisioned at a tier [sufficient for expected load](search-capacity-planning.md). One service can host multiple indexes, subject to the [maximum limits of the tier you select](search-limits-quotas-capacity.md#index-limits), with each index isolated from another. In Azure Cognitive Search, requests can only be directed to one index, minimizing the chance of accidental or intentional data retrieval from other indexes in the same service.

Although most customers use just one service, service redundancy might be necessary if operational requirements include the following:

+ [Business continuity and disaster recovery (BCDR)](../availability-zones/cross-region-replication-azure.md). Azure Cognitive Search does not provide instant failover in the event of an outage.

+ [Multi-tenant architectures](search-modeling-multitenant-saas-applications.md) sometimes call for two or more services.

+ Globally deployed applications might require search services in each geography to minimize latency.

> [!NOTE]
> In Azure Cognitive Search, you cannot segregate indexing and querying operations; thus, you would never create multiple services for segregated workloads. An index is always queried on the service in which it was created (you cannot create an index in one service and copy it to another).

A second service is not required for high availability. High availability for queries is achieved when you use 2 or more replicas in the same service. Replica updates are sequential, which means at least one is operational when a service update is rolled out. For more information about uptime, see [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## Add more services to a subscription

Cognitive Search restricts the [number of resources](search-limits-quotas-capacity.md#subscription-limits) you can initially create in a subscription. If you exhaust your maximum limit, file a new support request to add more search services.

1. Sign in to the Azure portal and find your search service.

1. On the left-navigation pane, scroll down and select **New Support Request.**

1. In **Issue type**, choose **Service and subscription limits (quotas).**

1. Select the subscription that needs more quota.

1. Under **Quota type**, select **Search** and then select **Next**.

1. In the **Problem details** section, select **Enter details**.

1. Follow the prompts to select the location and tier for which you want to increase the limit.

1. Add the number of new services you would like to add to your quota. The value must not be empty and must between 0 to 100. For example, the maximum number of S2 services is 8. If you want 12 services, you would request 4 of S2 services.

1. When you're finished, select **Save and continue** to continue creating your support request.

1. Provide the additional information required to file the request, and then select **Next**.

1. On **Review + create**, review the details that you'll send to support, and then select **Create**. 

## Next steps

After provisioning a service, you can continue in the portal to create your first index.

> [!div class="nextstepaction"]
> [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md)

Want to optimize and save on your cloud spending?

> [!div class="nextstepaction"]
> [Start analyzing costs with Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
