---
title: Průvodce publikováním technické aplikace SaaS | Dokumentace Microsoftu
description: Vysvětluje, jak publikování nabídky SaaS aplikace na webu marketplace vhodná.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 7430e57ceaec8a280c17f1276d503919a9703182
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51228891"
---
<a name="saas-application-technical-publishing-guide"></a>SaaS aplikace technický průvodce publikováním
===========================================

Vítá vás Průvodce publikováním technická aplikací SaaS. Tato příručka je určená k Release candidate a existujících vydavatelů seznamu svých aplikací a služeb v AppSource nebo Azure Marketplace pomocí nabídky SaaS aplikací.

Přehled všechny ostatní nabídky Marketplace najdete [Příručka pro vydavatele na webu Marketplace](https://aka.ms/sellerguide).


<a name="what-are-pre-requisites-for-publishing-a-saas-app"></a>Jaké jsou požadavky pro publikování aplikace SaaS?
-------------------------------------------------

Portál pro publikování poskytuje přístup na základě rolí na portálu pro povolení více osob ke spolupráci na publikování nabídky. Další informace najdete v tématu [spravovat uživatele](./cloud-partner-portal-manage-users.md). 

Před publikováním nabídky jménem vydavatel účtu, jeden z uživatelé, kteří mají *vlastníka* role je potřeba souhlasit s [Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft PrivacyStatement](https://www.microsoft.com/privacystatement/default.aspx), a [Microsoft Azure Certified ProgramAgreement](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


<a name="creating-an-offer"></a>Vytvoření nabídky
-----------------

Tato část popisuje postup vytvoření nové nabídky SaaS aplikace.

![Přehled nabídky SaaS](media/cpp-creating-saas-offers/saas-offer-overview.png)

Nabídky SaaS aplikace se skládá z pěti oddílů, jsou popsané v následující tabulce:

| **Části nabídky**  | **Popis**                                                                                                     |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------|
| Nabídka nastavení     | Umožňuje definovat jedinečný název a ID pro aplikace SaaS                                                                         |
| Technické informace     | Při konfiguraci typu řešení SaaS a poskytují podrobné informace o připojení pro vaši aplikaci                            |
| Testovací verze         | Volitelný oddíl, který umožňuje definovat službu, která vám umožní zákazníkům otestujte předtím, než ji zakoupit. |
| Prezentace podrobnosti | Obsahuje marketing, právní, Správa potenciálních zákazníků a seznam oddílů:   <br/> -Části Marketing umožňuje zadat popis a loga, vyžaduje se pro nabídky správně zobrazit v portálu user portal webu marketplace.  <br/> – Správa potenciálních zákazníků vám umožní definovat místo where přesměrovat zájemců koncový uživatel generovaných v Azure Marketplace End user portal.  <br/> – Právní část umožňuje zadat zásady ochrany osobních údajů a použití termín právní dokumentace.  |
| Kontakt            | Umožňuje zadat kontaktní informace podpory nabídky.                                                                  |
|  |  |


### <a name="creating-a-new-offer"></a>Vytváří se nová nabídka

Po přihlášení na portál Cloud Partner, vyberte **nová nabídka** položka na levém řádku nabídek, které se zobrazí nabídka dostupných nabídek. Následující obrázek ukazuje příklad tyto nabídky:

![Nová nabídka SaaS](media/cpp-creating-saas-offers/saas-new-offer.png)

Vyberte nabídky, která vám byla schválena pro ze seznamu a otevře se nová nabídka formuláře.

![Formulář nové nabídky SaaS](media/cpp-creating-saas-offers/saas-new-offer-2.png)

Následující tabulka popisuje pole nabídky:

| **Nabídka pole** | **Popis**                                                                                            |
|------------------|----------------------------------------------------------------------------------------------------------- |
| ID nabídky         | Jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID se nebude zobrazovat v adresách URL produktu a účetních sestav. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. Všimněte si, že toto pole je uzamčen, jakmile uvedete nabídky. Například pokud vydavatele, Contoso, publikuje nabídky s ID nabídky ukázka vm, se zobrazí v Azure marketplace jako: [https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sample-vm?tab=Overview](https://azuremarketplace.microsoft.com/) |
| ID vydavatele     | ID vydavatele je svůj jedinečný identifikátor na webu Marketplace. Všechno, co by měl být vašich nabídek ze strany připojit své ID vydavatele. ID vydavatele nelze změnit po uložení nabídky.                                                                                       |
| Název             | Toto je zobrazovaný název nabídky. Toto je název, který se zobrazí na webu Azure Marketplace a na webu Azure Portal. Může mít maximálně 50 znaků. Pokyny v tomto poli se zahrnou rozpoznat název značky pro tento produkt. Nezahrnujte název společnosti, pokud to je, jak je na trh. Pokud v rámci této nabídky jsou marketing na svůj vlastní web, ujistěte se, že název je přesně jak zobrazí na vašem webu.             |
|  |  |

Klikněte na tlačítko **Uložit** uložte svůj postup. Další část popisuje přidání plány pro vaši nabídku.


### <a name="technical-information"></a>Technické informace

Část technické informace umožňuje zadejte následující informace:

![Nabídky SaaS technické informace](media/cpp-creating-saas-offers/saas-new-offer-technical-info.png)

Nejdůležitější rozhodnutí je určuje, zda chcete poskytnout seznam SaaS nebo mít commerce povolena. Pokud se najdou SaaS, musíte vybrat mezi:

-   Free – zadejte adresu URL aplikace SaaS, kde Zákazníci mohou získat vaše aplikace.
-   Bezplatná zkušební verze – zadejte adresu URL aplikace SaaS, kde Zákazníci můžou spouštět zkušební verze, než může zakoupit nabídku.
-   Kontaktujte mě – pouze relevantní, pokud máte připojený systém správy potenciálních zákazníků, tato možnost umožňuje zákazníkům požádat, aby kontaktovat a je s vámi sdílí nový zájemce.

Pokud jste aplikaci SaaS, která je na webu Azure Marketplace a chce povolit commerce prostřednictvím Microsoft transakce, vyberte **prodávat prostřednictvím Azure**.  
Další informace o připojování aplikace SaaS, naleznete v tématu [SaaS - prodávat prostřednictvím Azure](./cloud-partner-portal-saas-offer-subscriptions.md).


### <a name="test-drive"></a>Testovací verze

Vytvoření zkušebního prostředí pro vaše zákazníky je osvědčeným postupem je zajistit, že můžete koupit bez obav. Možnosti zkušebních verzí dostupné Test Drive je nejúčinnější pro vysoce kvalitní generování potenciálních zákazníků a větší převodu těchto potenciálních zákazníků.

Zákazníkům přináší praktická, samostatně prováděného zkušební verzi klíčové funkce a výhody, které jsme vám ukázali ve scénáři Skutečná implementace váš produkt.

![Nabídky SaaS testovací verze](media/cpp-creating-saas-offers/saas-new-offer-test-drive.png)


#### <a name="how-a-test-drive-works"></a>Jak funguje testovací verze

Potenciální zákazník prohledá a zjistí aplikace na webu Marketplace. Zákazník přihlásí a souhlasí s podmínkami použití. V tomto okamžiku zákazník přijme předem nakonfigurované prostředí pro akci pro pevný počet hodin, když obdržíte vysoce kvalifikovaných potenciálního zákazníka do se.

![Nabídky SaaS test Drive 2](media/cpp-creating-saas-offers/saas-new-offer-test-drive-2.png)

Bez ohledu na to, jak komplexní je vaše aplikace jednotky testů společnosti Microsoft vám pomůže přineste svůj produkt do života pro zákazníka. Nejsou k dispozici tři různé typy testů jednotek, každý podle typu produktu, scénáře a marketplace, které jsou na.

-   **Azure Resource Manageru** -Azure ARM Test Drive je šablonu nasazení, která obsahuje všechny prostředky Azure, které tvoří vytváří Vydavatel řešení. Produkty, které patří tento typ testovací verze jsou ty, které používají jenom prostředky Azure.
-   **Aplikace logiky** – Test aplikace logiky A jednotka je šablonu nasazení, která slouží k zahrnovat všechny architektury komplexní řešení. Všechny aplikace Dynamics nebo vlastní produkty by měl použít tento typ testovací verze.
-   **Power BI** – vyzkoušejte A Power BI se skládá z vložený odkaz na míru sestavené řídicí panel. Jakýkoli produkt, který chce prokázat, že že interaktivní vizuálu Power BI by měl použít tento typ testovací verze. Vše, co potřebujete k nahrání je vaše adresa URL vložené Power BI.

Tady jsou hlavní publikování kroky pro přidání testu jednotky:

1.  Definování scénáře vaše testovací verze
2.  Vytvořit nebo upravit šablony Resource Manageru
3.  Vytvoření podrobné příručce k testovací verzi
4.  Nové publikování vaší nabídky

Další informace najdete v tématu [Test Drive](./what-is-test-drive.md).


### <a name="storefront-details"></a>Prezentace podrobnosti

SaaS aplikace potřebují první dvě části Souhrn a popis, který poskytuje informace o vaší aplikaci.

![Prezentace podrobnosti nabídky SaaS](media/cpp-creating-saas-offers/saas-new-offer-storefront-details.png)

Následující tabulka popisuje nabídky\'s podrobnosti z prodejních míst:

| **Nabídka pole**        | **Popis**                                                                                          |
|-------------------------| ---------------------------------------------------------------------------------------------------------|
| Souhrn nabídky           | Souhrn hodnotové vaší nabídky. Zobrazí se na stránce hledání vaší nabídky. Měla by být delší než 100 znaků.   |
| Popis nabídky       | Popis, který se zobrazí na stránce podrobností aplikace. Maximální povolený počet je 1300 znaků *Poznámka* toto pole přijímá obsah HTML s tagy jako & ltp\>, & lth1\>, & lth2\>, & ltli\>, atd., který vám umožní zajistit pro obsah prezentovatelný mnohem víc. Publikování portálu tým pracuje na přidávání funkce, která umožňují zobrazit náhled svých prezentace podrobnosti zavádět postupně díky čemuž je obsah více prezentovatelný – mezitím můžete použít kterýkoli můžete použít libovolný online v reálném čase HTML nástrojů, jako je http://htmledit.squarefree.com zobrazíte jak se váš popis vypadat. |
| Odvětví              | Vyberte odvětví, že vaše nabídka je nejlepší zarovnán. Pokud vaše aplikace má vztah k více oborů, můžete vybrat maximálně dvě. |
| Navrhované kategorií    | Vyberte příslušné kategorie nabídky nejlépe zarovnán. Můžete vybrat nejvýše tři kategorie.     |
| Verze aplikace     | Zadejte číslo verze aplikace                                                                |
| Klíčová slova pro vyhledávání (maximálně 3) | Zadejte až tři klíčová slova pro vyhledávání, které zákazníci můžou využít k vyhledání aplikace na webu Marketplace prezentace. |
|  |  |


### <a name="marketing-artifacts"></a>Artefakty marketing

Části marketingové artefaktů můžete zadat na webu Azure Marketplace marketingové prostředky, jako jsou loga, videa, snímky obrazovky a dokumenty:

![Nabídky SaaS marketingové artefaktů](media/cpp-creating-saas-offers/saas-new-offer-marketing-artifacts.png)

Následující tabulka popisuje pole uvádění na trh:

| **Nabídka pole** | **Popis**                                                                                                          |
|------------------| ------------------------------------------------------------------------------------------------------------------------ |
| Loga            | Pokud jste **prodávat prostřednictvím Azure** aplikace SaaS, měli byste zadat všechny bitové kopie logo. Pokud jste právě výpis, pouze 2 loga jsou povinné. Všechny nahrané v portál partnerů cloudu loga musí pomocí následujících pokynů:     <br/> -Zachovat počet primární a sekundární barvy na vaše logo nízké. Design Azure má jednoduchou paletu barev.     <br/> -Vyhněte se použití černé nebo bílé jako barva pozadí loga. Barvy motivu na webu Azure Portal je černobílý. Místo toho použijte některé barvy, které by vaše logo viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy. Pokud používáte průhledným pozadím, ujistěte se, že logem a text nejsou černou, bílá nebo modrou.     <br/> -Nepoužívejte barevného přechodu pozadí na loga.     <br/> -Předejde text, dokonce i vaše společnost nebo název značky, loga. Vzhled a chování vašeho loga musí být "ploché" a přechody se měli vyhnout.    <br/> By neměl být roztažený – obrázek loga.                   |
| Videa           | Umožňuje přidat odkazy tohoto videa o vaší nabídce. Můžete použít odkazy na videa YouTube nebo Vimeo, které se zobrazují společně s vaší nabídky zákazníkům. Musíte také zadat obrázek miniatury videa, s obrázkem png 1280 × 720 pixelů. Může mít maximálně čtyři videí na nabídku. |
| Dokumenty        | Umožňuje přidat marketingové dokumenty pro vaši nabídku. Všechny dokumenty musí být ve formátu PDF a může mít maximálně tři dokumentů na nabídku.                                                                                                                                                      |
| Snímky obrazovek      | Umožňuje přidat snímky obrazovky vaší nabídky. Je maximálně pět snímky obrazovky, které mohou být přidány za nabídky. Maximální velikost je 1280 × 720 pixelů.                                                                                                                                             |
| Užitečné odkazy     | Umožňuje přidat externí adresy URL pro vaši nabídku Nápověda, přejděte na diagramy architektury nebo jiných webových stránek, které byste měli zákazník naleznete v tématu.                                                                                                                                                              |
|  |  |

Následující obrázek ukazuje, jak se zobrazují informace ve výsledku hledání Marketplace:

![Nabídky SaaS marketingové informace vydavatele](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info.png)

Následující obrázek ukazuje, jak se nabídky zobrazí na webu Marketplace po zákazník klikne na menší dlaždici nabídky:

![Nabídky SaaS marketingové info2 vydavatele](media/cpp-creating-saas-offers/saas-new-offer-marketing-publisher-info-2.png)


### <a name="lead-management-and-legal-information"></a>Průběžné řízení a právní informace

Právní část umožňuje nastavit právní dokumentace nabídky.
Existují dva právní dokumenty, vyžaduje se pro každou nabídku aplikace SaaS: zásady ochrany osobních údajů a podmínky použití. Další informace najdete v tématu  
[Konfigurace potenciálních zákazníků](./cloud-partner-portal-get-customer-leads.md).

![Nabídky SaaS marketingové právní informace](media/cpp-creating-saas-offers/saas-new-offer-legal-info.png)

Následující tabulka popisuje vlastnosti právní oddílu:

| **Nabídka pole**   | **Popis**                                                                                 |
|--------------------| ----------------------------------------------------------------------------------------------- |
| Adresa URL zásad ochrany osobních údajů | Adresa URL zásad ochrany osobních údajů vaší společnosti.                                                       |
| Podmínky použití       | Podmínky použití pro vaši nabídku. Zadejte nebo zkopírujte a vložte podmínky použití zde. Základní informace o využití ve formátu HTML je povolený, jako je toto pole přijímá html obsahu pomocí značky jako & ltp\>, & lth1\>, & lth2\>, & ltli\>atd. *Důležité* důrazně doporučujeme zkontrolovat a zobrazit náhled HTML, kterou jste vytvořili v prohlížeči před odesláním nabídky. |
|  |  |


### <a name="contact-information"></a>Kontaktní informace

V této části zadáte kontakty podpory ve vaší společnosti, které se bude zodpovědní za technickou podporu zákazníků předplatili.
Existují tři hlavní oblasti: technickým kontaktem, kontaktujte podporu a adresy URL podpory:

![Nabídky SaaS marketingové kontaktním informacím.](media/cpp-creating-saas-offers/saas-new-offer-contact-info.png)


| **Obraťte se na**         | **Popis**                                                                                                                          |
|---------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| Technický kontakt | Zadejte jméno, e-mailu a telefonní číslo technickým kontaktem, že Microsoft může kontaktovat podporu a obchodní problémy. |
| Kontakt podpory     | Zadejte jméno, e-mailu, telefonní číslo a podporu adresu URL, která vaši zákazníci se můžou obrátit, když mají žádosti o podporu.                  |
|  |  |


Po připravený vaší nabídky, a dosáhnete publikovat, prochází nabídky certifikace. Můžeme otestovat aplikaci SaaS pomocí ruční ověření kolem testování adresu URL, pokud je poskytnout seznam nebo vybrané koncové body, pokud mají prodávat prostřednictvím Azure. Během této ruční schválení rozhodneme také odpovídající výkladní skříň, které vaše aplikace měla zobrazit na (AppSource, webu Azure Marketplace nebo obojí).

<a name="updating-the-offer"></a>Aktualizuje se nabídka
------------------

Existují různé typy aktualizací, které můžete chtít provést pro vaši nabídku poté, co byla publikována a je v provozu. Všechny změny provedené v nové verzi nabídky by měla uložit a publikovat jej odrážejí na webu Marketplace.

<a name="deleting-an-existing-offer"></a>Odstraňuje se stávající nabídky
--------------------------

Můžete rozhodnout odebrat svou nabídku z webu Marketplace. Odebrání nabídky zajišťuje, že noví zákazníci mohou už koupit a nasadit vaši nabídku, ale nemá žádný vliv na stávající zákazníky. Ukončení nabídky je proces ukončení služby a/nebo licenční smlouvy mezi vámi a vašimi stávajícími zákazníky.

Pokyny a zásady týkající se odebrání a ukončení nabídky se řídí smlouvy vydavatele Microsoft Marketplace (viz sekce 7) a zásadách pro účast (viz část 6.2). Tato část obsahuje informace týkající se odstranění jiné podporované scénáře a kroky, které můžete provést pro ně.

### <a name="delete-the-live-offer"></a>Odstranění nabídky za provozu

Existují různé aspekty, které je třeba se postarat Pokud žádost o odebrání za provozu nabídky. Postupujte podle kroků níže zobrazíte pokyny od týmu podporu k odebrání za provozu nabídky z Azure Marketplace:

1.  Lístek podpory pomocí tohoto odkazu
2.  V seznamu typ problému vyberte **správa nabídek**a vyberte v seznamu kategorií **úpravy nabídka a skladová položka již v produkčním prostředí**.
3.  Odeslat žádost

Tým podpory vás provede odstranění nabídky.

> [!NOTE] 
> Odstranění nabídky nemá vliv na aktuální nákupy na základě této nabídky. Tyto nákupy zákazníků budou nadále fungovat jako předtím.
Tuto nabídku však nebudou k dispozici pro všechny nové nákupy po odstranění je dokončeno.
