---
title: Vytvoření nabídky konzultačních služeb v Partnerském centru – Azure Marketplace
description: Zjistěte, jak publikovat nabídku poradenské služby pro Azure Marketplace nebo AppSource pomocí Centra partnerů.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869796"
---
# <a name="create-a-consulting-service-offer"></a>Vytvoření nabídky konzultační ch služeb

> [!IMPORTANT]
> Přesouváme správu vašich nabídek konzultačních služeb z portálu partnerů cloudu do Partnerského centra. Dokud se vaše nabídky nebudou migrovat, postupujte podle pokynů v [nabídce konzultačních služeb Azure a Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) pro portál cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak publikovat nabídku služby Poradenství pro [Azure Marketplace](https://azuremarketplace.microsoft.com/) nebo [AppSource](https://appsource.microsoft.com/). Seznam Konzultační služby nabízí na základě Microsoft [Dynamics 365](https://dynamics.microsoft.com/) a Power Platform na AppSource. Nabídky služeb Seznam Consulting na základě Microsoft Azure na Azure Marketplace.

Chcete-li vytvořit nabídku služeb Poradenství v azure marketplace nebo appsource consulting služby, musíte [mít nejprve účet vydavatele v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account)a váš účet musí být zaregistrovaný v programu commercial marketplace. Před vytvořením nabídky si přečtěte požadavky v [oblasti požadavků na služby Consulting](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites).

## <a name="publishing-benefits"></a>Publikování výhod

Výhody publikování na komerčním trhu:

- Propagujte svou společnost pomocí značky Microsoft.
- Potenciálně oslovte více než 100 milionů uživatelů Office 365 a Dynamics 365 na AppSource a více než 200 000 organizací prostřednictvím Azure Marketplace.
- Získejte vysoce kvalitní potenciální zákazníky z těchto tržišť.
- Vaše služby budou propagované týmy microsoftu a telesales

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Po splnění výše popsaných požadavků vytvořte nabídku služeb Consulting následujícím postupem.

1. Přihlaste se do [Centra partnerů](https://partner.microsoft.com)a v horní nabídce vyberte **řídicí panel.**
2. Na levém panelu vyberte **Commercial Marketplace**a pak vyberte **Přehled**.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="Ilustruje menu pro komerční tržiště":::

3. Vyberte **+ Nová nabídka**, pak vyberte Konzultační **služby**.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Znázorňuje tlačítko pro vytvoření nové nabídky.":::

4. Zadejte **ID nabídky**. Jedná se o jedinečný identifikátor pro každou nabídku ve vašem účtu.

    - Toto ID je viditelné pro zákazníky na webové adrese pro nabídku tržiště.
    - Používejte pouze malá písmena, čísla, pomlčky a podtržítka, ale žádné mezery. Délka je omezena na 50 znaků. Pokud například zadáte **test-offer-1**, bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adresa URL nabídky .
    - ID nabídky nelze&#39;změnit po výběru **možnosti Vytvořit**.

5. Zadejte **alias Nabídka**. Toto je název používaný k odkazování na nabídku v Partnerském centru.

    - Tento název není&#39;použit na trhu. To&#39;se liší od názvu nabídky a dalších hodnot, které jsou zobrazeny zákazníkům. Toto pole můžete použít k přiřazení názvu k nabídce, která je pro vás užitečnější pro interní identifikaci nabídky. zákazníkům se nezobrazuje.
    - Alias nabídky nelze&#39;změnit po výběru **možnosti Vytvořit**.

Po zadání těchto dvou hodnot vyberte **Vytvořit** a pokračujte na stránku **Nastavení nabídky.**

## <a name="offer-setup"></a>Nastavení nabídky

Po zadání aliasu ID nabídky a nabídky vytvoří Centrum partnerů pracovní nabídku a zobrazí stránku **Nastavení nabídky.** Podle těchto kroků nastavte nabídku.

### <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

Při publikování nabídky na trhu pomocí Partnerského centra ji _musíte_ připojit k systému správy vztahů se zákazníky (CRM) nebo k systému automatizace marketingu. To vám umožní získat kontaktní informace zákazníků, jakmile někdo projeví zájem o váš produkt nebo jej používá.

1. Vyberte **Připojit,** chcete-li určit, kam chcete odesílat zájemce zákazníků. Partnerské centrum podporuje následující systémy:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zapojení zákazníků
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš CRM systém není&#39;výše uveden, použijte [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) k ukládání dat zájemců zákazníků a pak data exportujte do systému CRM.

2. Při publikování v Partnerském centru propojte svou nabídku s cílem zájemce.
3. Zkontrolujte, zda je správně nakonfigurováno připojení k cíli zájemce. Po publikování v Centru partnerů&#39;ověříme připojení a zašleme vám testovacího zájemce. Zatímco zobrazíte náhled nabídky před její mizením, můžete také otestovat připojení zájemců tím, že se pokusíte nabídku zakoupit sami v prostředí preview.
4. Ujistěte se, že připojení k cíli zájemce zůstane aktualizováno, abyste&#39;nepřišli o žádné zájemce.

Tady jsou některé další zdroje pro správu zájemců:

- [Přehled řízení potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled správy zájemců](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zkontrolujte, zda je blokování automaticky otevíraných míst vypnuté)

Před pokračováním k dalšímu oddílu Vlastnosti vyberte **Uložit pracovní verzi.**

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje nastavit primární produkt, který vaše služba Consulting nabízí nejlepší kryty, nastavit typ služby Consulting a zvolit příslušné produkty.

1. V rozevíracím seznamu vyberte **primární produkt.**
2. V rozevíracím seznamu vyberte **typ služby Consulting:**

    - **Posouzení** : Vyhodnocení prostředí&#39;zákazníka s cílem určit použitelnost řešení a poskytnout odhad nákladů a načasování.
    - **Instruktáž** : Úvod do řešení nebo konzultační služby k načerpání zájmu zákazníků pomocí architektur, ukázky a příklady zákazníků.
    - **Implementace** : Kompletní instalace, která má za následek plně funkční řešení. Limit na řešení, která mohou být implementována za dva týdny nebo méně.
    - **Doklad koncepce** : Implementace s omezeným rozsahem k určení, zda řešení splňuje požadavky zákazníka.
    - **Workshop** : Interaktivní angažmá prováděné na&#39;pro zákazníka. Může zahrnovat školení, instruktáže, hodnocení nebo ukázky postavené na datech nebo prostředí zákazníků&#39;.

1. Pokud jste vybrali primární produkt **Azure**, vyberte až tři **oblasti řešení**. Zákazníkům na Azure Marketplace je jejich hledání snazší. Pokud jste&#39;nevybrali Azure, tento krok přeskočte.
2. Pokud jste vybrali _jiný_ primární produkt než Azure, vyberte až tři **příslušné produkty**. Ty usnadňují zákazníkům v AppSource najít vaši nabídku. Podrobnosti naleznete v [tématu Microsoft AppSource Consulting Service Listing Guidelines](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Vyberte až šest **odvětví,** na která se vaše nabídka vztahuje. Zákazníkům tak bude snazší najít vaši nabídku.
4. Přidejte až tři **kompetence,** které vaše společnost získala k zobrazení na výpisu nabídky konzultační služby. Je vyžadována alespoň jedna kompetence s výjimkou Azure Expert MSP&#39;s a Azure Networking MSP&#39;s.

Před pokračováním do další části Nabídka vyberte **Uložit pracovní verzi.**

## <a name="offer-listing"></a>Nabídka výpis

Zde&#39;ll definovat podrobnosti nabídky, které jsou zobrazeny na trhu. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky se ujistěte, že dodržujete zásady uvedené na [stránce zásad společnosti Microsoft&#39;.](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)

> [!NOTE]
> Podrobnosti nabídky nejsou&#39;nemusí být v angličtině, pokud &quot;popis nabídky začíná frází, Tato aplikace je k dispozici pouze v [neanglickém jazyce]. &quot; Je&#39;také v pořádku poskytnout užitečný odkaz na nabídku obsahu v jazyce, který&#39;se liší od toho, který je použit v podrobnostech nabídky.

### <a name="name"></a>Název

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předvyplněno textem, který jste zadali do pole **Alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Může být chráněnochranou od ochranné známky (a můžete zahrnout ochranné známky nebo symboly autorských práv).
- Může&#39;t více než 50 znaků.
- Může&#39;obsahovat emodži.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Uveďte krátký popis nabídky. To může být až 100 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky až o 3 000 znacích. Zobrazí se zákazníkům v přehledu výpisu na marketplace.

Do popisu uveďte jednu nebo více z následujících věcí:

- Hodnota a klíčové výhody, které vaše nabídka poskytuje
- Kategorie nebo průmyslová sdružení nebo obojí
- Příležitosti k nákupu v aplikaci
- Veškerá požadovaná zveřejnění

Zde je několik tipů pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách vašeho popisu. Zahrňte následující položky:
  - Popis nabídky.
  - Typ uživatele, který těží z nabídky.
  - Zákazník potřebuje nebo vydá adresy nabídek.
- Nezapomeňte, že prvních několik vět může být zobrazeno ve výsledcích hledání.
- Nespoléhejte&#39;na funkce a funkce při prodeji produktu. Místo toho se zaměřte na hodnotu, kterou vaše nabídka poskytuje.
- Snažte se používat oborově specifickou slovní zásobu nebo formulace založené na výhodách.

Chcete-li, aby byl popis poutavější, naformátujte popis pomocí editoru s formátovaným textem. Editor s formátovaným textem umožňuje přidávat čísla, odrážky, tučné písmo, kurzívu a odsazení, aby byl popis čitelnější.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Ilustruje editor s formátovaným textem pro napsání popisu nabídky." border="false":::

### <a name="keywords"></a>Klíčová slova

Zadejte až tři klíčová slova pro vyhledávání, která jsou relevantní pro váš primární produkt a konzultační službu. Díky tomu bude snazší najít vaši nabídku.

### <a name="duration"></a>Doba trvání

Nastavte očekávanou dobu trvání tohoto závazku se zákazníkem.

### <a name="contact-information"></a>Kontaktní informace

Je nutné zadat jméno, e-mail a telefonní číslo **primárního** a **sekundárního kontaktu**. Tyto informace nejsou&#39;zákazníkům zobrazeny. Je k dispozici společnosti Microsoft a může být poskytnuta partnerům poskytovatele cloudových řešení (CSP).

### <a name="supporting-documents"></a>Podklady

Přidejte až tři (ale alespoň jeden) podpůrné dokumenty PDF pro vaši nabídku.

### <a name="marketplace-images"></a>Obrázky na marketplace

Poskytněte loga a obrázky, které chcete použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazané obrázky budou odmítnuty.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že místní síť neblokuje službu https://upload.xboxlive.com používanou partnerským centrem.

#### <a name="store-logos"></a>Loga obchodu

Poskytněte soubory PNG s logem nabídky&#39;v každé z následujících velikostí pixelů:

- **Malé (48 x 48)**
- **Velké (216 x 216)**

Všechna loga jsou povinná a používají se na různých místech v seznamu tržiště.

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každý musí mít velikost 1280 x 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelně)

Přidejte až čtyři videa, která ukazují vaši nabídku. Zadejte video&#39;s názvem, jeho webovou adresou (URL) a miniaturou .png obrázku videa o rozměrech 1280 x 720 pixelů.

Než budete pokračovat do další části Ceny a dostupnost, vyberte **Uložit pracovní verzi.**

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Zde definujete prvky, jako jsou ceny, trh a soukromý klíč.

1. **Trh**: Nastavte na trhu, ve které bude vaše nabídka k dispozici. Můžete vybrat pouze jeden trh na nabídku.
    1. Na trzích Spojených států nebo Kanady vyberte **možnost Upravit stavy** (nebo provincie), abyste **určili,** kde bude vaše nabídka k dispozici.
2. **Náhled publika**: Nakonfigurujte **skrytí klávesy,** které slouží k nastavení soukromé cílové skupiny pro vaši nabídku.
3. **Ceny**: Určete, zda je vaše nabídka **bezplatná** nebo **placená** nabídka.

    > [!NOTE]
    > Konzultační nabídky služeb jsou pouze pro výpis. Veškeré transakce se budou uskutečnit přímo, mimo komerční trh.

4. U placené nabídky zadejte **cenu a měnu** a zda je cena **pevná** nebo **odhadovaná**. Pokud je odhad, musíte v popisu zadat, jaké faktory ovlivní cenu.
5. Vyberte **Uložit pracovní verzi**.

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky můžete nabídku odeslat ke kontrole a publikování.

1. Až budete připraveni publikovat nabídku služeb Poradenství, klikněte na **Zkontrolovat a publikovat**.
2. Projděte si podrobnosti na stránce konečného odeslání.
3. Pokud je to nutné, napište poznámku certifikačnímu týmu, pokud se domníváte, že některé z podrobností vaší nabídky vyžadují vysvětlení.
4. Až budete připraveni, vyberte **Odeslat**.
5. Stránka **Přehled nabídky** ukazuje, v jaké fázi publikování se vaše nabídka nachází.

Další informace o tom, jak dlouho můžete očekávat, že vaše nabídka bude v každé fázi publikování, naleznete [v tématu Kontrola stavu publikování nabídky Commercial Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status).

## <a name="update-your-existing-consulting-service-offers"></a>Aktualizace stávajících nabídek konzultačních služeb

- [Aktualizace stávající nabídky na komerčním trhu](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
