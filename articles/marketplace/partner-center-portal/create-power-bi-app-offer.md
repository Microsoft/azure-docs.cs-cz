---
title: Vytvoření nabídky aplikace Power BI – Azure Marketplace
description: Přečtěte si, jak vytvořit a publikovat nabídku aplikace Power BI pro Microsoft AppSource.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: dc036ae3cba6aa4d3a689562afffb991fadc8c0a
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867600"
---
# <a name="create-a-power-bi-app"></a>Vytvoření aplikace Power BI

> [!IMPORTANT]
> Správu nabídek aplikací Power BI přesouváme z portálu partnerů cloudu do Centra partnerů. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v [části Vytvoření nabídky aplikace Power BI](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/power-bi/cpp-create-offer) pro portál cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak vytvořit a publikovat nabídku aplikace Power BI pro Microsoft [AppSource](https://appsource.microsoft.com/).

Než budete moct vytvořit nabídku aplikace Power BI, musíte mít komerční účet marketplace v Partnerském centru. Pokud jste si ho ještě nevytvořili, přečtěte si informace o vytvoření účtu Commercial Marketplace v účtu [Partnerského centra.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se do [Centra partnerů](https://partner.microsoft.com/dashboard/home).
2. V levé navigační nabídce vyberte **Přehled komerčního tržiště** > **.**

    :::image type="content" source="media/power-bi-menu-overview.png" alt-text="přehled nabídky komerčního tržiště" border="false":::

3. Vyberte **+ Nová nabídka** > **aplikace Power BI**. Zobrazí se dialogové okno **Nová nabídka.**

> [!IMPORTANT]
> Pokud&#39;možnost **Aplikace Power BI** není zobrazená nebo není&#39;povolená, váš účet nemá&#39;oprávnění k vytvoření tohoto typu nabídky. Zkontrolujte, zda jste&#39;splnili všechny [požadavky](create-power-bi-app-overview.md) pro tento typ nabídky, včetně registrace vývojářského účtu.

### <a name="offer-id-and-alias"></a>ID nabídky a alias

Zadejte **ID nabídky**. Jedná se o jedinečný identifikátor pro každou nabídku ve vašem účtu.

- Toto ID je viditelné pro zákazníky ve webové adrese pro nabídku marketplace a šablony Azure Resource Manager, pokud je to možné.
- Použijte při tom jenom malá písmena a číslice. Může obsahovat pomlčky a podtržítka, ale žádné mezery a je omezena na 50 znaků. Pokud například zadáte **test-offer-1** , bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.

- ID nabídky nelze změnit po výběru **možnosti Vytvořit**.

Zadejte **alias Nabídka**. Toto je název používaný k odkazování na nabídku v Partnerském centru.

- Tento název se na trhu nepoužívá a liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.
- To nelze změnit po výběru **možnosti Vytvořit**.

Po zadání těchto dvou hodnot vyberte **Vytvořit** a pokračujte na stránku Přehled nabídky.

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazí vizuální znázornění kroků potřebných k publikování této nabídky (dokončené i nadcházející) a jak dlouho by mělo trvat dokončení každého kroku.

Obsahuje odkazy k provádění operací na tuto nabídku na základě výběru, který provedete. Příklad:

- Pokud je nabídka konceptem - [Odstranit koncept nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Pokud je nabídka živá - [Přestaňte nabídku prodávat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Pokud je nabídka ve verzi preview - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Pokud jste nedokončili odhlášení majitele stránek - [Zrušení publikování](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Nastavení nabídky

Podle těchto kroků nastavte nabídku.

### <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

Při publikování nabídky na trhu pomocí Partnerského centra ji musíte připojit k systému správy vztahů se zákazníky (CRM). To vám umožní získat kontaktní informace zákazníků, jakmile někdo projeví zájem o váš produkt nebo jej používá.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující crm systémy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zapojení zákazníků
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš CRM systém není uveden výše, použijte [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) k ukládání dat potenciálních zákazníků. Potom exportujte data do systému CRM.

2. Při publikování v Partnerském centru propojte svou nabídku s cílem zájemce.
3. Zkontrolujte, zda je správně nakonfigurováno připojení k cíli zájemce. Po publikování v Centru partnerů ověříme připojení a pošleme vám testovacího zájemce. Zatímco zobrazíte náhled nabídky před její mizením, můžete také otestovat připojení zájemců tím, že se pokusíte nabídku zakoupit sami v prostředí preview.
4. Ujistěte se, že připojení k cíli zájemce zůstane aktualizované, abyste nepřišli o žádné zájemce.

Tady jsou některé další zdroje pro správu zájemců:

- [Přehled řízení potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled správy zájemců](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ujistěte se, že je blokování automaticky otevíraných míst vypnuté)

Před pokračováním k dalšímu oddílu Vlastnosti vyberte **Uložit pracovní verzi.**

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používaná k seskupení nabídky na trhu, verzi aplikace a právní smlouvy, které vaši nabídku podporují.

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně tři kategorie. Tyto kategorie se používají k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu a zobrazují se na stránce s podrobnostmi o nabídce. V popisu nabídky vysvětlete, jak vaše nabídka podporuje tyto kategorie.

### <a name="industry"></a>Odvětví

Volitelně vyberte v každém odvětví až dvě odvětví a dvě vertikály. Zatímco kategorie se používají pro zobrazení vaší nabídky, odvětví a vertikály se používají ve vyhledávacích filtrech a používají se ve výkladní skříni. Pokud vaše nabídka cílí na určité odvětví a/nebo vertikální, použijte popis nabídky, abyste vysvětlili, jak vaše nabídka podporuje vybraná odvětví nebo vertikály. Pokud vaše nabídka není&#39;konkrétní obor, ponechejte tuto část prázdnou.

> [!NOTE]
> Vzhledem k tomu, že pracujeme na zavádění nových průmyslových odvětví a vertikál, abychom zlepšili zkušenosti s objevováním nabídek, některá odvětví nebo vertikály ještě nemusí být na výloze viditelné. Průmyslová odvětví a vertikály označené (*) budou k dispozici k budoucímu datu. Všechny publikované nabídky jsou zjistitelné prostřednictvím vyhledávání klíčových slov.
<p>&nbsp;

| **Odvětví** | **Pododvětví** |
| --- | --- |
| *Automobilový průmysl | *Automobilový průmysl |
| Zemědělství | *Ostatní - Nesegmentované |
| Distribuce | *Velkoobchod<br>Balíková a balíková doprava |
| Education | *Vysokoškolské<br> vzdělávání* Základní a střední vzdělávání / K-12<br>*Knihovny a muzea |
| Finanční služby | *Bankovnictví a<br> kapitálové trhy* Pojištění |
| Státní správa | *Obrana a zpravodajství (dříve se nazývá<br> národní a veřejná bezpečnost)* Veřejná bezpečnost a spravedlnost<br>*Civilní vláda |
| Zdravotní péče (dříve se nazývá zdraví) | *Zdravotní plátce<br> * Poskytovatel zdravotní péče<br>*Léčiva |
| Výroba a zdroje (dříve se nazývá Výroba) | *Chemické a agrochemické<br> * Diskrétní výroba<br>*Energie |
| Maloobchod a spotřební zboží (dříve se nazývá maloobchod) | *Spotřební<br> zboží* Maloobchodníci |
| *Média a komunikace (dříve se nazývala Média a zábava) | *Média a<br> zábava* Telekomunikační |
| Professional Services | *Právní<br> * Profesionální služby pro partnery |
| *Architektura a stavebnictví (dříve se nazývá Architecture Engineering) | *Ostatní - Nesegmentované |
| * Pohostinství a cestování | *Hotely<br> a volný čas* Cestování a doprava<br>*Restaurace a stravovací služby |
| *Ostatní odvětví veřejného sektoru | *Lesnictví a<br> rybolov* Neziskovky |
| *Nemovitosti | *Ostatní - Nesegmentované |

### <a name="legal"></a>Právní informace

#### <a name="terms-and-conditions"></a>podmínky a ujednání

Chcete-li poskytnout vlastní smluvní podmínky, zadejte do pole **Smluvní podmínky** až 10 000 znaků. Pokud vaše smluvní podmínky vyžadují delší popis, zadejte jeden webový odkaz na místo, kde je lze nalézt. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, než budou moci vaši nabídku vyzkoušet.

Před pokračováním do další části Nabídka vyberte **Uložit pracovní verzi.**

## <a name="offer-listing"></a>Nabídka výpis

Zde definujete podrobnosti nabídky, které se zobrazují na trhu. To zahrnuje název nabídky, popis, obrázky a tak dále.

### <a name="language"></a>Jazyk

Vyberte jazyk, ve kterém bude vaše nabídka uvedena. V současné době je angličtina **(Spojené státy)** jedinou dostupnou možností.

Definujte podrobnosti tržiště (například název nabídky, popis a obrázky) pro každý jazyk nebo trh. Chcete-li tyto informace zadat, vyberte název jazyka/trhu.

> [!NOTE]
> Podrobnosti nabídky nemusí být v angličtině, pokud popis nabídky &quot;začíná frází, Tato aplikace je k dispozici pouze v [neanglickém jazyce]. &quot; Je&#39;také v pořádku poskytnout užitečný odkaz na nabídku obsahu v jazyce, který&#39;se liší od toho, který je použit v nabídce výpisu.

### <a name="name"></a>Název

Název, který zde zadáte, se zobrazí jako název nabídky. Toto pole je předvyplněno textem, který jste zadali do pole **Alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Může být chráněnochranou od ochranné známky (a můžete zahrnout ochranné známky nebo symboly autorských práv).
- Nesmí mít dlouhou více než 50 znaků.
- Nelze zahrnout emodži.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Uveďte krátký popis nabídky. To může být až 100 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky až o 3 000 znacích. Zobrazí se zákazníkům v přehledu výpisu na marketplace.

Do popisu uveďte jednu nebo více z následujících věcí:

- Hodnota a klíčové výhody, které vaše nabídka poskytuje.
- Kategorie nebo průmyslová sdružení nebo obojí.
- Příležitosti k nákupu v aplikaci.
- Jakékoliv požadované informace.

Zde je několik tipů pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách vašeho popisu. Zahrňte následující položky:
  - Popis nabídky.
  - Typ uživatele, který těží z nabídky.
  - Zákazník potřebuje nebo vydá adresy nabídek.
- Nezapomeňte, že prvních několik vět může být zobrazeno ve výsledcích hledání.
- Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou vaše nabídka poskytuje.
- Snažte se používat oborově specifickou slovní zásobu nebo formulace založené na výhodách.

Chcete-li, aby vaše nabídka **Popis** byl poutavější, naformátujte popis pomocí editoru rtf. Editor s formátovaným textem umožňuje přidávat čísla, odrážky, tučné písmo, kurzívu a odsazení, aby byl popis čitelnější.

:::image type="content" source="media/power-bi-rich-text-editor.png" alt-text="ilustruje editor rtf" border="false":::

### <a name="search-keywords"></a>Hledat klíčová slova

Zadejte až tři volitelná klíčová slova pro vyhledávání, která zákazníkům pomohou najít vaši nabídku na trhu. Nejlepších výsledků dosáhnete v popisu také pomocí těchto klíčových slov.

### <a name="helpprivacy-web-addresses"></a>Nápověda/webové adresy pro ochranu osobních údajů

Poskytněte odkazy, které zákazníkům pomohou lépe porozumět vaší nabídce.

#### <a name="help-link"></a>Odkaz na nápovědu

Zadejte webovou adresu, na které mohou zákazníci získat další informace o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte webovou adresu do zásad ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše nabídka je v souladu se zákony a předpisy na ochranu osobních údajů. Jste také zodpovědní za zveřejnění platných zásad ochrany osobních údajů na svých webových stránkách.

### <a name="contact-information"></a>Kontaktní informace

Je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu**. Tyto informace nejsou&#39;zákazníkům zobrazeny. Je k dispozici společnosti Microsoft a může být poskytnuta partnerům poskytovatele cloudových řešení (CSP).

- Kontakt podpory (povinné): Pro obecné dotazy podpory.
- Technický kontakt (povinné): Pro technické dotazy a certifikační problémy.
- Kontakt programu CSP (volitelné): Pro dotazy prodejce týkající se programu CSP.

V části **Kontakt podpory** uveďte webovou adresu **webu podpory,** kde mohou partneři najít podporu pro vaši nabídku.

### <a name="supporting-documents"></a>Podklady

Poskytněte alespoň jeden a až tři související marketingové dokumenty ve formátu PDF. Například bílé knihy, brožury, kontrolní seznamy nebo prezentace.

### <a name="marketplace-images"></a>Obrázky na marketplace

Poskytněte loga a obrázky, které chcete použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazané obrázky budou odmítnuty.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že místní síť neblokuje službu https://upload.xboxlive.com používanou partnerským centrem.

#### <a name="store-logos"></a>Loga obchodu

Poskytněte soubory PNG s logem nabídky&#39;ve dvou velikostech: **Malé** (48 x 48 pixelů) a **Velké** (216 x 216 pixelů).

Obě loga jsou povinná a používají se na různých místech v seznamu tržiště.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte alespoň jeden a až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každý musí mít velikost 1280 x 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelně)

Přidejte až pět videí, která ukazují vaši nabídku. Zadejte video&#39;s názvem, jeho webovou adresou a miniaturou .png obrázku videa o rozměrech 1280 x 720 pixelů.

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

Další informace o vytváření nabídek najdete v [tématu Nabídka doporučených postupů pro nabízení nabídek](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

## <a name="technical-configuration"></a>Technická konfigurace

Propagujte svou aplikaci ve službě Power BI na produkční prostředí a poskytněte odkaz instalačního programu aplikace Power BI, který zákazníkům umožní nainstalovat vaši aplikaci. Další informace najdete v [tématu Publikování aplikací pomocí řídicích panelů a sestav v Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Doplňkový obsah

Poskytněte další informace o vaší nabídce, které nám pomohou ji ověřit. Tyto informace nejsou&#39;zákazníkům zobrazeny ani publikovány na trhu.

### <a name="validation-assets"></a>Validační prostředky

Volitelně můžete přidat pokyny (až 3 000 znaků), které pomohou týmu pro ověření pravosti společnosti Microsoft nakonfigurovat, připojit a otestovat vaši aplikaci. Zahrňte typická nastavení konfigurace, účty, parametry nebo jiné informace, které lze použít k testování možnosti Připojit data. Tyto informace jsou viditelné pouze pro ověřovací tým a používají se pouze pro účely ověření.

## <a name="review-and-publish"></a>Kontrola a publikování

Po&#39;dokončení všech požadovaných částí nabídky můžete odeslat nabídku ke kontrole a publikování.

V pravém horním rohu portálu vyberte **Zkontrolovat a publikovat**.

Na stránce recenze můžete:

- Podívejte se na stav dokončení pro každou část nabídky. Můžete&#39;publikovat, dokud nebudou všechny části nabídky označeny jako úplné.
  - **Nezahájeno** – oddíl nebyl spuštěn a je třeba jej dokončit.
  - **Neúplné** – v oddíle jsou chyby, které je třeba opravit nebo které vyžadují poskytnutí dalších informací. Pokyny naleznete v částech, které jsou uvedeny výše v tomto dokumentu.
  - **Dokončeno** - V sekci jsou všechna požadovaná data a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky dokončeny.
- Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že je vaše aplikace správně testována. Uveďte také všechny doplňkové poznámky, které jsou užitečné pro pochopení vaší nabídky.

Chcete-li odeslat nabídku k publikování, vyberte **publikovat**.

Pošleme vám e-mail s upozorněním, kdy bude k dispozici předběžná verze nabídky ke kontrole a schválení. Pokud chcete nabídku zveřejnit (nebo soukromou nabídku soukromému publiku), přejděte do Partnerského centra a vyberte **Možnost Go-live**.
