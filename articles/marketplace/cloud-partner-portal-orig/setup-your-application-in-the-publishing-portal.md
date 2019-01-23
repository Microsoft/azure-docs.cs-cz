---
title: Nastavení aplikace v portálu pro publikování | Dokumentace Microsoftu
description: Podle pokynů v nastavení aplikace v portálu pro publikování cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 7b5bb0bf8ff5fac10c47cf5bdf9564903f4d6b94
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448479"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Nastavení aplikace na portálu publikování

Nyní jste připraveni k nastavení aplikace na portálu publikování.

## <a name="login-and-create-a-new-offer"></a>Přihlášení a vytvořte novou nabídku

1. Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/).
2. V levém navigačním panelu klikněte na "+ New nabízejí" a vyberte "Dynamics 365 for Customer Engagement."

![Vyberte novou nabídku](./media/CRMScreenShot14.png)

3. Nová nabídka zobrazení "Editoru" teď pro vás otevřen a jsme připraveni začít vytvářením obsahu.

![Nová nabídka obrazovka](./media/CRMScreenShot15.png)

4. "Formuláře", které je potřeba doplnit jsou viditelné na levé straně v rámci zobrazení "Editoru". Každý "formulář" obsahuje sadu polí, která se mají doplnit. Vyžaduje se pole označená červenou hvězdičkou (\*).

Existují čtyři hlavní formuláře pro vytváření obsahu Dynamics 365 Customer Engagement nabídky.

* Nabídka nastavení
* Technické informace
* Prezentace podrobnosti
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Vyplňte formulář pro nastavení nabídky

Formulář pro nastavení nabídky je základní formulář a zadejte nastavení nabídky. Různá pole jsou popsané níže.

### <a name="offer-id"></a>ID nabídky

Toto je jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID se nebude zobrazovat v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. Toto pole je uzamčen, jakmile uvedete nabídky.

například, pokud vydavatele **"contoso"** vydavatelé vytvoří v rámci nabídky s ID nabídky **"ukázkové webové aplikace"**, se zobrazí v AppSource jako "https://appsource.microsoft.com/marketplace/apps/contoso.sample-WebApp?tab=Overview"

### <a name="publisher-id"></a>ID vydavatele

Tento rozevírací seznam umožňuje zvolit profil vydavatele, který chcete publikovat v rámci této nabídky. Toto pole je uzamčen, jakmile uvedete nabídky.

### <a name="name"></a>Název

Toto je zobrazovaný název nabídky. Toto je název, který se zobrazí v [AppSource](https://appsource.microsoft.com/). Může mít maximálně 50 znaků.

Klikněte na "Save" rozdělanou práci uložit. Dalším krokem je přidání technické informace pro vaši nabídku.

## <a name="fill-out-the-technical-info-form"></a>Vyplňte formulář technické informace


Technické informace o formulář je, kde bude vyplňte informace specifické pro vaše Dynamics 365 Customer Engagement řešení. Se ukazatel myši je k dispozici s dalšími informacemi. Viz následující příklad.

![Technické informace obrazovky](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informace o aplikaci

Většina vydavatelé ponechá tato pole s výchozími hodnotami, uživatele, ne, ne a prázdné adresy URL konfigurace aplikace podle výše uvedeném snímku obrazovky.

### <a name="crm-package"></a>Balíček aplikace CRM

![Informace o balíčku CRM](./media/CRMScreenShot17.png)

Následuje vysvětlení těchto polí:

* Název souboru balíčku: Název souboru jste vytvořili v předchozím kroku při vytváření souboru zip, který je váš balíček AppSource CRM. V příkladu výše je "Microsoft\_SamplePackage.zip".
* Adresa URL umístění balíčku: Toto je adresa URL k účtu Azure Storage, který obsahuje název souboru balíčku, který je zadaný výše. To je adresa URL vytvořené v kroku 9 v části výše.
* Existuje více než jeden balíček crm v souboru balíčku: Vyberte možnost Ano **pouze** pokud podporujete více verzí aplikace crm pomocí různých balíčků. Pro většinu partnerů bude jím "Ne". Pokud vyberete Ano, musíte vytvořit balíčky AppSource pro každou verzi vašeho řešení. _Poznámka: NENÍ to požádá, pokud máte více **zip** soubory. Pokud máte víc souborů solution.zip ale jenom jedna verze, byste měli stále vybrat "Ne". Nástroj pro vytváření balíčků se pohromadě tyto na automaticky._

### <a name="crm-package-availability"></a>Dostupnost balíčku CRM

V této části vyberte jaké oblasti vašeho balíčku bude k dispozici do CRM. Informace, na které oblasti slouží kterých zemí najdete v tématu odkazu: [http://o365datacentermap.azurewebsites.net/](http://o365datacentermap.azurewebsites.net/)

Poznámka: Nasazení do Německo "Suverénních a Cloud pro státní správu USA" suverénních vyžaduje speciální oprávnění a ověření během certifikace

## <a name="storefront-details"></a>Prezentace podrobnosti

### <a name="offer-summary"></a>Souhrn nabídky

Toto je souhrn vaší nabídky návrh hodnoty. Zobrazí se na stránce hledání vaší nabídky. Měla by být delší než 100 znaků.

### <a name="offer-description"></a>Popis nabídky

Toto je popis, který se zobrazí na stránce podrobností aplikace. Maximální povolený počet je 1300 znaků

### <a name="industries"></a>Odvětví

Vyberte odvětví, že vaše aplikace je nejlepší zarovnán. Pokud má vaše aplikace má vztah k více oborů, můžete nechat prázdné.

### <a name="categories"></a>Kategorie

Vyberte příslušné kategorie, které jsou relevantní pro vaši aplikaci. Vyberte až 3.

### <a name="app-type"></a>Typ aplikace

Vyberte typ zkušební verzi, která vám umožní vaši aplikaci na AppSource. "Free" znamená, že vaše aplikace je zdarma. "Zkušební verze" znamená, že zákazníci si můžou vyzkoušet vaše aplikace na krátkou dobu na AppSource. "Žádost o zkušební verzi" není podporován pro Dynamics 365 Customer Engagement aplikací. Nevybírejte tuto možnost.

### <a name="help-link-for-your-app"></a>Odkaz na nápovědu pro vaši aplikaci

Zadejte adresu URL na stránku, která má pomoci související informace pro vaši aplikaci.

### <a name="supported-countriesregions"></a>Podporované země/oblasti

Toto pole určuje země nebo oblasti, ve kterých bude k dispozici pro zkušební verzi vaší nabídky.

### <a name="supported-languages"></a>Podporované jazyky

Vyberte jazyky, které vaše aplikace podporuje. Pokud vaše aplikace podporuje další jazyky, které nejsou v tomto seznamu, pokračovat v publikování vaší nabídky a pošlete nám e-mail na: [ appsource@microsoft.com ](mailto:appsource@microsoft.com) a dejte nám vědět.

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze pro vaši aplikaci

### <a name="app-release-date"></a>Datum vydání verze aplikace

Zadejte datum vydání verze pro vaši aplikaci

### <a name="products-your-app-works-with-max-3"></a>Produkty, které vaše aplikace funguje se (maximálně 3)

Konkrétní produkty, které vaše aplikace funguje se. Můžete vytvořit seznam maximálně tři produkty. Seznam produktu, klikněte na znaménko plus (vedle nový) a vytvoří se nové otevřít textové pole pro zadání názvu produktu, který vaše aplikace funguje se.

### <a name="search-keywords-max-3"></a>Klíčová slova pro vyhledávání (maximálně 3)

AppSource umožňuje zákazníkům hledat na základě klíčových slov. Můžete zadat sadu klíčových slov, u kterých vaše aplikace zobrazí se zákazníky.

Například pokud aplikace "E-mailem žádají služba" e-mailů, poštovní, poštovní služba může být několik klíčových slov. Volte slova, které uživatelé budou pravděpodobně používat k hledání pro vaši aplikaci do AppSource vyhledávacího pole.

### <a name="hide-key"></a>Skrýt klíče

Toto je klíč, který se zkombinuje s adresou URL náhledu nabídky ke skrytí před veřejné zobrazení. Není heslo. Můžete zadat libovolný řetězec tady.

### <a name="offer-logo-png-format-48x48"></a>Nabídka logo (formátu png, 48 x 48)

Tím se zobrazí na stránce hledání vaší aplikace. **Je povolen pouze formátu png.** Nahrajte obrázek png s překladem 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Nabídka logo (formátu png, 216 x 216)

Zobrazí se na stránku podrobností aplikace. **Je povolen pouze formátu png.** Nahrajte obrázek png s překladem 216PX\*216PX

### <a name="videos"></a>Videa

Můžete nahrát maximálně čtyři videa. Pro každé video, které chcete nahrát musíte vyplnit název videa, adresa URL (YouTube nebo Vimeo pouze) a miniaturu pro přidružení k videu. Miniatura musí být ve formátu png a musí být 1280PX\*720PX. Chcete-li přidat nový video(s), klikněte na znaménko plus. Thumbnail(s) videa se zobrazí na stránce podrobností aplikace.

### <a name="documents"></a>Dokumenty

Můžete nahrát maximálně tři dokumenty ve formátu PDF. Pro každý dokument, který chcete nahrát musíte vyplnit název dokumentu a nahrajete daný dokument. Dokument musí být ve formátu pdf.

Pokud chcete přidat nové dokumenty, klikněte na znaménko plus

### <a name="screenshots"></a>Snímky obrazovek

Toto jsou snímky obrazovky, který se zobrazí na stránce s podrobnostmi AppSource pro aplikaci.

### <a name="privacy-policy"></a>Zásada zabezpečení

Zadejte adresu URL zásad ochrany osobních údajů vaší aplikace

### <a name="terms-of-use"></a>Podmínky použití

Zadejte podmínky použití vaší aplikace. Zákazníci AppSource je potřeba přijmout tyto podmínky předtím, než se pokusí vaše aplikace

### <a name="support-url"></a>Adresa URL podpory

Zadejte adresu URL podpory pro vaši aplikaci.

### <a name="lead-destination"></a>Vedoucí cíl

Vyberte systému CRM, kde vést se uloží. Zde vyberte "Azure Table", pokud máte jeden z následujících systémů CRM: Salesforce, Marketo, Microsoft Dynamics CRM. Systému CRM, zde je, kde napíšeme podrobnosti koncových uživatelů, kteří vyzkouší vaši aplikaci na AppSource (zájemce). V závislosti na systému CRM, které vyberete klikněte na odpovídající následující adresu URL pro informace o tom, jak projít další sadu polí

* [Tabulka Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Prezentace podrobnosti

Kontaktní údaje se používají pouze pro vnitřní komunikace mezi partnerem a Microsoftem. Poznámka: Je důležité používat e-mailovou adresu, která je sledována v těchto polích. Tento e-mail budeme používat ke komunikaci s vámi na průběh v publikování na AppSource. Pouze adresu URL podpory se nebude zobrazovat na zákazníky.
