---
title: Vytvoření nabídky Dynamics 365 for Operations na komerčním webu Marketplace
description: Postup vytvoření nové nabídky Dynamics 365 for Operations pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu Cloud Solution Provider (CSP) prostřednictvím portálu pro komerční tržiště v partnerském centru Microsoftu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: a7a77cc18ddc1ab8664d49aa3c2ac049a683ed3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710630"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Vytvoření nabídky Dynamics 365 for Operations

Toto téma vysvětluje, jak vytvořit novou nabídku Dynamics 365 for Operations. [Microsoft Dynamics 365 pro finance a operace](https://dynamics.microsoft.com/finance-and-operations) je služba pro plánování podnikových zdrojů (ERP), která podporuje pokročilé finance, provoz, výrobu a správu dodavatelských řetězců. Všechny nabídky pro Dynamics 365 pro operace musí projít procesem certifikace.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

>[!NOTE]
> Po publikování nabídky se úpravy nabídky aktualizují v partnerském centru a v online obchodě po opětovném odeslání nabídky k publikování.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 pro operace**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v online obchodech po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- ID nabídky v kombinaci s ID vydavatele musí být kratší než 40 znaků.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery. Pokud je například vaše Vydavatel s ID testpublisherid a zadáte **zkušební nabídku – 1** , Webová adresa nabídky bude `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Jak chcete potenciálním zákazníkům pracovat s touto nabídkou pro výpis?

Vyberte možnost, kterou chcete použít pro tuto nabídku.

#### <a name="get-it-now-free"></a>Získat hned (zdarma)

Seznamte se s vaší nabídkou pro zákazníky zdarma.

#### <a name="free-trial-listing"></a>Bezplatná zkušební verze (výpis)

Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

> [!NOTE]
> Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli prostřednictvím Azure Active Directory (Azure AD) k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft.

#### <a name="contact-me"></a>Kontaktujte mě

Shromažďovat kontaktní údaje zákazníka propojením systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [zájemci zákazníků](#customer-leads).

### <a name="test-drive"></a>Testovací jednotka

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si předplatné před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. Pokud se chcete dozvědět víc, začněte tím, [co je Test Drive](../what-is-test-drive.md).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

### <a name="customer-leads"></a>Potenciální zákazníci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="category"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md).

### <a name="industry"></a>Obor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze vaší nabídky. Zákazníci uvidí tuto verzi uvedenou na stránce s podrobnostmi nabídky.

### <a name="terms-and-conditions"></a>podmínky a ujednání

V poli **podmínky a ujednání** zadejte vlastní právní podmínky. Můžete taky zadat adresu URL, kde se můžou vaše podmínky a ujednání najít. Zákazníci budou muset tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tato stránka zobrazuje jazyky, ve kterých bude vaše nabídka uvedená. V současné době je jako jediná dostupná možnost **Angličtina (USA)** .

Pro každý jazyk nebo trh budete muset zadat podrobnosti o Marketplace (název nabídky, popis, obrázky atd.). Chcete-li poskytnout tyto informace, vyberte název jazyka/trhu.

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky, podmínky použití atd.) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource:

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Produkty
3. Kategorie
4. Obory
5. Adresa podpory (odkaz)
6. Podmínky použití
7. Zásady ochrany osobních údajů
8. Název nabídky
9. Snímky obrazovky a videa
10. Popis

### <a name="name"></a>Název

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole je předem vyplněno textem, který jste zadali pro možnost **alias nabídky** při vytváření nabídky, ale můžete tuto hodnotu změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

### <a name="short-description"></a>Krátký popis

Zadejte krátký popis vaší nabídky, maximálně 100 znaků. Tento popis se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Hledaná klíčová slova

Volitelně můžete zadat až tři klíčová slova pro hledání, která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků zkuste použít tato klíčová slova i v popisu.

### <a name="products-your-app-works-with"></a>Produkty, se kterými pracuje vaše aplikace

Pokud chcete zákazníkům sdělit, že vaše aplikace pracuje s konkrétními produkty, zadejte do tohoto pole až tři názvy produktů.

### <a name="support-urls"></a>Adresy URL podpory

V této části můžete zadat odkazy, které zákazníkům pomohou pochopit Další informace o vaší nabídce.

#### <a name="help-link"></a>Odkaz na Help

Zadejte adresu URL, kde se zákazníci mohou dozvědět více o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="contacts"></a>Kontakty

V této části zadejte jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** zadejte **adresu URL podpory** , kde partneři CSP můžou najít podporu pro vaši nabídku.

### <a name="supporting-documents"></a>Podpůrné dokumenty

Poskytněte aspoň jeden (a až tři) související marketingové dokumenty, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace. Tyto dokumenty musí být ve formátu PDF.

### <a name="marketplace-images"></a>Image Marketplace

V této části můžete zadat loga a obrázky, které se použijí při zobrazení vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu. png.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto, aby se vytvořilo **malé** logo. Volitelně můžete tuto položku nahradit jinou imagí později.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshoty

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Vyžaduje se aspoň jeden snímek obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až čtyři videa, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

[Osvědčené postupy pro výpisy nabídky na webu Marketplace](../gtm-offer-listing-best-practices.md)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="availability"></a>Dostupnost

Tato stránka poskytuje možnosti, kde a jak má být nabídka dostupná.

### <a name="markets"></a>Trhy

V této části můžete zadat trhy, ve kterých by měla být vaše nabídka dostupná. Provedete to tak, že vyberete **Upravit trhy,** ve kterém se zobrazí místní okno pro **Výběr trhu** .

Ve výchozím nastavení nejsou vybrané žádné trhy. Vyberte alespoň jeden trh pro publikování vaší nabídky. Klikněte na  **Vybrat vše** , aby vaše nabídka byla dostupná na všech možných trzích, nebo vyberte konkrétní trhy, které chcete přidat. Po dokončení vyberte **Uložit**.

Vaše výběry se tady vztahují jenom na nové akvizice; Pokud už má vaše aplikace na určitém trhu nějaký uživatel a později tento trh odeberete, lidé, kteří si tuto nabídku už na tomto trhu mají, můžou i nadále používat, ale žádní noví zákazníci na tomto trhu nebudou moct nabídku získat.

> [!IMPORTANT]
> Je vaše zodpovědnost za splnění jakýchkoli místních právních požadavků, a to i v případě, že tyto požadavky nejsou uvedené ani v partnerském centru.

Mějte na paměti, že i když vyberete všechny trhy, místní zákony a omezení nebo jiné faktory mohou zabránit tomu, aby některé nabídky byly uvedené v některých zemích a oblastech.

### <a name="preview-audience"></a>Cílová skupina Preview

Před publikováním vaší nabídky v širší nabídce na webu Marketplace je nejprve nutné ji zpřístupnit pro skupinu s omezeným počtem verzí **Preview**. Zadejte **Skrýt klíč** (libovolný řetězec s malými písmeny a číslicemi). Členové vaší cílové skupiny ve verzi Preview můžou použít tento skrytý klíč jako token k zobrazení náhledu vaší nabídky na webu Marketplace.

Až budete připraveni zpřístupnit nabídku a odebrat omezení verze Preview, je nutné odebrat **Skrýt klíč** a znovu publikovat.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration"></a>Technická konfigurace

Tato stránka definuje technické podrobnosti používané pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat.

### <a name="solution-identifier"></a>Identifikátor řešení

Zadejte identifikátor řešení (GUID) pro vaše řešení.

Zjištění identifikátoru řešení:

1. V Microsoft Dynamics Lifecycle Services (LCS) vyberte **Správa řešení**.
2. Vyberte své řešení a pak vyhledejte **identifikátor řešení** v **přehledu balíčku**. Pokud je identifikátor prázdný, vyberte **Upravit** a znovu publikovat balíček a pak to zkuste znovu.

### <a name="release-version"></a>Verze vydaných verzí

Vyberte verzi Dynamics 365 pro finance a operace, se kterými toto řešení funguje.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive-technical-configuration"></a>Technická konfigurace testovací verze

Tato stránka umožňuje nastavit ukázku ("testovací jednotka"), která zákazníkům umožňuje vyzkoušet si vaši nabídku před nákupem. Přečtěte si další informace o [testovacích jednotkách](../what-is-test-drive.md).

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

Až dokončíte nastavování testovacích jednotek, vyberte **Uložit koncept** a teprve potom pokračujte.

## <a name="supplemental-content"></a>Doplňkový obsah

Tato stránka vám umožní zadat další informace o vaší nabídce, které nám pomůžou vaši nabídku ověřit. Tyto informace se zákazníkům nezobrazují ani nepublikují na webu Marketplace.

### <a name="validation-assets"></a>Prostředky ověřování

V této části nahrajte [sestavu analýzy přizpůsobení (auto)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) . Tato sestava je vygenerována analýzou modelů přizpůsobení a rozšíření na základě předdefinované sady pravidel osvědčených postupů.

Tento soubor musí být ve formátu. xls nebo. xlsx. Pokud máte více než jednu sestavu, můžete nahrát soubor. zip obsahující všechny sestavy.

### <a name="does-solution-include-localizations"></a>Zahrnuje řešení lokalizace?

Vyberte **Ano** , pokud řešení umožňuje používání místních standardů a zásad (například pokud splňuje různá pravidla mezd, která vyžaduje jiné země nebo oblasti). Jinak vyberte **No** (Ne).

### <a name="does-solution-enable-translations"></a>Povoluje řešení překlady?

Odpovězte na **Ano** , pokud se text v řešení dá přeložit do jiných jazyků. Jinak vyberte **No** (Ne).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="publish"></a>Publikovat

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **zkontrolovat a publikovat** v pravém horním rohu portálu.

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - **Nezahájeno** – oddíl se nedotkl a měl by být dokončený.
    - **Nedokončeno** – oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se k oddílům a aktualizujte je.
    - **Complete** – část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci. Další informace o pokynech pro testování a dokončení prvního publikování naleznete v tématu [AppSource Dynamics 365 Finance and Operational Validation](https://docs.microsoft.com/azure/marketplace/dynamics-365-finance-operations-functional-validation).
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit. Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném.

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
