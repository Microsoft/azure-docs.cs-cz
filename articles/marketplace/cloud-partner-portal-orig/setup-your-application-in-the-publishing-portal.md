---
title: Nastavení aplikace na portálu pro publikování
description: Pokyny, jak nastavit aplikaci na portálu pro publikování v cloudu
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 1bf89f94d91e0d809428cf8098db0fb37afdb47a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814685"
---
# <a name="setup-your-application-in-the-publishing-portal"></a>Nastavení aplikace na portálu pro publikování

Nyní jste připraveni nastavit aplikaci na portálu pro publikování.

## <a name="login-and-create-a-new-offer"></a>Přihlaste se a vytvořte novou nabídku.

1. Přihlaste se k [portál partnerů cloudu](https://cloudpartner.azure.com/).
2. V levém navigačním panelu klikněte na + nová nabídka a vyberte Dynamics 365 pro zákaznickou zapojení.

![Výběr nové nabídky](./media/CRMScreenShot14.png)

1. Otevře se nová nabídka "Editor", která je teď otevřená a Vy jsme připraveni začít s vytvářením obsahu.

![Obrazovka nové nabídky](./media/CRMScreenShot15.png)

1. Formuláře "Forms", které je třeba vyplnit, jsou zobrazeny vlevo v zobrazení "Editor". Každý "formu" tvoří sadu polí, která mají být vyplněna. Povinná pole jsou označena červenou hvězdičkou (\*).

Existují čtyři hlavní formuláře pro vytváření nabídek Dynamics 365 for Customer Engagement.

* Nastavení nabídky
* Technické informace
* Prezentace podrobnosti
* Kontakty

## <a name="fill-out-the-offer-settings-form"></a>Vyplnění formuláře nastavení nabídky

Formulář nastavení nabídky je základní formulář pro určení nastavení nabídky. Různá pole jsou popsána níže.

### <a name="offer-id"></a>ID nabídky

Toto je jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID bude viditelné v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nemůže končit spojovníkem a může mít maximálně 50 znaků. Po živé nabídce bude toto pole uzamčené.

Pokud například Vydavatel **"contoso"** vytvoří nabídku s ID nabídky **"Sample-WebApp"** , zobrazí se v AppSource jako "https:\//appsource.Microsoft.com/Marketplace/Apps/contoso.Sample-WebApp?TAB=Overview".

### <a name="publisher-id"></a>ID vydavatele

V tomto rozevíracím seznamu můžete zvolit profil vydavatele, pod kterým chcete tuto nabídku publikovat. Po živé nabídce bude toto pole uzamčené.

### <a name="name"></a>Name (Název)

Toto je zobrazovaný název vaší nabídky. Toto je název, který se zobrazí v [AppSource](https://appsource.microsoft.com/). Může mít maximálně 50 znaků.

Kliknutím na Uložit uložte svůj průběh. Dalším krokem je přidání technických informací pro vaši nabídku.

## <a name="fill-out-the-technical-info-form"></a>Vyplňte formulář technické informace.


Ve formuláři technické informace najdete informace specifické pro řešení Dynamics 365 for Customer Engagement. Po najetí myší na se zobrazí další informace. Viz následující příklad.

![Obrazovka s technickými informacemi](./media/CRMScreenShot16.png)

### <a name="application-info"></a>Informace o aplikaci

Většina vydavatelů ponechá tato pole výchozími hodnotami, uživatel, ne, ne a prázdná adresa URL konfigurace aplikace na snímku obrazovky výše.

### <a name="crm-package"></a>Balíček CRM

![Informace o balíčku CRM](./media/CRMScreenShot17.png)

Tady je vysvětlení těchto polí:

* Název souboru balíčku: název souboru, který jste vytvořili v předchozím kroku při vytváření souboru zip, který je vaším balíčkem AppSource CRM. Ve výše uvedeném příkladu je to "Microsoft\_SamplePackage. zip".
* Adresa URL umístění balíčku: Toto je adresa URL účtu Azure Storage, který obsahuje výše uvedený název souboru balíčku. Je to adresa URL vytvořená v kroku 9 výše uvedené části.
* V souboru balíčku je více než jeden balíček CRM **: Pokud** podporujete více verzí CRM s různými balíčky, vyberte Ano. U většiny partnerů bude tato možnost "ne". Pokud vyberete Ano, budete muset vytvořit balíčky AppSource pro každou verzi vašeho řešení. _Poznámka: Pokud máte více souborů **zip** , nedotazuje se na ně. Pokud máte více souborů. zip, ale jenom jednu verzi, měli byste pořád vybrat ne. Nástroj pro balení bude tyto aplikace automaticky spojovat._

### <a name="crm-package-availability"></a>Dostupnost balíčku CRM

V této části vyberte, které oblasti aplikace CRM budou zpřístupněny pro. Informace o tom, které oblasti CRM slouží pro země nebo oblasti, najdete na tomto odkazu: [https://o365datacentermap.azurewebsites.net/](https://o365datacentermap.azurewebsites.net/)

Poznámka: nasazení do Německa "svrchovan and US Gov Cloud" v rámci certifikace vyžaduje zvláštní oprávnění a ověření.

## <a name="storefront-details"></a>Prezentace podrobnosti

### <a name="offer-summary"></a>Souhrn nabídky

Toto je souhrn hodnoty na pozici vaší nabídky. Zobrazí se na stránce pro hledání vaší nabídky. Hodnota by měla být maximálně 100 znaků.

### <a name="offer-description"></a>Popis nabídky

Toto je popis, který se zobrazí na stránce s podrobnostmi o vaší aplikaci. Povolené maximum je 1300 znaků.

### <a name="industries"></a>Odvětví

Vyberte odvětví, na které je aplikace nejlépe zarovnána. Pokud má vaše aplikace vztah k více oborům, můžete nechat pole prázdné.

### <a name="categories"></a>Kategorie

Vyberte kategorie, které jsou relevantní pro vaši aplikaci. Vyberte maximálně 3.

### <a name="app-type"></a>Typ aplikace

Vyberte typ zkušební verze, kterou aplikace povolí v AppSource. ' Free ' znamená, že je vaše aplikace zadarmo. "Zkušební" znamená, že zákazníci můžou vaši aplikaci po krátkou dobu vyzkoušet na AppSource. Možnost žádost o zkušební verzi není podporovaná pro Dynamics 365 pro aplikace Customer Engagement. Tuto možnost nevybírejte.

### <a name="help-link-for-your-app"></a>Odkaz na nápovědu pro vaši aplikaci

Zadejte adresu URL stránky, která obsahuje nápovědu související s informacemi pro vaši aplikaci.

### <a name="supported-countriesregions"></a>Podporované země/oblasti

Toto pole určuje země nebo oblasti, ve kterých bude nabídka k dispozici pro zkušební verzi.

### <a name="supported-languages"></a>Podporované jazyky

Vyberte jazyky, které vaše aplikace podporuje. Pokud vaše aplikace podporuje další jazyky, které nejsou v tomto seznamu, pokračujte v publikování vaší nabídky a pošlete nám e-mail na adrese: [appsource@microsoft.com](mailto:appsource@microsoft.com) , abychom nás věděli.

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze vaší aplikace.

### <a name="app-release-date"></a>Datum vydání aplikace

Zadejte datum vydání vaší aplikace.

### <a name="products-your-app-works-with-max-3"></a>Produkty, se kterými pracuje vaše aplikace (max. 3)

Konkrétní seznam produktů, se kterými vaše aplikace pracuje. Můžete uvést maximálně tři produkty. Pokud chcete zobrazit seznam produktů, klikněte na znaménko plus (vedle novinek) a vytvoří se nové otevřené textové pole pro zadání názvu produktu, ve kterém vaše aplikace pracuje.

### <a name="search-keywords-max-3"></a>Hledaná klíčová slova (max 3)

AppSource umožňuje zákazníkovi vyhledávat na základě klíčových slov. Můžete zadat sadu klíčových slov, pro které se vaše aplikace bude zobrazovat zákazníkům.

Například pokud je aplikace e-maily "Moje e-mailová služba", poštovní služba může být některá klíčová slova. Vyberte slova, která uživatelé budou nejspíš používat k hledání vaší aplikace v vyhledávacím poli AppSource.

### <a name="hide-key"></a>Skrýt klíč

Klíč, který se bude kombinovat s adresou URL náhledu nabídky, aby se skryla z veřejného zobrazení. Nejedná se o heslo. Sem můžete zadat libovolný řetězec.

### <a name="offer-logo-png-format-48x48"></a>Logo nabídky (formát PNG, 48x48)

Tato akce se zobrazí na stránce vyhledávání vaší aplikace. **Je povolený pouze formát PNG.** Nahrání obrázku PNG s rozlišením 48PX\*48PX

### <a name="offer-logo-png-format-216x216"></a>Logo nabídky (formát PNG, 216x216)

Tato stránka se zobrazí na stránce s podrobnostmi vaší aplikace. **Je povolený pouze formát PNG.** Nahrání obrázku PNG s rozlišením 216PX\*216PX

### <a name="videos"></a>Videa

Můžete nahrát maximálně čtyři videa. Pro každé video, které chcete nahrát, je potřeba vyplnit název videa, adresu URL (jenom YouTube nebo Vimeo) a miniaturu, kterou chcete přidružit k videu. Miniatura musí být ve formátu PNG a musí být 1280PX\*720PX. Chcete-li přidat nové videa, klikněte na znaménko plus. Miniatury videa se zobrazí na stránce podrobností vaší aplikace.

### <a name="documents"></a>Dokumenty

Ve formátu PDF můžete nahrát maximálně tři dokumenty. Pro každý dokument, který chcete nahrát, je potřeba vyplnit název dokumentu a nahrajte ho. Dokument musí být ve formátu PDF.

Chcete-li přidat nové dokumenty, klikněte na znaménko plus.

### <a name="screenshots"></a>Snímky obrazovek

Jsou to snímky obrazovky, které se zobrazí na stránce s podrobnostmi o AppSource vaší aplikace.

### <a name="privacy-policy"></a>Zásada zabezpečení

Zadejte adresu URL zásad ochrany osobních údajů vaší aplikace.

### <a name="terms-of-use"></a>Podmínky použití

Zadejte podmínek použití vaší aplikace. Zákazníci AppSource musí tyto podmínky přijmout, aby mohli vyzkoušet vaši aplikaci.

### <a name="support-url"></a>Adresa URL podpory

Zadejte adresu URL podpory pro vaši aplikaci.

### <a name="lead-destination"></a>Cíl zájemce

Vyberte systém CRM, ve kterém se bude ukládat zájemce. Pokud máte jeden z následujících systémů CRM: Salesforce, Marketo, Microsoft Dynamics CRM, vyberte tabulku Azure. Systém CRM, který zde vyberete, je místo, kde zapíšeme podrobnosti o koncových uživatelích, kteří si vyzkouší vaši aplikaci na AppSource (zájemci). V závislosti na zvoleném systému CRM klikněte na odpovídající adresu URL níže, kde najdete informace o tom, jak dokončit další sadu polí.

* [Tabulka Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
* [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
* [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
* [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)

## <a name="storefront-details"></a>Prezentace podrobnosti

Kontaktní údaje se používají pro interní komunikaci mezi partnerem a Microsoftem. Poznámka: je důležité použít e-mailovou adresu, která je sledována v těchto polích. Tento e-mail budeme používat ke komunikaci s vámi a vaším průběhem publikování na AppSource. Pouze adresa URL podpory bude viditelná pro zákazníky.
