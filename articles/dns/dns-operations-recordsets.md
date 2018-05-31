---
title: Správa záznamů DNS v Azure DNS pomocí Azure PowerShell | Microsoft Docs
description: Správa sad záznamů DNS a záznamy v Azure DNS při hostování vaší domény ve službě Azure DNS. Všechny příkazy prostředí PowerShell pro operace na sady záznamů a záznamy.
services: dns
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: kumud
ms.openlocfilehash: 511af342727dc46369ae70d60a7e9a3171bf986d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
ms.locfileid: "32778831"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Správa záznamů DNS a sady záznamů v Azure DNS pomocí Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI 1.0](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI 2.0](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Tento článek ukazuje, jak spravovat záznamy DNS pro zónu DNS pomocí prostředí Azure PowerShell. Záznamy DNS se taky dají spravovat pomocí napříč platformami [rozhraní příkazového řádku Azure](dns-operations-recordsets-cli.md) nebo [portál Azure](dns-operations-recordsets-portal.md).

V příkladech v tomto článku předpokládá, že už máte [nainstalovaný Azure PowerShell, přihlášení a vytvořit zónu DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Vytvořit nový záznam DNS

Pokud nový záznam se stejným názvem a typem jako stávající záznam, budete muset [ho přidat do existující sady záznamů](#add-a-record-to-an-existing-record-set). Pokud nový záznam má jiný název a typ, který má všechny existující záznamy, budete muset vytvořit novou sadu záznamů. 

### <a name="create-a-records-in-a-new-record-set"></a>Vytvořit "A" záznamy v nové sady záznamů

Sady záznamů vytvoříte pomocí rutiny `New-AzureRmDnsRecordSet`. Při vytváření sady záznamů, budete muset zadat sady záznamu název zóny, čas k live (TTL), typ záznamu a záznamy, který se má vytvořit.

Parametry pro přidání záznamů do sady záznamů se liší podle typu sady záznamů. Například pokud používáte sadu záznamů typu "A", budete muset zadat IP adresu pomocí parametru `-IPv4Address`. Další parametry jsou používány pro jiné typy záznamů. V tématu [Příklady dalších typů záznamu](#additional-record-type-examples) podrobnosti.

Následující příklad vytvoří záznamů s relativním názvem "www" v zóně DNS "contoso.com". Plně kvalifikovaný název sady záznamů je "www.contoso.com". Typ záznamu je "A", a hodnota TTL 3 600 sekund. Sada záznamů obsahuje jediný záznam, s IP adresou: 1.2.3.4'.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Vytvoření záznamu nastaven na 'vrcholu' zónu (v takovém případě "contoso.com"), použijte název sady záznamů "\@" (bez uvozovek):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Pokud potřebujete vytvořit záznam nastavení, která obsahuje více než jeden záznam, místní pole a je nutné přidat záznamy, vytvořte nejdříve předat pole do `New-AzureRmDnsRecordSet` následujícím způsobem:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Sady záznamů metadat](dns-zones-records.md#tags-and-metadata) lze přidružit každá sada záznamů specifická data jako páry klíč hodnota. Následující příklad ukazuje postup vytvoření záznamů s dvě položky metadat se oddělení = finanční ' a ' prostředí = produkční '.

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS podporuje také 'prázdné, sady záznamů, které může fungovat jako zástupný symbol rezervovat název DNS před vytvořením záznamy DNS. Prázdný sady záznamů jsou viditelné v rovině řízení Azure DNS, ale zobrazí na názvových serverů Azure DNS. Následující příklad vytvoří prázdná sada záznamů:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Vytvořte záznamy jiné typy

S zobrazená v podrobnosti o tom, jak vytvořit záznamy 'A', následující příklady ukazují, jak vytvořit záznamy jiné typy záznamů nepodporuje Azure DNS.

V každém případě ukážeme, jak vytvořit sadu, která obsahuje jediný záznam záznamů. Starší příklady "A" záznamů lze upravit k vytvoření sady záznamů jiných typů obsahující více záznamů s metadaty, nebo k vytvoření sady záznamů o prázdný.

Jsme neudělujte příklad se vytvořit sadu záznamů SOA, protože se vytvářejí SOAs a odstranit s každou zónou DNS a nelze vytvořit nebo odstranit samostatně. Ale [SOA lze upravit, jak je znázorněno v příkladu novější](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Vytvoření sady záznamů AAAA s jedním záznamem

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Vytvořte záznam KÁ nastavit s jedním záznamem

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Vytvoření sady záznamů CNAME s jedním záznamem

> [!NOTE]
> Standardech DNS nepovoluje záznamy CNAME na vrcholu zóny (`-Name '@'`), ani se povolit sad záznamů obsahující více než jeden záznam.
> 
> Další informace najdete v tématu [záznamy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Vytvoření sady záznamů MX s jedním záznamem

V tomto příkladu používáme název sady záznamů "\@" k vytvoření záznamu MX ve vrcholu zóny (v tomto případě "contoso.com").


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Vytvoření sady záznamů NS s jedním záznamem

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Vytvoření sady záznamů PTR s jedním záznamem

V takovém případě se Moje-arpa-server zone.com' představuje zóny zpětného vyhledávání ARPA představující rozsahu IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres. Název záznamu "10" je poslední oktet IP adresy v tomto rozsahu IP reprezentována tento záznam.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Vytvoření sady záznamů SRV s jedním záznamem

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records), zadejte  *\_služby* a  *\_protokol* v názvu sady záznamů. Není nutné zahrnout ' \@' v názvu sady záznamů při vytváření záznamu SRV nastavit ve vrcholu zóny.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Vytvořit záznam TXT s jedním záznamem

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délku řetězce v záznamů TXT podporovány, naleznete v části [záznamů TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Získání sady záznamů

Chcete-li načíst existující sady záznamů, použijte `Get-AzureRmDnsRecordSet`. Tato rutina vrátí místní objekt, který reprezentuje sady záznamů v Azure DNS.

Stejně jako u `New-AzureRmDnsRecordSet`, musí být zadaný název sady záznamů *relativní* název, což znamená, je nutné vyloučit název zóny. Budete taky muset zadat typ záznamu a sadu zón, který obsahuje záznam.

Následující příklad ukazuje, jak načíst sadu záznamů. V tomto příkladu je zadána zóny pomocí `-ZoneName` a `-ResourceGroupName` parametry.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativně můžete také určit zóny pomocí objekt zóny, předaným pomocí `-Zone` parametr.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Seznam sad záznamů

Můžete také použít `Get-AzureRmDnsZone` do seznamu sad záznamů v zóně, díky vynechání `-Name` nebo `-RecordType` parametry.

Následující příklad vrací sady všech záznamů v zóně:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Následující příklad ukazuje, jak všechny daného typu sady záznamů s typem záznamu při vynechání záznam sady název se dá načíst:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Pokud chcete načíst všechny sady záznamů se zadaným názvem mezi typy záznamů, budete muset načíst všechny sady záznamů a poté filtrování výsledků:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Ve všech výše uvedených příkladech zóny lze zadat buď pomocí `-ZoneName` a `-ResourceGroupName`parametry (jak je znázorněno), nebo zadáním objekt zóny:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidání záznamu do existující sady záznamů

Přidání záznamu do existující sady záznamů, použijte následující tři kroky:

1. Získat existující sady záznamů

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Přidejte nový záznam pro místní sady záznamů. Toto je offline operace.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět ke službě Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Pomocí `Set-AzureRmDnsRecordSet` *nahrazuje* existující sady záznamů v Azure DNS (a všechny záznamy, které obsahuje) s zadané sady záznamů. [Značka Etag kontroly](dns-zones-records.md#etags) zajišťují souběžných změny se nepřepíšou. Můžete použít nepovinný `-Overwrite` přepínač tak, aby potlačit těchto kontrol.

Tato posloupnost operací může být také *přesměruje*, což znamená předat objekt sady záznamů pomocí kanálu spíše než předání jako parametr:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Výše uvedené příklady ukazují, jak přidat záznam "A" do existující sady záznamů typu "A". Podobně jako posloupnost operací se používá k přidání záznamů do sad záznamů jiných typů, nahraďte `-Ipv4Address` parametr `Add-AzureRmDnsRecordConfig` s další parametry, které jsou specifické pro každý typ záznamu. Parametry pro každý typ záznamu jsou stejné jako u `New-AzureRmDnsRecordConfig` rutiny, jak je znázorněno v [Příklady dalších typů záznamu](#additional-record-type-examples) výše.

Sady záznamů typu 'CNAME' nebo 'SOA, nemůže obsahovat více než jeden záznam. Toto omezení mohou nastat z norem DNS. Není omezení Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odebrat záznam z existující sady záznamů

Postup odebrání záznamu ze sady záznamů se podobá postupu přidání záznamu do existující sady záznamů:

1. Získat existující sady záznamů

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Odeberte záznam z objektu místní sady záznamů. Toto je offline operace. Záznam, který má být odebrán musí být přesně shodovat s existujícím záznamem v rámci všech parametrů.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět ke službě Azure DNS. Použít nepovinný `-Overwrite` přepínač tak, aby potlačit [kontroluje Etag](dns-zones-records.md#etags) souběžných změny.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Pomocí výše uvedených pořadí odebrat poslední záznam ze záznamů sady nedojde k odstranění sady záznamů, místo zůstanou prázdná sada záznamů. Odebrat záznam zcela nastavit, najdete v části [odstranit sadu záznamů](#delete-a-record-set).

Podobně jako při přidávání záznamů do sady záznamů, pořadí operací odebrat sadu záznamů lze také přesměrovat:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Podporuje předávání vhodné parametry specifický pro různé typy záznamů `Remove-AzureRmDnsRecordSet`. Parametry pro každý typ záznamu jsou stejné jako u `New-AzureRmDnsRecordConfig` rutiny, jak je znázorněno v [Příklady dalších typů záznamu](#additional-record-type-examples) výše.


## <a name="modify-an-existing-record-set"></a>Upravit existující sady záznamů

Postup úpravy existující sady záznamů jsou podobné kroky, které můžete provést při přidávání nebo odebírání záznamů z sada záznamů:

1. Načtení záznamu stávajícího nastavit pomocí `Get-AzureRmDnsRecordSet`.
2. Upravte objekt místní sady záznamů podle:
    * Přidání nebo odebrání záznamů
    * Změna parametrů existující záznamy
    * Změna záznamu nastavte metadata a čas pro live (TTL)
3. Potvrdit změny pomocí `Set-AzureRmDnsRecordSet` rutiny. To *nahrazuje* existující sady záznamů v Azure DNS s zadané sady záznamů.

Při použití `Set-AzureRmDnsRecordSet`, [kontroluje Etag](dns-zones-records.md#etags) zajišťují souběžných změny se nepřepíšou. Můžete použít nepovinný `-Overwrite` přepínač tak, aby potlačit těchto kontrol.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Chcete-li aktualizovat záznam, v existující sady záznamů

V tomto příkladu se nám změnit IP adresu existující "A" záznamu:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Úprava záznamu SOA

Nelze přidat nebo odebrat záznamy z automaticky vytvořený záznam SOA nastavit ve vrcholu zóny (`-Name "@"`, včetně uvozovek nahoře). Ale můžete upravit některý z parametrů v rámci záznamu SOA (s výjimkou "hostitel") a hodnota TTL sady záznamu.

Následující příklad ukazuje, jak změnit *e-mailu* vlastnost záznamu SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Chcete-li upravit NS záznamů ve vrcholu zóny

S každou zónou DNS se automaticky vytvoří záznam NS nastavit ve vrcholu zóny. Obsahuje názvy názvových serverů Azure DNS přiřadit do zóny.

Můžete přidat další název nastavit servery na tento záznam NS, podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit TTL a metadat pro tuto sadu záznamů. Však nelze odebrat ani změnit předem vyplněná názvových serverů Azure DNS.

Všimněte si, že vztahuje se pouze na vrcholu zóny sady záznamů NS. Jiné sady záznamů NS v pásmu (jak je používá delegovat podřízených zónách) můžete změnit bez omezení.

Následující příklad ukazuje, jak přidat do sady ve vrcholu zóny záznamů NS k další název serveru:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Chcete-li upravit metadata sady záznamů

[Sady záznamů metadat](dns-zones-records.md#tags-and-metadata) lze přidružit každá sada záznamů specifická data jako páry klíč hodnota.

Následující příklad ukazuje, jak upravit metadata existující sady záznamů:

```powershell
# Get the record set
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzureRmDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Odstranit sadu záznamů

Sady záznamů lze odstranit pomocí `Remove-AzureRmDnsRecordSet` rutiny. Odstranění sady záznamů se také odstraní všechny záznamy v sadě záznamů.

> [!NOTE]
> Nelze odstranit, SOA a sady záznamů NS ve vrcholu zóny (`-Name '@'`).  Azure DNS tyto vytvořena automaticky při zóny byl vytvořen a je při odstranění zóny automaticky odstraní.

Následující příklad ukazuje, jak odstranit sadu záznamů. V tomto příkladu název sady záznamů, typ sady záznamů, název zóny a skupina prostředků se každý zadaný explicitně.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Sada záznamů můžete alternativně zadat podle názvu a typu a zóny zadán pomocí objektu:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Jako pomocí třetí volby, samotné sady záznamů je možné zadat pomocí objekt sada záznamů:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Když zadáte sady k odstranění pomocí objektu sady záznamů, záznamů [kontroluje Etag](dns-zones-records.md#etags) zajišťují souběžných změny nebudou odstraněny. Můžete použít nepovinný `-Overwrite` přepínač tak, aby potlačit těchto kontrol.

Objekt sady záznamů lze také přesměrovat místo předávány jako parametr:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` a `Remove-AzureRmDnsRecordSet` podporují výzvy k potvrzení.

Každá rutina vyzve k potvrzení, pokud `$ConfirmPreference` proměnné předvoleb prostředí PowerShell má hodnotu `Medium` nebo nižší. Protože výchozí hodnota pro `$ConfirmPreference` je `High`, tyto výzvy nejsou zadána při pomocí výchozích nastavení prostředí PowerShell.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí. 

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Další postup

Další informace o [zóny a záznamy v Azure DNS](dns-zones-records.md).
<br>
Zjistěte, jak [chránit zóny a záznamy](dns-protect-zones-recordsets.md) při použití Azure DNS.
<br>
Zkontrolujte [Azure DNS PowerShell referenční dokumentaci k nástroji](/powershell/module/azurerm.dns).
