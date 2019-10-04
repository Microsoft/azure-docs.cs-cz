---
title: Správa zón DNS v Azure DNS – Azure CLI | Microsoft Docs
description: Můžete spravovat zóny DNS pomocí rozhraní příkazového řádku Azure CLI. Tento článek popisuje, jak aktualizovat, odstranit a vytvořit zóny DNS na Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: victorh
ms.openlocfilehash: 14d0512a10329f36872d111825261ebc5ef71976
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959385"
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli"></a>Správa Zóny DNS v Azure DNS pomocí Azure CLI

> [!div class="op_single_selector"]
> * [Bran](dns-operations-dnszones-portal.md)
> * [Prostředí](dns-operations-dnszones.md)
> * [Rozhraní příkazového řádku Azure](dns-operations-dnszones-cli.md)


Tato příručka ukazuje, jak spravovat zóny DNS pomocí rozhraní příkazového řádku Azure CLI pro různé platformy, které je dostupné pro Windows, Mac a Linux. Zóny DNS můžete spravovat také pomocí [Azure PowerShell](dns-operations-dnszones.md) nebo Azure Portal.

V této příručce se konkrétně zabývá veřejné zóny DNS. Informace o použití rozhraní příkazového řádku Azure ke správě privátních zón v Azure DNS najdete v tématu Začínáme [s Azure DNS Private Zones pomocí Azure CLI](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Úvod

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-for-azure-dns"></a>Nastavení rozhraní příkazového řádku Azure pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující položky.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Nainstalujte nejnovější verzi rozhraní příkazového řádku Azure CLI, která je k dispozici pro Windows, Linux nebo MAC. Další informace najdete v [části instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

### <a name="sign-in-to-your-azure-account"></a>Přihlaste se ke svému účtu Azure.

Otevřete okno konzoly a proveďte ověření pomocí vašich přihlašovacích údajů. Další informace najdete v tématu [přihlášení k Azure z Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```
az login
```

### <a name="select-the-subscription"></a>Vyberte předplatné.

Ověřte odběry účtu.

```
az account list
```

Vyberte si předplatné Azure, které chcete použít.

```azurecli
az account set --subscription "subscription name"
```

### <a name="optional-to-installuse-azure-dns-private-zones-feature"></a>Volitelné: pro instalaci/použití funkce Azure DNS Private Zones
Funkce privátní zóny Azure DNS je k dispozici prostřednictvím rozšíření Azure CLI. Instalace rozšíření DNS Azure CLI 
```
az extension add --name dns
``` 

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určovaly umístění. Slouží jako výchozí umístění pro prostředky v dané skupině prostředků. Vzhledem k tomu, že všechny prostředky DNS jsou globální, nikoli regionální, Volba umístění skupiny prostředků nemá žádný vliv na Azure DNS.

Pokud používáte existující skupinu prostředků, můžete tento krok přeskočit.

```azurecli
az group create --name myresourcegroup --location "West US"
```

## <a name="getting-help"></a>Získání nápovědy

Všechny příkazy rozhraní příkazového řádku Azure, které se týkají Azure DNS začínají na `az network dns`. K dispozici je pro každý příkaz pomocí možnosti `--help` (krátký tvar `-h`).  Příklad:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí příkazu `az network dns zone create`. Nápovědu najdete v tématu `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Vytvoření zóny DNS pomocí značek

Následující příklad ukazuje, jak vytvořit zónu DNS se dvěma [Azure Resource Manager značkami](dns-zones-records.md#tags) *Project = demo* a *env = test*pomocí parametru `--tags` (krátký tvar `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Získání zóny DNS

K načtení zóny DNS použijte `az network dns zone show`. Nápovědu najdete v tématu `az network dns zone show --help`.

Následující příklad vrátí zónu DNS *contoso.com* a přidružená data ze skupiny prostředků *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group myresourcegroup --name contoso.com
```

V následujícím příkladu je odpověď.

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

Všimněte si, že `az network dns zone show` nevrátí záznamy DNS. K vypsání záznamů DNS použijte `az network dns record-set list`.


## <a name="list-dns-zones"></a>Výpis zón DNS

Chcete-li vytvořit výčet zón DNS, použijte `az network dns zone list`. Nápovědu najdete v tématu `az network dns zone list --help`.

Zadáním skupiny prostředků vypíšete jenom zóny v rámci skupiny prostředků:

```azurecli
az network dns zone list --resource-group MyResourceGroup
```

Vynechání skupiny prostředků vypíše seznam všech zón v předplatném:

```azurecli
az network dns zone list 
```

## <a name="update-a-dns-zone"></a>Aktualizace zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí `az network dns zone update`. Nápovědu najdete v tématu `az network dns zone update --help`.

Tento příkaz neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets-cli.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Tyto vlastnosti jsou v současné době omezeny na [Azure Resource Manager značky](dns-zones-records.md#tags) pro prostředek zóny.

Následující příklad ukazuje, jak aktualizovat značky v zóně DNS. Existující značky jsou nahrazené zadanou hodnotou.

```azurecli
az network dns zone update --resource-group myresourcegroup --name contoso.com --set tags.team=support
```

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí `az network dns zone delete`. Nápovědu najdete v tématu `az network dns zone delete --help`.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci zóny. Tuto operaci nelze vrátit zpět. Pokud se zóna DNS používá, služby, které používají zónu, se při odstranění zóny nezdaří.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [jak chránit zóny a záznamy DNS](dns-protect-zones-recordsets.md).

Tento příkaz zobrazí výzvu k potvrzení. Volitelný přepínač `--yes` potlačí tuto výzvu.

Následující příklad ukazuje, jak odstranit zónu *contoso.com* ze skupiny prostředků *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Další kroky

Naučte se [Spravovat sady záznamů a záznamy](dns-getstarted-create-recordset-cli.md) v zóně DNS.

Přečtěte si, jak [delegovat doménu na Azure DNS](dns-domain-delegation.md).

