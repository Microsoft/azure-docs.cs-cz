---
title: Začínáme s DNS Azure pomocí Azure CLI 2.0 | Dokumentace Microsoftu
description: Naučíte se vytvořit zónu a záznam DNS v DNS Azure. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první zónu a záznam DNS pomocí Azure CLI 2.0.
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
ms.date: 03/10/2017
ms.author: kumud
ms.openlocfilehash: d24eaa4974f8bff09b337384e4fd139edb6ebd70
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30175235"
---
# <a name="get-started-with-azure-dns-using-azure-cli-20"></a>Začínáme s DNS Azure pomocí Azure CLI 2.0

> [!div class="op_single_selector"]
> * [Azure Portal](dns-getstarted-portal.md)
> * [PowerShell](dns-getstarted-powershell.md)
> * [Azure CLI 2.0](dns-getstarted-cli.md)

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS pomocí Azure CLI 2.0 pro různé platformy, které je dostupné pro Windows, Mac a Linux. Tyto kroky můžete provést také pomocí webu Azure Portal nebo Azure PowerShellu.

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Tyto pokyny předpokládají, že již máte nainstalované Azure CLI 2.0 a jste k němu přihlášeni. Nápovědu získáte v tématu [Správa zón DNS pomocí Azure CLI 2.0](dns-operations-dnszones-cli.md).

Azure DNS teď podporuje také privátní zóny DNS (aktuálně ve verzi Public Preview). Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí rozhraní příkazového řádku](./private-dns-getstarted-cli.md).

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS se vytvoří skupina prostředků, která bude obsahovat zónu DNS. Následuje ukázka příkazu.

```azurecli
az group create --name MyResourceGroup --location "West US"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Pokud chcete zobrazit nápovědu k tomuto příkazu, zadejte `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Například nápovědu k záznamům A získáte příkazem `azure network dns record-set A add-record -h`.

Následující příklad vytvoří záznam s relativním názvem „www“ v zóně DNS „contoso.com“ ve skupině prostředků „MyResourceGroup“. Plně kvalifikovaný název sady záznamů je „www.contoso.com“. Typ záznamu je A a používá se IP adresa 1.2.3.4 a výchozí hodnota TTL 3 600 sekund (1 hodina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

Informace o dalších typech záznamů, sadách záznamů s více než jedním záznamem, alternativních hodnotách TTL a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí Azure CLI 2.0](dns-operations-recordsets-cli.md).

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Aktualizace názvových serverů

Jakmile budete spokojeni se správným nastavením zóny a záznamů DNS, bude potřeba nakonfigurovat váš název domény tak, aby používal názvové servery DNS Azure. Tím umožníte ostatním uživatelům na internetu najít vaše záznamy DNS.

Názvové servery vaší zóny můžete zobrazit příkazem `az network dns zone show`. Chcete-li zobrazit názvy názvových serverů, použijte výstup ve formátu JSON, jak je ukázáno v následujícím příkladu.

```azurecli
az network dns zone show -g MyResourceGroup -n contoso.com -o json

{
  "etag": "00000003-0000-0000-b40d-0996b97ed101",
  "id": "/subscriptions/a385a691-bd93-41b0-8084-8213ebc5bff7/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com",
  "location": "global",
  "maxNumberOfRecordSets": 5000,
  "name": "contoso.com",
  "nameServers": [
    "ns1-01.azure-dns.com.",
    "ns2-01.azure-dns.net.",
    "ns3-01.azure-dns.org.",
    "ns4-01.azure-dns.info."
  ],
  "numberOfRecordSets": 3,
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "type": "Microsoft.Network/dnszones"
}
```

Tyto názvové servery by měly být nakonfigurované u registrátora názvu domény (u kterého jste zakoupili název domény). Registrátor vám nabídne možnost nastavit názvové servery pro doménu. Další informace najdete v tématu [Delegování domény do DNS Azure](dns-domain-delegation.md).

## <a name="delete-all-resources"></a>Odstranění všech prostředků
 
Pokud chcete odstranit všechny prostředky vytvořené v rámci tohoto článku, proveďte následující krok:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o DNS Azure najdete v tématu [Přehled DNS Azure](dns-overview.md).

Další informace o správě zón DNS v DNS Azure najdete v tématu [Správa zón DNS v DNS Azure pomocí Azure CLI 2.0](dns-operations-dnszones-cli.md).

Další informace o správě záznamů DNS v DNS Azure najdete v tématu [Správa záznamů a sad záznamů DNS v DNS Azure pomocí Azure CLI 2.0](dns-operations-recordsets-cli.md).
