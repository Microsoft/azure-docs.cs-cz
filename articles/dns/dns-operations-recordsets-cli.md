---
title: Správa záznamů DNS ve službě Azure DNS pomocí příkazového příkazového příkazu Azure
description: Správa sad záznamů DNS a záznamů na Azure DNS při hostování vaší domény na Azure DNS.
author: rohinkoul
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: conceptual
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: rohink
ms.openlocfilehash: 4e017dc940e1d32888ff279904e44d34db1fd5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76936890"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Správa záznamů DNS a sad záznamů v Azure DNS pomocí azure cli

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

Tento článek ukazuje, jak spravovat záznamy DNS pro zónu DNS pomocí rozhraní API Azure napříč platformami, které je dostupné pro Windows, Mac a Linux. Záznamy DNS můžete spravovat taky pomocí [Azure PowerShellu](dns-operations-recordsets.md) nebo [portálu Azure](dns-operations-recordsets-portal.md).

Příklady v tomto článku předpokládají, že jste již [nainstalovali azure CLI, přihlášení a vytvořili zónu DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Chcete-li vytvořit záznam `az network dns record-set <record-type> add-record` DNS, `<record-type>` použijte příkaz (kde je typ záznamu, tj. a, srv, txt atd.) Nápovědu naleznete `az network dns record-set --help`v tématu .

Při vytváření záznamu je třeba zadat název skupiny prostředků, název zóny, název sady záznamů a podrobnosti o vytvářeném záznamu. Uvedený název sady záznamů musí být *relativní* název, což znamená, že musí vyloučit název zóny.

Pokud sada záznamů ještě neexistuje, tento příkaz ji vytvoří. Pokud sada záznamů již existuje, tento příkaz přidá zadaný záznam do existující sady záznamů.

Pokud se vytváří nová sada záznamů, použije se výchozí hodnota TTL (Time to Live) 3 600. Pokyny k použití různých ttl naleznete [v tématu Vytvoření sady záznamů DNS](#create-a-dns-record-set).

Následující příklad vytvoří záznam A s názvem *www* v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*. IP adresa záznamu A je *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Chcete-li vytvořit záznam nastavený v vrcholu zóny (v tomto případě "contoso.com"), použijte název záznamu "\@", včetně uvozovek:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Vytvoření sady záznamů DNS

Ve výše uvedených příkladech byl záznam DNS buď přidán do existující sady záznamů, nebo byla sada záznamů vytvořena *implicitně*. Před přidáním záznamů do ní můžete také *explicitně* vytvořit sadu záznamů. Azure DNS podporuje "prázdné" sady záznamů, které mohou sloužit jako zástupný symbol pro rezervaci názvu DNS před vytvořením záznamů DNS. Prázdné sady záznamů jsou viditelné v rovině ovládacího prvku Azure DNS, ale nezobrazují se na názvových serverech Azure DNS.

Sady záznamů jsou `az network dns record-set <record-type> create` vytvářeny pomocí příkazu. Nápovědu získáte příkazem `az network dns record-set <record-type> create --help`.

Vytvoření sady záznamů explicitně umožňuje zadat vlastnosti sady záznamů, jako je [například Čas-To-Live (TTL)](dns-zones-records.md#time-to-live) a metadata. [Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít k přidružení dat specifických pro aplikaci ke každé sadě záznamů jako dvojice klíč-hodnota.

Následující příklad vytvoří prázdnou sadu záznamů typu "A" s 60sekundovým `--ttl` TTL `-l`pomocí parametru (krátký tvar ):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Následující příklad vytvoří sadu záznamů se dvěma položkami metadat, "dept=finance" a `--metadata` "environment=production", pomocí parametru :

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Po vytvoření prázdné sady záznamů lze `azure network dns record-set <record-type> add-record` záznamy přidat pomocí popisu v [porozce Vytvoření záznamu DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Vytvoření záznamů jiných typů

Po podrobném zobrazení, jak vytvořit záznamy "A", následující příklady ukazují, jak vytvořit záznam jiných typů záznamů podporovaných službou Azure DNS.

Parametry použité k zadání dat záznamu se liší podle typu záznamu. Například pro záznam typu A zadáváte adresu IPv4 pomocí parametru `--ipv4-address <IPv4 address>`. Parametry pro každý typ záznamu `az network dns record-set <record-type> add-record --help`lze uvést pomocí .

V každém případě ukážeme, jak vytvořit jeden záznam. Záznam je přidán do existující sady záznamů nebo sada záznamů vytvořena implicitně. Další informace o explicitním vytváření sad záznamů a definování parametrů sady záznamů naleznete [v tématu Vytvoření sady záznamů DNS](#create-a-dns-record-set).

Neuvádíme příklad pro vytvoření sady záznamů SOA, protože SOA jsou vytvořeny a odstraněny s každou zónou DNS a nelze je vytvořit ani odstranit samostatně. Soa však [může být změněn, jak je znázorněno v pozdějším příkladu](#to-modify-an-soa-record).

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
> Standardy DNS nepovolují záznamy CNAME na`--Name "@"`vrcholu zóny ( ), ani nepovolují sady záznamů obsahující více než jeden záznam.
> 
> Další informace naleznete v tématu [CNAME records](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Vytvoření záznamu MX

V tomto příkladu použijeme název\@sady záznamů " " k vytvoření záznamu MX na vrcholu zóny (v tomto případě "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Vytvoření záznamu NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Vytvoření záznamu PTR

V tomto případě "my-arpa-zone.com" představuje zónu ARPA představující rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres.  Název záznamu 10 je posledním oktetem adresy IP v tomto rozsahu IP adres reprezentovaném tímto záznamem.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Vytvoření záznamu SRV

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records)zadejte * \_službu* a * \_protokol* v názvu sady záznamů. Při vytváření záznamu SRV nastaveného na vrcholu zóny není třeba do názvu sady záznamů zahrnout "\@".

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Vytvoření záznamu TXT

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délce řetězce podporované v záznamech TXT naleznete v [tématu TXT records](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Získání sady záznamů

Chcete-li načíst existující `az network dns record-set <record-type> show`sadu záznamů, použijte . Nápovědu získáte příkazem `az network dns record-set <record-type> show --help`.

Stejně jako při vytváření záznamu nebo sady záznamů musí být uvedený název sady záznamů *relativní* masy, což znamená, že musí vyloučit název zóny. Musíte také zadat typ záznamu, zónu obsahující sadu záznamů a skupinu prostředků obsahující zónu.

Následující příklad načte záznam *www* typu A ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Sady záznamů seznamu

Pomocí příkazu `az network dns record-set list` můžete vypsat všechny záznamy v zóně DNS. Nápovědu získáte příkazem `az network dns record-set list --help`.

Tento příklad vrátí všechny sady záznamů v zóně *contoso.com*ve skupině prostředků *MyResourceGroup*bez ohledu na název nebo typ záznamu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

Tento příklad vrátí všechny sady záznamů, které odpovídají danému typu záznamu (v tomto případě záznamy "A"):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidání záznamu do existující sady záznamů

Můžete použít `az network dns record-set <record-type> add-record` jak k vytvoření záznamu v nové sadě záznamů, tak k přidání záznamu do existující sady záznamů.

Další informace naleznete [v tématech Vytvoření záznamu DNS](#create-a-dns-record) a Vytvoření záznamů jiných [typů](#create-records-of-other-types) výše.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odebrání záznamu z existující sady záznamů

Chcete-li odebrat záznam DNS z `az network dns record-set <record-type> remove-record`existující sady záznamů, použijte . Nápovědu získáte příkazem `az network dns record-set <record-type> remove-record -h`.

Tento příkaz odstraní záznam DNS ze sady záznamů. Pokud je odstraněn poslední záznam v sadě záznamů, odstraní se také samotná sada záznamů. Chcete-li místo toho zachovat `--keep-empty-record-set` sadu prázdných záznamů, použijte tuto možnost.

Je třeba zadat záznam, který má být odstraněn, a zónu, ze které má `az network dns record-set <record-type> add-record`být odstraněn, za použití stejných parametrů jako při vytváření záznamu pomocí . Tyto parametry jsou popsány v části [Vytvoření záznamu DNS](#create-a-dns-record) a Vytvoření záznamů jiných [typů](#create-records-of-other-types) výše.

Následující příklad odstraní záznam A s hodnotou 1.2.3.4 ze sady záznamů s názvem *www* v zóně *contoso.com*ve skupině prostředků *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Úprava existující sady záznamů

Každá sada záznamů obsahuje záznamy [time-to-live (TTL),](dns-zones-records.md#time-to-live) [metadata](dns-zones-records.md#tags-and-metadata)a DNS. V následujících částech je vysvětleno, jak upravit jednotlivé z těchto vlastností.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Úprava záznamu A, AAAA, CAA, MX, NS, PTR, SRV nebo TXT

Chcete-li upravit existující záznam typu A, AAAA, CAA, MX, NS, PTR, SRV nebo TXT, měli byste nejprve přidat nový záznam a potom odstranit existující záznam. Podrobné pokyny k odstranění a přidání záznamů naleznete v předchozích částech tohoto článku.

Následující příklad ukazuje, jak upravit záznam "A" z IP adresy 1.2.3.4 na IP adresu 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Záznamy v automaticky vytvořeném záznamu NS nastaveném na vrcholu zóny`--Name "@"`( včetně uvozovek) nelze přidávat, odebírat ani upravovat. Pro tuto sadu záznamů jsou povoleny pouze změny upravit sadu záznamů TTL a metadata.

### <a name="to-modify-a-cname-record"></a>Úprava záznamu CNAME

Na rozdíl od většiny ostatních typů záznamů může sada záznamů CNAME obsahovat pouze jeden záznam.  Aktuální hodnotu proto nelze nahradit přidáním nového záznamu a odebráním existujícího záznamu, stejně jako u jiných typů záznamů.

Místo toho chcete upravit záznam `az network dns record-set cname set-record`CNAME, použijte . Nápovědu naleznete v tématu`az network dns record-set cname set-record --help`

Příklad upraví *sadu* záznamů CNAME v zóně *contoso.com*ve skupině prostředků *MyResourceGroup*tak, aby místo jeho existující hodnoty ukazoval na "www.fabrikam.net":

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Úprava záznamu SOA

Na rozdíl od většiny ostatních typů záznamů může sada záznamů CNAME obsahovat pouze jeden záznam.  Aktuální hodnotu proto nelze nahradit přidáním nového záznamu a odebráním existujícího záznamu, stejně jako u jiných typů záznamů.

Místo toho chcete upravit záznam `az network dns record-set soa update`SOA, použijte . Nápovědu získáte příkazem `az network dns record-set soa update --help`.

Následující příklad ukazuje, jak nastavit vlastnost "e-mail" záznamu SOA pro zónu *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Úprava záznamů NS na vrcholu zóny

Záznam NS nastavený na vrcholu zóny je automaticky vytvořen s každou zónou DNS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Do této sady záznamů NS můžete přidat další názvové servery, které podporují co-hostingové domény s více než jedním poskytovatelem DNS. Můžete také upravit TTL a metadata pro tuto sadu záznamů. Předem vyplněné názvové servery Azure DNS však nelze odebrat ani upravit.

Všimněte si, že to platí pouze pro záznam NS nastavený na vrcholu zóny. Jiné sady záznamů NS ve vaší zóně (jako slouží k delegování podřízených zón) lze změnit bez omezení.

Následující příklad ukazuje, jak přidat další názvový server do záznamu NS nastaveného na vrcholu zóny:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Úprava ttl existující sady záznamů

Chcete-li upravit hodnotu TTL `azure network dns record-set <record-type> update`existující sady záznamů, použijte . Nápovědu získáte příkazem `azure network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak upravit sadu záznamů TTL, v tomto případě na 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>Změna metadat existující sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) lze použít k přidružení dat specifických pro aplikaci ke každé sadě záznamů jako dvojice klíč-hodnota. Chcete-li upravit metadata existující sady `az network dns record-set <record-type> update`záznamů, použijte . Nápovědu získáte příkazem `az network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak upravit sadu záznamů se dvěma položkami metadat, "dept=finance" a "environment=production". Všimněte si, že všechna existující metadata jsou *nahrazena* uvedenými hodnotami.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Odstranění sady záznamů

Sady záznamů lze odstranit `az network dns record-set <record-type> delete` pomocí příkazu. Nápovědu získáte příkazem `azure network dns record-set <record-type> delete --help`. Odstraněním sady záznamů se také odstraní všechny záznamy v rámci sady záznamů.

> [!NOTE]
> Sady záznamů SOA a NS nelze odstranit na`--name "@"`vrcholu zóny ( ).  Ty jsou vytvořeny automaticky při vytvoření zóny a jsou automaticky odstraněny při odstranění zóny.

Následující příklad odstraní sadu záznamů s názvem *www* typu A ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Budete vyzváni k potvrzení operace odstranění. Chcete-li tuto výzvu `--yes` potlačit, použijte přepínač.

## <a name="next-steps"></a>Další kroky

Další informace o [zónách a záznamech v Azure DNS](dns-zones-records.md).
<br>
Zjistěte, jak [chránit zóny a záznamy](dns-protect-zones-recordsets.md) při používání Azure DNS.
