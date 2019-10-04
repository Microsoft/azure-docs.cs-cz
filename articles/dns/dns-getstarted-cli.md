---
title: Rychlý Start – vytvoření zóny Azure DNS a záznamu pomocí Azure CLI
description: Rychlý Start – Zjistěte, jak vytvořit zónu a záznam DNS v Azure DNS. Toto je podrobný Průvodce vytvořením a správou první zóny a záznamu DNS pomocí rozhraní příkazového řádku Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: b5d842c2d6ff84a0f17c4e8be0bfade018edc48b
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959976"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>Rychlý Start: vytvoření zóny Azure DNS a záznamu pomocí Azure CLI

Tento článek vás provede kroky k vytvoření první zóny a záznamu DNS pomocí rozhraní příkazového řádku Azure, které je dostupné pro Windows, Mac a Linux. Tyto kroky můžete provést také pomocí [Azure Portal](dns-getstarted-portal.md) nebo [Azure PowerShell](dns-getstarted-powershell.md).

Zóna DNS se používá k hostování záznamů DNS pro konkrétní doménu. Pokud chcete začít hostovat vaši doménu v Azure DNS, musíte pro tento název domény vytvořit zónu DNS. Každý záznam DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, musíte nakonfigurovat názvové servery pro doménu. Každý z těchto kroků je popsaný níže.

Azure DNS podporuje i privátní zóny DNS. Další informace o privátních zónách DNS najdete v tématu [použití Azure DNS u privátních domén](private-dns-overview.md). Příklad vytvoření privátní zóny DNS najdete v tématu Začínáme [s Azure DNS privátní zóny pomocí](./private-dns-getstarted-cli.md)rozhraní příkazového řádku (CLI).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS vytvořte skupinu prostředků, která bude obsahovat zónu DNS:

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí příkazu `az network dns zone create`. Nápovědu k tomuto příkazu zobrazíte tak, že zadáte `az network dns zone create -h`.

Následující příklad vytvoří zónu DNS s názvem *contoso. xyz* ve skupině prostředků *MyResourceGroup*. Použijte příklad k vytvoření zóny DNS, ve které nahradíte hodnoty vlastními.

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

K vytvoření záznamu DNS použijte příkaz `az network dns record-set [record type] add-record`. Nápovědu k záznamům najdete v tématu `azure network dns record-set A add-record -h`.

Následující příklad vytvoří záznam s relativním názvem "www" v zóně DNS "contoso. xyz" ve skupině prostředků "MyResourceGroup". Plně kvalifikovaný název sady záznamů je "www. contoso. xyz". Typ záznamu je A, IP adresa "10.10.10.10" a výchozí hodnota TTL 3600 sekund (1 hodina).

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>Zobrazit záznamy

Chcete-li zobrazit seznam záznamů DNS v zóně, spusťte příkaz:

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>Testování překladu názvů

Teď, když máte testovací zónu DNS s testovacím záznamem "A", můžete otestovat překlad IP adres pomocí nástroje s názvem *nslookup*. 

**Testování překladu názvů DNS:**

1. Chcete-li získat seznam názvových serverů pro vaši zónu, spusťte následující rutinu:

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. Zkopírujte jeden název názvového serveru z výstupu předchozího kroku.

1. Otevřete příkazový řádek a spusťte následující příkaz:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Příklad:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Mělo by se zobrazit něco podobného jako na následující obrazovce:

   ![nástrojem](media/dns-getstarted-portal/nslookup.PNG)

Název hostitele **www\.contoso.xyz** se přeloží na **10.10.10.10**, stejně jako jste nakonfigurovali. Tento výsledek ověří, že překlad názvů funguje správně.

## <a name="delete-all-resources"></a>Odstranit všechny prostředky

Pokud už je nepotřebujete, můžete všechny prostředky vytvořené v rámci tohoto rychlého startu odstranit odstraněním skupiny prostředků:

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď, když jste vytvořili svou první zónu a záznam DNS pomocí Azure CLI, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)
