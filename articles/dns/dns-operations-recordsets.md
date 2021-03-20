---
title: Správa záznamů DNS v Azure DNS pomocí Azure PowerShell | Microsoft Docs
description: Správa sad záznamů DNS a záznamů na Azure DNS při hostování vaší domény v Azure DNS. Všechny příkazy PowerShellu pro operace se sadami záznamů a záznamy.
services: dns
documentationcenter: na
author: rohinkoul
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: f4e713f54ab4702b21763dc9fc6c7b606f94a945
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011587"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Správa záznamů a sad záznamů DNS v Azure DNS pomocí Azure PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Rozhraní příkazového řádku Azure Classic](./dns-operations-recordsets-cli.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

V tomto článku se dozvíte, jak spravovat záznamy DNS pro zónu DNS pomocí Azure PowerShell. Záznamy DNS se dají spravovat taky pomocí [Azure CLI](dns-operations-recordsets-cli.md) pro různé platformy nebo [Azure Portal](dns-operations-recordsets-portal.md).

V příkladech v tomto článku se předpokládá, že jste už [nainstalovali Azure PowerShell, přihlášeni a vytvořili ZÓNU DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Vytvořit nový záznam DNS

Pokud má nový záznam stejný název a typ jako existující záznam, budete [ho muset přidat do existující sady záznamů](#add-a-record-to-an-existing-record-set). Pokud má nový záznam jiný název a typ pro všechny existující záznamy, je nutné vytvořit novou sadu záznamů. 

### <a name="create-a-records-in-a-new-record-set"></a>Vytvoření záznamů A v nové sadě záznamů

Sady záznamů vytvoříte pomocí rutiny `New-AzDnsRecordSet`. Při vytváření sady záznamů je nutné zadat název sady záznamů, zónu, hodnotu TTL (Time to Live), typ záznamu a záznamy, které mají být vytvořeny.

Parametry pro přidání záznamů do sady záznamů se liší podle typu sady záznamů. Například při použití sady záznamů typu A je třeba zadat IP adresu pomocí parametru `-IPv4Address` . Další parametry jsou používány pro jiné typy záznamů. Podrobnosti najdete v tématu Další příklady typů záznamů.

V následujícím příkladu je vytvořena sada záznamů s relativním názvem "www" v zóně DNS "contoso.com". Plně kvalifikovaný název sady záznamů je "www.contoso.com". Typ záznamu je a, hodnota TTL je 3600 sekund. Sada záznamů obsahuje jeden záznam s IP adresou "1.2.3.4".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Pokud chcete vytvořit sadu záznamů na vrcholu zóny (v tomto případě ' contoso.com '), použijte název sady záznamů ' \@ ' (kromě uvozovek):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Pokud potřebujete vytvořit sadu záznamů obsahující více než jeden záznam, nejprve vytvořte místní pole a přidejte záznamy a pak předejte pole následujícím `New-AzDnsRecordSet` způsobem:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít pro přiřazení dat specifických pro aplikaci ke každé sadě záznamů, jako páry klíč-hodnota. Následující příklad ukazuje, jak vytvořit sadu záznamů se dvěma položkami metadat, "Odděl = finance" a "Environment = produkce".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS podporuje taky prázdné sady záznamů, které můžou fungovat jako zástupný symbol pro rezervaci názvu DNS před vytvořením záznamů DNS. Prázdné sady záznamů jsou viditelné v Azure DNS rovině ovládacího prvku, ale zobrazí se na Azure DNS názvových serverech. Následující příklad vytvoří prázdnou sadu záznamů:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Vytváření záznamů dalších typů

V následujících příkladech se dozvíte, jak vytvořit záznamy A, a ukazuje, jak vytvořit záznamy jiných typů záznamů podporovaných nástrojem Azure DNS.

V každém případě ukážeme, jak vytvořit sadu záznamů obsahující jeden záznam. Předchozí příklady pro záznamy A je možné upravit tak, aby se vytvořily sady záznamů dalších typů, které obsahují několik záznamů, metadata nebo vytvořit prázdné sady záznamů.

Neposkytujeme příklad pro vytvoření sady záznamů SOA, protože SOAs se vytváří a odstraňují s každou zónou DNS a nedá se vytvořit ani odstranit samostatně. [Záznam SOA je však možné upravit, jak je znázorněno v pozdějším příkladu](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Vytvoření sady záznamů AAAA s jedním záznamem

```powershell
New-AzDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Vytvoření sady záznamů CAA s jedním záznamem

```powershell
New-AzDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Vytvoření sady záznamů CNAME s jedním záznamem

> [!NOTE]
> Standardy DNS nepovolují záznamy CNAME na vrcholu zóny ( `-Name '@'` ), ani nepovolují sady záznamů obsahující více než jeden záznam.
> 
> Další informace najdete v tématu [záznamy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Vytvoření sady záznamů MX s jedním záznamem

V tomto příkladu používáme název sady záznamů ' \@ ' k vytvoření záznamu MX ve vrcholu zóny (v tomto případě ' contoso.com ').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Vytvoření sady záznamů NS s jedním záznamem

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Vytvoření sady záznamů PTR s jedním záznamem

V tomto případě "my-arpa-zone.com" představuje zónu zpětného vyhledávání ARPA představující rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres. Název záznamu ' 10 ' je poslední oktet IP adresy v rámci tohoto rozsahu IP adres reprezentovaného tímto záznamem.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Vytvoření sady záznamů SRV s jedním záznamem

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records)zadejte *\_ službu* a *\_ protokol* v názvu sady záznamů. \@Při vytváření sady záznamů SRV v vrcholu zóny není nutné do názvu sady záznamů zahrnout ' '.

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Vytvoření sady záznamů TXT s jedním záznamem

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délce řetězce podporované v záznamech TXT najdete v tématu [záznamy TXT](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Získání sady záznamů

K načtení existující sady záznamů použijte `Get-AzDnsRecordSet` . Tato rutina vrátí místní objekt, který představuje sadu záznamů v Azure DNS.

Stejně jako v `New-AzDnsRecordSet` případě musí být zadaný název sady záznamů *relativní* název, což znamená, že musí název zóny vyloučit. Je také nutné zadat typ záznamu a zónu obsahující sadu záznamů.

Následující příklad ukazuje, jak načíst sadu záznamů. V tomto příkladu je zóna určena pomocí `-ZoneName` `-ResourceGroupName` parametrů a.

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativně můžete také zadat zónu pomocí objektu zóny, která byla předána pomocí `-Zone` parametru.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Seznam sad záznamů

Můžete také použít `Get-AzDnsZone` k vypsání sad záznamů v zóně, a to vynecháním `-Name` parametrů a/nebo `-RecordType` .

Následující příklad vrátí všechny sady záznamů v zóně:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Následující příklad ukazuje, jak lze načíst všechny sady záznamů daného typu zadáním typu záznamu při vynechání názvu sady záznamů:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Chcete-li načíst všechny sady záznamů se zadaným názvem, pro různé typy záznamů, je nutné načíst všechny sady záznamů a následně vyfiltrovat výsledky:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Ve všech výše uvedených příkladech může být zóna zadána buď pomocí `-ZoneName` `-ResourceGroupName` parametrů a (jak je znázorněno), nebo zadáním objektu zóny:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidání záznamu do existující sady záznamů

Chcete-li přidat záznam do existující sady záznamů, postupujte podle následujících tří kroků:

1. Získání existující sady záznamů

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Přidejte nový záznam do místní sady záznamů. Jedná se o offline operaci.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpátky do služby Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Při použití se `Set-AzDnsRecordSet` *nahradí* existující sada záznamů v Azure DNS (a všechny záznamy, které obsahuje) se zadanou sadou záznamů. [Kontroly ETag](dns-zones-records.md#etags) se používají k zajištění toho, aby souběžné změny nebyly přepsány. `-Overwrite`Tyto kontroly můžete potlačit pomocí volitelného přepínače.

Tato posloupnost operací se dá také přesměrovat do *kanálu*, což znamená, že předáte objekt sady záznamů pomocí kanálu a nebudete ho moct předat jako parametr:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Výše uvedené příklady ukazují, jak přidat záznam "A" do existující sady záznamů typu "A". Podobná posloupnost operací se používá k přidání záznamů do sad záznamů jiných typů a nahrazení `-Ipv4Address` parametru `Add-AzDnsRecordConfig` s jinými parametry specifickými pro každý typ záznamu. Parametry pro každý typ záznamu jsou stejné jako u `New-AzDnsRecordConfig` rutiny, jak je znázorněno v dalších příkladech typu záznamu.

Sady záznamů typu "CNAME" nebo "SOA" nemohou obsahovat více než jeden záznam. Toto omezení se projevuje ve standardech DNS. Nejedná se o omezení Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odebrat záznam z existující sady záznamů

Proces odebrání záznamu ze sady záznamů je podobný procesu přidání záznamu do existující sady záznamů:

1. Získání existující sady záznamů

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Odeberte záznam z místního objektu sady záznamů. Jedná se o offline operaci. Odebraný záznam musí mít přesnou shodu s existujícím záznamem ve všech parametrech.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpátky do služby Azure DNS. Pomocí volitelného `-Overwrite` přepínače potlačíte [kontroly značek ETag](dns-zones-records.md#etags) pro souběžné změny.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Když pomocí výše uvedeného pořadí odeberete poslední záznam ze sady záznamů, sada záznamů se neodstraní a místo toho opustí prázdnou sadu záznamů. Pokud chcete sadu záznamů úplně odebrat, přečtěte si téma [odstranění sady záznamů](#delete-a-record-set).

Podobně jako při přidávání záznamů do sady záznamů lze také vytvořit kanál operací pro odebrání sady záznamů:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Různé typy záznamů jsou podporovány předáním příslušných parametrů specifických pro typ `Remove-AzDnsRecordSet` . Parametry pro každý typ záznamu jsou stejné jako u `New-AzDnsRecordConfig` rutiny, jak je znázorněno v dalších příkladech typu záznamu.


## <a name="modify-an-existing-record-set"></a>Úprava existující sady záznamů

Postup změny existující sady záznamů je podobný postupům, které provedete při přidávání nebo odebírání záznamů ze sady záznamů:

1. Načtení existující sady záznamů pomocí `Get-AzDnsRecordSet` .
2. Upravte objekt místní sady záznamů pomocí:
    * Přidávání nebo odebírání záznamů
    * Změna parametrů existujících záznamů
    * Změna metadat sady záznamů a hodnoty TTL (Time to Live)
3. Potvrďte změny pomocí `Set-AzDnsRecordSet` rutiny. Tím se *nahradí* existující sada záznamů v Azure DNS se specifikovanou sadou záznamů.

Při použití `Set-AzDnsRecordSet` jsou [kontroly ETag](dns-zones-records.md#etags) použity k zajištění toho, aby souběžné změny nebyly přepsány. `-Overwrite`Tyto kontroly můžete potlačit pomocí volitelného přepínače.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aktualizace záznamu v existující sadě záznamů

V tomto příkladu změníme IP adresu existujícího záznamu A:

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Postup úpravy záznamu SOA

Nemůžete přidávat ani odebírat záznamy z automaticky vytvořené sady záznamů SOA ve vrcholu zóny ( `-Name "@"` včetně uvozovek). Můžete však změnit libovolný parametr v rámci záznamu SOA (kromě "hostitel") a hodnoty TTL sady záznamů.

Následující příklad ukazuje, jak změnit vlastnost *email* záznamu SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Úprava záznamů NS ve vrcholu zóny

Záznam NS nastavený na vrcholu zóny se automaticky vytvoří s každou zónou DNS. Obsahuje názvy Azure DNS názvových serverů přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, aby se podporovaly domény spoluhostování s více než jedním poskytovatelem DNS. Můžete také upravit hodnotu TTL a metadata této sady záznamů. Nemůžete ale odebrat ani změnit předem vyplněné Azure DNS názvové servery.

Všimněte si, že to platí jenom pro záznam NS, který je nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako používané pro delegování podřízených zón) se dají upravovat bez omezení.

Následující příklad ukazuje, jak přidat další názvový server do sady záznamů NS ve vrcholu zóny:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Úprava metadat sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít pro přiřazení dat specifických pro aplikaci ke každé sadě záznamů, jako páry klíč-hodnota.

Následující příklad ukazuje, jak změnit metadata existující sady záznamů:

```powershell
# Get the record set
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"

# Add 'dept=finance' name-value pair
$rs.Metadata.Add('dept', 'finance') 

# Remove metadata item named 'environment'
$rs.Metadata.Remove('environment')  

# Commit changes
Set-AzDnsRecordSet -RecordSet $rs
```


## <a name="delete-a-record-set"></a>Odstranění sady záznamů

Sady záznamů lze odstranit pomocí `Remove-AzDnsRecordSet` rutiny. Odstraněním sady záznamů dojde také k odstranění všech záznamů v rámci sady záznamů.

> [!NOTE]
> Sady záznamů SOA a NS nelze odstranit v zóně vrcholu ( `-Name '@'` ).  Tato Azure DNS vytvořena automaticky při vytvoření zóny a automaticky je odstraní při odstranění zóny.

Následující příklad ukazuje, jak odstranit sadu záznamů. V tomto příkladu jsou explicitně specifikované název sady záznamů, typ sady záznamů, název zóny a skupina prostředků.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativně lze sadu záznamů zadat podle názvu a typu a zóny určené pomocí objektu:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Třetí možností je, že samotná sada záznamů může být zadaná pomocí objektu sady záznamů:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Když zadáte sadu záznamů, která se má odstranit pomocí objektu sady záznamů, pro zajištění, že se neodstraní souběžné změny, se použijí [kontroly ETag](dns-zones-records.md#etags) . `-Overwrite`Tyto kontroly můžete potlačit pomocí volitelného přepínače.

Objekt sady záznamů lze také přesměrovat, místo aby byl předán jako parametr:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` a `Remove-AzDnsRecordSet` podporují výzvy k potvrzení.

Každá rutina vyzve k potvrzení, pokud `$ConfirmPreference` má proměnná předvolby PowerShellu hodnotu `Medium` nebo nižší. Vzhledem k tomu, že výchozí hodnota pro `$ConfirmPreference` je `High` , tyto výzvy se při použití výchozího nastavení PowerShellu neobsahují.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí. 

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Další kroky

Další informace o [zónách a záznamech v Azure DNS](dns-zones-records.md).
<br>
Naučte se [chránit zóny a záznamy](dns-protect-zones-recordsets.md) při použití Azure DNS.
<br>
Přečtěte si [referenční dokumentaci k Azure DNS PowerShellu](/powershell/module/az.dns).