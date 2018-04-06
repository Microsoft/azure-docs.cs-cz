---
title: Začínáme s privátními zónami DNS Azure pomocí PowerShellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit privátní zónu a záznam DNS v DNS Azure. Pomocí tohoto podrobného průvodce můžete vytvořit a spravovat první privátní zónu a záznam DNS pomocí PowerShellu.
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2017
ms.author: kumud
ms.openlocfilehash: d9e5c9b8caa5349fbcda9b71f7524fb9e99b976c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="get-started-with-azure-dns-private-zones-using-powershell"></a>Začínáme s privátními zónami DNS Azure pomocí PowerShellu

Tento článek vás provede kroky k vytvoření první privátní zóny a záznamu DNS pomocí Azure PowerShellu.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

K hostování záznamů DNS v určité doméně se používá zóna DNS. Pokud chcete začít hostovat svou doménu v DNS Azure, musíte vytvořit zónu DNS pro daný název domény. Všechny záznamy DNS pro vaši doménu se pak vytvoří v této zóně DNS. Když chcete publikovat privátní zónu DNS do virtuální sítě, zadáte seznam virtuálních sítí, které mají povoleno překládat záznamy v rámci této zóny.  Ty označujeme jako virtuální sítě pro překlad.  Můžete také zadat virtuální síť, pro kterou bude Azure DNS uchovávat záznamy názvů hostitelů při každém vytvoření virtuálního počítače, změně jeho IP adresy nebo jeho zničení.  Tu označujeme jako registrační virtuální síť.

# <a name="get-the-preview-powershell-modules"></a>Získání modulů PowerShellu ve verzi Preview
Tyto pokyny předpokládají, že už máte nainstalovaný Azure PowerShell, jste k němu přihlášeni a zajistili jste, že máte požadované moduly pro funkci privátních zón. 

[!INCLUDE [dns-powershell-setup](../../includes/dns-powershell-setup-include.md)]

## <a name="create-the-resource-group"></a>Vytvoření skupiny prostředků

Před vytvořením zóny DNS se vytvoří skupina prostředků, která bude obsahovat zónu DNS. Následuje ukázka příkazu.

```powershell
New-AzureRMResourceGroup -name MyResourceGroup -location "westus"
```

## <a name="create-a-dns-private-zone"></a>Vytvoření privátní zóny DNS

Zónu DNS vytvoříte pomocí rutiny `New-AzureRmDnsZone` s parametrem ZoneType s hodnotou Private. Následující příklad vytvoří zónu DNS *contoso.local* ve skupině prostředků *MyResourceGroup* a zpřístupní tuto zónu DNS pro virtuální síť *MyAzureVnet*. Nahraďte hodnoty vlastními a použijte tento příklad k vytvoření zóny DNS.

Poznámka: Pokud parametr ZoneType vynecháte, zóna se vytvoří jako veřejná zóna. Pokud potřebujete vytvořit privátní zónu, musíte tento parametr zadat. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -ResolutionVirtualNetworkId @($vnet.Id)
```

Pokud potřebujete, aby platforma Azure v této zóně automaticky vytvářela záznamy názvů hostitelů, místo parametru *ResolutionVirtualNetworkId* použijte parametr *RegistrationVirtualNetworkId*.  U registračních virtuálních sítí je automaticky povolen překlad.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyAzureVnet -ResourceGroupName VnetResourceGroup
New-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ZoneType Private -RegistrationVirtualNetworkId @($vnet.Id)
```

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Sady záznamů vytvoříte pomocí rutiny `New-AzureRmDnsRecordSet`. Následující příklad vytvoří záznam s relativním názvem db v zóně DNS contoso.local ve skupině prostředků MyResourceGroup. Plně kvalifikovaný název sady záznamů je db.contoso.local. Typ záznamu je A, IP adresa je 10.0.0.4 a hodnota TTL je 3 600 sekund.

```powershell
New-AzureRmDnsRecordSet -Name db -RecordType A -ZoneName contoso.local -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "10.0.0.4")
```

Informace o dalších typech záznamů, sadách záznamů s více než jedním záznamem a úpravě existujících záznamů najdete v tématu [Správa záznamů a sad záznamů DNS pomocí Azure PowerShellu](dns-operations-recordsets.md). 

## <a name="view-records"></a>Zobrazení záznamů

K výpisu záznamů DNS ve vaší zóně použijte:

```powershell
Get-AzureRmDnsRecordSet -ZoneName contoso.local -ResourceGroupName MyResourceGroup
```

# <a name="list-dns-private-zones"></a>Výpis privátních zón DNS

Vynecháním názvu zóny v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón ve skupině prostředků. Tato operace vrátí pole objektů zón.

```powershell
$zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup
```

Vynecháním názvu zóny i názvu skupiny prostředků v rutině `Get-AzureRmDnsZone` můžete zobrazit výčet všech zón v předplatném Azure.

```powershell
$zoneList = Get-AzureRmDnsZone
```

## <a name="update-a-dns-private-zone"></a>Aktualizace privátní zóny DNS

Změny prostředku zóny DNS je možné provádět pomocí rutiny `Set-AzureRmDnsZone`. Tato rutina neaktualizuje žádné sady záznamů DNS v rámci zóny (viz [Správa záznamů DNS](dns-operations-recordsets.md)). Slouží pouze k aktualizaci vlastností samotného prostředku zóny. Vlastnosti zóny s možností zápisu jsou aktuálně omezené na [značky Azure Resource Manageru pro prostředek zóny](dns-zones-records.md#tags) a na parametry RegistrationVirtualNetworkId a ResolutionVirtualNetworkId pro privátní zóny.

Následující příklad nahradí registrační virtuální síť propojenou se zónou za novou virtuální síť MyNewAzureVnet.

Upozorňujeme, že oproti vytváření nesmíte v případě aktualizace zadat parametr ZoneType. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -RegistrationVirtualNetworkId @($vnet.Id)
```

Následující příklad nahradí virtuální síť pro překlad propojenou se zónou za novou virtuální síť MyNewAzureVnet.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name MyNewAzureVnet -ResourceGroupName MyResourceGroup
Set-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup -ResolutionVirtualNetworkId @($vnet.Id)
```

## <a name="delete-a-dns-private-zone"></a>Odstranění privátní zóny DNS

Privátní zóny DNS je možné odstranit pomocí rutiny `Remove-AzureRmDnsZone` stejně jako veřejné zóny.

> [!NOTE]
> Odstraněním zóny DNS dojde také k odstranění všech záznamů DNS v rámci dané zóny. Tato operace se nedá vrátit zpět. Pokud se zóna DNS používá, služby využívající tuto zónu při jejím odstranění selžou.
>
>Informace o ochraně před náhodným odstraněním zóny najdete v tématu [Ochrana záznamů a zón DNS](dns-protect-zones-recordsets.md).

Zónu DNS můžete odstranit některým z následujících dvou způsobů:

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Určení zóny pomocí názvu zóny a názvu skupiny prostředků

```powershell
Remove-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyAzureResourceGroup
```

### <a name="specify-the-zone-using-a-zone-object"></a>Určení zóny pomocí objektu $zone

Zónu k odstranění můžete určit pomocí objektu `$zone` vráceného rutinou `Get-AzureRmDnsZone`.

```powershell
$zone = Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup
Remove-AzureRmDnsZone -Zone $zone
```

Místo předání jako parametru je možné objekt zóny předat také rourou:

```powershell
Get-AzureRmDnsZone -Name contoso.local -ResourceGroupName MyResourceGroup | Remove-AzureRmDnsZone

```

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsZone`, `Set-AzureRmDnsZone` a `Remove-AzureRmDnsZone` podporují výzvy k potvrzení.

Rutiny `New-AzureRmDnsZone` a `Set-AzureRmDnsZone` zobrazí výzvu k potvrzení v případě, že má proměnná předvolby PowerShellu `$ConfirmPreference` hodnotu `Medium` nebo nižší. Kvůli potenciálně velkému dopadu odstranění zóny DNS zobrazí rutina `Remove-AzureRmDnsZone` výzvu k potvrzení v případě, že má proměnná PowerShellu `$ConfirmPreference` jinou hodnotu než `None`.

Vzhledem k tomu, že výchozí hodnota proměnné `$ConfirmPreference` je `High`, ve výchozím nastavení zobrazí výzvu k potvrzení pouze rutina `Remove-AzureRmDnsZone`.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí.

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).


## <a name="delete-all-resources"></a>Odstranění všech prostředků

Pokud chcete odstranit všechny prostředky vytvořené v rámci tohoto článku, proveďte následující krok:

```powershell
Remove-AzureRMResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Další kroky

Další informace o privátních zónách DNS najdete v tématu [Použití DNS Azure pro privátní domény](private-dns-overview.md).

Přečtěte si o několika obvyklých [scénářích privátních zón](./private-dns-scenarios.md), které se dají realizovat s využitím privátních zón v Azure DNS.

Další informace o správě záznamů DNS v DNS Azure najdete v tématu [Správa záznamů a sad záznamů DNS v DNS Azure pomocí PowerShellu](dns-operations-recordsets.md).

