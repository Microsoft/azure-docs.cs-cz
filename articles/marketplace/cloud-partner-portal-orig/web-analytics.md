---
title: Web Analytics | Dokumentace Microsoftu
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 20a4e24800b2fa05990d294cb374841c50c4f79b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809234"
---
<a name="web-analytics"></a>Web Analytics
=============

Tento článek obsahuje, že jste s pokyny o tom, jak učí a používají Web Analytics pro co nejlepší rozvíjet vaše podnikání. Tato karta Insights je aktuálně dostupná pro všechny nabídky AppSource.

Teď, když jste vytvořili a publikování vaší nabídky, je další část vaší cesty ke sledování a měřit její\' úspěch. S **Web Analytics**, přidali jsme možnost zobrazit, přesně jak dobře provádí každé z nabídek na webu marketplace. Začněte svou cestu, přejděte na stránce Přehled na levé straně portál Cloud Partner zobrazit nová karta Analytics.

![WebAnalytics stránky](./media/si-getting-started/WebAnalytics1.png)

Zobrazí se panel s bohatými funkcemi pro vaše ID vydavatele, která byla vytvořena pomocí Microsoft Power BI a umožní vám prohlédnout všechny svoje nabídky\' data, která se aktualizují každý den.

<a name="microsoft-campaigns"></a>**Microsoft kampaně**
-----------------------

Abyste mohli rozvíjet vaše nabídky a sledovat růstu vaší nabídky, zavedli jsme možnost používat **kampaně Microsoftu** na portál partnerů cloudu. Kampaní jsou nově podporované funkce pro web marketplace, které vám umožní si můžete sledovat různé kanály, které odesílají zákazníky na stránce s podrobnostmi o vaší aplikaci.

### <a name="how-to-make-a-campaign"></a>**Jak je nechat kampaně**

Nejjednodušší způsob, jak popisují kampaní je, že přidáte vlastní slovo nebo termín k adrese URL, které jsou na stránce podrobností aplikace na webu Marketplace. Google, Bing, LinkedIn a řadu dalších lokalit doporučujeme vytvářet reklamy, propojení z jejich webu do požadovaného webu.

Obecně platí pomáhá zákazníkům nové jednotky do produktu se tyto aktivity dál rozšiřuji a je nutné měřit její úspěšnost. jak je to všech kanálů. Je to, odkud pochází kampaní.

Existují dva způsoby, jak generovat vlastní kampaně.

1. Přidat parametr dotazu k adrese URL **mktcmpid** , který popisuje, co je kampaně a jaké stránky nebo události tito zákazníci přicházející ze zařízení.

Například můžete použít: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

2. (Rozšířené): použijte jednu z našich podporovaných, obecný kampaně ID v adrese URL. Chceme narážely s další ref značky, které je třeba použít, aby podporujeme konvence automaticky rozpoznat tyto další značky:
    
    1. **UTM\_kampaně**
    2. **UTM\_zdroje**
    3. **REF**
    4. **src**

Například můžete použít: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Můžete mít kombinaci více těchto ID kampaně dále určit více zdrojů řízení provozu u kampaně, jako je například zákazník, odkud (e-mailu, blogu, zdroje sociálních médií, atd.).

Příklad:

1. Bulletin odkazující server:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. Odkazující server LinkedIn:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zajistit, že kampaně předávání všech stránek**

Můžou existovat scénáře, kdy vaše kampaně mají zprostředkující stránku, která řídí provoz, který pak pokračuje k odeslání zákazníků na webu Marketplace. Je důležité předávání kampaň počáteční ID do konečná adresa URL, která posíláte na webu Marketplace.

Zde naleznete příklad:

1. Marketing zaměstnance nakoupí služby Active Directory z Googlu přesměrovat provoz na společnost\'s cílovou stránkou <https://contoso.com>. Cílová stránka má \"zkuste produkt\" odkaz na <https://appsource.com>.
2. Uživatel klikne na ad a jsou ve své společnosti\'s cílovou stránkou.
    1.  Adresa URL referenčního seznamu = google.com
    2.  Adresa URL cílové stránky = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. Uživatel klikne \"zkuste produkt\" propojit a přejde do AppSource.
    1. Adresa URL referenčního seznamu =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Adresa URL cílové stránky (**zajistěte, aby tuto adresu URL utm\_kampaně a utm\_přidán na tuto adresu URL zdroje**) = [ https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername? **utm\_kampaně = MyCampaignAdName & utm\_zdroje = MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak vyhodnotit úspěšnosti kampaně
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Stránka počty návštěv podle kampaně**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Toto je rozpis jednotlivých váš denní návštěv stránky podle kampaně, které pocházejí.

### <a name="conversion-rate-by-campaign"></a>**Konverzní poměr kampaní**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Podobně jako ukážeme, jak konverzní poměr celý nabídky, v tomto grafu můžete zobrazit rozpis jak pracují různé kampaní. Tento graf by měl pomoci identifikovat, odkud jsou vyšší míra kampaní převodu.

### <a name="distribution-by-campaign"></a>**Distribuce podle kampaně**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Podobně jako u podíváme, jak domén vašich zákazníků, tento graf vám umožní vidět rozdělení dat na kampaň, která uživatelé přicházejí na webu Marketplace v části. \_NoCampaign znamená, že zákazník neměl kampaně ID v adrese url při jejich přejde na webu marketplace.

<a name="next-steps"></a>**Další kroky**
--------------

Teď, když budete mít možnost sledovat úspěch vaší nabídky, chceme vám doporučujeme vytvářet vlastní kampaně.

Pokud máte dotazy nebo požadavky, můžete je sdílejte prostřednictvím zpětnou vazbu, nachází v pravém horním rohu.

![Zpětnou vazbu v portál Cloud Partner](./media/si-getting-started/WebAnalytics5.png)
