---
title: Vytvoření nabídky aplikace Power BI v Microsoft AppSource
description: Naučte se, jak vytvořit a publikovat Power BI nabídku aplikace Microsoft AppSource.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/22/2020
ms.openlocfilehash: bff20468e8185073f5c192c1e115bc405dd089eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693607"
---
# <a name="create-a-power-bi-app-offer"></a>Vytvoření nabídky aplikací Power BI

Tento článek popisuje, jak vytvořit a publikovat nabídku aplikace Power BI k [Microsoft AppSource](https://appsource.microsoft.com/).

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Power BI aplikace služby**.

   ![Znázorňuje levou navigační nabídku.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v online obchodech po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

> [!IMPORTANT]
> Pokud se **aplikace Power BI služby** nezobrazí nebo není povolená, váš účet nemá oprávnění k vytvoření tohoto typu nabídky. Zkontrolujte prosím, že jste splnili všechny [požadavky](create-power-bi-app-overview.md) na tento typ nabídky, včetně registrace pro vývojářský účet.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1** , bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po zvolení možnost **vytvořit** se alias nabídky nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-overview"></a>Přehled nabídky

Tato stránka zobrazuje vizuální znázornění kroků požadovaných k publikování této nabídky (dokončené i nadcházející) a jak dlouho by měly být jednotlivé kroky dokončeny.

Obsahuje odkazy na provádění operací s touto nabídkou na základě vámi provedeného výběru. Například:

- Pokud je nabídka nabídkou konceptu a odstranění konceptu
- Pokud je nabídka živá, [přestanou prodávat nabídku](update-existing-offer.md#stop-selling-an-offer-or-plan) .
- Pokud je nabídka ve verzi Preview – [Přejít do provozu](../review-publish-offer.md#previewing-and-approving-your-offer)
- Pokud jste nedokončili registraci vydavatele [, zrušte publikování.](../review-publish-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="customer-leads"></a>Potenciální zákazníci

Když publikujete nabídku na webu Marketplace s partnerským centrem, musíte ji připojit k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující systémy CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) pro zákaznickou zapojení
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Pokud systém CRM není v tomto seznamu, použijte [koncový bod](commercial-marketplace-lead-management-instructions-https.md) [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) nebo a HTTPS k ukládání zákaznických dat. Pak exportujte data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

Tady jsou některé další zdroje informací o vedoucím řízení:

- [Potenciální zákazníci z nabídky komerčního marketplace](commercial-marketplace-get-customer-leads.md)
- [Běžné otázky týkající se správy potenciálních zákazníků](../lead-management-faq.md#common-questions-about-lead-management)
- [Řešení chyb konfigurace potenciálních zákazníků](../lead-management-faq.md#publishing-config-errors)
- [Přehled řízení vedoucích](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka vám umožní definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="category"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Obor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>Právní informace

#### <a name="terms-and-conditions"></a>podmínky a ujednání

Pokud chcete zadat vlastní podmínky a ujednání, zadejte v poli **podmínky a ujednání** až 10 000 znaků. Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat k další části seznamu nabídek, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tady definujete podrobnosti nabídky, které se zobrazí na webu Marketplace. To zahrnuje název nabídky, popis, obrázky a tak dále.

### <a name="language"></a>Jazyk

Vyberte jazyk, ve kterém bude vaše nabídka uvedená. V současné době je jako jediná dostupná možnost **Angličtina (USA)** .

Definujte podrobnosti o Marketplace (například název nabídky, popis a obrázky) pro každý jazyk nebo trh. Chcete-li poskytnout tyto informace, vyberte název jazyka/trhu.

> [!NOTE]
> Podrobnosti nabídky nejsou v anglickém jazyce nutné, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Také je v pořádku poskytnutí užitečného odkazu na nabídku obsahu v jazyce, který se liší od toho, který se používá v seznamu nabídek.

Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):

:::image type="content" source="media/example-power-bi-app.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Produkty
3. Kategorie
4. Obory
5. Adresa podpory (odkaz)
6. Podmínky použití
7. Zásady ochrany osobních údajů
8. Název nabídky
9. Souhrn
10. Description
11. Snímky obrazovky a videa

### <a name="name"></a>Name

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Dá se použít jako ochranná známka (a může obsahovat symboly ochranné známky nebo autorských práv).
- Nemůže být delší než 50 znaků.
- Nejde zahrnout emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky. Může to být až 100 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>Hledaná klíčová slova

Zadejte až tři volitelná klíčová slova pro hledání, která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků také použijte Tato klíčová slova v popisu.

### <a name="helpprivacy-web-addresses"></a>Webová adresa pro ochranu a ochranu osobních údajů

Poskytněte odkazy, které zákazníkům pomohou lépe porozumět vaší nabídce.

#### <a name="help-link"></a>Odkaz na Help

Zadejte webovou adresu, kde se zákazníci mohou dozvědět více o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte webovou adresu do zásad ochrany osobních údajů vaší organizace. Zodpovídáte za zajištění, že vaše nabídka vyhovuje zákonům a předpisům o ochraně osobních údajů. Zodpovídáte také za publikování platných zásad ochrany osobních údajů na vašem webu.

### <a name="contact-information"></a>Kontaktní informace

Musíte zadat jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují. Je k dispozici pro společnost Microsoft a může být poskytnuta partnerům Cloud Solution Provider (CSP).

- Kontakt podpory (povinné): pro obecné dotazy týkající se podpory.
- Technický kontakt (povinné): pro technické dotazy a problémy certifikace.
- Kontakt programu CSP (volitelné): pro otázky prodejců související s programem CSP.

V části **kontakt podpory** zadejte webovou adresu **webu podpory** , kde můžou partneři najít podporu pro vaši nabídku.

### <a name="supporting-documents"></a>Podpůrné dokumenty

Poskytněte aspoň jeden a až tři související marketingové dokumenty ve formátu PDF. Například dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace.

### <a name="marketplace-images"></a>Image Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz se odmítne.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje `https://upload.xboxlive.com` službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto, aby se vytvořilo **malé** logo. Volitelně můžete tuto položku nahradit jinou imagí později.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshoty

Přidejte alespoň jeden a až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každá velikost musí být 1280 × 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelné)

Přidejte až pět videí, která předvádí vaši nabídku. Zadejte název videa, jeho webovou adresu a miniaturu obrázku PNG ve velikosti 1280 × 720 pixelů.

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

Další informace o vytváření seznamů nabídek najdete v tématu [nabídky Doporučené postupy](../gtm-offer-listing-best-practices.md).

## <a name="technical-configuration"></a>Technická konfigurace

Zvyšte svou aplikaci ve službě Power BI na produkční prostředí a poskytněte odkaz na instalační program aplikace Power BI, který zákazníkům umožňuje nainstalovat vaši aplikaci. Další informace najdete v tématu [publikování aplikací s řídicími panely a sestavami v Power BI](/power-bi/service-create-distribute-apps).

## <a name="supplemental-content"></a>Doplňkový obsah

Poskytněte vám další informace o vaší nabídce, které nám pomůžou ověřit. Tyto informace se zákazníkům nezobrazují ani nepublikují na webu Marketplace.

### <a name="validation-assets"></a>Prostředky ověřování

Volitelně můžete přidat pokyny (až 3 000 znaků), které vám pomohou týmu ověřování Microsoft konfigurovat, připojit a testovat vaši aplikaci. Zahrňte typická nastavení konfigurace, účty, parametry nebo další informace, které se dají použít k otestování možnosti připojit data. Tyto informace jsou viditelné pouze pro ověřovací tým a používají se pouze pro účely ověření.

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky můžete odeslat nabídku pro kontrolu a publikování.

V pravém horním rohu portálu vyberte **zkontrolovat a publikovat**.

Na stránce pro kontrolu můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky. Dokud nebudou všechny oddíly nabídky označeny jako úplné, nelze publikovat.
  - **Nespuštěno** – oddíl se nespustil a musí se dokončit.
  - **Nedokončeno** – oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje zadání dalších informací. Pokyny najdete v předchozích částech tohoto dokumentu.
  - **Complete** – oddíl obsahuje všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky dokončené.
- Poskytněte pokyny pro testování certifikačního týmu, aby se zajistilo správné testování vaší aplikace. Poskytněte také všechny doplňkové poznámky, které jsou užitečné pro porozumění vaší nabídce.

Pokud chcete odeslat nabídku pro publikování, vyberte **publikovat**.

Pošleme vám e-mail s oznámením, že je k dispozici verze Preview této nabídky ke kontrole a schválení. Pokud chcete svou nabídku publikovat na veřejném, klikněte na partnerské centrum a vyberte **jít-Live**.
