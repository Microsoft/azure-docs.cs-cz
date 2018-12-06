---
title: Rychlý start – Vytvoření zóny Azure DNS a záznam pomocí Azure CLI
description: Rychlý start – Naučíte se vytvořit zónu a záznam DNS v Azure DNS. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první zónu a záznam DNS pomocí Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: e61975d81fd5920feb5fd47845c67d0aa5293ae6
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52962007"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Rychlý start: Vytvoření zóny Azure DNS a záznam pomocí Azure CLI

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS pomocí Azure CLI, které je dostupné pro Windows, Mac a Linux. Tyto kroky můžete provést také pomocí webu [Azure Portal](dns-getstarted-portal.md) nebo [Azure PowerShellu](dns-getstarted-powershell.md).

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Azure DNS teď podporuje také privátní zóny DNS (aktuálně ve verzi Public Preview). Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu [Začínáme s privátními zónami Azure DNS pomocí rozhraní příkazového řádku](./private-dns-getstarted-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS vytvořte skupinu prostředků, která bude obsahovat zónu DNS.

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří příkazem `az network dns zone create`. Pokud chcete zobrazit nápovědu k tomuto příkazu, zadejte `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS s názvem *contoso.com* ve skupině prostředků *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.com
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Nápovědu k záznamům A najdete v tématu `azure network dns record-set A add-record -h`.

Následující příklad vytvoří záznam s relativním názvem „www“ v zóně DNS „contoso.com“ ve skupině prostředků „MyResourceGroup“. Plně kvalifikovaný název sady záznamů je „www.contoso.com“. Typ záznamu je A a používá se IP adresa 1.2.3.4 a výchozí hodnota TTL 3600 sekund (1 hodina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.com -n www -a 1.2.3.4
```

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte následující příkaz:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.com
```

## <a name="update-name-servers"></a>Aktualizace názvových serverů

Jakmile budete spokojení se správným nastavením zóny a záznamů DNS, bude potřeba nakonfigurovat váš název domény tak, aby používal názvové servery Azure DNS a umožňoval ostatním uživatelům internetu vyhledání vašich záznamů DNS.

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

Tyto názvové servery by měly být nakonfigurované u registrátora názvu domény (u kterého jste zakoupili název domény). Registrátor vám nabídne možnost nastavit názvové servery pro doménu. Další informace najdete v tématu [kurz: hostování domény v Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="delete-all-resources"></a>Odstranění všech prostředků
 
Pokud už nejsou potřeba, můžete všechny prostředky vytvořené v rámci tohoto rychlého startu odstranit odstraněním této skupiny prostředků:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další postup

Teď, když jste vytvořili svoji první zónu a záznam DNS pomocí Azure CLI, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
