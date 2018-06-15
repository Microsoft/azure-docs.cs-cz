---
title: Začínáme se službou Azure DNS Private Zones pomocí Azure CLI 2.0 | Microsoft Docs
description: Zjistěte, jak vytvořit privátní zónu a záznam DNS v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první privátní zónu a záznam DNS pomocí Azure CLI 2.0.
services: dns
documentationcenter: na
author: KumuD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: fb0aa0a6-d096-4d6a-b2f6-eda1c64f6182
ms.service: dns
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2018
ms.author: kumud
ms.openlocfilehash: d10f3201adb972468d8de7e66a940232ed19562d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30191445"
---
# <a name="get-started-with-azure-dns-private-zones-using-azure-cli-20"></a>Začínáme se službou Azure DNS Private Zones pomocí Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](private-dns-getstarted-powershell.md)
> * [Azure CLI 2.0](private-dns-getstarted-cli.md)

Tento článek vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure CLI 2.0 pro různé platformy, které je dostupné pro Windows, Mac a Linux. Tyto kroky můžete provést také pomocí Azure PowerShellu.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Ty označujeme jako virtuální sítě pro překlad.  Můžete také zadat virtuální síť, pro kterou bude Azure DNS uchovávat záznamy názvů hostitelů při každém vytvoření virtuálního počítače, změně jeho IP adresy nebo jeho zničení.  Tu označujeme jako registrační virtuální síť.

Tyto pokyny předpokládají, že už máte nainstalované Azure CLI 2.0, jste k němu přihlášeni a nainstalovali jste potřebné rozšíření rozhraní příkazového řádku, které podporuje privátní zóny. Nápovědu získáte v tématu [Správa zón DNS pomocí Azure CLI 2.0](dns-operations-dnszones-cli.md).

## <a name="to-installuse-azure-dns-private-zones-feature-public-preview"></a>Instalace nebo použití funkce Azure DNS Private Zones (Public Preview)
Funkce Azure DNS Private Zones je vydaná ve verzi Public Preview prostřednictvím rozšíření Azure CLI. Nainstalujte rozšíření Azure CLI dns. 

```
az extension add --name dns
``` 

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS se vytvoří skupina prostředků, která bude obsahovat zónu DNS. Následuje ukázka příkazu.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-private-zone"></a>Vytvoření privátní zóny DNS

Privátní zónu DNS můžete vytvořit pomocí příkazu `az network dns zone create`. Pokud chcete zobrazit nápovědu k tomuto příkazu, zadejte `az network dns zone create --help`.

Následující příklad vytvoří privátní zónu DNS *contoso.local* ve skupině prostředků *MyResourceGroup* a pomocí parametru resolution-vnets ji zpřístupní pro virtuální síť *MyAzureVnet* (propojí je). Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --resolution-vnets MyAzureVnet
```

Poznámka: Ve výše uvedeném příkladu patří virtuální síť MyAzureVnet do stejné skupiny prostředků a stejného předplatného jako privátní zóna. Pokud potřebujete propojit virtuální síť, která patří do jiné skupiny prostředků nebo jiného předplatného, musíte v parametru --resolution-vnets zadat úplné ID Azure Resource Manageru, a ne pouze název virtuální sítě. 

Pokud potřebujete, aby platforma Azure v této zóně automaticky vytvářela záznamy názvů hostitelů, místo parametru *resolution-vnets* použijte parametr *registration-vnets*.  U registračních virtuálních sítí je automaticky povolen překlad.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.local --zone-type Private --registration-vnets MyAzureVnet
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Například nápovědu k záznamům A získáte příkazem `azure network dns record-set A add-record --help`.

Následující příklad vytvoří záznam s relativním názvem ip1 v zóně DNS contoso.local ve skupině prostředků MyResourceGroup. Plně kvalifikovaný název sady záznamů je ip1.contoso.local. Typ záznamu je A a IP adresa je 10.0.0.1.

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.local -n ip1 -a 10.0.0.1
```

Informace o dalších typech záznamů, sadách záznamů s více než jedním záznamem, alternativních hodnotách TTL a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="get-a-dns-private-zone"></a>Získání privátní zóny DNS

Pokud chcete načíst privátní zónu DNS, použijte příkaz `az network dns zone show`. Nápovědu získáte příkazem `az network dns zone show --help`.

Následující příklad vrátí zónu DNS *contoso.local* a související data ze skupiny prostředků *MyResourceGroup*. 

```azurecli
az network dns zone show --resource-group MyResourceGroup --name contoso.local
```

Dalším příkladem je tato odpověď.

```json
{
  "etag": "00000002-0000-0000-3d4d-64aa3689d201",
  "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.local",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.local",
  "nameServers": null,
  "numberOfRecordSets": 1,
  "registrationVirtualNetworks": [],
  "resolutionVirtualNetworks": [
    {
      "additionalProperties": {},
      "id": "/subscriptions/147a22e9-2356-4e56-b3de-1f5842ae4a3b/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyAzureVnet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
  "resourceGroup": "MyResourceGroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones",
  "zoneType": "Private"
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

Tento příkaz neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets-cli.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. U privátních zón můžete aktualizovat registrační virtuální síť nebo virtuální síť pro překlad propojené se zónou. 

Následující příklad ukazuje aktualizaci virtuální sítě pro překlad propojené s privátní zónou DNS. Stávající propojená virtuální síť pro překlad se nahradí zadanou novou virtuální sítí.

```azurecli
az network dns zone update --resource-group MyResourceGroup --name contoso.local --zone-type Private --resolution-vnets MyNewAzureVnet
```

## <a name="delete-a-dns-zone"></a>Odstranění zóny DNS

Zóny DNS je možné odstranit pomocí příkazu `az network dns zone delete`. Nápovědu získáte příkazem `az network dns zone delete --help`.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).

Tento příkaz zobrazí výzvu k potvrzení. Volitelný přepínač `--yes` tuto výzvu potlačí.

Následující příklad ukazuje odstranění zóny *contoso.local* ze skupiny prostředků *MyResourceGroup*.

```azurecli
az network dns zone delete --resource-group myresourcegroup --name contoso.local
```

## <a name="delete-all-resources"></a>Odstranění všech prostředků
 
Pokud chcete odstranit všechny prostředky vytvořené v rámci tohoto článku, proveďte následující krok:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o DNS Azure najdete v tématu [Přehled DNS Azure](dns-overview.md).

Další informace o správě zón DNS v DNS Azure najdete v tématu [Správa zón DNS v DNS Azure pomocí Azure CLI 2.0](dns-operations-dnszones-cli.md).

Další informace o správě záznamů DNS v DNS Azure najdete v tématu [Správa záznamů a sad záznamů DNS v DNS Azure pomocí Azure CLI 2.0](dns-operations-recordsets-cli.md).
