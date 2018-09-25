---
title: Správa záznamů DNS v DNS Azure pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Správa DNS sad záznamů a záznamů v Azure DNS, při hostování vaší domény ve službě Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: 5356a3a5-8dec-44ac-9709-0c2b707f6cb5
ms.service: dns
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 05/15/2018
ms.author: victorh
ms.openlocfilehash: 1f1ee4f69cc1ab656df04ed30cae6f4c3e55bfa7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963811"
---
# <a name="manage-dns-records-and-recordsets-in-azure-dns-using-the-azure-cli"></a>Správa DNS záznamů a sad záznamů v DNS Azure pomocí Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](dns-operations-recordsets-portal.md)
> * [Azure CLI](dns-operations-recordsets-cli.md)
> * [PowerShell](dns-operations-recordsets.md)

V tomto článku se dozvíte, jak spravovat záznamy DNS pro zónu DNS pomocí Azure CLI pro různé platformy, které je dostupné pro Windows, Mac a Linux. Můžete také spravovat svoje záznamy DNS pomocí [prostředí Azure PowerShell](dns-operations-recordsets.md) nebo [webu Azure portal](dns-operations-recordsets-portal.md).

V příkladech v tomto článku předpokládá, že jste již [nenainstalovali Azure CLI, přihlásili jste se a vytvořili jste zónu DNS](dns-operations-dnszones-cli.md).

## <a name="introduction"></a>Úvod

Před vytvářením záznamů DNS v DNS Azure je nejprve nutné pochopit, jak DNS Azure organizuje záznamy DNS v sadách záznamů DNS.

[!INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]

Další informace o záznamech DNS v DNS Azure najdete v tématu [Zóny a záznamy DNS](dns-zones-records.md).

## <a name="create-a-dns-record"></a>Vytvoření záznamu DNS

Chcete-li vytvořit záznam DNS, použijte `az network dns record-set <record-type> add-record` příkaz (kde `<record-type>` je typ záznamu, tj srv, txt, atd.) Nápovědu získáte příkazem `az network dns record-set --help`.

Při vytváření záznamu je třeba zadat název skupiny prostředků, název zóny, název sady záznamů a podrobnosti o vytvářeném záznamu. Musí být zadaný název sady záznamů *relativní* název, což znamená, je nutné vyloučit název zóny.

Pokud sada záznamů ještě neexistuje, tento příkaz ji vytvoří. Pokud sada záznamů již existuje, tento příkaz přidá zadaný záznam do existující sady záznamů.

Pokud se vytváří nová sada záznamů, použije se výchozí hodnota TTL (Time to Live) 3 600. Pokyny k používání různých hodnot TTL najdete v tématu [vytvoření sady záznamů DNS](#create-a-dns-record-set).

Následující příklad vytvoří záznam A s názvem *www* v zóně *contoso.com* ve skupině prostředků *MyResourceGroup*. IP adresa záznamu A je *1.2.3.4*.

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

K vytvoření záznamu nastavit na vrcholu zóny (v tomto případě "contoso.com"), použijte název záznamu "\@", včetně uvozovek:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --ipv4-address 1.2.3.4
```

## <a name="create-a-dns-record-set"></a>Vytvoření sady záznamů DNS

Ve výše uvedených příkladech záznam DNS buď přidal do existující sady záznamů nebo vytvoření sady záznamů *implicitně*. Můžete také vytvořit sadu záznamů *explicitně* před přidání záznamů do něj. Azure DNS podporuje "prázdný" sady záznamů, které můžou fungovat jako zástupný symbol rezervovat název DNS před vytvářením záznamů DNS. Prázdné sady záznamů jsou viditelné v rovině řízení Azure DNS, ale nejsou zobrazeny na názvové servery Azure DNS.

Sady záznamů se vytvoří s použitím `az network dns record-set <record-type> create` příkazu. Nápovědu získáte příkazem `az network dns record-set <record-type> create --help`.

Vytváření záznamu explicitně nastavit vám umožní určit vlastnosti sady záznamů, jako [Time To Live (TTL)](dns-zones-records.md#time-to-live) a metadata. [Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) slouží k přidružení každá sada záznamů specifická data jako páry klíč hodnota.

Následující příklad vytvoří skupinu prázdný záznam typu "A" s hodnotou TTL 60 sekund pomocí `--ttl` parametr (krátký tvar `-l`):

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --ttl 60
```

Následující příklad vytvoří se dvě položky metadat sadu záznamů "dept = finance" a "prostředí produkční =", pomocí `--metadata` parametr:

```azurecli
az network dns record-set a create --resource-group myresourcegroup --zone-name contoso.com --name www --metadata "dept=finance" "environment=production"
```

Vytvoření prázdná sada záznamů záznamy můžete přidat pomocí `azure network dns record-set <record-type> add-record` jak je popsáno v [vytvořit záznam DNS](#create-a-dns-record).

## <a name="create-records-of-other-types"></a>Vytvoření záznamů z ostatních typů

S vidět v podrobnosti o tom, jak vytvořit záznamy "A", následující příklady ukazují, jak vytvořit záznam o dalších typech záznamů podporovány službou Azure DNS.

Parametry použité k zadání dat záznamu se liší podle typu záznamu. Například pro záznam typu A zadáváte adresu IPv4 pomocí parametru `--ipv4-address <IPv4 address>`. Parametry pro jednotlivé typy záznamů je možné uvést pomocí `az network dns record-set <record-type> add-record --help`.

V obou případech vám ukážeme, jak vytvořit jeden záznam. Záznam se přidá do existující sady záznamů nebo sady záznamů vytvořena implicitně. Další informace o vytváření sad záznamů a definování záznam parametr explicitně nastavit, najdete v článku [vytvoření sady záznamů DNS](#create-a-dns-record-set).

Neposkytujeme příklad k vytvoření sady záznamů SOA od SOAs se vytvoří a odstraní s každou zónou DNS a nelze vytvořit ani odstranit samostatně. Ale [SOA lze upravit, jak je znázorněno v příkladu dále](#to-modify-an-SOA-record).

### <a name="create-an-aaaa-record"></a>Vytvoření záznamů AAAA

```azurecli
az network dns record-set aaaa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-aaaa --ipv6-address 2607:f8b0:4009:1803::1005
```

### <a name="create-an-caa-record"></a>Vytvoření záznamů CAA

```azurecli
az network dns record-set caa add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-caa --flags 0 --tag "issue" --value "ca1.contoso.com"
```

### <a name="create-a-cname-record"></a>Vytvoření záznamu CNAME

> [!NOTE]
> Standardy DNS nepovolují záznamy CNAME ve vrcholu zóny (`--Name "@"`), ani umožňují sady záznamů, který obsahuje více než jeden záznam.
> 
> Další informace najdete v tématu [záznamy CNAME](dns-zones-records.md#cname-records).

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.contoso.com
```

### <a name="create-an-mx-record"></a>Vytvoření záznamů MX

V tomto příkladu vytvoříme s využitím názvu sady záznamů "\@" záznam MX ve vrcholu zóny (v tomto případě "contoso.com").

```azurecli
az network dns record-set mx add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --exchange mail.contoso.com --preference 5
```

### <a name="create-an-ns-record"></a>Vytvoření záznamů NS

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-ns --nsdname ns1.contoso.com
```

### <a name="create-a-ptr-record"></a>Vytvořit záznam PTR

V tomto případě "my-arpa-zone.com" představuje zónu ARPA představující váš rozsah IP adres. Každá sada záznamů PTR v této zóně odpovídá IP adrese v rámci tohoto rozsahu IP adres.  Název záznamu "10" je poslední oktet IP adresu v rámci tohoto rozsahu IP adres reprezentována tento záznam.

```azurecli
az network dns record-set ptr add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name my-arpa.zone.com --ptrdname myservice.contoso.com
```

### <a name="create-an-srv-record"></a>Vytvoření záznamů SRV

Při vytváření [sady záznamů SRV](dns-zones-records.md#srv-records), zadejte  *\_služby* a  *\_protokol* v názvu sady záznamů. Není nutné zahrnout "\@" v názvu sady záznamů po vytvoření záznamů SRV sady ve vrcholu zóny.

```azurecli
az network dns record-set srv add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name _sip._tls --priority 10 --weight 5 --port 8080 --target sip.contoso.com
```

### <a name="create-a-txt-record"></a>Vytvořit záznam TXT

Následující příklad ukazuje, jak vytvořit záznam TXT. Další informace o maximální délku řetězce v záznamů TXT podporovány, naleznete v tématu [záznamů TXT](dns-zones-records.md#txt-records).

```azurecli
az network dns record-set txt add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-txt --value "This is a TXT record"
```

## <a name="get-a-record-set"></a>Získání sady záznamů

Pokud chcete načíst existující sady záznamů, použijte `az network dns record-set <record-type> show`. Nápovědu získáte příkazem `az network dns record-set <record-type> show --help`.

Při vytváření záznamu nebo sadu záznamů, musí být zadaný název sady záznamů *relativní* název, což znamená, je nutné vyloučit název zóny. Musíte také zadat typ záznamu, zónu, která obsahuje sady záznamů a skupinu prostředků obsahující zóny.

Následující příklad načte záznam *www* a typ ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a show --resource-group myresourcegroup --zone-name contoso.com --name www
```

## <a name="list-record-sets"></a>Seznam sad záznamů

Můžete zobrazit seznam všech záznamů v zóně DNS s využitím `az network dns record-set list` příkazu. Nápovědu získáte příkazem `az network dns record-set list --help`.

V tomto příkladu vrátí sady všech záznamů v zóně *contoso.com*, ve skupině prostředků *MyResourceGroup*, bez ohledu na název a typ záznamu:

```azurecli
az network dns record-set list --resource-group myresourcegroup --zone-name contoso.com
```

V tomto příkladu vrátí všechny sady záznamů, které odpovídají daného typu záznamu (v tomto případě "A" záznamy):

```azurecli
az network dns record-set a list --resource-group myresourcegroup --zone-name contoso.com 
```

## <a name="add-a-record-to-an-existing-record-set"></a>Přidejte záznam do existující sady záznamů

Můžete použít `az network dns record-set <record-type> add-record` i k vytvoření záznamu v nové sadě záznamů nebo přidání záznamu do existujícího sada záznamů.

Další informace najdete v tématu [vytvořit záznam DNS](#create-a-dns-record) a [vytvářet záznamy z ostatních typů](#create-records-of-other-types) výše.

## <a name="remove-a-record-from-an-existing-record-set"></a>Odeberte záznam z existující sady záznamů.

Chcete-li odebrat záznam DNS z existující sady záznamů, použijte `az network dns record-set <record-type> remove-record`. Nápovědu získáte příkazem `az network dns record-set <record-type> remove-record -h`.

Tento příkaz odstraní záznam DNS ze sady záznamů. Pokud poslední záznam v sadě záznamů se odstraní, odstraní se také samotné sady záznamů. Chcete-li ponechat prázdné místo sady záznamů, použijte `--keep-empty-record-set` možnost.

Musíte zadat záznam, který chcete odstranit, a zóna je nutné ji odstranit, pomocí stejné parametry jako při vytváření záznamu pomocí `az network dns record-set <record-type> add-record`. Tyto parametry jsou popsány v [vytvořit záznam DNS](#create-a-dns-record) a [vytvářet záznamy z ostatních typů](#create-records-of-other-types) výše.

Následující příklad odstraní záznam A s hodnotou '1.2.3.4' z tohoto záznamu sadu *www* v zóně *contoso.com*, ve skupině prostředků *MyResourceGroup*.

```azurecli
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "www" --ipv4-address 1.2.3.4
```

## <a name="modify-an-existing-record-set"></a>Upravit existující sady záznamů

Každá sada záznamů obsahuje [time to live (TTL)](dns-zones-records.md#time-to-live), [metadat](dns-zones-records.md#tags-and-metadata)a záznamy DNS. Následující části popisují, jak upravit každé z těchto vlastností.

### <a name="to-modify-an-a-aaaa-caa-mx-ns-ptr-srv-or-txt-record"></a>Chcete-li upravit záznam A, AAAA, CAA, MX, NS, PTR, SRV a TXT

Pokud chcete upravit existující záznam typu A, AAAA, CAA, MX, NS, PTR, SRV a TXT, by měl nejprve přidat nový záznam a pak odstraňte existující záznam. Podrobné pokyny o tom, jak odstranit a přidání záznamů najdete v dřívějších částech tohoto článku.

Následující příklad ukazuje, jak upravit záznam "A" z IP adresa 1.2.3.4 na IP adresu 5.6.7.8:

```azurecli
az network dns record-set a add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 5.6.7.8
az network dns record-set a remove-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name www --ipv4-address 1.2.3.4
```

Nelze přidat, odebrat nebo upravit záznamy v automaticky vytvořené sady ve vrcholu zóny záznamů NS (`--Name "@"`, včetně uvozovek nahoře). Pro tuto sadu záznamů jsou povoleny pouze změny záznam upravit nastavení TTL a metadata.

### <a name="to-modify-a-cname-record"></a>K úpravě záznamu CNAME

Na rozdíl od většina ostatních typů záznamů může obsahovat sadu záznamů CNAME jenom jeden záznam.  Proto je nelze nahradit aktuální hodnotu přidáním nového záznamu a odebrání existujícího záznamu, jako u jiných typů záznamů.

Místo toho pokud chcete upravit záznam CNAME, použijte `az network dns record-set cname set-record`. Nápovědu najdete v tématu `az network dns record-set cname set-record --help`

Tento příklad upraví sadu záznamů CNAME *www* v zóně *contoso.com*, ve skupině prostředků *MyResourceGroup*, aby odkazoval na "www.fabrikam.net" namísto jeho stávající hodnota:

```azurecli
az network dns record-set cname set-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name test-cname --cname www.fabrikam.net
``` 

### <a name="to-modify-an-soa-record"></a>Úprava záznamu SOA

Na rozdíl od většina ostatních typů záznamů může obsahovat sadu záznamů CNAME jenom jeden záznam.  Proto je nelze nahradit aktuální hodnotu přidáním nového záznamu a odebrání existujícího záznamu, jako u jiných typů záznamů.

Místo toho použijte k úpravě záznamu SOA `az network dns record-set soa update`. Nápovědu získáte příkazem `az network dns record-set soa update --help`.

Následující příklad ukazuje, jak nastavit vlastnost "email" záznamu SOA zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set soa update --resource-group myresourcegroup --zone-name contoso.com --email admin.contoso.com
```

### <a name="to-modify-ns-records-at-the-zone-apex"></a>K úpravě záznamů NS ve vrcholu zóny

S každou zónou DNS se automaticky vytvoří sadu ve vrcholu zóny záznamů NS. Obsahuje názvy názvových serverů Azure DNS přiřazených k zóně.

Můžete přidat další názvové servery pro tento záznam NS nastavit podporuje společné hosting domén s více než jednoho poskytovatele DNS. Můžete také upravit hodnotu TTL a metadata pro tuto sadu záznamů. Ale nejde odebrat ani změnit předem naplněných názvové servery Azure DNS.

Všimněte si, že to platí pouze pro záznam NS, nastavte ve vrcholu zóny. Bez omezení, lze upravit jinými sadami záznamů NS v pásmu (jak se používá k delegování podřízené zóny).

Následující příklad ukazuje, jak přidat další názvový server sadu ve vrcholu zóny záznamů NS:

```azurecli
az network dns record-set ns add-record --resource-group myresourcegroup --zone-name contoso.com --record-set-name "@" --nsdname ns1.myotherdnsprovider.com 
```

### <a name="to-modify-the-ttl-of-an-existing-record-set"></a>Chcete-li změnit hodnotu TTL existující sady záznamů

Chcete-li upravit hodnoty TTL existující sady záznamů, použijte `azure network dns record-set <record-type> update`. Nápovědu získáte příkazem `azure network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak upravit sadu záznamů TTL, v tomto případě na 60 sekund:

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set ttl=60
```

### <a name="to-modify-the-metadata-of-an-existing-record-set"></a>K úpravě metadat existující sady záznamů

[Metadata sady záznamů](dns-zones-records.md#tags-and-metadata) slouží k přidružení každá sada záznamů specifická data jako páry klíč hodnota. Chcete-li upravit metadata existující sady záznamů, použijte `az network dns record-set <record-type> update`. Nápovědu získáte příkazem `az network dns record-set <record-type> update --help`.

Následující příklad ukazuje, jak nastavit dvě položky metadat, pokud chcete záznam upravit "dept = finance" a "prostředí produkční =". Všimněte si, že se všechna existující metadata *nahradí* podle zadané hodnoty.

```azurecli
az network dns record-set a update --resource-group myresourcegroup --zone-name contoso.com --name www --set metadata.dept=finance metadata.environment=production
```

## <a name="delete-a-record-set"></a>Odstranit záznamovou sadu

Sady záznamů lze odstranit pomocí `az network dns record-set <record-type> delete` příkazu. Nápovědu získáte příkazem `azure network dns record-set <record-type> delete --help`. Odstranění záznamu sady také odstraní všechny záznamy v rámci sady záznamů.

> [!NOTE]
> Nelze odstranit SOA a sady záznamů NS ve vrcholu zóny (`--name "@"`).  Tyto jsou vytvořeny automaticky při zóny byla vytvořena a jsou automaticky odstraněny při jejím odstranění.

Následující příklad odstraní záznam sadu *www* a typ ze zóny *contoso.com* ve skupině prostředků *MyResourceGroup*:

```azurecli
az network dns record-set a delete --resource-group myresourcegroup --zone-name contoso.com --name www
```

Zobrazí se výzva k potvrzení operace odstranění. Chcete-li tuto výzvu potlačit, použijte `--yes` přepnout.

## <a name="next-steps"></a>Další postup

Další informace o [zón a záznamů v Azure DNS](dns-zones-records.md).
<br>
Zjistěte, jak [ochrana záznamů a zón](dns-protect-zones-recordsets.md) při použití Azure DNS.
