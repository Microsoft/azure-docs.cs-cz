---
title: Cortana intelligence | Dokumentace Microsoftu
description: Publikováním nabídky na Cortana intelligence.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 74cde720af012b3355b813cf6da2b2bdf10b9b8e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257203"
---
# <a name="publish-a-cortana-intelligence-offer-using-the-cloud-partner-portal"></a>Publikovat nabídku na Cortana Intelligence pomocí portál Cloud Partner

Tento článek popisuje, jak publikovat nabídku na Cortana Intelligence pomocí portál partnerů cloudu.

## <a name="prerequisites"></a>Požadavky

Portál Cloud Partner podporuje přístup na základě rolí na portál, který umožňuje přispěvatelům spolupracovat na publikování nabídky. Další informace najdete v tématu [cloudu portálu spravovat uživatele](./cloud-partner-portal-manage-users.md).

Před publikováním nabídky jménem vydavatel účtu, jeden z uživatelé, kteří mají \"vlastníka\" role je potřeba souhlasit s [Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/), [prohlášení o ochraně osobních údajů Microsoft](https://www.microsoft.com/privacystatement/default.aspx), a [smlouva k programu Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="to-start-creating-a-cortana-inteligence-offer"></a>Chcete začít vytvářet nabídky Cortana Inteligence

Jakmile jsou splněné všechny požadavky, jste připravení začít vytváření nabídkou Cortana Inteligence.

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/).
2.  V levém navigačním panelu vyberte **+ nová nabídka**.
3. Vyberte **Cortana Intelligence**.
4. Vyberte **Editor** kartu **nová nabídka** zobrazení konfigurujte následující možnosti:
    -   Nabídka nastavení
    -   Technické informace
    -   Prezentace podrobnosti
    -   Kontakty

    Každý z předchozích možností zobrazení, která při vyplňování formuláře.  Povinné pole pro každý formulář jsou označené červenou hvězdičkou (\*).

## <a name="to-configure-offer-settings"></a>Ke konfiguraci nastavení nabídky

Nastavení nabízejí poskytuje základní informace týkající se nabídky, jako je například ID nabídky, ID vydavatele a název nabídky.

### <a name="offer-id"></a>ID nabídky

Toto je jedinečný identifikátor nabídky v rámci profilu vydavatele.
Toto ID je viditelný v adresách URL produktu. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. 
>[!Note]
>Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

**Příklad:**

Pokud vydavatel **contoso** vytvoří v rámci nabídky s ID nabídky *ukázka Cortana Intelligence*, se zobrazí v AppSource jako "https://appsource.microsoft.com/marketplace/apps/\**contoso\**.\*Ukázka Cortana Intelligence\*? tab = Overview ".

### <a name="publisher-id"></a>ID vydavatele

Rozevírací seznam profilů vydavatele. Používá se při volbě vydavatele profil, který chcete publikovat v rámci této nabídky.

>[!Note]
>Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

### <a name="name"></a>Název

Zobrazovaný název nabídky. Tento název se zobrazí v [AppSource](https://appsource.microsoft.com). Může mít maximálně 50 znaků.

Po dokončení poskytující informace, které potřebujete v nabízejí nastavení, vyberte **Uložit** přejděte k části technické informace o nabídky. možnost.

## <a name="to-configure-technical-info"></a>Technické informace o konfiguraci

Pomocí tohoto zobrazení poskytují technické informace, které se zobrazí na stránce nabídky. Následující pole použít pro toto zobrazení.

### <a name="partner-mpn-id"></a>Id MPN partnera.

Pokud si nejste registrovaná partnera společnosti Microsoft, přejděte [partneři webu](https://partners.microsoft.com/) a přihlaste se k získání ID vaší organizace partnera. Zadejte pro toto ID **partnerů programu MPN Id**.

### <a name="analytics-components-used"></a>Součástí analýzy použitých

Vyberte všechny součásti, které používá vaše nabídky. Vyberte aspoň 1 komponentu. Vyberte **Microsoft R** jenom v případě, že používáte některou z paní licence SQL 2016 R services, HDInsight Premium nebo paní běžící na virtuálních počítačích.

### <a name="additional-components-used"></a>Další součásti, které používají

Vyberte všechny ostatní součásti, které používá vaše řešení.

### <a name="customer-name-using-solution"></a>Jméno zákazníka pomocí řešení

Zadejte jméno zákazníka, který používá toto řešení v produkčním prostředí. 

>[!Note]
>Tyto informace není publikována na AppSource. Používá se pouze pro vyhodnocení řešení.

### <a name="azure-consumption-requirement-met"></a>Splněn požadavek využití Azure?

Určuje, jestli řešení generuje alespoň $1 tisíc za měsíc na zákazníka součástí sady Azure, nebo pokud se toto řešení využívá Microsoft R.

>[!Note]
>Tyto informace není publikována na AppSource. Používá se pouze pro vyhodnocení řešení.

### <a name="demo-video-url"></a>Adresa URL videa Demo

Zadejte adresu URL videa pro řešení/aplikaci na ukázku, můžete předem prodejní tým zobrazit zákazníky. 

>[!Note]
>Tyto informace není publikována na AppSource. Používá se pouze pro vyhodnocení řešení.

#### <a name="demo-video-guidelines"></a>Pokyny pro ukázkové video

- Video délka by měla být menší než 15 minut.
- Video by měl být upraven minimálně záznam funkcí řešení. 
- Video světel klíčové aspekty funkcí přístupných a se zaměřuje na integraci pokročilých analýz. 
- Ukázka videa **nebudou** být zpřístupněn veřejně zákazníkům, ale očekává představovat způsob řešení *by* zobrazí potenciálního zákazníka. V důsledku toho by měl být skriptované a opakovatelné.
- Použijte libovolný nástroj pro záznam obrazovky, který exportuje standardní formát videa (například MPEG) k vytvoření vašeho videa. 

Následující pokyny ukazují, jak vytvořit videa pomocí Skypu pro firmy. 

1. [Začněte na schůzce](https://support.office.com/article/Start-a-Skype-for-Business-conference-call-8dc8ac52-91ac-4db9-8672-11551fdaf997)
2. [Nasdílejte svoji plochu](https://support.office.com/article/Share-your-screen-in-Skype-for-Business-2d436dc9-d092-4ef1-83f1-dd9f7a7cd3fc)
3. [Začátek nahrávání](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232)
4. Poté, co zastavíte záznam, [publikovat nahrávání pomocí Správce nahrávání](https://support.office.com/article/Recording-Manager-save-and-publish-59a3beb7-c700-40cf-ab21-bc82a2b06351)

Nahrané video nahrajte na službu, která umožňuje vygenerovat sdílený adresy URL. Například [odkaz hostovaného na Onedrivu nebo Sharepointu](https://support.office.com/article/Share-sites-or-documents-with-people-outside-your-organization-80e49744-e30f-44db-8d51-16661b1d4232).

### <a name="supported-regions"></a>Podporované oblasti

Vyberte všechny oblasti, které podporuje vaše řešení. Vyberte aspoň 1 oblast.

### <a name="power-bi-desktop-file-pbix"></a>Klasické pracovní plochy soubor Power BI (.pbix)

Pokud je k dispozici, nahrajte soubor .pbix. Ujistěte se, že data se importují do souboru, neodkazuje externě. Vložené sestavy vytvoříme za vás.

### <a name="solution-architecture"></a>Architektury řešení

Nahrajte dokument, který podrobně popisuje architekturu vašeho řešení. Odesílání vodítek diagram architektury řešení, najdete v části [šablony pracovního postupu řešení pokročilé analýzy](https://partnersprofilesint.blob.core.windows.net/partner-assets/documents/AppSource%20Solution%20Publishing%20Guide%20Docs/Advanced%20Analytics%20Solution%20Workflow%20Template%20V.2017.3.23.pptx).

>[!Note]
>Tyto informace není publikována na AppSource. Používá se pouze pro vyhodnocení řešení.

### <a name="select-segments"></a>Vyberte segmenty

Vyberte všechny segmenty související odvětví. Pokud je vaše aplikace pro všechny neuvedené segment, zadejte název do segmentů **ostatní** pole. Jste omezeni na tři slova segmentu.

### <a name="select-business-processes"></a>Vyberte obchodní procesy

Vyberte obchodní procesy, které nejlépe popisují vaše řešení. Pokud je vaše aplikace pro všechny procesy, neuvedené v seznamu, zadejte název do procesu **ostatní** pole. Jste omezeni na tři slova pro proces.

### <a name="trial-info"></a>Informace o zkušební verzi

-   **Adresa URL zkušební verze SaaS:** zadejte adresu URL pro zkušební prostředí vaší aplikace.
-   **Adresa URL pro test jednotky zkušební verze:** zadejte adresu URL pro vaši aplikaci Test Drive prostředí.

Další informace o zkušebních verzích najdete v tématu **typ aplikace** v další části tohoto článku.

Po dokončení poskytující informace, které potřebujete v technické informace, vyberte **Uložit** přejděte k části z prodejních míst podrobnosti nabídky. 

## <a name="to-configure-storefront-details"></a>Nakonfigurujte podrobnosti výkladní skříň

### <a name="offer-summary"></a>Souhrn nabídky

Toto je souhrn vaší nabídky návrh hodnoty. Zobrazí se na stránce hledání vaší nabídky. Maximální délka souhrn je 100 znaků.

### <a name="offer-description"></a>Popis nabídky

Toto je popis, který se zobrazí na stránce podrobností aplikace.
Maximální délka popisu je 1300 znaků.

Všimněte si, že toto pole přijímá html obsahu se značkami jako \<p\>, \<h1\>, \<h2\>, \<li\>, atd., což umožňuje mnohem více zajistit pro obsah přístupné. Publikování portálu tým pracuje na přidávání funkce, která umožňují zobrazit náhled svých prezentace podrobnosti zavádět postupně díky čemuž je obsah více prezentovatelný – mezitím můžete použít kterýkoli můžete použít libovolný online v reálném čase html nástrojů, jako je [ http://htmledit.squarefree.com ](http://htmledit.squarefree.com/) zobrazíte, jak váš popis vypadat.

Doporučený formát popis je mít rozdělit na dílčí oddíly na základě hodnoty text propositions, každý s zvýrazněná dílčí záhlaví. Návštěvníci obvykle přehled nad pole "nabízejí souhrn" a dílčí záhlaví, chcete-li získat gist adres aplikace a proč by měl považují za aplikace v právě rychlého přehledu. Proto je důležité získat uživatele\'pozornost s jejich udat důvod ke čtení na zobrazíte podrobnosti.

#### <a name="partner-examples"></a>Příklady partnera

- [Optimalizace inventáře Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview)
- [Přizpůsobení Plexure maloobchodního prodeje](https://appsource.microsoft.com/product/web-apps/plexure.c82dc2fc-817b-487e-ae83-1658c1bc8ff2?tab=Overview)
- [Služby pro občany AvePoint](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview)

### <a name="industries"></a>Odvětví

Vyberte odvětví, že vaše aplikace je nejlepší zarovnán. Pokud vaše aplikace má vztah k několika oborech, nechte pole prázdné.

### <a name="categories"></a>Kategorie

Vyberte příslušné kategorie, které jsou relevantní pro vaši aplikaci. Vyberte maximálně dvě kategorie.

### <a name="app-type"></a>Typ aplikace

Vyberte typ zkušební verzi, která bude podporovat aplikace na AppSource. Zvolte na následující testy:
- **Bezplatné** znamená, že vaše aplikace je zdarma.
- **Zkušební verze** znamená, že zákazníci si můžou vyzkoušet vaše během zadaného období.
- **Požádat o zkušební verzi** znamená, že zákazníci můžou požádat o zkušební verzi aplikace.

Partneři můžete poskytnout dva druhy prostředí pro zkušební verze na AppSource.

- **Zkušební verze** možnost se jmenuje také **zákazníka vedla zkušební verze (CL)** může být jedna z následujících typů: 
    - Zkušební verze SaaS
        - Odběratele můžete přejít na adresu Url, která poskytují vám nebo vašemu partnerovi. Zákazník prochází AAD federovaný, jednotné přihlašování mít vždy v poli zkušební prostředí.
        - Toto je aplikace SaaS s více tenantů, který izoluje uživatele konfigurace nebo dat od jiných uživatelů. Nebo toto prostředí se poskytuje pouze podmnožinu, která používá operací jen pro čtení.

        **Příklady:**

        - [Provádění maloobchodních POP AFS](https://appsource.microsoft.com/product/web-apps/afs.fa9fc926-3bc3-43dd-becd-3ef41b52c10b?tab=Overview)
        - [Služby pro občany avepointu poskytovat](https://appsource.microsoft.com/product/web-apps/avepoint.7738ac97-fd40-4ed3-aaab-327c3e0fe0b3?tab=Overview). (Tato aplikace poskytuje způsob s cestami k Vymazat vybrané sady osob uživatele.)

     - Testovací verze
        - Vaše (nebo váš partner) řešení, nebo podmnožina to se dá zabalit pomocí šablon Azure Resource Manageru, které lze vytvořit instanci AppSource a. AppSource můžete spravovat aplikaci v rámci předplatného na partnera s časem zabalení a udržování horké/studené fond instancí atd.
        - Můžeme poskytnout šablony a ukázky kódu pro případ, že zvolíte tuto možnost.

        **Příklady:**

        - [Optimalizace inventáře Neal Analytics](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview&tag=CISHome)

- **Žádost o zkušební verzi** (**zkušebních verzí vedla partnera / čas načtení stránky**) možnost vyžaduje, aby zákazníci k vyplnění formuláře kontaktní informace pro zpracování partnera. Partnera sleduje a poskytuje ukázku nebo zkušební verze aplikace. Další informace najdete v tématu [AppSource zkušební prostředí návod](https://aka.ms/trialexperienceforwebapps) videa pro základní přehled.

>[!Note]
>Data, která ukazuje **zkušebních verzí vedla zákazníka** mají vyšší generování potenciálních zákazníků potenciální než **zkušebních verzí vedla partnera**.


### <a name="help-link-for-your-app"></a>Odkaz na nápovědu pro vaši aplikaci

Zadejte adresu URL na stránku, která obsahuje informace o vaší aplikace.

### <a name="supported-countriesregions"></a>Podporované země/oblasti

Toto pole určuje zemích nebo oblastech, kde bude k dispozici pro zkušební verzi vaší nabídky.

![Podporované země/oblasti](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot3.png)

### <a name="supported-languages"></a>Podporované jazyky

Vyberte jazyky, které vaše aplikace podporuje. Pokud vaše aplikace podporuje jazyky, které nejsou v tomto seznamu, publikování vaší nabídky a pošlete nám e-mail na: <appsource@microsoft.com> a dejte nám vědět o podpoře pro jiné jazyky.

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze pro vaši aplikaci.

### <a name="products-your-app-works-with"></a>Produkty, které vaše aplikace funguje s

Seznam konkrétní produkty, které vaše aplikace funguje se. Můžete vytvořit seznam maximálního počtu 3 produktů. Pro zobrazení seznamu produktu, vyberte **znaménko plus** (vedle nový) a vytvoří se pro vás otevřít textové pole. Zadejte název produktu, který vaše aplikace funguje se.

### <a name="hide-key"></a>Skrýt klíče

Je to klíč v kombinaci s adresou URL náhledu nabídky ke skrytí nabídky z veřejného zobrazení. Není heslo. Můžete zadat libovolný alfanumerický řetězec.

### <a name="offer-logo-small"></a>Nabídka logo (malé)

Toto logo se zobrazí na stránce hledání vaší aplikace. Formát obrázku png je pouze formát, který je povolen. Velikost obrázku je 48 x 48 pixelů.

### <a name="offer-logo-large"></a>Nabídka logo (velká)

Toto logo se zobrazí na stránce podrobností aplikace. Formát obrázku png je pouze formát, který je povolen. Velikost obrázku je 48 pixelů x 48 pixelů.

### <a name="video"></a>Video

Můžete nahrát maximálně čtyři videa. Pro každé video chcete nahrát:
- Zadejte název videa
- Zadejte adresu URL (YouTube nebo Vimeo jenom)
- Zadejte na miniaturu pro přidružení k videu. Miniatura musí používat formát obrázku png a jeho velikost musí být v pixelech 1280 X 720 pixelů. 

Chcete-li přidat nový video(s) **+ nová**, jak je znázorněno v následující snímek obrazovky.

![Přidání nového videa](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot4.png)

### <a name="document"></a>Dokument

Můžete nahrát maximálně tři dokumenty. Každý dokument musíte použít formát souboru PDF. Chcete-li přidat nový dokument:

- Vyberte **+ nová**
- Zadejte název dokumentu
- Vyberte **nahrát** k nahrání dokumentu.

![Přidat nový dokument](./media/cloud-partner-portal-publish-cortana-intelligence-app/CISScreenshot5.png)

### <a name="privacy-policy"></a>Zásada zabezpečení

Zadejte adresu URL pro zásady ochrany osobních údajů vaší aplikace

### <a name="terms-of-use"></a>Podmínky použití

Zadejte podmínky použití vaší aplikace. Zákazníci AppSource je potřeba přijmout tyto podmínky předtím, než se pokusí vaší aplikace.

>[!Note]
>Toto pole přijímá obsahu v jazyce HTML značky jako je třeba obsah html < p\>, < h1\>, a < li\>. Tyto značky můžete použít k formátování obsahu. 

### <a name="lead-destination"></a>Vedoucí cíl

Vyberte systému CRM, kam se budou ukládat vaše potenciálních zákazníků. 

Vyberte **Azure Table** Pokud pracujete s některou z těchto systémů CRM: Microsoft Dynamics CRM, Salesforce nebo Marketo. 

Pokud chcete získat další informace o systému CRM, které chcete použít, vyberte jednu z podporovaných systémů prostřednictvím následujících odkazů.

-   [Tabulka Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md)
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md)
-   [Microsoft Dynamics CRM](./cloud-partner-portal-lead-management-instructions-dynamics.md)
-   [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md)
