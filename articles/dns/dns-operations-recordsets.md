---
title: Správa záznamů DNS v DNS Azure pomocí Azure Powershellu | Dokumentace Microsoftu
description: Správa DNS sad záznamů a záznamů v Azure DNS, při hostování vaší domény ve službě Azure DNS. Všechny příkazy prostředí PowerShell pro operace v sadách záznamů a záznamy.
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: 7136a373-0682-471c-9c28-9e00d2add9c2
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 12/21/2016
ms.author: victorh
ms.openlocfilehash: c60dded96df091b1a715fb7b972e9d7a23608d44
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818817"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-azure-powershell"></a>Správa DNS záznamů a sad záznamů v DNS Azure pomocí Azure Powershellu

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure Classic CLI](dns-operations-recordsets-cli-nodejs.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

V tomto článku se dozvíte, jak spravovat záznamy DNS pro zónu DNS pomocí Azure Powershellu. Je také možné spravovat záznamy DNS pomocí napříč platformami [rozhraní příkazového řádku Azure](dns-operations-recordsets-cli.md) nebo [webu Azure portal](dns-operations-recordsets-portal.md).

V příkladech v tomto článku předpokládá, že jste již [nainstalovaný Azure PowerShell, přihlásili jste se a vytvořili jste zónu DNS](dns-operations-dnszones.md).

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).


## <a name="create-a-new-dns-record"></a>Vytvoří nový záznam DNS

Pokud má nový záznam stejný název a typ jako existující záznam, budete muset [ho přidat do existující sady záznamů](#add-a-record-to-an-existing-record-set). Pokud má nový záznam jiný název a typ všechny existující záznamy, musíte vytvořit novou sadu záznamů. 

### <a name="create-a-records-in-a-new-record-set"></a>Vytvoření záznamů "A" v nové sady záznamů

Sady záznamů vytvoříte pomocí rutiny `New-AzureRmDnsRecordSet`. Při vytváření sady záznamů, je třeba zadat záznam nastavte název, zónu, čas na live (TTL), typ záznamu a záznamy, který se má vytvořit.

Parametry pro přidání záznamů do sady záznamů se liší podle typu sady záznamů. Například pokud používáte sadu záznamů typu "A", je třeba zadat IP adresu pomocí parametru `-IPv4Address`. Další parametry jsou používány pro další typy záznamů. Příklady dalších typů záznamu podrobnosti.

Následující příklad vytvoří záznam s relativním názvem "www" nastavit v zóně DNS "contoso.com". Plně kvalifikovaný název sady záznamů je "www.contoso.com". Typ záznamu je "A", a hodnota TTL je 3 600 sekund. Sada záznamů obsahuje jediný záznam, s IP adresou "1.2.3.4".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Vytvořit sadu záznamů ve "vrcholu" zóny (v tomto případě "contoso.com"), použijte název sady záznamů "\@" (bez uvozovek):

```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") 
```

Pokud potřebujete vytvořit více než jeden záznam obsahující sadu záznamů, místního pole a přidat záznamy, vytvořte nejdříve předat pole do `New-AzureRmDnsRecordSet` následujícím způsobem:

```powershell
$aRecords = @()
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4"
$aRecords += New-AzureRmDnsRecordConfig -IPv4Address "2.3.4.5"
New-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName MyResourceGroup -Ttl 3600 -RecordType A -DnsRecords $aRecords
```

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) slouží k přidružení každá sada záznamů specifická data jako páry klíč hodnota. Následující příklad ukazuje, jak vytvořit sadu s dvě položky metadat záznamů "dept = finance" a "prostředí = produkční".

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -IPv4Address "1.2.3.4") -Metadata @{ dept="finance"; environment="production" } 
```

Azure DNS také podporuje "prázdný" sady záznamů, které můžou fungovat jako zástupný symbol rezervovat název DNS před vytvářením záznamů DNS. Prázdné sady záznamů jsou viditelné v rovině řízení Azure DNS, ale zobrazují na názvové servery Azure DNS. Následující příklad vytvoří prázdná sada záznamů:

```powershell
New-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords @()
```

## <a name="create-records-of-other-types"></a>Vytvoření záznamů z ostatních typů

S vidět v podrobnosti o tom, jak vytvořit záznamy "A", následující příklady ukazují, jak vytvořit záznamy o dalších typech záznamů podporovány službou Azure DNS.

V obou případech vám ukážeme, jak vytvořit sadu obsahující jednoho záznamu záznamů. Předchozí příklady "A" záznamů lze upravit k vytvoření sady záznamů jiných typů, který obsahuje více záznamů, se metadata, nebo chcete-li vytvořit prázdné sady záznamů.

Neposkytujeme příklad k vytvoření sady záznamů SOA od SOAs se vytvoří a odstraní s každou zónou DNS a nelze vytvořit ani odstranit samostatně. Ale [SOA lze upravit, jak je znázorněno v příkladu dále](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record-set-with-a-single-record"></a>Vytvoření sady záznamů AAAA s jedním záznamem

```powershell
New-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ipv6Address "2607:f8b0:4009:1803::1005") 
```

### <a name="create-a-caa-record-set-with-a-single-record"></a>Vytvořit sadu s jedním záznamem záznamů CAA

```powershell
New-AzureRmDnsRecordSet -Name "test-caa" -RecordType CAA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Caaflags 0 -CaaTag "issue" -CaaValue "ca1.contoso.com") 
```

### <a name="create-a-cname-record-set-with-a-single-record"></a>Vytvoření sady záznamů CNAME s jedním záznamem

> [!NOTE]
> Standardy DNS nepovolují záznamy CNAME ve vrcholu zóny (`-Name '@'`), ani umožňují sady záznamů, který obsahuje více než jeden záznam.
> 
> Další informace najdete v tématu [záznamy CNAME](dns-zones-records.md#cname-records).


```powershell
New-AzureRmDnsRecordSet -Name "test-cname" -RecordType CNAME -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Cname "www.contoso.com") 
```

### <a name="create-an-mx-record-set-with-a-single-record"></a>Vytvoření sady záznamů MX s jedním záznamem

V tomto příkladu vytvoříme s využitím názvu sady záznamů "\@' Chcete-li vytvořit záznam MX ve vrcholu zóny (v tomto případě"contoso.com").


```powershell
New-AzureRmDnsRecordSet -Name "@" -RecordType MX -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Exchange "mail.contoso.com" -Preference 5) 
```

### <a name="create-an-ns-record-set-with-a-single-record"></a>Vytvoření sady záznamů NS s jedním záznamem

```powershell
New-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Nsdname "ns1.contoso.com") 
```

### <a name="create-a-ptr-record-set-with-a-single-record"></a>Vytvoření sady záznamů PTR s jedním záznamem

V tomto případě "Moje-arpa-zone.com" představuje zónu zpětného vyhledávání ARPA představující váš rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres. Název záznamu "10" je poslední oktet IP adresu v rámci tohoto rozsahu IP adres reprezentována tento záznam.

```powershell
New-AzureRmDnsRecordSet -Name 10 -RecordType PTR -ZoneName "my-arpa-zone.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Ptrdname "myservice.contoso.com") 
```

### <a name="create-an-srv-record-set-with-a-single-record"></a>Vytvoření sady záznamů SRV s jedním záznamem

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records), zadejte  *\_služby* a  *\_protokol* v názvu sady záznamů. Není nutné zahrnout "\@" v názvu sady záznamů po vytvoření záznamů SRV sady ve vrcholu zóny.

```powershell
New-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Priority 0 -Weight 5 -Port 8080 -Target "sip.contoso.com") 
```


### <a name="create-a-txt-record-set-with-a-single-record"></a>Vytvoření sady záznamů TXT s jedním záznamem

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délku řetězce v záznamů TXT podporovány, naleznete v tématu [záznamů TXT](dns-zones-records.md#txt-records).

```powershell
New-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -Ttl 3600 -DnsRecords (New-AzureRmDnsRecordConfig -Value "This is a TXT record") 
```


## <a name="get-a-record-set"></a>Získání sady záznamů

Pokud chcete načíst existující sady záznamů, použijte `Get-AzureRmDnsRecordSet`. Tato rutina vrátí místní objekt, který představuje sady záznamů v Azure DNS.

Stejně jako u `New-AzureRmDnsRecordSet`, musí být zadaný název sady záznamů *relativní* název, což znamená, je nutné vyloučit název zóny. Také je třeba zadat typ záznamu a sadu zón, který obsahuje záznam.

Následující příklad ukazuje, jak načíst sady záznamů. V tomto příkladu je určen pomocí zóny `-ZoneName` a `-ResourceGroupName` parametry.

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Alternativně můžete také určení zóny pomocí objektu zóny předaným pomocí `-Zone` parametru.

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs = Get-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

## <a name="list-record-sets"></a>Seznam sad záznamů

Můžete také použít `Get-AzureRmDnsZone` do seznamu sad záznamů v zóně, vynecháním `-Name` a/nebo `-RecordType` parametry.

Následující příklad vrátí sady všech záznamů v zóně:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Následující příklad ukazuje, jak všech záznamů z daného typu můžete načíst tak, že zadáte typ záznamu při vynechání záznam název sady:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Načíst všechny sady záznamů s daným názvem, mezi typy záznamů, budete muset načíst všechny sady záznamů a potom vyfiltrujte z výsledků:

```powershell
$recordsets = Get-AzureRmDnsRecordSet -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | where {$_.Name.Equals("www")}
```

Ve všech výše uvedených příkladech zóny se dá nastavit pomocí `-ZoneName` a `-ResourceGroupName`parametry (jak je vidět), nebo zadáním objektu zóny:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
$recordsets = Get-AzureRmDnsRecordSet -Zone $zone
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidejte záznam do existující sady záznamů

Chcete-li přidat záznam do existující sady záznamů, použijte následující tři kroky:

1. Získat existující sady záznamů

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Přidáte nový záznam do místní sady záznamů. Toto je offline operace.

    ```powershell
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět do služby Azure DNS. 

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $rs
    ```

Pomocí `Set-AzureRmDnsRecordSet` *nahradí* existující sady záznamů v Azure DNS (a všechny záznamy, které obsahuje) pomocí zadané sady záznamů. [Značka Etag kontroly](dns-zones-records.md#etags) se používají k zajištění souběžných změn se nepřepíšou. Můžete použít nepovinnou `-Overwrite` přepínač tak, aby potlačit tyto kontroly.

Tato posloupnost operací může být také *směrované*, což znamená, předejte objekt sady záznamů pomocí kanálu, spíše než předá jako parametr:

```powershell
Get-AzureRmDnsRecordSet -Name "www" –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Add-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Výše uvedené příklady ukazují, jak přidat záznam "A" do existující sady záznamů typu "A". Podobně jako posloupnost operací se používá k přidání záznamů do sad záznamů z ostatních typů, přičemž nahradíte `-Ipv4Address` parametr `Add-AzureRmDnsRecordConfig` s další parametry, které jsou specifické pro jednotlivé typy záznamů. Parametry pro každý typ záznamu jsou stejné jako v případě `New-AzureRmDnsRecordConfig` rutiny, jak je znázorněno výše uvedené příklady dalších typů záznamu.

Sady záznamů typu "CNAME" nebo "SOA" nemůže obsahovat více než jeden záznam. Toto omezení mohou nastat z norem DNS. Není omezení Azure DNS.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odstranit záznam z existující sady záznamů

Postup odebrání záznamu ze sady záznamů je podobný procesu pro přidání záznamu do existující sady záznamů:

1. Získat existující sady záznamů

    ```powershell
    $rs = Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A
    ```

2. Odeberte záznam z objektu místní sadu záznamů. Toto je offline operace. Záznam, který má být odebrán musí být přesnou shodou s existujícím záznamem v rámci všech parametrů.

    ```powershell
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "5.6.7.8"
    ```

3. Potvrďte změnu zpět do služby Azure DNS. Použít nepovinný `-Overwrite` přepínač tak, aby potlačit [Etag kontroluje](dns-zones-records.md#etags) souběžných změn.

    ```powershell
    Set-AzureRmDnsRecordSet -RecordSet $Rs
    ```

Pomocí výše uvedených pořadí odebrat poslední záznam ze sady záznamů nedojde k odstranění sady záznamů, ale ponechá prázdná sada záznamů. Odeberte zcela sadu záznamů, najdete v článku [odstranit záznamovou sadu](#delete-a-record-set).

Podobně pro přidání záznamů do sady záznamů, posloupnost operací odebrat sadu záznamů můžete předat také rourou:

```powershell
Get-AzureRmDnsRecordSet -Name www –ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" -RecordType A | Remove-AzureRmDnsRecordConfig -Ipv4Address "5.6.7.8" | Set-AzureRmDnsRecordSet
```

Podporuje různé typy záznamů předávání do příslušné parametry specifické pro typ. `Remove-AzureRmDnsRecordSet`. Parametry pro každý typ záznamu jsou stejné jako v případě `New-AzureRmDnsRecordConfig` rutiny, jak je znázorněno výše uvedené příklady dalších typů záznamu.


## <a name="modify-an-existing-record-set"></a>Upravit existující sady záznamů

Postup pro úpravu existující sady záznamů je podobný kroky, které můžete provést při přidávání nebo odebírání záznamů z sada záznamů:

1. Načíst existující sady s použitím záznamů `Get-AzureRmDnsRecordSet`.
2. Upravte objekt místní sadu záznamů podle:
    * Přidávání nebo odebírání záznamů
    * Změna parametrů pro existující záznamy
    * Změna záznamu nastavte metadata a čas na live (TTL)
3. Potvrzení provedených změn pomocí `Set-AzureRmDnsRecordSet` rutiny. To *nahradí* existující sady záznamů v Azure DNS s zadané sady záznamů.

Při použití `Set-AzureRmDnsRecordSet`, [Etag kontroluje](dns-zones-records.md#etags) se používají k zajištění souběžných změn se nepřepíšou. Můžete použít nepovinnou `-Overwrite` přepínač tak, aby potlačit tyto kontroly.

### <a name="to-update-a-record-in-an-existing-record-set"></a>Aktualizovat záznam do existující sady záznamů

V tomto příkladu Změníme existující záznam "A" IP adresa:

```powershell
$rs = Get-AzureRmDnsRecordSet -name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Ipv4Address = "9.8.7.6"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-an-soa-record"></a>Úprava záznamu SOA

Nelze přidat ani odebrat záznamy automaticky vytvořený záznam SOA nastavit ve vrcholu zóny (`-Name "@"`, včetně uvozovek nahoře). Však můžete upravit některé parametry v záznamu SOA (s výjimkou "hostitel") a hodnota TTL sady záznamu.

Následující příklad ukazuje, jak změnit *e-mailu* vlastnosti záznamu SOA:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
$rs.Records[0].Email = "admin.contoso.com"
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>K úpravě záznamů NS ve vrcholu zóny

S každou zónou DNS se automaticky vytvoří sadu ve vrcholu zóny záznamů NS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Můžete přidat další názvové servery pro tento záznam NS nastavit podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit hodnotu TTL a metadata pro tuto sadu záznamů. Ale nejde odebrat ani změnit předem naplněných názvové servery Azure DNS.

Všimněte si, že to platí pouze pro záznam NS, nastavte ve vrcholu zóny. Bez omezení, lze upravit jinými sadami záznamů NS v pásmu (jak se používá k delegování podřízené zóny).

Následující příklad ukazuje, jak přidat další názvový server sadu ve vrcholu zóny záznamů NS:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Add-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname ns1.myotherdnsprovider.com
Set-AzureRmDnsRecordSet -RecordSet $rs
```

### <a name="to-modify-record-set-metadata"></a>Upravit metadata sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) slouží k přidružení každá sada záznamů specifická data jako páry klíč hodnota.

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


## <a name="delete-a-record-set"></a>Odstranit záznamovou sadu

Sady záznamů lze odstranit pomocí `Remove-AzureRmDnsRecordSet` rutiny. Odstranění záznamu sady také odstraní všechny záznamy v rámci sady záznamů.

> [!NOTE]
> Nelze odstranit SOA a sady záznamů NS ve vrcholu zóny (`-Name '@'`).  Azure DNS tyto vytvoří automaticky při zóny byla vytvořena a je odstraní automaticky při jejím odstranění.

Následující příklad ukazuje, jak odstranit záznamovou sadu. V tomto příkladu název sady záznamů, typ sady záznamů, název zóny a skupinu prostředků se každý zadaný explicitně.

```powershell
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
```

Můžete také sadu záznamů se dá nastavit tak, že název a typ a zóně zadat pomocí objektu:

```powershell
$zone = Get-AzureRmDnsZone -Name "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -Name "www" -RecordType A -Zone $zone
```

Třetí možností, samotné sady záznamů se dá nastavit pomocí objektu sady záznamů:

```powershell
$rs = Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup"
Remove-AzureRmDnsRecordSet -RecordSet $rs
```

Když zadáte sadu odstranit pomocí objektu sady záznamů, záznamů [Etag kontroluje](dns-zones-records.md#etags) se používají k zajištění souběžných změny nebudou odstraněny. Můžete použít nepovinnou `-Overwrite` přepínač tak, aby potlačit tyto kontroly.

Objekt sady záznamů, můžete také rourou místo předání jako parametru:

```powershell
Get-AzureRmDnsRecordSet -Name www -RecordType A -ZoneName "contoso.com" -ResourceGroupName "MyResourceGroup" | Remove-AzureRmDnsRecordSet
```

## <a name="confirmation-prompts"></a>Výzvy k potvrzení

Všechny rutiny `New-AzureRmDnsRecordSet`, `Set-AzureRmDnsRecordSet` a `Remove-AzureRmDnsRecordSet` podporují výzvy k potvrzení.

Každá rutina zobrazí výzvu k potvrzení, pokud `$ConfirmPreference` hodnotu má proměnná předvolby Powershellu `Medium` nebo nižší. Protože výchozí hodnota pro `$ConfirmPreference` je `High`, tyto výzvy nejsou uvedeny, při použití výchozího nastavení prostředí PowerShell.

Aktuální nastavení `$ConfirmPreference` můžete přepsat pomocí parametru `-Confirm`. Pokud zadáte `-Confirm` nebo `-Confirm:$True`, rutina před spuštěním zobrazí výzvu k potvrzení. Pokud zadáte `-Confirm:$False`, rutina výzvu k potvrzení nezobrazí. 

Další informace o `-Confirm` a `$ConfirmPreference` najdete v tématu [Informace o proměnných předvoleb](https://msdn.microsoft.com/powershell/reference/5.1/Microsoft.PowerShell.Core/about/about_Preference_Variables).

## <a name="next-steps"></a>Další postup

Další informace o [zón a záznamů v Azure DNS](dns-zones-records.md).
<br>
Zjistěte, jak [ochrana záznamů a zón](dns-protect-zones-recordsets.md) při použití Azure DNS.
<br>
Zkontrolujte [referenční dokumentace k Azure DNS Powershellu](/powershell/module/azurerm.dns).
