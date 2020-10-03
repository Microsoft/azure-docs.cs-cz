---
title: Vytvoření nabídky Business Central v Dynamics 365 – Microsoft Commercial Marketplace
description: Přečtěte si postup a požadavky pro vytvoření nové nabídky Business Central v Dynamics 365 v partnerském centru Microsoftu na komerčním webu Microsoft Marketplace. Nabídku můžete vypsat nebo prodávat v Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: navits09
ms.author: navits
ms.date: 08/07/2020
ms.openlocfilehash: e42111de7d05f184e1153ddc351c3f1fa10cc86f
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665234"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Vytvoření nabídky Dynamics 365 Business Central

Tento článek vysvětluje, jak vytvořit novou nabídku podnikového centra Dynamics 365. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) je systém pro plánování podnikových zdrojů (ERP), který zpracovává rozsáhlou škálu obchodních procesů, včetně finančních prostředků, operací, dodavatelských řetězců, CRM a řízení projektů a elektronického obchodování. Balíčky Premium podporují také klasický model nasazení a výrobu. Všechny nabídky pro Dynamics 365 Business Central musí projít procesem certifikace.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 Business Central**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-dynamics-365-business-central.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v online obchodech po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po zvolení možnost **vytvořit**se alias nabídky nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

Pomocí těchto kroků můžete nastavit nabídku.

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

V této fázi služby Dynamics 365 Business Central nepodporují Test Drive. Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí políčka **Povolit testovací jednotku** .

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

Tady můžete definovat podrobnosti pro vaši nabídku, jako je název, popis a obrázky.

> [!NOTE]
> Podrobnosti o seznamu nabídky můžete poskytnout jenom v jednom jazyce. Není nutné, aby byla v angličtině, pokud popis nabídky začíná frází "Tato aplikace je dostupná pouze v [jazykovém jazyce, který není v angličtině]." Také je přijatelné zadat *adresu URL pro odkaz na nápovědu* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

Tady je příklad toho, jak se zobrazují informace o nabídce v Microsoft AppSource (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):

:::image type="content" source="media/example-d365-business-central.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Microsoft AppSource.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Produkty
3. Kategorie
4. Adresa podpory (odkaz)
5. Podmínky použití
6. Zásady ochrany osobních údajů
7. Název nabídky
8. Shrnutí
9. Description
10. Snímky obrazovky a videa

### <a name="name"></a>Name

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

### <a name="helpprivacy-urls"></a>Adresa URL pro ochranu a ochranu osobních údajů

V této části můžete zadat odkazy, které zákazníkům pomohou pochopit Další informace o vaší nabídce.

#### <a name="help-link"></a>Odkaz na Help

Zadejte adresu URL, kde se zákazníci mohou dozvědět více o vaší nabídce. Odkaz na vaši **pomoc** nemůže být stejný jako vaše **Adresa URL podpory** (vysvětlení najdete níže).

#### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="contact-information"></a>Kontaktní informace

V této části musíte zadat jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** je také nutné zadat **adresu URL podpory** , kde mohou partneři CSP najít podporu pro vaši nabídku. Vaše adresa URL podpory nemůže být stejná jako odkaz na vaši **pomoc**.

### <a name="supporting-documents"></a>Podpůrné dokumenty

Poskytněte aspoň jeden (a až tři) související marketingové dokumenty, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace. Tyto dokumenty musí být ve formátu. PDF.

### <a name="marketplace-images"></a>Image Marketplace

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto, aby se vytvořilo **malé** logo. Volitelně můžete tuto položku nahradit jinou imagí později.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje `https://upload.xboxlive.com` službu používanou partnerským centrem.

#### <a name="screenshots"></a>Screenshoty

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Vyžadují se aspoň tři snímky obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

[Osvědčené postupy pro výpisy nabídky na webu Marketplace](../gtm-offer-listing-best-practices.md)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="availability"></a>Dostupnost

Tato stránka vám nabídne možnosti, kde a jak má být nabídka dostupná.

### <a name="markets"></a>Trhy

V této části můžete zadat trhy, ve kterých by měla být vaše nabídka dostupná. Provedete to tak, že vyberete **Upravit trhy**, ve kterém se zobrazí místní okno pro **Výběr trhu** .

Vyberte aspoň jeden trh, aby se vaše nabídka publikovala. Zvolte **možnost Vybrat vše** , pokud chcete nabídku zpřístupnit na všech možných trzích, nebo vyberte konkrétní trhy, které chcete přidat.

Vaše výběry se tady vztahují jenom na nové akvizice; Pokud už má vaše aplikace na určitém trhu nějaký uživatel a později tento trh odeberete, lidé, kteří si tuto nabídku už na tomto trhu mají, můžou i nadále používat, ale žádní noví zákazníci na tomto trhu nebudou moct nabídku získat.

> [!IMPORTANT]
> Je vaše zodpovědnost za splnění jakýchkoli místních právních požadavků, a to i v případě, že tyto požadavky nejsou uvedené ani v partnerském centru.

Mějte na paměti, že i když vyberete všechny trhy, místní zákony, omezení nebo jiné faktory, může se stát, že některé nabídky nebudou uvedené v některých zemích a oblastech.

### <a name="preview-audience"></a>Cílová skupina Preview

Před publikováním vaší nabídky v širší nabídce na webu Marketplace je nejprve nutné ji zpřístupnit pro skupinu s omezeným počtem verzí **Preview**. Zadejte **Skrýt klíč** (libovolný řetězec s malými písmeny a číslicemi). Členové vaší cílové skupiny ve verzi Preview můžou použít tento skrytý klíč jako token k zobrazení náhledu vaší nabídky na webu Marketplace.

Až budete připraveni zpřístupnit nabídku a odebrat omezení verze Preview, je nutné odebrat **Skrýt klíč** a znovu publikovat.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration"></a>Technická konfigurace

Tato stránka definuje technické podrobnosti používané pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat.

### <a name="package-type"></a>Typ balíčku

Vyberte možnost, která se vztahuje na vaši nabídku:

* **Přidání na** – doplňková aplikace rozšiřuje prostředí a stávající funkce Dynamics 365 Business Central. Podrobnosti najdete v tématu věnovaném [doplňkovým aplikacím](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
* **Connect** – aplikace připojení se dá použít ve scénáři, ve kterém musí být navázáno připojení Point-to-Point mezi Dynamics 365 Business Central a řešením nebo službou třetí strany. Podrobnosti najdete v tématu [připojení aplikací](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Nahrání souboru

Pokud jste vybrali možnost **Přidat** výše, kam nahrajete soubor balíčku nabídky společně se soubory balíčku pro jakékoli rozšíření, na kterém má závislosti.

#### <a name="extensions-package-file"></a>Soubor balíčku rozšíření

Nahrajte soubor balíčku rozšíření (. app) pro vaši nabídku.

#### <a name="library-package-file"></a>Soubor balíčku knihovny

Vyžaduje se, pokud se vaše nabídka musí nainstalovat spolu s jinou příponou, která se na Marketplace nepublikuje. Pokud ano, nahrajte sem soubor. app.

>[!NOTE]
>Soubor balíčku závislostí se už nepoužívá. Místo toho nahrajte soubor balíčku knihovny.

### <a name="url-to-app-installation"></a>Adresa URL pro instalaci aplikace

Pokud jste vybrali **připojit** výše, zadejte adresu pro instalaci aplikace zde. Pro připojené služby, které nevyžadují instalaci, zadejte adresu cílové stránky nebo přihlašovací stránky vaší služby.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive-technical-configuration"></a>Technická konfigurace testovací verze

Tato stránka umožňuje nastavit ukázku ("testovací jednotka"), která zákazníkům umožňuje vyzkoušet si vaši nabídku před nákupem. Přečtěte si další informace o [testovacích jednotkách](../what-is-test-drive.md).

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

Až dokončíte nastavování testovacích jednotek, vyberte **Uložit koncept** a teprve potom pokračujte.

## <a name="supplemental-content"></a>Doplňkový obsah

Tato stránka vám umožní zadat další informace o vaší nabídce, které nám pomůžou vaši nabídku ověřit. Tyto informace se zákazníkům nezobrazují ani nepublikují na webu Marketplace.

### <a name="target-release"></a>Cílová verze

Určete, která verze Microsoft Dynamics Business Central má vaše řešení cíle: **aktuální**, **Další hlavní**nebo **další vedlejší**. Tyto informace nám umožní správně otestovat vaše řešení.

### <a name="supported-editions"></a>Podporované edice

Pokud vaše nabídka vyžaduje edici Premium z Microsoft Dynamics 365 Business Central, vyberte jenom **Premium** . V opačném případě vyberte možnost **základy** i **Premium**.

### <a name="key-usage-scenario"></a>Scénář použití klíče

Je nutné nahrát soubor PDF se základními scénáři použití vaší nabídky, které jsou uvedeny v dokumentu (formát PDF). Všechny uvedené scénáře mohou být ověřeny týmem ověřování, než schválíme vaši nabídku na webu Marketplace.

### <a name="app-tests-automation"></a>Automatizace testů aplikací

Pokud je vaše nabídka doplňkovou aplikací, je nutné nahrát soubor **testů aplikace pro automatizaci** (. app). Tento soubor se nedá použít pro připojení aplikací.

### <a name="test-accounts"></a>Testovací účty

Pokud je potřeba testovací účet, aby mohl náš Certifikační tým správně zkontrolovat vaši nabídku, nahrajte soubor. PDF,. doc nebo. docx pomocí informací o **testovacích účtech** .

## <a name="publish"></a>Publikovat

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** . 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - *Nespuštěno* – znamená, že se oddíl nedotkl a je potřeba ho dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se k oddílům a aktualizujte je.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Až budete mít k dispozici verzi Preview, pošleme vám e-mail, který vám pomůže zkontrolovat a schválit. Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
