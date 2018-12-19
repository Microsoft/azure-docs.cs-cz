---
title: Jak vytvořit Dynamics 365 pro operace nabízejí přes portál partnerů cloudu | Dokumentace Microsoftu
description: Jak vytvořit Dynamics 365 pro operace nabízejí přes portál Cloud Partner
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
ms.openlocfilehash: 41f3fd55be02364b4028642db9db1d3f47043afa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245082"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>Jak vytvořit Dynamics 365 pro operace nabízejí přes portál Cloud Partner

Portálu pro publikování poskytuje přístup na základě rolí na portálu pro povolení více osob, abyste mohli spolupracovat na publikování nabídky. Zobrazit [cloudu portálu spravovat uživatele](./cloud-partner-portal-manage-users.md) pro další informace.

Před publikováním nabídky jménem vydavatel účtu, jeden z uživatelé, kteří mají \"vlastníka\" role je potřeba souhlasit s [Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/), [prohlášení o ochraně osobních údajů Microsoft](https://www.microsoft.com/privacystatement/default.aspx), a [smlouva k programu Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Jak vytvořit nový Dynamics 365 pro operace nabídky

Jakmile jsou splněné všechny požadavky, jste připravení začít vytváření vaší Dynamics 365 pro operace nabídku.

1. Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/).
2. V levém navigačním panelu klikněte na \"+ nová nabídka\" a vyberte \"Dynamics 365 pro operace\".
3. Nová nabídka \"Editor\" zobrazení je teď otevřené pro vás a jsme připraveni začít vytvářením obsahu.
4. \"Forms\" jsou viditelné na levé straně v rámci, které je potřeba doplnit \"Editor\" zobrazení. Každý \"formuláře\" sestává ze sady polí, která se mají doplnit. Vyžaduje se pole označená červenou hvězdičkou (\*).

Existují čtyři hlavní formuláře pro vytváření obsahu Dynamics 365 pro operace nabídky:

- Nabídka nastavení
- Technické informace
- Prezentace podrobnosti
- Kontakty

## <a name="how-to-fill-out-the-offer-settings-form"></a>Jak vyplnit formulář pro nastavení nabídky

Formulář pro nastavení nabídky je základní formulář a zadejte nastavení nabídky. Různá pole jsou popsané níže.

### <a name="offer-id"></a>ID nabídky

Toto je jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID se nebude zobrazovat v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. Toto pole je uzamčen, jakmile uvedete nabídky.

Například pokud vydavatele contoso vydavatele vytvoří nabídka s ID nabídky *dynamics365 vzorku pro operace*, se zobrazí v AppSource jako \"https://appsource.microsoft.com/marketplace/apps/**contoso**.*Ukázka dynamics 365 pro operace* ? tab = přehled\"

### <a name="publisher-id"></a>ID vydavatele

Tento rozevírací seznam umožňuje zvolit profil vydavatele, který chcete publikovat v rámci této nabídky. Toto pole je uzamčen, jakmile uvedete nabídky.

Název

Toto je zobrazovaný název nabídky. Toto je název, který se zobrazí v [AppSource](https://appsource.microsoft.com). Může mít maximálně 50 znaků.

Klikněte na \"Uložit\" uložte svůj postup. Dalším krokem by a vyplňte technické informace pro vaši nabídku.

## <a name="how-to-fill-out-the-technical-info-form"></a>Jak vyplnit formulář technické informace

Technické informace o formulář obsahuje informace, které se zobrazí na stránce nabídky. Pokyny pro různá pole jsou popsané níže.

![Nová nabídka obrazovka](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>Identifikátor řešení

Nejprve je identifikátor vašeho řešení.

1. Najít tento identifikátor, přejděte na životní cyklus služeb a vybrat řešení správy.
2. Po výběru příslušné řešení, se zobrazí identifikátor řešení v přehledu balíčku. \*\*Pokud identifikátor je prázdný, vyberte možnost upravit a znovu publikovat balíček pro identifikátor řešení se zobrazí.

### <a name="validation-assets"></a>Ověření prostředky

AUTĚ (sestavy analýzy přizpůsobení) nahrajte ji sem.

### <a name="does-solution-enable-translations"></a>Řešení povolení překlady?

Vyberte \'Ano\' nebo \'ne\'.

### <a name="does-solution-include-localizations"></a>Zahrnuje řešení Localization(s)?

Vyberte \'Ano\' nebo \'ne\'.

### <a name="product-version"></a>Verze produktu

Vyberte nový AX. Nakonec klikněte na Uložit.

## <a name="how-to-fill-out-storefront-details-form"></a>Jak vyplňte formulář podrobností z prodejních míst.

Nejprve je vaše podrobnosti nabídky.

1. **Souhrn nabídky**

    \- Zadejte krátký popis vašeho řešení (maximálně 100 znaků).

2. **Popis nabídky**

    \- Zadejte stručný popis vašeho řešení. Popis by měl mít funkční nároky vašeho řešení a přímo by mělo odpovídat BPM knihovny. Pokud třeba máte funkce například x, y, z v marketingového obsahu, během poslední kontroly jsme se ujistěte se, že tyto jsou popsány v knihovně BPM uvnitř LCS.

![obrazovka s podrobnostmi prezentace](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>Podrobnosti

![obrazovka s podrobnostmi prezentace](./media/publish_d365_new_offer/storefront_details.png)

1. **Obory** – zkontrolujte mít délku maximálně dva obory z se zadanými možnostmi.
2. **Kategorie** – zkontrolujte mít délku maximálně tři kategorie z se zadanými možnostmi.
3. **Typ aplikace** – vyberte se zadanými možnostmi.
4. **Odkaz na nápovědu pro vaši aplikaci** – zadejte odkaz na nápovědu pro vaše řešení.
5. **Podporované země/oblasti** – zkontrolujte z se zadanými možnostmi.
6. **Podporované jazyky** – zkontrolujte z se zadanými možnostmi.
7. **Verze aplikace** – zadejte verzi vašeho řešení, která byla vydána. (například 1.0.0.0)
8. **Datum vydání aplikace** – zadejte verzi datum vaší solution(mm/dd/yyyy).
9. **Vaše aplikace funguje s produkty** -konkrétní produkty, které vaše aplikace funguje se. Můžete vytvořit seznam maximálně tři produkty. Seznam produktu, klikněte na znaménko plus (vedle nový) a vytvoří se nové otevřít textové pole pro zadání názvu produktu, který vaše aplikace funguje se.
10. **Klíčová slova pro vyhledávání** – zadejte běžných termínů, které uživatelé mohou využít při hledání najít vaše řešení. \*\*Tato klíčová slova se nezobrazí na webu Marketplace.
11. **Skrýt klíč** – to je co klíč, který by kombinovat s adresou URL náhledu nabídky ke skrytí před veřejné zobrazení. Není heslo. Můžete zadat libovolný řetězec tady.

### <a name="marketing-artifacts"></a>Artefakty marketing

1. Dále nahrává vaše **loga**, **snímky obrazovky**. \*\*Mějte prosím na paměti velikosti pro každý nahraný a všech imagí musí být ve formátu PNG.
2. **Ukázka videa** – klikněte na \"+ nový\". Nahrajte video ukázku vašeho řešení (pouze odkazy YouTube nebo Vimeo). \*\*. Mějte prosím na paměti, že byste měli nahrávat miniaturu zadané velikosti aby vaše video se zobrazí v testovacím prostředí.
3. **Dokumenty** – odeslat všechny dokumenty týkající se vašeho řešení a nezapomeňte zadat název dokumentu.

### <a name="legal"></a>Právní informace

Tyto informace odkaz na zásady ochrany osobních údajů a podmínky použití. Zadejte řešení adresa URL zásad ochrany osobních údajů a podmínky použití. \*\*Zákazník bude moct zobrazit tyto zásady na portálu.

### <a name="customer-support"></a>Zákaznická podpora

Adresa URL podpory se vaši uživatelé viděli pouze na portálu.

### <a name="leads-management"></a>Správa potenciálních zákazníků

Vyberte systému CRM, kde vést se uloží. Vyberte \"Azure Table\" sem, pokud máte jednu z následujících systémů CRM: Salesforce, Marketo, Microsoft Dynamics CRM. Systému CRM, zde je, kde napíšeme podrobnosti koncových uživatelů, kteří vyzkouší vaši aplikaci na AppSource (zájemce). V závislosti na systému CRM, které vyberete klikněte na odpovídající následující adresu URL pro informace o tom, jak projít další sadu polí.

![Podrobnosti o vedoucí správy](./media/publish_d365_new_offer/leads.png)

1. Tabulka Azure najdete v tématu [zde](https://aka.ms/leadsettingforazuretable)
2. Dynamics CRM online, najdete v tématu [zde](https://aka.ms/leadsettingfordynamicscrm)
3. Pro Marketo [zde](https://aka.ms/leadsettingformarketo)
4. Salesforce najdete [zde](https://aka.ms/leadsettingforsalesforce)

## <a name="how-to-fill-out-the-contacts-form"></a>Jak vyplnit formulář Kontakty.

Tato informace se použijí pro společnost Microsoft a Zákaznická podpora. Zadejte technickým kontaktem a Zákaznická podpora vaší společnosti a adresa URL podpory pro vaše řešení. Tyto informace se použije, jako jediný bod kontaktu, pokud má otázky o vašich řešení a také pro zákaznickou podporu Microsoftu.

![Nabídka kontakty obrazovky](./media/publish_d365_new_offer/Contacts.png)
