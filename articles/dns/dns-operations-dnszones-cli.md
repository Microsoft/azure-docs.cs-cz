---
title: Manage DNS zones in Azure DNS - Azure CLI | Microsoft Docs
description: You can manage DNS zones using Azure CLI. This article shows how to update, delete and create DNS zones on Azure DNS.
services: dns
documentationcenter: na
author: asudbring
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: allensu
ms.openlocfilehash: e1a3c401de32beb9757011ac306443334da8b867
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74211926"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>How to manage DNS Zones in Azure DNS using the Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI](dns-operations-dnszones-cli.md)


This guide shows how to manage your DNS zones by using the cross-platform Azure CLI, which is available for Windows, Mac and Linux. You can also manage your DNS zones using [Azure PowerShell](dns-operations-dnszones.md) or the Azure portal.

This guide specifically deals with Public DNS zones. For information on using Azure CLI to manage Private Zones in Azure DNS, see [Get started with Azure DNS Private Zones using Azure CLI](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Představení

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Nastavení rozhraní příkazového řádku Azure pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure, která je dostupná pro Windows, Linux a Mac. Další informace najdete v tématu [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Přihlášení k účtu Azure

Otevřete okno konzoly a proveďte ověření pomocí svých přihlašovacích údajů. Další informace najdete v tématu [Přihlášení k Azure z rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

```
az login
```

### <a name="select-the-subscription"></a>Výběr předplatného

Zkontrolujte předplatná pro příslušný účet.

```
az account list
```

Zvolte předplatné Azure, které chcete použít.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Optional: To install/use Azure DNS Private Zones feature
The Azure DNS Private Zone feature is available via an extension to the Azure CLI. Nainstalujte rozšíření Azure CLI dns. 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. To slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Všechny prostředky DNS jsou ale globální, a ne místní, takže volba umístění skupiny prostředků nemá na Azure DNS žádný vliv.

Pokud používáte některou ze stávajících skupin prostředků, můžete tento krok přeskočit.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Získání nápovědy

All Azure CLI commands relating to Azure DNS start with `az network dns`. Help is available for each command using the `--help` option (short form `-h`).  Například:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Nápovědu získáte příkazem `az network dns zone create -h`.

The following example creates a DNS zone called *contoso.com* in the resource group called *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>To create a DNS zone with tags

The following example shows how to create a DNS zone with two [Azure Resource Manager tags](dns-zones-records.md#tags), *project = demo* and *env = test*, by using the `--tags` parameter (short form `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Get a DNS zone

To retrieve a DNS zone, use `az network dns zone show`. Nápovědu získáte příkazem `az network dns zone show --help`.

The following example returns the DNS zone *contoso.com* and its associated data from resource group *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

Dalším příkladem je tato odpověď.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-04.azure-dns.com.",
    "ns2-04.azure-dns.net.",
    "ns3-04.azure-dns.org.",
    "ns4-04.azure-dns.info."
  ],
  "numberOfRecordSets": 4,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Všimněte si, že příkaz `az network dns zone show` nevrací záznamy DNS. Pokud chcete vypsat záznamy DNS, použijte příkaz `az network dns record-set list`.


## <a name="list-dns-zones"></a>Výpis zón DNS

Pokud chcete zobrazit výčet zón DNS, použijte příkaz `az network dns zone list`. Nápovědu získáte příkazem `az network dns zone list --help`.

Zadáním skupiny prostředků můžete vypsat pouze zóny v rámci této skupiny prostředků:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Pokud skupinu prostředků vynecháte, vypíší se všechny zóny v předplatném:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí příkazu `az network dns zone update`. Nápovědu získáte příkazem `az network dns zone update --help`.

Tento příkaz neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets-cli.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. These properties are currently limited to the [Azure Resource Manager 'tags'](dns-zones-records.md#tags) for the zone resource.

The following example shows how to update the tags on a DNS zone. The existing tags are replaced by the value specified.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí příkazu `az network dns zone delete`. Nápovědu získáte příkazem `az network dns zone delete --help`.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).

Tento příkaz zobrazí výzvu k potvrzení. Volitelný přepínač `--yes` tuto výzvu potlačí.

The following example shows how to delete the zone *contoso.com* from resource group *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Další kroky

Learn how to [manage record sets and records](dns-getstarted-create-recordset-cli.md) in your DNS zone.

Learn how to [delegate your domain to Azure DNS](dns-domain-delegation.md).

