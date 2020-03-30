---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 261ae22348cd82b129727261c619727917e19c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73832057"
---
### <a name="record-names"></a>Názvy záznamů

V Azure DNS se záznamy zadávají pomocí relativních názvů. *Plně kvalifikovaný* název domény (FQDN) obsahuje název zóny, zatímco *relativní* název nikoli. Například relativní název `www` záznamu v `contoso.com` zóně udává `www.contoso.com`plně kvalifikovaný název záznamu .

Záznam**** „vrcholu“ je záznam DNS v kořeni (nebo „vrcholu“) zóny DNS. Například v zóně `contoso.com`DNS má vrcholný záznam také `contoso.com` plně kvalifikovaný název (někdy se nazývá *nahá* doména).  Podle konvence se relativní\@název ' ' používá k reprezentaci vrcholových záznamů.

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