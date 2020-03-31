---
title: Správa záznamů DNS ve službě Azure DNS pomocí Azure PowerShellu | Dokumenty společnosti Microsoft
description: Správa sad záznamů DNS a záznamů na Azure DNS při hostování vaší domény na Azure DNS. Všechny příkazy prostředí PowerShell pro operace v sadách záznamů a záznamech.
services: dns
documentationcenter: na
author: rohinkoul
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: rohink
ms.openlocfilehash: b9244d9b2bdc9cb20195bbc103c0b1eb48a9de63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932534"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Správa záznamů DNS a sad záznamů ve Službě Azure DNS pomocí Azure PowerShellu

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure Classic CLI](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Tento článek ukazuje, jak spravovat záznamy DNS pro zónu DNS pomocí Azure PowerShellu. Záznamy DNS lze spravovat také pomocí příkazového [příkazu Azure](dns-operations-recordsets-cli.md) pro různé platformy nebo [portálu Azure](dns-operations-recordsets-portal.md).

Příklady v tomto článku předpokládají, že jste už [nainstalovali Azure PowerShell, přihlásili se a vytvořili zónu DNS](dns-operations-dnszones.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Vytvoření nového záznamu DNS

Pokud má nový záznam stejný název a typ jako existující záznam, je třeba [jej přidat do existující sady záznamů](#add-a-record-to-an-existing-record-set). Pokud má nový záznam jiný název a typ než všechny existující záznamy, je třeba vytvořit novou sadu záznamů. 

### <a name="create-a-records-in-a-new-record-set"></a>Vytvoření záznamů "A" v nové sadě záznamů

Sady záznamů vytvoříte pomocí rutiny `New-AzDnsRecordSet`. Při vytváření sady záznamů je třeba zadat název sady záznamů, zónu, aktuální čas (TTL), typ záznamu a záznamy, které mají být vytvořeny.

Parametry pro přidání záznamů do sady záznamů se liší podle typu sady záznamů. Například při použití sady záznamů typu "A" je třeba zadat adresu `-IPv4Address`IP pomocí parametru . Další parametry se používají pro jiné typy záznamů. Podrobnosti naleznete v příkladech dalšího typu záznamu.

Následující příklad vytvoří sadu záznamů s relativním názvem www v zóně DNS "contoso.com". Plně kvalifikovaný název sady záznamů je "www.contoso.com". Typ záznamu je 'A' a TTL je 3600 sekund. Sada záznamů obsahuje jeden záznam s IP adresou '1.2.3.4'.

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Chcete-li vytvořit záznam nastavený na vrcholu zóny (v tomto případě "contoso.com"),\@použijte název sady záznamů ' ' (kromě uvozovek):

```powershell
New-AzDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Pokud potřebujete vytvořit sadu záznamů obsahující více než jeden záznam, nejprve vytvořte místní pole `New-AzDnsRecordSet` a přidejte záznamy a poté předejte pole následujícím způsobem:

```powershell
$aRecords = @()
$aRecords += New-AzDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít k přidružení dat specifických pro aplikaci ke každé sadě záznamů jako dvojice klíč-hodnota. Následující příklad ukazuje, jak vytvořit sadu záznamů se dvěma položkami metadat, "dept=finance" a "environment=production".

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS také podporuje "prázdné" sady záznamů, které mohou fungovat jako zástupný symbol pro rezervaci názvu DNS před vytvořením záznamů DNS. Prázdné sady záznamů jsou viditelné v rovině ovládacího prvku Azure DNS, ale zobrazují se na názvových serverech Azure DNS. Následující příklad vytvoří prázdnou sadu záznamů:

```powershell
New-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Vytvoření záznamů jiných typů

Po podrobném zobrazení, jak vytvořit záznamy "A", následující příklady ukazují, jak vytvořit záznamy jiných typů záznamů podporovaných službou Azure DNS.

V každém případě ukážeme, jak vytvořit sadu záznamů obsahující jeden záznam. Dřívější příklady záznamů "A" lze upravit tak, aby vytvářely sady záznamů jiných typů obsahujících více záznamů s metadaty nebo pro vytváření prázdných sad záznamů.

Neuvádíme příklad pro vytvoření sady záznamů SOA, protože SOA jsou vytvořeny a odstraněny s každou zónou DNS a nelze je vytvořit ani odstranit samostatně. Soa však [může být změněn, jak je znázorněno v pozdějším příkladu](#to-modify-an-soa-record).

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
> Standardy DNS nepovolují záznamy CNAME na`-Name '@'`vrcholu zóny ( ), ani nepovolují sady záznamů obsahující více než jeden záznam.
> 
> Další informace naleznete v tématu [CNAME records](dns-zones-records.md#cname-records).


```powershell
New-AzDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Vytvoření sady záznamů MX s jedním záznamem

V tomto příkladu použijeme název\@sady záznamů ' ' k vytvoření záznamu MX na vrcholu zóny (v tomto případě 'contoso.com').


```powershell
New-AzDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Vytvoření sady záznamů NS s jedním záznamem

```powershell
New-AzDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Vytvoření sady záznamů PTR s jedním záznamem

V tomto případě "my-arpa-zone.com" představuje zónu zpětného vyhledávání ARPA představující rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres. Název záznamu 10 je posledním oktetem adresy IP v tomto rozsahu IP adres reprezentovaném tímto záznamem.

```powershell
New-AzDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Vytvoření sady záznamů SRV s jedním záznamem

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records)zadejte * \_službu* a * \_protokol* v názvu sady záznamů. Při vytváření záznamu SRV nastaveného na vrcholu zóny není třeba zahrnout do názvu sady záznamů .\@

```powershell
New-AzDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Vytvoření sady záznamů TXT s jedním záznamem

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délce řetězce podporované v záznamech TXT naleznete v [tématu TXT records](dns-zones-records.md#txt-records).

```powershell
New-AzDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Získání sady záznamů

Chcete-li načíst existující `Get-AzDnsRecordSet`sadu záznamů, použijte . Tato rutina vrátí místní objekt, který představuje záznam nastavený v Azure DNS.

Stejně `New-AzDnsRecordSet`jako u , musí být uvedený název sady záznamů *relativní* název, což znamená, že musí vyloučit název zóny. Musíte také zadat typ záznamu a zónu obsahující sadu záznamů.

Následující příklad ukazuje, jak načíst sadu záznamů. V tomto příkladu je zóna určena pomocí parametrů `-ZoneName` a. `-ResourceGroupName`

```powershell
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativně můžete také určit zónu pomocí objektu `-Zone` zóny, který je předán pomocí parametru.

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Sady záznamů seznamu

Můžete také `Get-AzDnsZone` použít k vyseznamování sad záznamů `-Name` v zóně `-RecordType` vynecháním parametrů a/nebo parametrů.

Následující příklad vrátí všechny sady záznamů v zóně:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Následující příklad ukazuje, jak lze načíst všechny sady záznamů daného typu zadáním typu záznamu při vynechání názvu sady záznamů:

```powershell
$recordsets = Get-AzDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Chcete-li načíst všechny sady záznamů s daným názvem, napříč typy záznamů je třeba načíst všechny sady záznamů a potom filtrovat výsledky:

```powershell
$recordsets = Get-AzDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Ve všech výše uvedených příkladech lze zónu `-ZoneName` `-ResourceGroupName`zadat buď pomocí parametrů a (jak je znázorněno), nebo zadáním objektu zóny:

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

2. Přidejte nový záznam do místní sady záznamů. Jedná se o off-line operaci.

    ```powershell
    Add-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět do služby Azure DNS. 

    ```powershell
    Set-AzDnsRecordSet -RecordSet $rs
    ```

Použití `Set-AzDnsRecordSet` *nahradí* existující sadu záznamů v Azure DNS (a všechny záznamy, které obsahuje) se zadanou sadou záznamů. [Etag kontroly](dns-zones-records.md#etags) se používají k zajištění souběžných změn nejsou přepsány. Volitelné `-Overwrite` přepínače můžete potlačit tyto kontroly.

Tato posloupnost operací může být také *potrubím*, což znamená, že předáte objekt sady záznamů pomocí kanálu, nikoli jej předáte jako parametr:

```powershell
Get-AzDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Výše uvedené příklady ukazují, jak přidat záznam "A" do existující sady záznamů typu A. Podobná posloupnost operací se používá k přidání záznamů do sad záznamů `-Ipv4Address` jiných `Add-AzDnsRecordConfig` typů a nahrazení parametru jinými parametry specifickými pro každý typ záznamu. Parametry pro každý typ záznamu jsou `New-AzDnsRecordConfig` stejné jako pro rutinu, jak je znázorněno v příkladech typu další záznam výše.

Sady záznamů typu CNAME nebo SOA nesmí obsahovat více než jeden záznam. Toto omezení vyplývá ze standardů DNS. Nejedná se o omezení Služby Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odebrání záznamu z existující sady záznamů

Proces odebrání záznamu ze sady záznamů je podobný procesu přidání záznamu do existující sady záznamů:

1. Získání existující sady záznamů

    ```powershell
    $rs = Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Odeberte záznam z objektu místní sady záznamů. Jedná se o off-line operaci. Odebraný záznam musí být přesnou shodou s existujícím záznamem napříč všemi parametry.

    ```powershell
    Remove-AzDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět do služby Azure DNS. Pomocí volitelného `-Overwrite` přepínače potlačte [kontroly Etag](dns-zones-records.md#etags) u souběžných změn.

    ```powershell
    Set-AzDnsRecordSet -RecordSet $Rs
    ```

Při použití výše uvedené sekvence k odebrání posledního záznamu ze sady záznamů sada záznamů neodstraní sadu záznamů, ale ponechá prázdnou sadu záznamů. Pokud chcete sadu záznamů odebrat úplně, [přečtěte si](#delete-a-record-set)informace o odstranění sady záznamů .

Podobně jako při přidávání záznamů do sady záznamů lze také pipedovat posloupnost operací k odebrání sady záznamů:

```powershell
Get-AzDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzDnsRecordSet
```

Různé typy záznamů jsou podporovány předáním příslušné `Remove-AzDnsRecordSet`parametry specifické pro daný typ . Parametry pro každý typ záznamu jsou `New-AzDnsRecordConfig` stejné jako pro rutinu, jak je znázorněno v příkladech typu další záznam výše.


## <a name="modify-an-existing-record-set"></a>Úprava existující sady záznamů

Kroky pro úpravu existující sady záznamů jsou podobné krokům, které provedete při přidávání nebo odebírání záznamů ze sady záznamů:

1. Načtení existující sady `Get-AzDnsRecordSet`záznamů pomocí aplikace .
2. Upravte objekt místní sady záznamů takto:
    * Přidání nebo odebrání záznamů
    * Změna parametrů existujících záznamů
    * Změna metadat sady záznamů a času na život (TTL)
3. Sute změny `Set-AzDnsRecordSet` pomocí rutiny. Tím *se nahradí* existující sada záznamů v Azure DNS zadanou sadou záznamů.

Při `Set-AzDnsRecordSet`použití se [kontroly Etag](dns-zones-records.md#etags) používají k zajištění souběžných změn, které nejsou přepsány. Volitelné `-Overwrite` přepínače můžete potlačit tyto kontroly.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aktualizace záznamu v existující sadě záznamů

V tomto příkladu změníme IP adresu existujícího záznamu "A":

```powershell
$rs = Get-AzDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Úprava záznamu SOA

Záznamy nelze přidávat ani odebírat z automaticky vytvořeného záznamu`-Name "@"`SOA nastaveného na vrcholu zóny ( , včetně uvozovek). Můžete však upravit libovolný parametr v záznamu SOA (s výjimkou "Host") a sady záznamů TTL.

Následující příklad ukazuje, jak změnit vlastnost *Email* záznamu SOA:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Úprava záznamů NS na vrcholu zóny

Záznam NS nastavený na vrcholu zóny je automaticky vytvořen s každou zónou DNS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, které podporují co-hostingové domény s více než jedním poskytovatelem DNS. Můžete také upravit TTL a metadata pro tuto sadu záznamů. Předem vyplněné názvové servery Azure DNS však nelze odebrat ani upravit.

Všimněte si, že to platí pouze pro záznam NS nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako slouží k delegování podřízených zón) lze změnit bez omezení.

Následující příklad ukazuje, jak přidat další názvový server do záznamu NS nastaveného na vrcholu zóny:

```powershell
$rs = Get-AzDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Změna metadat sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít k přidružení dat specifických pro aplikaci ke každé sadě záznamů jako dvojice klíč-hodnota.

Následující příklad ukazuje, jak upravit metadata existující sady záznamů:

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

Sady záznamů lze odstranit `Remove-AzDnsRecordSet` pomocí rutiny. Odstraněním sady záznamů se také odstraní všechny záznamy v rámci sady záznamů.

> [!NOTE]
> Sady záznamů SOA a NS nelze odstranit na`-Name '@'`vrcholu zóny ( ).  Azure DNS je vytvořil automaticky při vytvoření zóny a automaticky je odstraní při odstranění zóny.

Následující příklad ukazuje, jak odstranit sadu záznamů. V tomto příkladu je explicitně zadán název sady záznamů, typ sady záznamů, název zóny a skupina prostředků.

```powershell
Remove-AzDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Případně lze sadu záznamů zadat podle názvu a typu a zónu určenou pomocí objektu:

```powershell
$zone = Get-AzDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Jako třetí možnost lze samotnou sadu záznamů zadat pomocí objektu sady záznamů:

```powershell
$rs = Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzDnsRecordSet -RecordSet $rs
```

Pokud zadáte sadu záznamů, která má být odstraněna pomocí objektu sady záznamů, [kontroly Etag](dns-zones-records.md#etags) se používají k zajištění souběžných změn. Volitelné `-Overwrite` přepínače můžete potlačit tyto kontroly.

Objekt sady záznamů lze také piped místo předání jako parametr:

```powershell
Get-AzDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzDnsRecordSet
```

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzDnsRecordSet`, `Set-AzDnsRecordSet` a `Remove-AzDnsRecordSet` podporují výzvy k potvrzení.

Každá rutina vyzve k potvrzení, pokud má proměnná předvoleb `$ConfirmPreference` Prostředí PowerShell hodnotu `Medium` nebo nižší. Vzhledem k `$ConfirmPreference` tomu, že výchozí hodnota pro je `High`, tyto výzvy nejsou uvedeny při použití výchozího nastavení prostředí PowerShell.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí. 

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables).

## <a name="next-steps"></a>Další kroky

Další informace o [zónách a záznamech v Azure DNS](dns-zones-records.md).
<br>
Zjistěte, jak [chránit zóny a záznamy](dns-protect-zones-recordsets.md) při používání Azure DNS.
<br>
Projděte si [referenční dokumentaci k Prostředí Azure DNS PowerShell](/powershell/module/az.dns).
