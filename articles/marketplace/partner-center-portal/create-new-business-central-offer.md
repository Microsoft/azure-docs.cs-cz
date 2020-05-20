---
title: Vytvoření nabídky Business Central v Dynamics 365 – Microsoft Commercial Marketplace
description: Přečtěte si postup a požadavky pro vytvoření nové nabídky Business Central v Dynamics 365 na portálu pro komerční tržišti v partnerském centru. Nabídku můžete vypsat nebo prodávat v Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP).
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: f8fb7a3858b8991b2dc27bd07fe6c4b004be28df
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701360"
---
# <a name="create-a-dynamics-365-business-central-offer"></a>Vytvoření nabídky Dynamics 365 Business Central

Tento článek vysvětluje, jak vytvořit novou nabídku podnikového centra Dynamics 365. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) je systém pro plánování podnikových zdrojů (ERP), který zpracovává rozsáhlou škálu obchodních procesů, včetně finančních prostředků, operací, dodavatelských řetězců, CRM a řízení projektů a elektronického obchodování. Balíčky Premium podporují také klasický model nasazení a výrobu. Všechny nabídky pro Dynamics 365 Business Central musí projít procesem certifikace.

Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **Dynamics 365 Business Central**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-dynamics-365-bc.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v prodejní místa po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

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

Seznamte se svým zákazníkům zdarma tím, že zadáte platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), ke které mají přístup k vaší aplikaci.  Například, `https://contoso.com/my-app`.

#### <a name="free-trial-listing"></a>Bezplatná zkušební verze (výpis)

Seznamte se s vaší nabídkou pro zákazníky s odkazem na bezplatnou zkušební verzi tím, že zadáte platnou adresu URL (počínaje *protokolem HTTP* nebo *https*), kde můžou získat zkušební verzi.  Například, `https://contoso.com/trial/my-app`. Nabídka pro výpis bezplatných zkušebních verzí se vytváří, spravuje a konfiguruje pomocí vaší služby a nemá předplatné spravované Microsoftem.

> [!NOTE]
> Tokeny, které vaše aplikace získá prostřednictvím zkušebního připojení, se dají použít jenom k získání informací o uživateli prostřednictvím Azure Active Directory (Azure AD) k automatizaci vytváření účtů ve vaší aplikaci. Pro ověřování pomocí tohoto tokenu se nepodporují účty Microsoft.

#### <a name="contact-me"></a>Kontaktujte mě

Shromažďovat kontaktní údaje zákazníka propojením systému pro správu vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem Marketplace, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci CRM najdete v tématu [připojení správy zájemců](#connect-lead-management). 

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

- [Technické osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Osvědčené postupy pro testování a uvádění na trh](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

## <a name="connect-lead-management"></a>Připojit řízení zájemců

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně tři kategorie, které se použijí k umístění vaší nabídky do příslušných oblastí hledání na webu Marketplace. Nezapomeňte si vyžádat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. 

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

### <a name="name"></a>Name

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole je předem vyplněno textem, který jste zadali pro možnost **alias nabídky** při vytváření nabídky, ale můžete tuto hodnotu změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

### <a name="short-description"></a>Krátký popis

Zadejte krátký popis vaší nabídky, maximálně 100 znaků. Tento popis se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

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

Poskytněte loga a obrázky pro vaši nabídku. Všechny obrázky musí být ve formátu PNG. Nahrajte logo vaší nabídky ve dvou velikostech:

* **Malý** (48 x 48 pixelů)
* **Velký** (216 x 216 pixelů)

>[!NOTE]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje `https://upload.xboxlive.com` službu používanou partnerským centrem.

#### <a name="screenshots"></a>Screenshoty

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Vyžadují se aspoň tři snímky obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

[Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

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

#### <a name="dependency-package-file"></a>Soubor balíčku závislosti

Vyžaduje se, pokud se vaše nabídka musí nainstalovat spolu s jinou příponou, která už je publikovaná na webu Marketplace. Pokud ano, nahrajte `.app` `.zip` soubor nebo sem.

### <a name="url-to-app-installation"></a>Adresa URL pro instalaci aplikace

Pokud jste vybrali **připojit** výše, zadejte adresu pro instalaci aplikace zde. Pro připojené služby, které nevyžadují instalaci, zadejte adresu cílové stránky nebo přihlašovací stránky vaší služby.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive-technical-configuration"></a>Technická konfigurace testovacích jednotek

Tato stránka umožňuje nastavit ukázku ("testovací jednotka"), která zákazníkům umožňuje vyzkoušet si vaši nabídku před nákupem. Další informace najdete v článku [co je Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

K dispozici jsou následující typy testovacích jednotek, z nichž každá má vlastní požadavky na technickou konfiguraci.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikace logiky](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (technická konfigurace není nutná)

Další zdroje testovacích jednotek:

- [Marketingové osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technická konfigurace pro Azure Resource Manager testovacích jednotek

Šablona nasazení obsahující všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure. Přečtěte si další informace o nastavení [Azure Resource Manager testovacích jednotek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Oblasti** (povinné) – v současné době je 26 oblastí podporovaných v Azure, ve kterých se dá vaše testovací jednotka zpřístupnit. Obvykle budete chtít, aby byla testovací jednotka dostupná v oblastech, kde očekáváte největší počet zákazníků, aby mohli vybrat nejbližší oblast pro nejlepší výkon. Budete se muset ujistit, že vaše předplatné bude mít povolené nasazení všech prostředků potřebných v každé z oblastí, které vyberete.

- **Instance** – vyberte typ (horká nebo studená) a počet dostupných instancí, které se vynásobí počtem oblastí, ve kterých je vaše nabídka dostupná.

    **Hot** – tento typ instance je nasazený a očekává se přístup na vybranou oblast. Zákazníci můžou okamžitě přistupovat k *horkým* instancím testovacích jednotek, ale nemusíte čekat na nasazení. Kompromisem je, že tyto instance jsou vždycky spuštěné v předplatném Azure, takže budou mít větší náklady na dobu provozu. Důrazně doporučujeme mít aspoň jednu *aktivní* instanci, protože většina zákazníků nechce čekat na plná nasazení, takže když není k dispozici žádná *Hot* instance, výsledkem je vypnutí v zákaznickém využívání.

    **Studená** – tento typ instance představuje celkový počet instancí, které mohou být nasazeny v jednotlivých oblastech. Studené instance vyžadují pro nasazení celé testovací jednotky Správce prostředků šablonu, když zákazník požádá o testovací verzi, takže *studené* instance budou mnohem pomalejší, než se dosadí *za horké* instance. Kompromisy je, že je nutné platit jenom za dobu trvání testovacích jednotek *, ale v* předplatném Azure není vždycky spuštěná jako s *horkou* instancí.

- **Test drive Azure Resource Manager šablona** – nahrajte soubor. zip obsahující šablonu Azure Resource Manager.  Další informace o vytvoření šablony Azure Resource Manager v článku rychlý Start [vytváření a nasazování Azure Resource Manager šablon pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Doba trvání testovacích jednotek** (požadováno) – zadejte dobu, po kterou testovací jednotka zůstane aktivní, v počtu hodin. Testovací jednotka se po skončení tohoto časového období ukončí automaticky. Používejte pouze celá čísla (například "2" hodiny jsou platné, "1,5" není).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technická konfigurace pro testovací jednotku Dynamics 365

Společnost Microsoft může odebrat složitost nastavení testovacích jednotek hostováním a údržbou zřizování a nasazení služby pomocí tohoto typu testovacích jednotek. Konfigurace pro tento typ hostované testovací jednotky je stejná bez ohledu na to, jestli je testovací jednotka cílena na obchodní oddělení, zapojení zákazníků nebo cílovou skupinu operací.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Každý souběžný uživatel bude spotřebovat licenci Dynamics 365, zatímco je testovací jednotka aktivní, takže budete muset zajistit, aby bylo k dispozici dostatek licencí pro podporu maximálního limitu. Doporučená hodnota 3-5.

- **Doba trvání testovacího disku** (požadováno) – zadejte dobu, po kterou bude testovací jednotka aktivní, definováním počtu hodin. Po uplynutí této doby bude relace končit a už nebude využívat jednu z vašich licencí. V závislosti na složitosti vaší nabídky doporučujeme hodnotu 2-24 hodin. Tato doba může být nastavena pouze na celé číslo hodiny (například "2" hodiny, "1,5" není platný).  Uživatel může požádat o novou relaci, pokud dojde k nedostatku času a chcete znovu získat přístup k testovací jednotce.

- **Adresa URL instance** (požadováno) – adresa URL, na které zákazník zahájí svoji zkušební verzi. Obvykle adresa URL vaší instance Dynamics 365 spouští vaši aplikaci s nainstalovanou ukázkovou daty (například `https://testdrive.crm.dynamics.com` ).

- **Instance URL webového rozhraní API** (požadováno) – NAČTE adresu URL webového rozhraní API pro instanci Dynamics 365 přihlášením k účtu Microsoft 365 a přechodem na **Nastavení** \& gt;. **Vlastní nastavení** \& gt **Materiály** \& pro vývojáře gt **Instance webového rozhraní API (adresa URL kořenového adresáře služby)** zkopírujte zde NALEZENOU adresu URL (například `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Název role** (povinné) – zadejte název role zabezpečení, kterou jste definovali ve vlastní testovací jednotce Dynamics 365, která se uživateli přiřadí během zkušební jednotky (například Test-Drive-role).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technická konfigurace pro testovací jednotku aplikace logiky

Všechny vlastní produkty by měly používat tento typ šablony nasazení testovacích jednotek, který zahrnuje celou řadu složitých architektur řešení. Další informace o nastavení testovacích jednotek aplikace logiky najdete v [provozu](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) na webu GitHub a na službě [Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) .

- **Oblast** (povinný, rozevírací seznam s jedním výběrem) – aktuálně jsou k dispozici 26 oblastí podporovaných v Azure, ve kterých se vaše testovací jednotka dá zpřístupnit. Prostředky vaší aplikace logiky se nasadí do vybrané oblasti. Pokud vaše aplikace logiky obsahuje nějaké vlastní prostředky uložené v konkrétní oblasti, ujistěte se, že je zde vybraná oblast. Nejlepším způsobem je plně nasadit vaši aplikaci logiky lokálně na vaše předplatné Azure na portálu a před provedením tohoto výběru ověřit, jestli funguje správně.

- **Maximální počet souběžných testovacích jednotek** (požadováno) – nastaví maximální počet zákazníků, kteří můžou použít testovací jednotku najednou. Tyto testovací jednotky jsou už nasazené a umožňují zákazníkům okamžitý přístup k nim bez čekání na nasazení.

- **Doba trvání testovacího disku** (požadováno) – zadejte dobu, po kterou bude testovací jednotka aktivní, v počtu hodin. Testovací jednotka se po skončení tohoto časového období ukončí automaticky.

- **Název skupiny prostředků Azure** (požadováno) zadejte název [skupiny prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) , kde se vaše testovací jednotka aplikace logiky uloží.

- **Název aplikace logiky Azure** (povinné) – zadejte název aplikace logiky, která uživateli přiřadí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

- **Zrušit zřízení názvu aplikace logiky** (povinné) – zadejte název aplikace logiky, který po dokončení zákazníka odzřídí testovací jednotku. Tato aplikace logiky se musí uložit do skupiny prostředků Azure výše.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Pro Power BI testovacích jednotek není nutná technická konfigurace.

Produkty, které chtějí předvést interaktivní Power BI vizuál, můžou pomocí vloženého odkazu sdílet vlastní řídicí panel jako svoji testovací jednotku, nevyžaduje se žádná další technická konfigurace. Přečtěte si další informace o nastavení [Power BIch](https://docs.microsoft.com/power-bi/service-template-apps-overview) šablonových aplikací.

### <a name="deployment-subscription-details"></a>Podrobnosti předplatného nasazení

Chcete-li nasadit testovací jednotku vaším jménem, vytvořte a poskytněte samostatné a jedinečné předplatné Azure. (Nevyžaduje se pro Power BI testovací jednotky).

- **ID předplatného Azure** (vyžadované pro Azure Resource Manager a Logic Apps) – zadejte ID předplatného, které vám umožní udělit přístup ke službám účtů Azure za účelem vytváření sestav a fakturace využívání prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , které se použije pro testovací jednotky, pokud ho ještě nemáte. ID vašeho předplatného Azure můžete najít tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/) a přejdete na kartu **předplatná** v nabídce na levé straně. Výběrem karty se zobrazí vaše ID předplatného (například "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID tenanta Azure AD** (povinné) – zadejte [ID TENANTA](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu služby Active Directory, vyberte možnost * * vlastnosti a vyhledejte číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta vaší organizace můžete také vyhledat pomocí adresy vašeho názvu domény na adrese [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Název tenanta Azure AD** (vyžadováno pro dynamickou 365) – zadejte název Azure Active Directory (AD). Pokud chcete najít tento název, přihlaste se k [Azure Portal](https://portal.azure.com/)v pravém horním rohu bude název vašeho tenanta uvedený pod názvem vašeho účtu.

- **ID aplikace Azure AD** (požadováno) – zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **Registrace aplikací**a vyhledejte číslo **ID aplikace** uvedené v seznamu (například 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný kód klienta aplikace Azure AD** (povinné) – zadejte [tajný kód klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD. Tuto hodnotu najdete tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/). V nabídce vlevo vyberte kartu **Azure Active Directory** , vyberte **Registrace aplikací**a pak vyberte svoji aplikaci Test Drive. V dalším kroku vyberte **certifikáty a tajné klíče**, vyberte **nový tajný klíč klienta**, zadejte popis, v části **platnost**vyberte **nikdy** a pak zvolte **Přidat**. Nezapomeňte hodnotu zkopírovat. (Před tím, než to uděláte, opustíte stránku, nebo jinak nemáte přístup k této hodnotě.)

### <a name="test-drive-marketplace-listings"></a>Seznam na webu Marketplace pro testovací mechaniky

Možnost **výpisu na webu Marketplace** na kartě **testovací jednotka** je místo, kde můžete definovat podrobnosti pro prostředí testovacích jednotek.

> [!NOTE]
> Podrobnosti o seznamu testovacích jednotek můžete poskytnout pouze v jednom jazyku. Není nutné, aby byla v angličtině, pokud popis nabídky začíná frází "Tato aplikace je dostupná pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné, aby poskytovala *adresu URL pro odkaz na nápovědu* k obsahu v jiném jazyce, než jaký používala v obsahu vydaných testovacích jednotek.

- **Popis** (povinné) – Popište si testovou jednotku, co se bude objevovat, co je potřeba pro uživatele experimentovat, funkce, které se mají prozkoumat, a všechny relevantní informace, které uživatelům pomůžou určit, jestli se má vaše nabídka získat. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Přístup k informacím** (vyžadovaným pro Azure Resource Manager a Logic test Disks) – Vysvětlete, co zákazník potřebuje pro přístup k této testovací jednotce a k používání této jednotky znát. Projděte si scénář pro použití vaší nabídky a přesně toho, co by měl zákazník znát pro přístup k funkcím v rámci testovacích jednotek. Do tohoto pole lze zadat až 10 000 znaků textu.

- **User Manual** (požadováno) – podrobný návod k vašemu prostředí Test Drive. Ruční příručka uživatele by měla pokrývat přesně to, co chcete zákazníkovi získat ze zkušební jednotky a sloužit jako odkaz na případné dotazy, které mohou mít. Soubor musí být ve formátu PDF a po nahrání má název (maximálně 255 znaků).

- **Videa** (volitelné) – videa se dají nahrát na YouTube nebo Vimeo a tady se odkazuje s odkazem a obrázkem miniatury (533 x 324 pixelů), aby si zákazník mohl zobrazit návod k informacím, který jim pomůže lépe pochopit testovací verzi, včetně toho, jak úspěšně používat funkce vaší nabídky a pochopit scénáře, které zvýrazní jejich výhody.
  - **Název** (povinné)
  - **Adresa URL (jenom YouTube nebo Vimeo)** (povinné)
  - Obrázek **miniatury** (soubor musí být ve formátu PNG a 533 x 324 px)

Než budete pokračovat, vyberte **Uložit koncept** .

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

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** . 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - *Nespuštěno* – znamená, že se oddíl nedotkl a je potřeba ho dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se k oddílům a aktualizujte je.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- V části **poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, aby se zajistilo správné testování vaší aplikace, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Až budete mít k dispozici verzi Preview, pošleme vám e-mail, který vám pomůže zkontrolovat a schválit. Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném (nebo v případě soukromé nabídky pro soukromou cílovou skupinu).

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
