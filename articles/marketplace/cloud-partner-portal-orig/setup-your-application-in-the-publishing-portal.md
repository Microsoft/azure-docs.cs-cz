---
title: Nastavení aplikace na portálu pro publikování
description: Pokyny k nastavení aplikace na portálu pro publikování v cloudu.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 2676dbf72309eeb51be1f08e7bae2c1502cc671e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280197"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Nastavení aplikace na portálu pro publikování

Nyní jste připraveni nastavit aplikaci na portálu pro publikování.

## <a name="login-and-create-a-new-offer"></a>Přihlásit se a vytvořit novou nabídku

1. Přihlaste se k [portálu partnerů cloudu](https://cloudpartner.azure.com/).
2. Na levém navigačním panelu klikněte na "+ Nová nabídka" a vyberte "Dynamics 365 for Customer Engagement".

![Výběr nové nabídky](./media/CRMScreenShot14.png)

1. Nyní je pro vás otevřena nová nabídka "Editor" a jsme připraveni začít vytvářet.

![Obrazovka Nová nabídka](./media/CRMScreenShot15.png)

1. "Formuláře", které je třeba vyplnit, jsou viditelné vlevo v zobrazení "Editor". Každý "formulář" se skládá ze sady polí, která mají být vyplněna. Povinná pole jsou označena červenou hvězdičkou (\*).

Existují čtyři hlavní formuláře pro vytváření nabídky Dynamics 365 for Customer Engagement

* Nastavení nabídky
* Technické informace
* Podrobnosti o výloze
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Vyplňte formulář Nastavení nabídky

Formulář nastavení nabídky je základním formulářem pro určení nastavení nabídky. Níže jsou popsána různá pole.

### <a name="offer-id"></a>ID nabídky

Jedná se o jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID bude viditelné v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nemůže končit pomlčkou a může mít maximálně 50 znaků. Toto pole je uzamčeno, jakmile nabídka přejde do provozu.

například pokud vydavatel **"contoso"** vydavatelé vytvoří nabídku s ID nabídky **"ukázka-WebApp"**,\/zobrazí se v AppSource jako "https: /appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>Publisher ID

Tato rozevírací nabídka umožňuje vybrat profil vydavatele, pod kterým chcete tuto nabídku publikovat. Toto pole je uzamčeno, jakmile nabídka přejde do provozu.

### <a name="name"></a>Name (Název)

Toto je zobrazovaný název vaší nabídky. Toto je název, který se zobrazí v [AppSource](https://appsource.microsoft.com/). Může mít maximálně 50 znaků.

Klikněte na "Uložit" pro uložení vašeho pokroku. Dalším krokem by bylo přidat technické informace pro vaši nabídku.

## <a name="fill-out-the-technical-info-form"></a>Vyplňte formulář Technické informace


Formulář technických informací je místem, kde vyplníte informace specifické pro vaše řešení Dynamics 365 for Customer Engagement. Vznášející se nad vám představí více informací. Viz následující příklad.

![Obrazovka technických informací](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informace o aplikaci

Většina vydavatelů ponechá tato pole s výchozími hodnotami, uživatel, ne, ne, a prázdnou adresu URL konfigurace aplikace podle výše uvedeného snímku obrazovky.

### <a name="crm-package"></a>Balíček CRM

![Informace o balíčku APLIKACE CRM](./media/CRMScreenShot17.png)

Zde je vysvětlení pro tato pole:

* Název souboru balíčku: Název souboru, který jste vytvořili ve výše uvedeném kroku při vytváření souboru ZIP, který je vaším balíčkem CRM AppSource. Ve výše uvedeném příkladu\_je "Microsoft SamplePackage.zip".
* Adresa URL umístění balíčku: Toto je adresa URL účtu Služby Azure Storage, která obsahuje název souboru balíčku uvedený výše. Jedná se o adresu URL vytvořenou v kroku 9 výše uvedené části.
* Je v souboru balíčku více než jeden balíček CRM: Vyberte ano **pouze** v případě, že podporujete více verzí crm s různými balíčky. Pro většinu partnerů to bude "Ne". Pokud vyberete Ano, budete muset vytvořit AppSource balíčky pro každou verzi vašeho řešení. _Poznámka: To to není ptát, jestli máte více **zip** souborů. Pokud máte více souborů solution.zip, ale pouze jednu verzi, měli byste stále vybrat "Ne". Balicí nástroj je automaticky spojí._

### <a name="crm-package-availability"></a>Dostupnost balíčku CRM

V této části vyberte oblasti aplikace CRM, pro které bude váš balíček zpřístupněn. Informace o tom, které oblasti CRM slouží kterým zemím nebo oblastem, naleznete v odkazu:[https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Poznámka: Nasazení do Německa "Sovereign a US Gov Cloud" Sovereign vyžaduje zvláštní povolení a validaci během certifikace

## <a name="storefront-details"></a>Podrobnosti o výloze

### <a name="offer-summary"></a>Souhrn nabídky

Toto je souhrn nabídky hodnoty nabídky. Zobrazí se na vyhledávací stránce nabídky. Mělo by to být maximálně 100 znaků.

### <a name="offer-description"></a>Popis nabídky

Toto je popis, který se zobrazí na stránce s podrobnostmi o aplikaci. Maximální povolený počet je 1300 znaků.

### <a name="industries"></a>Odvětví

Vyberte odvětví, se kterým je vaše aplikace nejlépe zarovnaná. Pokud se vaše aplikace týká více odvětví, můžete toto pole ponechat prázdné.

### <a name="categories"></a>Kategorie

Vyberte kategorie, které jsou relevantní pro vaši aplikaci. Vyberte maximálně 3.

### <a name="app-type"></a>Typ aplikace

Vyberte typ zkušební verze, kterou vaše aplikace povolí na AppSource. "Zdarma" znamená, že vaše aplikace je zdarma. "Zkušební verze" znamená, že zákazníci mohou vyzkoušet vaši aplikaci na krátkou dobu na AppSource. Pro aplikace Dynamics 365 for Customer Engagement není podporována aplikace "Žádost o zkušební verzi". Tuto možnost nevybírejte.

### <a name="help-link-for-your-app"></a>Odkaz na nápovědu pro vaši aplikaci

Zadejte adresu URL na stránku s informacemi o nápovědě pro vaši aplikaci.

### <a name="supported-countriesregions"></a>Podporované země nebo oblasti

Toto pole určuje země nebo oblasti, ve kterých bude vaše nabídka k dispozici pro zkušební verzi.

### <a name="supported-languages"></a>Podporované jazyky

Vyberte jazyky, které vaše aplikace podporuje. Pokud vaše aplikace podporuje další jazyky, které nejsou v tomto seznamu, pokračujte v publikování nabídky a pošlete nám e-mail na adresu: [appsource@microsoft.com](mailto:appsource@microsoft.com) dejte nám vědět.

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze aplikace.

### <a name="app-release-date"></a>Datum vydání aplikace

Zadejte datum vydání aplikace.

### <a name="products-your-app-works-with-max-3"></a>Produkty, se kterými vaše aplikace pracuje (Max 3)

Produkty specifické pro seznam, se kterými vaše aplikace spolupracuje. Můžete uvést maximálně tři produkty. Chcete-li uvést produkt, klikněte na znaménko plus (vedle nového) a vytvoří se nové otevřené textové pole, ve které můžete zadat název produktu, se kterým vaše aplikace pracuje.

### <a name="search-keywords-max-3"></a>Klíčová slova pro vyhledávání (Max 3)

AppSource umožňuje zákazníkovi vyhledávat na základě klíčových slov. Můžete zadat sadu klíčových slov, pro která bude vaše aplikace zobrazena zákazníkům.

Například pokud je aplikace "Moje e-mailové služby" E-maily, Korespondence, Pošta služba může být některá klíčová slova. V vyhledávacím poli AppSource vyberte slova, která uživatelé pravděpodobně použijí k vyhledání vaší aplikace.

### <a name="hide-key"></a>Skrýt klíč

Jedná se o klíč, který bude kombinován s adresou URL náhledu nabídky, aby se skryl před veřejným zobrazením. Není to heslo. Zde můžete zadat libovolný řetězec.

### <a name="offer-logo-png-format-48x48"></a>Logo nabídky (formát png, 48x48)

Zobrazí se na vyhledávací stránce aplikace. **Je povolen pouze formát png.** Nahrání obrázku png s\*rozlišením 48PX 48PX

### <a name="offer-logo-png-format-216x216"></a>Logo nabídky (formát png, 216x216)

Zobrazí se na stránce s podrobnostmi aplikace. **Je povolen pouze formát png.** Nahrání obrázku png s rozlišením\*216PX 216PX

### <a name="videos"></a>Videa

Můžete nahrát maximálně čtyři videa. Pro každé video, které chcete nahrát, musíte vyplnit název videa, adresu URL (pouze YouTube nebo Vimeo) a miniaturu, abyste se k videu přidružili. Miniatura musí být ve formátu PNG a\*musí být 1280PX 720PX. Chcete-li přidat nové video (y), klikněte na znaménko plus. Miniatury videí se zobrazí na stránce s podrobnostmi aplikace.

### <a name="documents"></a>Dokumenty

Ve formátu PDF můžete nahrát maximálně tři dokumenty. Pro každý dokument, který chcete nahrát, musíte vyplnit název dokumentu a nahrát dokument. Dokument musí být ve formátu pdf.

Chcete-li přidat nové dokumenty, klikněte na znaménko plus

### <a name="screenshots"></a>Snímky obrazovek

Jedná se o snímky obrazovky, které se zobrazí na stránce podrobností AppSource pro vaši aplikaci.

### <a name="privacy-policy"></a>Zásady ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů aplikace.

### <a name="terms-of-use"></a>Podmínky použití

Zadejte podmínky používání aplikace. Zákazníci AppSource musí tyto podmínky přijmout, než budou moci aplikaci vyzkoušet.

### <a name="support-url"></a>Adresa URL podpory

Zadejte adresu URL podpory pro vaši aplikaci.

### <a name="lead-destination"></a>Cíl zájemce

Vyberte crm systém, do kterého budete mít zájemce. Pokud máte jeden z následujících crm systémů: Salesforce, Marketo, Microsoft Dynamics CRM, vyberte zde možnost "Tabulka Azure". Crm systém, který zde vyberete, je místo, kde budeme psát podrobnosti o koncových uživatelích, kteří si vyzkouší vaši aplikaci na AppSource (vede). V závislosti na vybraném systému CRM klepněte na příslušnou adresu URL níže, kde naleznete informace o tom, jak dokončit další sadu polí.

* [Tabulka Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Podrobnosti o výloze

Kontaktní údaje se používají pouze pro interní komunikaci mezi partnerem a společností Microsoft. Poznámka: Je důležité použít e-mailovou adresu, která je v těchto polích monitorována. Tento e-mail použijeme ke komunikaci s vámi o vašem postupu při publikování na AppSource. Zákazníkům bude viditelná pouze adresa URL podpory.
