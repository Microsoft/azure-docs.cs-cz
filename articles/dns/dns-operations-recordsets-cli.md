---
title: Správa záznamů DNS v Azure DNS pomocí rozhraní příkazového řádku Azure
description: Správa sad záznamů DNS a záznamů na Azure DNS při hostování vaší domény v Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: how-to
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 2d3989b3c477a35d602f1ccf3e45d6f597f5d78d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011557"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Správa záznamů a sad záznamů DNS v Azure DNS pomocí rozhraní příkazového řádku Azure

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

V tomto článku se dozvíte, jak spravovat záznamy DNS pro zónu DNS pomocí rozhraní příkazového řádku Azure CLI pro různé platformy, které je dostupné pro Windows, Mac a Linux. Záznamy DNS můžete spravovat také pomocí [Azure PowerShell](dns-operations-recordsets.md) nebo [Azure Portal](dns-operations-recordsets-portal.md).

V příkladech v tomto článku se předpokládá, že jste už [nainstalovali Azure CLI, jste přihlášeni a vytvořili ZÓNU DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Chcete-li vytvořit záznam DNS, použijte `az network dns record-set <record-type> add-record` příkaz (kde `<record-type>` je typ záznamu, tj. a, SRV, txt atd.) Nápovědu najdete v tématu `az network dns record-set --help` .

Při vytváření záznamu je třeba zadat název skupiny prostředků, název zóny, název sady záznamů a podrobnosti o vytvářeném záznamu. Zadaný název sady záznamů musí být *relativní* název, což znamená, že musí název zóny vyloučit.

Pokud sada záznamů ještě neexistuje, tento příkaz ji vytvoří. Pokud sada záznamů již existuje, tento příkaz přidá zadaný záznam do existující sady záznamů.

Pokud se vytváří nová sada záznamů, použije se výchozí hodnota TTL (Time to Live) 3 600. Pokyny k používání různých TTLs najdete v tématu [Vytvoření sady záznamů DNS](#create-a-dns-record-set).

Následující příklad vytvoří záznam A s názvem *www* v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*. IP adresa záznamu A je *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Pokud chcete vytvořit sadu záznamů na vrcholu zóny (v tomto případě "contoso.com"), použijte název záznamu " \@ ", včetně uvozovek:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Vytvoření sady záznamů DNS

Ve výše uvedených příkladech se záznam DNS buď přidal do existující sady záznamů, nebo se sada záznamů vytvořila *implicitně*. Sadu záznamů můžete vytvořit také *explicitně* před přidáním záznamů do ní. Azure DNS podporuje prázdné sady záznamů, které mohou sloužit jako zástupný symbol pro rezervaci názvu DNS před vytvořením záznamů DNS. Prázdné sady záznamů jsou viditelné v Azure DNS rovině ovládacího prvku, ale nezobrazují se na Azure DNS názvových serverech.

Sady záznamů se vytvářejí pomocí `az network dns record-set <record-type> create` příkazu. Nápovědu získáte příkazem `az network dns record-set <record-type> create --help`.

Při vytváření sady záznamů je explicitně možné zadat vlastnosti sady záznamů, jako je [TTL (Time to Live)](dns-zones-records.md#time-to-live) a metadata. [Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít pro přiřazení dat specifických pro aplikaci ke každé sadě záznamů, jako páry klíč-hodnota.

Následující příklad vytvoří prázdnou sadu záznamů typu "A" s hodnotou TTL 60 sekund pomocí `--ttl` parametru (krátký tvar `-l` ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Následující příklad vytvoří sadu záznamů se dvěma položkami metadat, "Odděl = finance" a "prostředí = produkce" pomocí `--metadata` parametru:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Díky vytvoření prázdné sady záznamů je možné záznamy přidat pomocí, `azure network dns record-set <record-type> add-record` jak je popsáno v tématu [Vytvoření záznamu DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Vytváření záznamů dalších typů

V následujících příkladech se dozvíte, jak vytvořit záznamy A, jak vytvořit záznam dalších typů záznamů podporovaných nástrojem Azure DNS.

Parametry použité k zadání dat záznamu se liší podle typu záznamu. Například pro záznam typu A zadáváte adresu IPv4 pomocí parametru `--ipv4-address <IPv4 address>`. Parametry pro každý typ záznamu lze uvést pomocí `az network dns record-set <record-type> add-record --help` .

V každém případě ukážeme, jak vytvořit jeden záznam. Záznam se přidá do existující sady záznamů nebo se sada záznamů vytvořila implicitně. Další informace o tom, jak explicitně vytvořit sady záznamů a definovat parametr sady záznamů, najdete v tématu [Vytvoření sady záznamů DNS](#create-a-dns-record-set).

Neposkytujeme příklad pro vytvoření sady záznamů SOA, protože SOAs se vytváří a odstraňují s každou zónou DNS a nedá se vytvořit ani odstranit samostatně. [Záznam SOA je však možné upravit, jak je znázorněno v pozdějším příkladu](#to-modify-an-soa-record).

### <a name="create-an-aaaa-record"></a>Vytvoření záznamu AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Vytvoření záznamu CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Vytvoření záznamu CNAME

> [!NOTE]
> Standardy DNS nepovolují záznamy CNAME na vrcholu zóny ( `--Name "@"` ), ani nepovolují sady záznamů obsahující více než jeden záznam.
> 
> Další informace najdete v tématu [záznamy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Vytvoření záznamu MX

V tomto příkladu použijeme název sady záznamů " \@ " k vytvoření záznamu MX ve vrcholu zóny (v tomto případě "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Vytvoření záznamu NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Vytvoření záznamu PTR

V tomto případě "my-arpa-zone.com" představuje zónu ARPA představující rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres.  Název záznamu ' 10 ' je poslední oktet IP adresy v rámci tohoto rozsahu IP adres reprezentovaného tímto záznamem.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Vytvoření záznamu SRV

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records)zadejte *\_ službu* a *\_ protokol* v názvu sady záznamů. \@Při vytváření sady záznamů SRV na vrcholu zóny není nutné zahrnout do názvu sady záznamů "".

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Vytvoření záznamu TXT

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délce řetězce podporované v záznamech TXT najdete v tématu [záznamy TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Získání sady záznamů

K načtení existující sady záznamů použijte `az network dns record-set <record-type> show` . Nápovědu získáte příkazem `az network dns record-set <record-type> show --help`.

Stejně jako při vytváření záznamu nebo sady záznamů musí být zadaný název sady záznamů *relativní* název, což znamená, že musí název zóny vyloučit. Je také potřeba zadat typ záznamu, zónu obsahující sadu záznamů a skupinu prostředků, která tuto zónu obsahuje.

Následující příklad načte záznam *webové* služby typu A ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Seznam sad záznamů

Pomocí příkazu můžete zobrazit seznam všech záznamů v zóně DNS `az network dns record-set list` . Nápovědu získáte příkazem `az network dns record-set list --help`.

Tento příklad vrátí všechny sady záznamů v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*, bez ohledu na název nebo typ záznamu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Tento příklad vrátí všechny sady záznamů, které odpovídají danému typu záznamu (v tomto případě záznamy ' A '):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidání záznamu do existující sady záznamů

K `az network dns record-set <record-type> add-record` Vytvoření záznamu v nové sadě záznamů nebo k přidání záznamu do existující sady záznamů můžete použít obojí.

Další informace najdete v tématech [Vytvoření záznamu DNS](#create-a-dns-record) a [Vytvoření záznamů dalších typů](#create-records-of-other-types) .

## <a name="remove-a-record-from-an-existing-record-set"></a>Odebere záznam z existující sady záznamů.

Chcete-li odebrat záznam DNS z existující sady záznamů, použijte `az network dns record-set <record-type> remove-record` . Nápovědu získáte příkazem `az network dns record-set <record-type> remove-record -h`.

Tento příkaz odstraní ze sady záznamů záznam DNS. Pokud se odstraní poslední záznam v sadě záznamů, odstraní se i samotná sada záznamů. Chcete-li místo toho ponechat prázdnou sadu záznamů, použijte `--keep-empty-record-set` možnost.

Je nutné zadat záznam, který chcete odstranit, a zónu, ze které má být odstraněn, pomocí stejných parametrů, jako při vytváření záznamu pomocí `az network dns record-set <record-type> add-record` . Tyto parametry jsou popsané v tématu [Vytvoření záznamu DNS](#create-a-dns-record) a [Vytvoření záznamů dalších typů](#create-records-of-other-types) .

Následující příklad odstraní záznam A s hodnotou 1.2.3.4 ze sady záznamů s názvem *www* v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Úprava existující sady záznamů

Každá sada záznamů obsahuje záznamy [TTL (Time-to-Live)](dns-zones-records.md#time-to-live), [metadata](dns-zones-records.md#tags-and-metadata)a DNS. V následujících částech se dozvíte, jak změnit každou z těchto vlastností.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Úprava záznamu A, AAAA, CAA, MX, NS, PTR, SRV nebo TXT

Pokud chcete upravit existující záznam typu A, AAAA, CAA, MX, NS, PTR, SRV nebo TXT, měli byste nejdřív přidat nový záznam a pak odstranit existující záznam. Podrobné pokyny k odstranění a přidání záznamů najdete v předchozích částech tohoto článku.

Následující příklad ukazuje, jak upravit záznam "A" z IP adresy 1.2.3.4 na IP adresu 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nemůžete přidávat, odebírat ani upravovat záznamy v sadě záznamů automaticky vytvořené NS na vrcholu zóny ( `--Name "@"` včetně uvozovek). V případě této sady záznamů jsou povoleny pouze změny, kterými je upravena hodnota TTL a metadata sady záznamů.

### <a name="to-modify-a-cname-record"></a>Postup úpravy záznamu CNAME

Na rozdíl od většiny ostatních typů záznamů může sada záznamů CNAME obsahovat jenom jeden záznam.  Proto nemůžete nahradit aktuální hodnotu přidáním nového záznamu a odebráním existujícího záznamu, stejně jako u jiných typů záznamů.

Místo toho pro úpravu záznamu CNAME použijte `az network dns record-set cname set-record` . Nápovědu najdete v tématu. `az network dns record-set cname set-record --help`

V příkladu se změní *Webová* sada záznamů CNAME v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*, aby odkazovala na ' www.fabrikam.NET ' namísto existující hodnoty:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Postup úpravy záznamu SOA

Na rozdíl od většiny ostatních typů záznamů může sada záznamů CNAME obsahovat jenom jeden záznam.  Proto nemůžete nahradit aktuální hodnotu přidáním nového záznamu a odebráním existujícího záznamu, stejně jako u jiných typů záznamů.

Místo toho pro úpravu záznamu SOA použijte `az network dns record-set soa update` . Nápovědu získáte příkazem `az network dns record-set soa update --help`.

Následující příklad ukazuje, jak nastavit vlastnost email záznamu SOA pro zónu *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Úprava záznamů NS ve vrcholu zóny

Záznam NS nastavený na vrcholu zóny se automaticky vytvoří s každou zónou DNS. Obsahuje názvy Azure DNS názvových serverů přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, aby se podporovaly domény spoluhostování s více než jedním poskytovatelem DNS. Můžete také upravit hodnotu TTL a metadata této sady záznamů. Nemůžete ale odebrat ani změnit předem vyplněné Azure DNS názvové servery.

Všimněte si, že to platí jenom pro záznam NS, který je nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako používané pro delegování podřízených zón) se dají upravovat bez omezení.

Následující příklad ukazuje, jak přidat další názvový server do sady záznamů NS ve vrcholu zóny:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Úprava hodnoty TTL existující sady záznamů

Chcete-li upravit hodnotu TTL existující sady záznamů, použijte `azure network dns record-set <record-type> update` . Nápovědu získáte příkazem `azure network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak upravit hodnotu TTL sady záznamů, v tomto případě na 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Úprava metadat existující sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít pro přiřazení dat specifických pro aplikaci ke každé sadě záznamů, jako páry klíč-hodnota. Chcete-li upravit metadata existující sady záznamů, použijte `az network dns record-set <record-type> update` . Nápovědu získáte příkazem `az network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak upravit sadu záznamů se dvěma položkami metadat, "oddělení = finance" a "prostředí = produkce". Všimněte si, že všechna existující metadata jsou *nahrazena* danými hodnotami.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Odstranění sady záznamů

Sady záznamů lze odstranit pomocí `az network dns record-set <record-type> delete` příkazu. Nápovědu získáte příkazem `azure network dns record-set <record-type> delete --help`. Odstraněním sady záznamů dojde také k odstranění všech záznamů v rámci sady záznamů.

> [!NOTE]
> Sady záznamů SOA a NS nelze odstranit v zóně vrcholu ( `--name "@"` ).  Ty se vytvoří automaticky při vytvoření zóny a automaticky se odstraní, když se zóna odstraní.

Následující příklad odstraní sadu záznamů s názvem *www* typu A ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Zobrazí se výzva k potvrzení operace odstranění. Chcete-li potlačit tuto výzvu, použijte `--yes` přepínač.

## <a name="next-steps"></a>Další kroky

Další informace o [zónách a záznamech v Azure DNS](dns-zones-records.md).
<br>
Naučte se [chránit zóny a záznamy](dns-protect-zones-recordsets.md) při použití Azure DNS.
