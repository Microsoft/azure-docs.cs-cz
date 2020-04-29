---
title: Webové analýzy
description: V tomto článku najdete pokyny k tomu, jak se naučit a používat webové analýzy k tomu, aby co nejlépe vzrostly vaše podnikání.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416286"
---
<a name="web-analytics"></a>Webové analýzy
=============

V tomto článku najdete pokyny k tomu, jak se naučit a používat webové analýzy k tomu, aby co nejlépe vzrostly vaše podnikání. Tato karta Insights je aktuálně dostupná pro všechny nabídky AppSource.

Teď, když jste vytvořili a publikovali vaši nabídku, je další částí vaší cesty sledování a měření\' úspěšnosti. Díky **webovým analýzám**jsme přidali možnost přesně vidět, jak dobře vaše nabídky na webu Marketplace dělají. Pokud chcete začít svoji cestu, přejděte na stránku přehledy na levé straně portál partnerů cloudu a podívejte se na novou kartu Analytics.

![Stránka WebAnalytics](./media/si-getting-started/WebAnalytics1.png)

Zobrazí se řídicí panel s bohatou sadou pro ID vašeho vydavatele, který je sestavený pomocí Microsoft Power BI, a umožňuje zobrazit každou z\' vašich nabídek data, která se denně aktualizují.

<a name="microsoft-campaigns"></a>**Kampaně Microsoftu**
-----------------------

Abychom mohli rozšířit vaše nabídky a sledovat růst vašich nabídek, povolili jsme možnost používat na portál partnerů cloudu **kampaně Microsoftu** . Kampaně jsou nově podporovanou funkcí pro Marketplace, která vám umožní sledovat různé kanály, které odesílají zákazníky na stránku s podrobnostmi o vaší aplikaci.

### <a name="how-to-make-a-campaign"></a>**Postup vytvoření kampaně**

Nejjednodušší způsob, jak popsat kampaně, je přidání vlastního slova nebo termínu na adresu URL, které se nachází na stránce s podrobnostmi o vaší aplikaci na webu Marketplace. Google, Bing, LinkedIn a spousta dalších webů vám pomůžete vytvořit inzerci a propojit z jejich webu s požadovaným webem.

Obecně platí, že tyto snahy vám pomůžou s tím, že vám budou pomáhat s novým zákazníkům na váš produkt a je důležité změřit úspěšnost jednotlivých kanálů. Tady jsou kampaně.

Existují dva způsoby, jak vytvořit vlastní kampaň.

1. Do adresy URL přidejte parametr dotazu **mktcmpid** , který popisuje, co je kampaň a na jakou stránku nebo událost pocházejí.

Můžete například použít:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Rozšířené): v adrese URL použijte jedno z našich podporovaných identifikátorů obecné kampaně. Chceme se dopracovat s dalšími značkami ref, které potřebujete použít, Proto podporujeme konvenci pro automatické rozpoznávání těchto dalších značek:
    
    1. **UTM\_kampaň**
    2. **UTM\_zdroj**
    3. **ref**
    4. **src**

Můžete například použít:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Můžete si vybrat, že budete mít kombinaci několika těchto ID kampaně k dalšímu určení více zdrojů provozního provozu pro kampaň, jako je například místo, odkud zákazník pochází (e-mail, blog, zdroj sociálních médií atd.).

Příklad:

1. Odkazující na Bulletin:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. Odkazující na LinkedIn:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zajištění toho, aby kampaně prošly všemi vašimi stránkami**

Může se jednat o scénář, ve kterém vaše kampaně mají zprostředkující stránku, na kterou právě nasazujete provoz, aby se zákazníci mohli na webu Marketplace odeslat. Počáteční ID kampaně je důležité předat do konečné adresy URL, kterou odešlete na web Marketplace.

Zde naleznete příklad:

1. Zaměstnanci marketingu koupí reklamy z Google na provoz na cílové stránce\' ```https://contoso.com```společnosti. Tato cílová stránka obsahuje \"odkaz na svůj produkt\" , na ```https://appsource.com```který odkazuje.
2. Uživatel klikne na reklamu a rozmístění na\'svou cílovou stránku společnosti.
    1.  Adresa URL odkazu = google.com
    2.  Adresa URL cílové stránky =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. Uživatel klikne na \"odkaz vyzkoušet můj\" produkt a přejde do AppSource.
    1. Adresa URL odkazu =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Adresa URL cílové stránky (**Ujistěte se, že tato\_adresa URL má\_k této adrese URL přidaný UTM kampaň a zdroj UTM**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Jak vyhodnotit úspěch kampaně
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Návštěvy stránky podle kampaně**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Toto je rozpis každé z každodenních návštěv na stránce kampaně, ze kterých pochází.

### <a name="conversion-rate-by-campaign"></a>**Konverzní poměr podle kampaně**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Podobně jako u zobrazení míry převodu celé nabídky můžete v tomto grafu zobrazit rozpis toho, jak vaše různé kampaně dělají. Tento graf by vám měl přispět k tomu, abyste určili, odkud vaše služby pro převod z vyšších sazeb přicházejí.

### <a name="distribution-by-campaign"></a>**Distribuce podle kampaně**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Podobně jako v tom, jak se podíváme na domény zákazníků, vám tento graf umožní zobrazit distribuci vašich dat na kampaň, kterou uživatelé přijdou na Marketplace v části. \_Nekampaň znamená, že zákazník neměl při přechodu na web Marketplace v adrese URL ID kampaně.

<a name="next-steps"></a>**Další kroky**
--------------

Teď, když máte možnost sledovat úspěch vašich nabídek, chceme vám povzbudit, abyste si mohli vytvořit vlastní kampaně.

Pokud máte dotazy nebo požadavky na funkce, sdílejte je prostřednictvím zpětné vazby, která se nachází v pravém horním rohu.

![Zpětná vazba v portál partnerů cloudu](./media/si-getting-started/WebAnalytics5.png)
