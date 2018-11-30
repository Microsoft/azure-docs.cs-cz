---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: f7fa018225d82e2db4a3c51b333aea6fcb9a1514
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330872"
---
### <a name="record-names"></a>Názvy záznamů

V Azure DNS se záznamy zadávají pomocí relativních názvů. „Plně kvalifikovaný“ *název* domény (FQDN) obsahuje název zóny, zatímco „relativní“ název ho *neobsahuje.* Například relativní název záznamu „www“ v zóně „contoso.com“ dává plně kvalifikovaný název záznamu www.contoso.com.

Záznam *„vrcholu“* je záznam DNS v kořeni (nebo *„vrcholu“*) zóny DNS. Například v zóně DNS nazvané „contoso.com“ má název vrcholu také plně kvalifikovaný název „contoso.com“ (někdy se označuje jako *základní* doména).  Podle konvence, relativní název '\@' slouží k vyjádření záznamů vrcholu.

### <a name="record-types"></a>Typy záznamů

Každý záznam DNS má název a typ. Záznamy jsou uspořádány do různých typů podle dat, která obsahují. Nejběžnějším typem je záznam „A“, který mapuje název na adresu IPv4. Dalším běžným typem záznamu je „MX“, který mapuje název na poštovní server.

Azure DNS podporuje všechny běžné typy záznamů DNS: A, AAAA, CAA, CNAME, MX, NS, PTR, SOA, SRV a TXT. [Záznamy SPF se vyjadřují pomocí záznamů TXT](../articles/dns/dns-zones-records.md#spf-records).

### <a name="record-sets"></a>Sady záznamů

Někdy můžete potřebovat vytvořit víc než jeden záznam DNS s daným názvem a typem. Předpokládejme například, že web „www.contoso.com“ je hostovaný na dvou různých IP adresách. Tento web vyžaduje dva různé záznamy A, jeden pro každou IP adresu. Tady je příklad sady záznamů:

    www.contoso.com.        3600    IN    A    134.170.185.46
    www.contoso.com.        3600    IN    A    134.170.188.221

Azure DNS spravuje všechny záznamy DNS pomocí *sady záznamů*. Sada záznamů (známá také jako sada *záznamů* „prostředků“) je kolekce záznamů DNS v zóně, které mají stejný název a jsou stejného typu. Většina sad záznamů obsahuje jediný záznam. Příklady jako ten výše, kde sada záznamů obsahuje více než jeden záznam, však nejsou výjimkou.

Předpokládejme třeba, že už jste vytvořili záznam A „www“ v zóně „contoso.com“, který odkazuje na IP adresu 134.170.185.46 (první záznam výše).  Když budete chtít vytvořit druhý záznam, bude potřeba tento záznam přidat do existující sady záznamů, ne vytvořit další sadu záznamů.

Výjimkou jsou typy záznamů SOA a CNAME. Standardy DNS nedovolují pro tyto typy více záznamů se stejným názvem, proto tyto sady záznamů můžou obsahovat jenom po jednom záznamu.