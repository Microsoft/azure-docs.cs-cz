---
title: 'Rychlý Start: vytvoření Azure DNS zóny a Azure PowerShell záznamu'
titleSuffix: Azure DNS
description: Naučíte se vytvořit zónu a záznam DNS v DNS Azure. Pomocí tohoto podrobného postupu můžete vytvořit a spravovat první zónu a záznam DNS pomocí Azure PowerShellu.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f2563c33d02490732f73fcf9d1a78f548ec2d3e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282220"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Rychlé zprovoznění: Vytvoření záznamu a zóny Azure DNS pomocí Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto rychlém zprovoznění vytvoříte první záznam a zónu Azure DNS pomocí Azure PowerShellu. Tyto kroky můžete provést také pomocí webu [Azure Portal](dns-getstarted-portal.md) nebo [Azure CLI](dns-getstarted-cli.md). 

K hostování záznamů DNS pro konkrétní doménu se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Nakonec, pokud chcete zónu DNS publikovat na internetu, bude potřeba nakonfigurovat pro doménu názvové servery. Jednotlivé kroky jsou popsány níže.

Azure DNS také podporuje vytváření privátních domén. Podrobné pokyny k vytvoření první privátní zóny DNS a záznamu najdete v tématu [Začínáme s privátními zónami DNS Azure pomocí PowerShellu](private-dns-getstarted-powershell.md).

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell lokálně nainstalované nebo Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS vytvořte skupinu prostředků, která bude obsahovat zónu DNS.

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Vytvoření zóny DNS

Zóna DNS se vytvoří pomocí rutiny `New-AzDnsZone`. Následující příklad vytvoří zónu DNS s názvem *contoso.xyz* ve skupině prostředků s názvem *MyResourceGroup*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem "www" v zóně DNS "contoso.xyz" ve skupině prostředků "MyResourceGroup". Plně kvalifikovaný název sady záznamů je "www.contoso.xyz". Typ záznamu je A, IP adresa je 10.10.10.10 a hodnota TTL je 3600 sekund.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>Testování překladu IP adres

Teď, když máte testovací zónu DNS s testovacím záznamem "A", můžete otestovat překlad IP adres pomocí nástroje s názvem *nslookup*. 

**Testování překladu názvů DNS:**

1. Chcete-li získat seznam názvových serverů pro vaši zónu, spusťte následující rutinu:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Zkopírujte jeden název názvového serveru z výstupu předchozího kroku.

1. Otevřete příkazový řádek a spusťte následující příkaz:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Například:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Mělo by se zobrazit něco podobného jako na následující obrazovce:

   ![Snímek obrazovky zobrazuje okno příkazového řádku s vyhledávacím příkazem n s a hodnotami pro server, adresu, název a adresu.](media/dns-getstarted-portal/nslookup.PNG)

Název hostitele **www \. contoso.xyz** se přeloží na **10.10.10.10**, stejně jako jste nakonfigurovali. Tento výsledek ověří, že překlad názvů funguje správně.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nejsou potřeba, můžete odstranit všechny prostředky vytvořené v rámci tohoto rychlého startu odstraněním skupiny prostředků:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Teď když jste vytvořili svoji první zónu a záznam DNS pomocí Azure Powershellu, můžete vytvořit záznamy pro webovou aplikaci ve vlastní doméně.

> [!div class="nextstepaction"]
> [Vytvoření záznamů DNS pro webovou aplikaci ve vlastní doméně](./dns-web-sites-custom-domain.md)

