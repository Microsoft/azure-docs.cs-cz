---
title: Vytvoření nabídky Dynamics 365 for Operations v Microsoft AppSource
description: Postup vytvoření nabídky Dynamics 365 for Operations v Microsoft AppSource. Seznam nebo prodej vaší nabídky v AppSource nebo prostřednictvím programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 12/02/2020
ms.openlocfilehash: bcb7f2d008c54981223b4839f9a750e6ee9ac5cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355137"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Vytvoření nabídky Dynamics 365 for Operations

Tento článek popisuje, jak vytvořit novou nabídku Dynamics 365 for Operations. [Microsoft Dynamics 365 pro finance a operace](https://dynamics.microsoft.com/finance-and-operations) je služba pro plánování podnikových zdrojů (ERP), která podporuje pokročilé finance, provoz, výrobu a správu dodavatelských řetězců. Všechny nabídky pro Dynamics 365 pro operace musí projít procesem certifikace.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

>[!NOTE]
> Po publikování nabídky se úpravy nabídky aktualizují v partnerském centru a v online obchodě po opětovném odeslání nabídky k publikování.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 pro operace**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-dynamics-365-operations.png)

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- ID nabídky v kombinaci s ID vydavatele musí být kratší než 40 znaků.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery. Pokud je například vaším vydavatelem ID `testpublisherid` a zadáte **test-nabídka-1**, Webová adresa nabídky bude `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- Toto ID se po výběru **vytvořit** nedá změnit.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po výběru **vytvořit** se tento název nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="alias"></a>Alias

Zadejte popisný název, který použijeme k tomu, abychom na tuto nabídku odkazovali výhradně v partnerském centru. Tento název (předem vyplněný s tím, co jste zadali při vytváření nabídky) se na webu Marketplace nepoužije a liší se od názvu nabídky, který se zákazníkům zobrazuje. Pokud chcete později aktualizovat název nabídky, otevřete stránku se [seznamem nabídek](#offer-listing) .

### <a name="setup-details"></a>Podrobnosti o nastavení

**Jak chcete, aby potenciální zákazníci mohli pracovat s touto nabídkou**, vyberte možnost, kterou chcete pro tuto nabídku použít.

- **Získejte ho hned (zdarma)** – Seznamte se s vaší nabídkou zdarma pro zákazníky.
- **Bezplatná zkušební verze (výpis)** – Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

    > [!NOTE]
    > Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli prostřednictvím Azure Active Directory (Azure AD) k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft.

- **Kontaktujte mě** – shromáždění zákaznických informací o zákaznících tak, že se připojíte k systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [zájemci zákazníků](#customer-leads).

### <a name="test-drive"></a>Testovací jednotka

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si předplatné před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. Pokud se chcete dozvědět víc, začněte tím, [co je Test Drive](../what-is-test-drive.md).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

### <a name="customer-leads"></a>Potenciální zákazníci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="categories"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md).

### <a name="industries"></a>Obory

[!INCLUDE [Industry Taxonomy](includes/industry-taxonomy.md)]

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze vaší nabídky. Zákazníci uvidí tuto verzi uvedenou na stránce s podrobnostmi nabídky.

### <a name="terms-and-conditions"></a>podmínky a ujednání

Sem zadejte vlastní právní podmínky a ujednání. Můžete také zadat adresu, na které se můžou vaše podmínky a ujednání najít. Zákazníci budou muset tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

<!--This page displays the languages in which your offer will be listed. Currently, **English (United States)** is the only available option.

Define marketplace details such as offer name, description, and images for each language/market. Select the language/market name to provide this info.-->This page lets you define offer details such as offer name, description, links, and contacts.

> [!NOTE]
> Poskytují podrobnosti o seznamech nabídek jenom v jednom jazyku. Není nutné, aby byla v angličtině, pokud popis nabídky začíná frází "Tato aplikace je dostupná pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):
<!-- update screen? -->
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
9. Description
10. Snímky obrazovky a videa

### <a name="marketplace-details"></a>Podrobnosti Marketplace

**Název** , který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole je předem vyplněno textem, který jste zadali pro možnost **alias nabídky** při vytváření nabídky, ale můžete tuto hodnotu změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

Pro **Shrnutí výsledků hledání** zadejte krátký popis vaší nabídky, maximálně 100 znaků. Tento popis se dá použít ve výsledcích hledání na webu Marketplace.

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

Volitelně můžete zadat až tři **klíčová slova pro hledání** , která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků také použijte Tato klíčová slova v popisu.

Pokud chcete zákazníkům sdělit, **se kterými produkty vaše aplikace pracuje**, zadejte až tři názvy produktů.

### <a name="helpprivacy-urls"></a>Adresa URL pro ochranu a ochranu osobních údajů

Zadejte **odkaz na nápovědu pro vaši aplikaci** (URL), kde se zákazníci mohou dozvědět více o vaší nabídce. Vaše adresa URL vaší pomoci nemůže být stejná jako vaše adresa URL podpory.

Zadejte **odkaz zásady ochrany osobních údajů** (URL) na zásady ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="contact-information"></a>Kontaktní informace

Zadejte jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** zadejte **adresu URL podpory** , kde partneři CSP můžou najít podporu pro vaši nabídku. Vaše adresa URL podpory nemůže být shodná s adresou URL vaší pomoci.

### <a name="supporting-documents"></a>Podpůrné dokumenty

Poskytněte aspoň jeden (a až tři) související marketingové dokumenty, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace ve formátu PDF.

### <a name="marketplace-media"></a>Média Marketplace

Poskytněte loga a obrázky, které se použijí při zobrazování vaší nabídky pro zákazníky. Všechny obrázky musí být ve formátu PNG.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="logos"></a>Značk

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije k vytvoření dalších požadovaných velikostí. Volitelně můžete tuto položku nahradit jinou imagí později.

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshoty

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Vyžaduje se aspoň jeden snímek obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí mít 1280 x 720 pixelů a ve formátu PNG.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až čtyři videa, která předvádí vaši nabídku. Videa musí být hostována na externím webu. U každého z nich zadejte název videa, jeho adresu a miniaturu obrázku (1280 x 720 pixelů).

Další materiály k seznamům Marketplace najdete v tématu [osvědčené postupy pro výpisy nabídky na webu Marketplace](../gtm-offer-listing-best-practices.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="availability"></a>Dostupnost

Tato stránka umožňuje definovat, kde a jak má být nabídka dostupná.

### <a name="markets"></a>Trhy

Pokud chcete určit trhy, kde má vaše nabídka k dispozici, vyberte **Upravit trhy** . zobrazí se místní okno pro **Výběr trhu** .

Vyberte alespoň jeden trh. Zvolte **možnost Vybrat vše** , pokud chcete nabídku zpřístupnit na všech možných trzích, nebo vyberte pouze konkrétní trhy, které chcete. Až budete hotovi, vyberte **Uložit**.

Vaše výběry se tady vztahují jenom na nové akvizice; Pokud už má vaše aplikace na určitém trhu nějaký uživatel a později tento trh odeberete, lidé, kteří si tuto nabídku už na tomto trhu mají i nadále používat, ji mohou i nadále používat, ale žádní noví zákazníci na tomto trhu nebudou moci získat vaši nabídku.

> [!IMPORTANT]
> Je vaše zodpovědnost za splnění jakýchkoli místních právních požadavků, a to i v případě, že tyto požadavky nejsou uvedené ani v partnerském centru. I když vyberete možnost všechny trhy, místní zákony, omezení nebo jiné faktory, může se stát, že některé nabídky nebudou uvedené v některých zemích a oblastech.

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

<!-- ## Test drive technical configuration

This page lets you set up a demonstration ("test drive") that allows customers to try your offer before purchasing it. Learn more in [What is test drive](../what-is-test-drive.md).

To enable a test drive, select the **Enable a test drive** check box on the [Offer setup](#test-drive) tab. To remove test drive from your offer, clear this check box.

When you've finished setting up your test drive, select **Save draft** before continuing.
-->
## <a name="supplemental-content"></a>Doplňkový obsah

Tato stránka vám umožní zadat další informace, které nám pomůžou vaši nabídku ověřit. Tyto informace se zákazníkům nezobrazují ani nepublikují na webu Marketplace.

### <a name="validation-assets"></a>Prostředky ověřování

V této části nahrajte [sestavu analýzy přizpůsobení (auto)](/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report) . Tato sestava je vygenerována analýzou modelů přizpůsobení a rozšíření na základě předdefinované sady pravidel osvědčených postupů.

Tento soubor musí být ve formátu. xls nebo. xlsx. Pokud máte více než jednu sestavu, můžete nahrát soubor. zip obsahující všechny sestavy.

### <a name="does-solution-include-localizations"></a>Zahrnuje řešení lokalizace?

Vyberte **Ano** , pokud řešení umožňuje používání místních standardů a zásad (například pokud splňuje různá pravidla mezd, která vyžaduje jiné země nebo oblasti). Jinak vyberte **No** (Ne).

### <a name="does-solution-enable-translations"></a>Povoluje řešení překlad (y)?

Odpovězte na **Ano** , pokud se text v řešení dá přeložit do jiných jazyků. Jinak vyberte **No** (Ne).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="publish"></a>Publikovat

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Po dokončení všech požadovaných oddílů nabídky vyberte možnost **zkontrolovat a publikovat** v pravém horním rohu portálu.

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - **Nezahájeno** – oddíl se nedotkl a musí se dokončit.
    - **Uncomplete** – oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje více informací. Vraťte se k oddílům a aktualizujte je.
    - **Complete** – část je dokončena, byla zadána veškerá požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci. Další informace o pokynech pro testování a dokončení prvního publikování naleznete v tématu [AppSource Dynamics 365 Finance and Operational Validation](../dynamics-365-finance-operations-functional-validation.md).
- Kliknutím na **Odeslat** nabídku pro publikování odešlete. Až bude verze Preview dostupná ke kontrole a schválení, pošleme vám e-mail. Vraťte se do partnerského centra a výběrem možnosti **Přejít do provozu** publikujte vaši nabídku na veřejném.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)