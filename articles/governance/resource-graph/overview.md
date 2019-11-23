---
title: Přehled služby Azure Resource Graph
description: Understand how the Azure Resource Graph service enables complex querying of resources at scale across subscriptions and tenants.
ms.date: 10/21/2019
ms.topic: overview
ms.openlocfilehash: 7a96faa8502fca6fc501985cd677ac28454f1ba1
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406691"
---
# <a name="what-is-azure-resource-graph"></a>Co je Azure Resource Graph?

Azure Resource Graph is a service in Azure that is designed to extend Azure Resource Management by providing efficient and performant resource exploration with the ability to query at scale across a given set of subscriptions so that you can effectively govern your environment. Tyto dotazy poskytují následující funkce:

- Možnost dotazu ohledně zdrojů s komplexním filtrováním, seskupováním a řazením podle vlastností zdroje.
- Ability to iteratively explore resources based on governance requirements.
- Schopnost posoudit dopad uplatnění zásad v rozsáhlém cloudovém prostředí.
- Ability to [detail changes made to resource properties](./how-to/get-resource-changes.md) (preview).

V této dokumentaci si podrobně projdete jednotlivé funkce.

> [!NOTE]
> Azure Resource Graph powers Azure portal's search bar, the new browse 'All resources' experience, and Azure Policy's [Change history](../policy/how-to/determine-non-compliance.md#change-history-preview)
> _visual diff_. It's designed to help customers manage large-scale environments.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Jak Resource Graph doplňuje Azure Resource Manager

Azure Resource Manager currently supports queries over basic resource fields, specifically - Resource name, ID, Type, Resource Group, Subscription, and Location. Resource Manager also provides facilities for calling individual resource providers for detailed properties one resource at a time.

S Azure Resource Graph můžete získat přístup k těmto vlastnostem, které poskytovatelé zdrojů vrátí, aniž by bylo nutné provádět individuální vyvolání u každého poskytovatele zdrojů. For a list of supported resource types, look for a **Yes** in the [Resources for complete mode deployments](../../azure-resource-manager/complete-mode-deletion.md) table. Additional resource types are found in the related [Resource Graph tables](./concepts/query-language.md#resource-graph-tables). An alternative way to see supported resource types is through the [Azure Resource Graph Explorer Schema browser](./first-query-portal.md#schema-browser).

With Azure Resource Graph, you can:

- Access the properties returned by resource providers without needing to make individual calls to each resource provider.
- View the last 14 days of change history made to the resource to see what properties changed and when. (Preview)

## <a name="how-resource-graph-is-kept-current"></a>How Resource Graph is kept current

When an Azure resource is updated, Resource Graph is notified by Resource Manager of the change.
Resource Graph then updates its database. Resource Graph also does a regular _full scan_. This scan ensures that Resource Graph data is current if there are missed notifications or when a resource is updated outside of Resource Manager.

> [!NOTE]
> Resource Graph uses a `GET` to the latest non-preview API of each resource provider to gather properties and values. As a result, the property expected may not be available. In some cases, the API version used has been overridden to provide more current or widely used properties in the results. See the [Show API version for each resource type](./samples/advanced.md#apiversion) sample for a complete list in your environment.

## <a name="the-query-language"></a>Dotazovací jazyk

Now that you have a better understanding of what Azure Resource Graph is, let's dive into how to construct queries.

It's important to understand that Azure Resource Graph's query language is based on the [Kusto query language](../../data-explorer/data-explorer-overview.md) used by Azure Data Explorer.

Nejprve se podívejte na podrobnosti o operacích a funkcích, které lze použít s Azure Resource Graph, viz [ jazyk dotazu pro graf zdrojů ](./concepts/query-language.md).
Chcete-li procházet zdroje, podívejte se na [ prozkoumat zdroje ](./concepts/explore-resources.md).

## <a name="permissions-in-azure-resource-graph"></a>Oprávnění v Azure Resource Graph

Pokud chcete používat Resource Graph, musíte mít odpovídající oprávnění v [řízení přístupu na základě role](../../role-based-access-control/overview.md), a to alespoň oprávnění ke čtení pro prostředky, které chcete dotazovat. Pokud pro objekt nebo skupinu objektů Azure nemáte alespoň oprávnění `read`, nevrátí se žádné výsledky.

> [!NOTE]
> Resource Graph uses the subscriptions available to a principal during login. To see resources of a new subscription added during an active session, the principal must refresh the context. This action happens automatically when logging out and back in.

Azure CLI and Azure PowerShell use subscriptions that the user has access to. When using REST API directly, the subscription list is provided by the user. If the user has access to any of the subscriptions in the list, the query results are returned for the subscriptions the user has access to. This behavior is the same as when calling [Resource Groups - List](/rest/api/resources/resourcegroups/list) \- you get resource groups you've access to without any indication that the result may be partial.
If there are no subscriptions in the subscription list that the user has appropriate rights to, the response is a _403_ (Forbidden).

## <a name="throttling"></a>Throttling

As a free service, queries to Resource Graph are throttled to provide the best experience and response time for all customers. If your organization wants to use the Resource Graph API for large-scale and frequent queries, use portal 'Feedback' from the [Resource Graph portal page](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph).
Provide your business case and select the 'Microsoft can email you about your feedback' checkbox in order for the team to contact you.

Resource Graph throttles queries at the user level. The service response contains the following HTTP headers:

- `x-ms-user-quota-remaining` (int): The remaining resource quota for the user. This value maps to query count.
- `x-ms-user-quota-resets-after` (hh:mm:ss): The time duration until a user's quota consumption is reset

For more information, see [Guidance for throttled requests](./concepts/guidance-for-throttled-requests.md).

## <a name="running-your-first-query"></a>Spusťte váš první dotaz

Azure Resource Graph Explorer, part of Azure portal, enables running Resource Graph queries directly in Azure portal. Pin the results as dynamic charts to provide real-time dynamic information to your portal workflow. For more information, see [First query with Azure Resource Graph Explorer](first-query-portal.md).

Resource Graph supports Azure CLI, Azure PowerShell, Azure SDK for .NET, and more. The query is structured the same for each language. Learn how to enable Resource Graph with:

- [Azure portal and Resource Graph Explorer](first-query-portal.md) 
- [Azure CLI](first-query-azurecli.md#add-the-resource-graph-extension)
- [Azure PowerShell](first-query-powershell.md#add-the-resource-graph-module)

## <a name="next-steps"></a>Další kroky

- Run your first query by using the [Azure portal](first-query-portal.md).
- Run your first query with [Azure CLI](first-query-azurecli.md).
- Run your first query with [Azure PowerShell](first-query-powershell.md).