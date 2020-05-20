---
title: Vytvoření nabídky Dynamics 365 for Customer Engagement & PowerApps na komerčním webu Microsoft Marketplace
description: Postup vytvoření nové služby Dynamics 365 pro zákaznickou zapojení & nabídky PowerApps pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu Cloud Solution Provider (CSP) v partnerském centru.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7de57c9a69e60e8c342e067ff1e95a0f2cfadec8
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699318"
---
# <a name="create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Vytvoření nabídky Dynamics 365 for Customer Engagement a PowerApps

Toto téma vysvětluje, jak vytvořit nový Dynamics 365 pro zákaznickou zapojení & nabídky PowerApps. Všechny aplikace pro Dynamics 365 pro zákaznickou zapojení (PowerApps, Sales, Service, Project Service a Field Service) musí projít naším procesem certifikace a podporovat zkušební prostředí. Proces certifikace zkontroluje vaše řešení na standardní požadavky, kompatibilitu a správné postupy. Zkušební prostředí umožňuje uživatelům nasadit vaše řešení do živého prostředí Dynamics 365.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 pro zákaznickou zapojení & PowerApps**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-dynamics-365-cepa.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v prodejní místa po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po výběru **vytvořit**se tato možnost nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

Pomocí těchto kroků můžete nastavit nabídku.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Jak chcete potenciálním zákazníkům pracovat s touto nabídkou pro výpis?

Vyberte možnost, kterou chcete použít pro tuto nabídku.

#### <a name="get-it-now-free"></a>Získat hned (zdarma)

Seznamte se svým zákazníkům zdarma tím, že zadáte platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), ke které mají přístup k vaší aplikaci.  Například, `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Bezplatná zkušební verze (výpis)

Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi tím, že zadáte platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), kde můžou získat zkušební verzi.  Například, `https://contoso.com/trial/my-app`. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

> [!NOTE]
> Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli prostřednictvím Azure Active Directory (Azure AD) k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft.

#### <a name="contact-me"></a>Kontaktujte mě

Shromažďovat kontaktní údaje zákazníka propojením systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [připojení správy zájemců](#connect-lead-management). 

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="test-drive"></a>Testovací jednotka

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si předplatné před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jednotkách](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka. Nakonfigurujte prostředí testovacích jednotek v části [Technická konfigurace testovacích jednotek](#test-drive-technical-configuration) dále v tomto tématu.

Další informace najdete v tématu [Test Drive vaší nabídky na komerčním webu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

#### <a name="type-of-test-drive"></a>Typ testovací jednotky

Vyberte z následujících možností:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** – šablona nasazení obsahující všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure.
- **[Dynamics 365 pro firmu Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** – Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro systém plánování podnikových prostředků podnikového systému (finance, operace, dodavatelský řetězec, CRM atd.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** – Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro systém zákaznických rezervací (prodej, servis, Project Service, Field Service atd.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** – Microsoft hostuje a udržuje službu testovacích jednotek (včetně zřizování a nasazení) pro finanční a provozní systém plánování podnikových prostředků (finance, operace, výroba, dodavatelský řetězec atd.). 
- **[Aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** – šablona nasazení zahrnující všechny komplexní architektury řešení Všechny vlastní produkty by měly používat tento typ testovacích jednotek.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** – vložený odkaz na uživatelsky sestavený řídicí panel. Produkty, které chtějí předvést interaktivní Power BI vizuál, by měly používat tento typ testovacích jednotek. Vše, co je třeba nahrát, je vaše vložená Power BI adresa URL.

#### <a name="additional-test-drive-resources"></a>Další zdroje testovacích jednotek

- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Doporučené postupy pro marketing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

## <a name="connect-lead-management"></a>Připojit řízení zájemců

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="category"></a>Kategorie

Vyberte alespoň jednu a až tři kategorie. Ty se používají k umístění vaší nabídky do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky.

### <a name="industry"></a>Obor

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="applicable-dynamics-365-products"></a>Příslušné produkty Dynamics 365

Vyberte všechny produkty Dynamics 365, pro které tato nabídka platí.

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze vaší nabídky. Zákazníci uvidí tuto verzi uvedenou na stránce s podrobnostmi nabídky. Pokud aktualizujete číslo verze z důvodu marketingu nebo popisných změn, zaškrtněte políčko **jenom marketing** . Tato možnost umožňuje obejít fáze certifikace a zřizování.

### <a name="terms-and-conditions"></a>podmínky a ujednání

Sem zadejte vlastní právní podmínky a ujednání. Můžete také zadat adresu, na které se můžou vaše podmínky a ujednání najít. Zákazníci budou muset tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tato stránka zobrazuje jazyky, ve kterých bude vaše nabídka uvedená. V současné době je jako jediná dostupná možnost **Angličtina (USA)** .

Tady můžete zadat podrobnosti o Marketplace pro každý jazyk/trh, jako je například název nabídky, popis a obrázky. Pro poskytnutí těchto informací vyberte název jazyka/trhu.

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky a podmínky použití) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="name"></a>Name

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole se předem vyplní textem, který jste zadali jako **alias nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

### <a name="short-description"></a>Krátký popis

Zadejte krátký popis vaší nabídky, maximálně 100 znaků. Tento popis se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>Hledaná klíčová slova

Volitelně můžete zadat až tři klíčová slova pro hledání, která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků také použijte Tato klíčová slova v popisu.

### <a name="products-your-app-works-with"></a>Produkty, se kterými pracuje vaše aplikace

Pokud chcete zákazníkům sdělit, že vaše aplikace pracuje s konkrétními produkty, zadejte do tohoto pole až tři názvy produktů.

### <a name="support-urls"></a>Adresy URL podpory

V této části můžete zadat odkazy, které zákazníkům pomohou pochopit Další informace o vaší nabídce.

#### <a name="help-link"></a>Odkaz na Help

Zadejte adresu, kde se zákazníci mohou dozvědět více o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte adresu do zásad ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="contacts"></a>Kontakty

Zadejte jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** je také nutné zadat **adresu URL podpory** , kde mohou partneři CSP najít podporu pro vaši nabídku.

### <a name="supporting-documents"></a>Podpůrné dokumenty

Poskytněte aspoň jeden (a až tři) související marketingové dokumenty, jako jsou dokumenty White Paper, brožury, kontrolní seznamy nebo prezentace ve formátu PDF.

### <a name="marketplace-images"></a>Image Marketplace

Poskytněte loga a obrázky pro vaši nabídku. Všechny obrázky musí být ve formátu PNG.

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Poskytněte logo vaší nabídky ve třech velikostech v pixelech:
- **Malý** (povinný; 48 × 48)
- **Velký** (povinný; 216 × 216)
- **Roztažitelné** (volitelné; 255 × 115)

#### <a name="hero"></a>Hero

Obrázek Hero je nepovinný. Pokud zadáte jednu, musí změřit 815 x 290 pixelů.

#### <a name="screenshots"></a>Screenshoty

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Vyžaduje se aspoň jeden snímek obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až čtyři videa, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

[Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="availability"></a>Dostupnost

Tato stránka umožňuje definovat, kde a jak má být nabídka dostupná.

### <a name="markets"></a>Trhy

Zadejte trhy, ve kterých by měla být vaše nabídka dostupná. Provedete to tak, že vyberete **Upravit trhy**, ve kterém se zobrazí místní okno pro **Výběr trhu** .

Ve výchozím nastavení nejsou vybrané žádné trhy, ale pro publikování vaší nabídky musíte vybrat aspoň jeden trh. Zvolte **možnost Vybrat vše** , pokud chcete nabídku zpřístupnit na všech možných trzích, nebo vyberte konkrétní trhy, které chcete přidat.

Vaše výběry se tady vztahují jenom na nové akvizice; Pokud už má vaše aplikace na určitém trhu nějaký uživatel a později tento trh odeberete, lidé, kteří si tuto nabídku už na tomto trhu mají, můžou i nadále používat, ale žádní noví zákazníci na tomto trhu nebudou moct nabídku získat.

> [!IMPORTANT]
> Je vaše zodpovědnost za splnění jakýchkoli místních právních požadavků, a to i v případě, že tyto požadavky nejsou uvedené ani v partnerském centru. I když vyberete možnost všechny trhy, místní zákony a omezení nebo jiné faktory můžou zabránit tomu, aby některé nabídky byly uvedené v některých zemích a oblastech.

### <a name="preview-audience"></a>Cílová skupina Preview

Před publikováním vaší nabídky v širší nabídce na webu Marketplace je nejprve nutné ji zpřístupnit pro skupinu s omezeným počtem verzí **Preview**. Zadejte **Skrýt klíč** (libovolný řetězec s malými písmeny a číslicemi). Členové vaší cílové skupiny ve verzi Preview můžou použít tento skrytý klíč jako token k zobrazení náhledu vaší nabídky na webu Marketplace.

Až budete připraveni zpřístupnit nabídku a odebrat omezení verze Preview, je nutné odebrat **Skrýt klíč** a znovu publikovat.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration"></a>Technická konfigurace

Tato stránka definuje technické podrobnosti používané pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se k tomu rozhodne získat.

### <a name="base-license-model"></a>Základní licenční model

Základní licenční model určuje, jak se zákazníkům přiřadí vaše aplikace v centru pro správu CRM. Vyberte **prostředek** pro licencování na základě instance nebo **uživatele** , pokud jsou licence přiřazeny k jednomu klientovi.

### <a name="requires-s2s-outbound-and-crm-secure-store-access"></a>Vyžaduje přístup k odchozímu přístupu S2S a přístup k zabezpečenému úložišti CRM

Toto políčko zaškrtněte, pokud chcete povolit konfiguraci odchozího přístupu typu Server-Server (S2S) pro CRM. Tato funkce vyžaduje zvláštní pozornost od týmu Dynamics 365 během fáze certifikace. Společnost Microsoft vás bude kontaktovat, abyste mohli provést další kroky pro podporu této funkce.

### <a name="application-configuration-url"></a>Adresa URL konfigurace aplikace

Zadejte adresu URL webové stránky konfigurace, která umožňuje zákazníkovi nakonfigurovat vaši aplikaci.

### <a name="crm-package"></a>Balíček CRM

Do pole **Adresa URL umístění balíčku** zadejte adresu URL Azure Storage účtu, který obsahuje nahraný soubor. zip balíčku CRM. Tato adresa URL by měla obsahovat klíč SAS s oprávněními jen pro čtení, který umožňuje Microsoftu vyzvednutí balíčku pro ověření.

Zaškrtněte políčko s názvem **v souboru balíčku je více než jeden balíček CRM, pokud je k**dispozici. Pokud ano, nezapomeňte zahrnout všechny balíčky v souboru. zip.

### <a name="crm-package-availability"></a>Dostupnost balíčku CRM

V této části vyberte **+ Přidat oblast** a určete geografické oblasti, ve kterých bude mít váš balíček CRM k dispozici pro zákazníky. Nasazení do následujících oblastí svrchovaného přístupu vyžaduje speciální oprávnění a ověření během certifikace: [Německo](https://docs.microsoft.com/azure/germany/), [Cloud pro státní správu USA](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)a Tip.

Ve výchozím nastavení se **Adresa URL konfigurace aplikace** , kterou jste zadali výše, použije pro každou oblast. Pokud dáváte přednost, můžete zadat samostatnou adresu URL konfigurace aplikace pro jednu nebo více konkrétních oblastí. 

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive-technical-configuration"></a>Technická konfigurace testovacích jednotek

Tato stránka umožňuje nastavit ukázku ("testovací jednotka"), která zákazníkům umožňuje vyzkoušet si vaši nabídku před nákupem. Další informace najdete v článku [co je Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Chcete-li povolit testovací jednotku, zaškrtněte políčko Povolit testovací jednotku na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

Další informace najdete v článku [co je Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

K dispozici jsou následující typy testovacích jednotek, z nichž každá má vlastní požadavky na technickou konfiguraci:

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikace logiky](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (technická konfigurace není nutná)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technická konfigurace pro Azure Resource Manager testovacích jednotek

Šablona nasazení obsahující všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure. Přečtěte si další informace o nastavení [Azure Resource Manager testovacích jednotek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Oblasti** (povinné) – v současné době je 26 oblastí podporovaných v Azure, ve kterých se dá vaše testovací jednotka zpřístupnit. Obvykle budete chtít, aby byla testovací jednotka dostupná v oblastech, kde očekáváte největší počet zákazníků, aby mohli vybrat nejbližší oblast pro nejlepší výkon. Budete se muset ujistit, že vaše předplatné bude mít povolené nasazení všech prostředků potřebných v každé z oblastí, které vyberete.
- **Instance** – vyberte typ (horká nebo studená) a počet dostupných instancí, které se vynásobí počtem oblastí, ve kterých je vaše nabídka dostupná.

    **Hot** – tento typ instance je nasazený a očekává se přístup na vybranou oblast. Zákazníci můžou okamžitě přistupovat k *horkým* instancím testovacích jednotek, ale nemusíte čekat na nasazení. Kompromisem je, že tyto instance jsou vždycky spuštěné v předplatném Azure, takže budou mít větší náklady na dobu provozu. Důrazně doporučujeme mít aspoň jednu *aktivní* instanci, protože většina zákazníků nechce čekat na plná nasazení, takže když není k dispozici žádná *Hot* instance, výsledkem je vypnutí v zákaznickém využívání.

    **Studená** – tento typ instance představuje celkový počet instancí, které mohou být nasazeny v jednotlivých oblastech. Studené instance vyžadují pro nasazení celé testovací jednotky Správce prostředků šablonu, když zákazník požádá o testovací verzi, takže *studené* instance budou mnohem pomalejší, než se dosadí *za horké* instance. Kompromisy je, že je nutné platit jenom za dobu trvání testovacích jednotek *, ale v* předplatném Azure není vždycky spuštěná jako s *horkou* instancí.

- **Test drive Azure Resource Manager šablona** – nahrajte soubor. zip obsahující šablonu Azure Resource Manager. Další informace o vytvoření šablony Azure Resource Manager v článku rychlý Start [vytváření a nasazování Azure Resource Manager šablon pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Doba trvání testovacích jednotek** (požadováno) – zadejte počet hodin, po který bude testovací jednotka aktivní. TestdDrive se po skončení tohoto časového období automaticky ukončí. Tato doba může být nastavena pouze na celé hodiny (například "2" hodiny jsou platné; "1,5" není).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technická konfigurace pro testovací jednotku Dynamics 365

Společnost Microsoft může odebrat složitost nastavení testovacích jednotek hostováním a údržbou zřizování a nasazení služby pomocí tohoto typu testovacích jednotek. Konfigurace pro tento typ hostované testovací jednotky je stejná bez ohledu na to, jestli je testovací jednotka cílena na obchodní oddělení, zapojení zákazníků nebo cílovou skupinu operací.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Každý souběžný uživatel bude používat licenci Dynamics 365, zatímco je testovací jednotka aktivní, takže budete muset zajistit, aby bylo k dispozici dostatek licencí pro podporu maximální sady omezení. Doporučená hodnota je 3-5.

- **Doba trvání testovacích jednotek** (požadováno) – zadejte počet hodin, po který bude testovací jednotka aktivní. Po uplynutí této doby bude relace končit a už nebude spotřebovávat jednu z vašich licencí. V závislosti na složitosti vaší nabídky doporučujeme hodnotu 2-24 hodin. Tato doba může být nastavena pouze na celé hodiny (například "2" hodiny jsou platné; "1,5" není). Uživatel může požádat o novou relaci, pokud dojde k nedostatku času a chcete znovu získat přístup k testovací jednotce.

- **Adresa URL instance** (požadováno) – adresa URL, na které zákazník zahájí svoji zkušební verzi. Obvykle adresa URL vaší instance Dynamics 365 spouští vaši aplikaci s nainstalovanou ukázkovou daty (například `https://testdrive.crm.dynamics.com` ).

- **Instance URL webového rozhraní API** (požadováno) – NAČTE adresu URL webového rozhraní API pro instanci Dynamics 365 přihlášením k účtu Microsoft 365 a přechodem na **Nastavení** \& gt;. **Vlastní nastavení** \& gt **Materiály** \& pro vývojáře gt **Instance webového rozhraní API (adresa URL kořenového adresáře služby)** zkopírujte zde NALEZENOU adresu URL (například `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Název role** (povinné) – zadejte název role zabezpečení, kterou jste definovali ve vlastní testovací jednotce Dynamics 365. Tento název role zabezpečení se uživateli přiřadí během zkušební jednotky (například Test-Drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technická konfigurace pro testovací jednotku aplikace logiky

Všechny vlastní produkty by měly používat tento typ šablony nasazení Test Drive, která zahrnuje celou řadu složitých architektur řešení. Další informace o nastavení testovacích jednotek aplikace logiky najdete v [provozu](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) na webu GitHub a na službě [Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Oblast** (povinný, rozevírací seznam s jedním výběrem) – aktuálně jsou k dispozici 26 oblastí podporovaných v Azure, ve kterých se vaše testovací jednotka dá zpřístupnit. Prostředky vaší aplikace logiky se nasadí do vybrané oblasti. Pokud vaše aplikace logiky obsahuje nějaké vlastní prostředky uložené v konkrétní oblasti, ujistěte se, že je zde vybraná oblast. Nejlepším způsobem, jak to udělat, je plně nasadit vaši aplikaci logiky lokálně na svém předplatném Azure na portálu a před provedením tohoto výběru ověřit, jestli funguje správně.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Tyto testovací jednotky jsou už nasazené a umožňují zákazníkům okamžitý přístup k nim bez čekání na nasazení.

- **Doba trvání testovacího disku** (požadováno) – zadejte dobu, po kterou bude testovací jednotka aktivní, v počtu hodin. Testovací jednotka se po skončení tohoto časového období ukončí automaticky.

- **Název skupiny prostředků Azure** (požadováno) – zadejte název [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) , kde se vaše testovací jednotka aplikace logiky uloží.

- **Název aplikace logiky Azure** (povinné) – zadejte název aplikace logiky, která uživateli přiřadí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

- **Zrušit zřízení názvu aplikace logiky** (povinné) – zadejte název aplikace logiky, který po dokončení zákazníka odzřídí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Pro Power BI testovacích jednotek není nutná technická konfigurace.

Produkty, které chtějí předvést interaktivní Power BI vizuál, můžou pomocí vloženého odkazu sdílet vlastní řídicí panel jako svoji testovací jednotku, nevyžaduje se žádná další technická konfigurace. Přečtěte si další informace o nastavení[Power BIch](https://docs.microsoft.com/power-bi/service-template-apps-overview) šablonových aplikací.

### <a name="deployment-subscription-details"></a>Podrobnosti předplatného nasazení

Abyste Microsoftu umožnili nasazení testovacích jednotek vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure (nevyžaduje se Power BI testovacích jednotek).

- **ID předplatného Azure** (vyžadované pro Azure Resource Manager a Logic Apps) – zadejte ID předplatného, které vám umožní udělit přístup ke službám účtů Azure za účelem vytváření sestav a fakturace využívání prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , které se použije pro testovací jednotky, pokud ho ještě nemáte. ID vašeho předplatného Azure můžete najít tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/) a přejdete na kartu **předplatná** v nabídce na levé straně. Výběrem karty se zobrazí vaše ID předplatného (například "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenanta Azure AD** (povinné) – zadejte [ID TENANTA](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **vlastnosti**a vyhledejte číslo **ID adresáře** v seznamu (například 50c464d3-4930-494c-963c-1e951d15360e). Můžete také vyhledat ID tenanta vaší organizace pomocí adresy URL vašeho názvu domény v umístění – [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Název tenanta Azure AD** (vyžadováno pro dynamickou 365) – zadejte název Azure Active Directory (AD). Pokud chcete najít tento název, přihlaste se k [Azure Portal](https://portal.azure.com/)v pravém horním rohu bude název vašeho tenanta uvedený pod názvem vašeho účtu.

- **ID aplikace Azure AD** (požadováno) – zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **Registrace aplikací**a vyhledejte číslo **ID aplikace** uvedené v seznamu (například 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný kód klienta aplikace Azure AD** (povinné) – zadejte [tajný kód klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD. Tuto hodnotu najdete tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/). V nabídce vlevo vyberte kartu **Azure Active Directory** , vyberte **Registrace aplikací**a pak vyberte svoji aplikaci Test Drive. V dalším kroku vyberte **certifikáty a tajné klíče**, vyberte **nový tajný klíč klienta**, zadejte popis, v části **platnost**vyberte **nikdy** a pak zvolte **Přidat**. Nezapomeňte hodnotu zkopírovat. Před zkopírováním hodnoty nedělejte opustit stránku, nebo k ní nemáte přístup.)

### <a name="test-drive-marketplace-listings"></a>Seznam na webu Marketplace pro testovací mechaniky

Možnost **výpisu na webu Marketplace** , která se nachází na kartě **testovací jednotka** , zobrazuje jazyky, ve kterých je dostupná vaše testovací jednotka. V současné době je k dispozici pouze **Angličtina (USA)** . Vyberte název jazyka a zadejte informace, které popisují prostředí testovacích jednotek.

- **Popis** (povinné) – Popište si testovou jednotku, co se bude objevovat, co je potřeba pro uživatele experimentovat, funkce, které se mají prozkoumat, a všechny relevantní informace, které uživatelům pomůžou určit, jestli se má vaše nabídka získat. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Přístup k informacím** (vyžadovaným pro Azure Resource Manager a Logic test Disks) – Vysvětlete, co zákazník potřebuje pro přístup k této testovací jednotce a k používání této jednotky znát. Projděte si scénář pro použití vaší nabídky a přesně toho, co by měl zákazník znát pro přístup k funkcím v rámci testovacích jednotek. Do tohoto pole lze zadat až 10 000 znaků textu.

- **User Manual** (požadováno) – podrobný návod k vašemu prostředí Test Drive. Ruční příručka uživatele by měla pokrývat přesně to, co chcete zákazníkovi získat ze zkušební jednotky a sloužit jako odkaz na případné dotazy, které mohou mít. Soubor musí být ve formátu PDF a po nahrání má název (maximálně 255 znaků).

- **Videa** (volitelné) – Nahrajte videa na externí hostitelský web odkaz a na obrázek miniatury (533 x 324 pixelů). Díky tomu mohou zákazníci zobrazit návod k informacím, které jim pomohou lépe pochopit testovací verzi, včetně toho, jak úspěšně používat funkce vaší nabídky a pochopit scénáře, které mají na jejich výhody přednost.
  - **Název** (povinné)
  - **Adresa** (povinné; Pouze YouTube nebo Vimeo)
  - Obrázek **miniatury** (soubor obrázku musí být ve formátu PNG a 533 x 324 pixelů)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="supplemental-content"></a>Doplňkový obsah

Tato stránka vám umožní zadat další informace o vaší nabídce, které nám pomůžou vaši nabídku ověřit. Tyto informace se zákazníkům nezobrazují ani nepublikují na webu Marketplace.

### <a name="key-usage-scenario"></a>Scénář použití klíče

Nahrajte soubor PDF, ve kterém jsou uvedené klíčové scénáře použití vaší nabídky. Všechny scénáře mohou být ověřeny týmem ověřování, než schválíme vaši nabídku na webu Marketplace.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **zkontrolovat a publikovat** v pravém horním rohu portálu.

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - **Nezahájeno** – oddíl se nedotkl a měl by být dokončený.
    - **Nedokončeno** – oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se k oddílům a aktualizujte je.
    - **Complete** – oddíl je kompletní, jsou k dispozici všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit. Vraťte se do partnerského centra a vyberte možnost **Přejít na aktivní** , aby se nabídka publikovala ve veřejném (nebo v případě soukromé nabídky soukromé cílové skupině).

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
