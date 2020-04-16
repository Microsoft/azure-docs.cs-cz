---
title: Webová analýza
description: Tento článek obsahuje pokyny, jak se naučit a používat Web Analytics k co nejlepšímu růstu vaší firmy.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416286"
---
<a name="web-analytics"></a>Webová analýza
=============

Tento článek obsahuje pokyny, jak se naučit a používat Web Analytics k co nejlepšímu růstu vaší firmy. V současné době je tato karta Přehledy dostupná pro všechny nabídky AppSource.

Nyní, když jste svou nabídku vytvořili a zveřejnili, je další\' částí vaší cesty sledovat a měřit její úspěch. S **Web Analytics**jsme přidali možnost přesně vidět, jak dobře si jednotlivé vaše nabídky vedou na trhu. Chcete-li zahájit cestu, přejděte na stránku Přehledy na levé straně portálu partnerů cloudu a podívejte se na novou kartu Analytics.

![Stránka WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Zobrazí se bohatý řídicí panel pro vaše ID aplikace Publisher, který byl vytvořen\' pomocí Microsoft Power BI a umožňuje zobrazit všechna data nabídek, která se aktualizují denně.

<a name="microsoft-campaigns"></a>**Kampaně společnosti Microsoft**
-----------------------

Abychom mohli zvýšit své nabídky a sledovat růst vašich nabídek, umožnili jsme vám používat **kampaně Microsoftu** na portálu partnerů cloudu. Kampaně jsou nově podporovanou funkcí pro tržiště, která vám umožní sledovat různé kanály, které posílají zákazníky na stránku s podrobnostmi o aplikaci.

### <a name="how-to-make-a-campaign"></a>**Jak vytvořit kampaň**

Nejjednodušší způsob, jak popsat kampaně, je, že do adresy URL přidáváte vlastní slovo nebo termín, který se přistane na stránce s podrobnostmi o aplikaci na trhu. Google, Bing, LinkedIn a mnoho dalších stránek doporučujeme vytvořit reklamu, odkaz z jejich stránek na požadované stránky.

Obecně platí, že tyto snahy jsou pomoci řídit nové zákazníky do vašeho produktu a je nezbytné měřit úspěch toho, jak každý z vašich kanálů dělá. Tady přicházejí kampaně.

Existují dva způsoby, jak vytvořit vlastní kampaň.

1. Přidejte do adresy URL parametr dotazu **mktcmpid,** který popisuje, co je kampaň a z jaké stránky/události tito zákazníci pocházejí.

Můžete například použít:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Upřesnit): Použijte jedno z našich podporovaných obecných ID kampaně v adrese URL. Chceme být vstřícní s dalšími značkami ref, které musíte použít, takže podporujeme úmluvu, abychom automaticky rozpoznali tyto další značky:
    
    1. **utm\_kampaň**
    2. **utm\_zdroj**
    3. **ref**
    4. **src**

Můžete například použít:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Můžete si vybrat, zda chcete mít kombinaci více id těchto kampaní, abyste dále identifikovali více zdrojů, které zvyšují návštěvnost kampaně, například odkud zákazník pochází (e-mail, blog, zdroj sociálních médií atd.).

Příklad:

1. Newsletter referrer:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. Odkazovná osoba linkedinu:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zajištění průchodu kampaní všemi stránkami**

Může napovědět situaci, kdy vaše kampaně mají mezistránku, na kterou zvyšujete návštěvnost, a pak pokračuje v odesílání zákazníků na trh. Je důležité předat počáteční ID kampaně do cílové adresy URL, kterou odešlete na tržiště.

Zde naleznete příklad:

1. Zaměstnanec marketingu kupuje reklamy od Googlu, ```https://contoso.com```aby mohl zvýšit návštěvnost vstupní stránky společnosti\'. Tato vstupní stránka \"má\" vyzkoušet můj ```https://appsource.com```odkaz na produkt, který jde do .
2. Uživatel klikne na reklamu a\'přistane na vstupní stránce své společnosti.
    1.  Adresa URL odkazu = google.com
    2.  Adresa URL vstupní stránky =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. Uživatel klikne \"na odkaz\" vyzkoušet můj produkt a přejde do AppSource.
    1. Adresa URL odkazu =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Adresa URL vstupní stránky (**Ujistěte\_se, že\_tato adresa URL obsahuje kampaň UTM a zdroj utm přidaný na tuto adresu URL**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak vyhodnotit úspěšnost kampaně
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Návštěvy stránek podle kampaně**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Toto je rozdělení jednotlivých vašich denních návštěv stránek podle kampaně, ze které pocházejí.

### <a name="conversion-rate-by-campaign"></a>**Míra konverze podle kampaně**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Podobně jako zobrazujeme míru konverze celé nabídky, můžete v tomto grafu zobrazit rozpis toho, jak si vedou různé kampaně. Tento graf by vám měl pomoci určit, odkud pocházejí vaše kampaně s vyšším konverzním poměrem.

### <a name="distribution-by-campaign"></a>**Rozdělení podle kampaně**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Podobně jako se díváme na domény vašich zákazníků, tento graf umožňuje zobrazit distribuci dat na kampaň, pod kterou uživatelé přicházejí na trh. \_NoCampaign znamená, že zákazník neměl ID kampaně v adrese url, když přešel na trh.

<a name="next-steps"></a>**Další kroky**
--------------

Nyní, když máte možnost sledovat úspěch svých nabídek, chceme vás povzbudit k vytvoření vlastních kampaní.

Pokud máte dotazy / požadavky na funkce, sdílejte je prostřednictvím zpětné vazby, která se nachází v pravém horním rohu.

![Zpětná vazba na portálu partnerů cloudu](./media/si-getting-started/WebAnalytics5.png)
