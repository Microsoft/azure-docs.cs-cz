---
title: Správa zón DNS v Azure DNS - 2.0 rozhraní příkazového řádku Azure | Microsoft Docs
description: Můžete spravovat pomocí Azure CLI 2.0 zóny DNS. Tento článek ukazuje, jak k aktualizaci, odstranění a vytvoření zóny DNS na Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 8ab63bc4-5135-4ed8-8c0b-5f0712b9afed
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2017
ms.author: kumud
ms.openlocfilehash: 3fee44e282424caa0a9e57dae1228d8af075e4a6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-manage-dns-zones-in-azure-dns-using-the-azure-cli-20"></a>Správa zón DNS v Azure DNS pomocí Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-dnszones-portal.md)
> * [PowerShell](dns-operations-dnszones.md)
> * [Azure CLI 2.0](dns-operations-dnszones-cli.md)


Tato příručka ukazuje, jak spravovat zóny DNS pomocí rozhraní příkazového řádku Azure a platformy, která je k dispozici pro Windows, Mac a Linux. Můžete také spravovat zóny DNS pomocí [prostředí Azure PowerShell](dns-operations-dnszones.md) nebo portálu Azure.

Tato příručka konkrétně zabývá veřejném DNS zóny. Informace o používání rozhraní příkazového řádku Azure můžete spravovat soukromé zóny v Azure DNS najdete v tématu [začít pracovat s zón DNS privátní Azure pomocí Azure CLI 2.0](private-dns-getstarted-cli.md).

## <a name="introduction"></a>Úvod

[!INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="set-up-azure-cli-20-for-azure-dns"></a>Nastavení Azure CLI 2.0 pro Azure DNS

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace ověřte, zda máte následující.

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

* Nainstalujte nejnovější verzi rozhraní Azure CLI 2.0, k dispozici pro Windows, Linux nebo MAC. Další informace najdete v tématu [Instalace rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

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

### <a name="optional-to-installuse-azure-dns-private-zones-feature-public-preview"></a>Volitelné: K instalaci nebo použití zón DNS privátní Azure funkci (Public Preview)
Funkce Azure DNS Private Zones je vydaná ve verzi Public Preview prostřednictvím rozšíření Azure CLI. Nainstalujte rozšíření Azure CLI dns. 
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

Všechny příkazy rozhraní příkazového řádku 2.0 týkající se Azure DNS začínat `az network dns`. Je k dispozici pro každý příkaz pomocí Nápověda `--help` možnost (krátký tvar `-h`).  Příklad:

```azurecli
az network dns --help
az network dns zone --help
az network dns zone create --help
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Nápovědu získáte příkazem `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků s názvem *MyResourceGroup*:

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com
```

### <a name="to-create-a-dns-zone-with-tags"></a>Vytvoření zóny DNS pomocí značek

Následující příklad ukazuje, jak vytvořit zónu DNS se dvěma [Azure Resource Manager značky](dns-zones-records.md#tags), *projektu = demo* a *env = test*, pomocí `--tags` parametr (krátký tvar `-t`):

```azurecli
az network dns zone create --resource-group MyResourceGroup --name contoso.com --tags "project=demo" "env=test"
```

## <a name="get-a-dns-zone"></a>Získat zóny DNS

Chcete-li načíst zónu DNS, použijte `az network dns zone show`. Nápovědu získáte příkazem `az network dns zone show --help`.

Následující příklad vrací zónu DNS *contoso.com* a související data ze skupiny prostředků *MyResourceGroup*. 

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

Tento příkaz neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets-cli.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Tyto vlastnosti jsou aktuálně omezená na [Azure Resource Manager, značky'](dns-zones-records.md#tags) prostředku zóny.

Následující příklad ukazuje, jak k aktualizaci značek u zóny DNS. Zadaná hodnota se nahradí stávající značky.

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

Následující příklad ukazuje, jak odstranit zónu *contoso.com* ze skupiny prostředků *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.com
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak [spravovat sady záznamů a záznamy](dns-getstarted-create-recordset-cli.md) ve vaší zóně DNS.

Zjistěte, jak [delegování domény do Azure DNS](dns-domain-delegation.md).

