---
title: Publikování spravované aplikace Azure na webu Azure Marketplace
description: Publikování spravované aplikace Azure na webu Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bc044c8b59c939163336ecab01546fc26a7a2643
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809361"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Publikování aplikace spravované v Azure na webu Azure Marketplace 
========================================================

Tento článek uvádí různé kroky při publikování nabídky spravovaných aplikací na webu Azure Marketplace.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Předpoklady pro publikování spravované aplikace 
---------------------------------------------------

Požadavky na výpis na webu Azure Marketplace

1.  Technická

    -   [Tvorba šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Šablony rychlý start Azure:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Vytvoření definice uživatelského rozhraní

        -   [Vytvoření souboru definice uživatelského rozhraní](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Netechnická (požadavky)

    -   Vaše společnost (nebo její pobočka) se musí nacházet v zemi původu Zákazník podporovanými webem Azure Marketplace

    -   Váš produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace podporovanými webem Azure Marketplace

    -   Zodpovídáte za poskytování technické podpory vašim zákazníkům: zdarma, placená nebo prostřednictvím podpory prostřednictvím komunity.

    -   Zodpovídáte za licencování váš software a všechny závislosti na softwaru třetích stran.

    -   Je nutné zadat obsah, který splňuje kritéria pro vaší nabídky na webu Azure Marketplace a portálu pro správu Azure

    -   Musíte souhlasit s podmínkami zásady účasti na Azure Marketplace a smlouvě s vydavatelem

    -   Musíte souhlasit s v souladu s podmínkami použití, prohlášení o ochraně osobních údajů Microsoft a Microsoft Azure Certified Program Agreement.

<a name="how-to-create-a-new-azure-application-offer"></a>Vytvoření nové nabídky aplikace Azure 
-------------------------------------------

Jakmile jsou splněné všechny požadavky, jste připravení začít s nabídkou na spravované aplikace pro vytváření. Než začneme, rychlý přehled toho, nabídka a skladová jednotka

**Nabídka**

V rámci nabídky Azure aplikace odpovídá třídu produktu, nabídky od vydavatele. Pokud máte nové řešení/aplikaci k publikování na webu Azure Marketplace, nové nabídky je způsob přechodu. Nabídka je vlastně kolekce SKU. Každé nabídky se zobrazí jako vlastní entity na webu Azure Marketplace.

**SKU**

Skladová jednotka je nabídka nejmenší prodejných jednotce je přidružená. V rámci stejné třídy produkt (nabídku), skladové položky vám umožní rozlišovat mezi různé funkce, které jsou podporovány, zda tato nabídka je spravovaná nebo nespravovaná a modely fakturace.

Pokud jste chtěli podporu různými modely fakturace přidat více jednotek SKU: například používání vlastní licence (BYOL), průběžných plateb (PAYG) atd. 

Přidání více jednotek SKU při každé skladovou Položku podporuje jiné sady funkcí a jinak ceny.

Skladová položka se zobrazí v nadřazené nabídky na webu Azure Marketplace a zobrazí se jako vlastní entita určené k prodeji na azure.com.

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com).

2.  V levém navigačním panelu klikněte na \"+ nová nabídka\" a vyberte \"aplikací Azure\".

    ![Nová nabídka](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Nová nabídka \"Editor\" zobrazení je teď otevřít, a můžete začít vytváření obsahu.

4.  \"Forms\" jsou viditelné na levé straně v rámci, které je potřeba doplnit \"Editor\" zobrazení. Každý \"formuláře\" sestává ze sady polí, která se mají doplnit. Povinné pole označená atributem červená hvězdička (\*).

    > Existují 4 hlavní formuláře pro vytváření spravované aplikace

    -   Nabídka nastavení

    -   Skladové položky

    -   Marketplace

    -   Podpora

<a name="how-to-fill-out-the-offer-settings-form"></a>Jak vyplnit formulář pro nastavení nabídky 
---------------------------------------

Formulář pro nastavení nabídky je základní formulář a zadejte nastavení nabídky.
Různá pole jsou popsané níže.

**ID nabídky**

Jedinečný identifikátor nabídky v rámci profilu vydavatele.
Uvidí v adresách URL produktu, šablon Resource Manageru, a oznámí fakturace. Pouze malé alfanumerické znaky nebo spojovníky (-) jsou povoleny. Nemůže končit pomlčkou nebo být větší než maximálně 50 znaků. Toto pole je uzamčen, jakmile uvedete nabídky.

**ID vydavatele**

Tento rozevírací seznam umožňuje zvolit profil vydavatele, který chcete publikovat v rámci této nabídky. Toto pole je uzamčen, jakmile uvedete nabídky.

**Název**

Zobrazovaný název nabídky. Název, který se zobrazí na webu Azure Marketplace a na webu Azure portal. Může mít maximálně 50 znaků. Pokyny v tomto poli se zahrnou rozpoznat název značky pro tento produkt. Nezahrnujte název společnosti, pokud to je, jak je na trh. Pokud v rámci této nabídky jsou marketing na svůj vlastní web, ujistěte se, že název je přesně jak zobrazí na vašem webu.

Klikněte na \"Uložit\" uložte svůj postup. Dalším krokem je přidání SKU pro vaši nabídku.

<a name="how-to-create-skus"></a>Postup vytvoření skladové položky 
------------------

Klikněte na \"SKU\" formuláře. Zde můžete zobrazit možnost \"přidejte skladovou jednotku\" pak kliknete na který vám umožní zadat \"SKU ID\".

![Nová nabídka skladové položky](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU ID\" je jedinečný identifikátor pro danou skladovou jednotku v rámci nabídky. Toto ID se nebude zobrazovat v adresách URL produktu, šablony ARM, a oznámí fakturace. Může se skládat jenom z malých alfanumerických znaků nebo pomlček (-).
Nesmí končit spojovníkem a nesmí být delší než 50 znaků. Toto pole je uzamčen, jakmile uvedete nabídky. V rámci nabídky může existovat několik skladových položek. Potřebujete SKU pro každý obrázek, který máte v úmyslu publikovat.

Jakmile se skladová položka se přidala, se zobrazí v seznamu skladových položek v rámci \"SKU\" formuláře. Klikněte na název skladové položky na ponoříte do detailů dané konkrétní skladové jednotce. Tady jsou některé podrobnosti pro některá pole.

Po kliknutí na \"novou skladovou Položku\", je potřeba vyplnit následující tvar:

![Nová nabídka - novou skladovou Položku](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Jak vyplnit části Podrobné informace o Sku 

**Název** – poskytuje název pro tuto Sku. Je to, co se zobrazí v galerii pro tuto položku.

**Souhrn** – poskytuje krátký popis pro tuto sku. Tento text se zobrazí přímo pod nadpisem.

**Popis** – poskytuje podrobný popis, o které skladová jednotka Potřebuje.

**Typ SKU** – povolené hodnoty jsou \"spravované aplikace\" a \"šablony řešení\". V takovém případě vyberete \"spravované aplikace\".

### <a name="how-to-fill-package-details-section"></a>Jak vyplnit části Podrobnosti o balíčku 

Balíček část obsahuje následující pole, které musí být vyplněno.

![Nová nabídka – nový balíček pro SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Aktuální verze** – obsahuje verzi balíčku, bude nahrávat.
By měl být ve formátu.

**Soubor balíčku** – balíček obsahuje následující soubory, které jsou komprimované do souboru .zip.

applianceMainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikaci a prostředky definované v jeho vytvoření. Můžete najít další podrobnosti o tom, jak vytvořit nasazení souborů šablon zde- <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro vytváření tohoto řešení nebo aplikace na portál na adrese azure.com. Můžete najít další podrobnosti o tom, jak vytvořit tento soubor zde- <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json – soubor šablony, který obsahuje pouze příslušný prostředek Microsoft.Solution/appliances. Vlastnosti klíče tohoto prostředku je potřeba vědět jsou následující:

-   \"druh\" – hodnota musí být \"Marketplace\" v případě scénáře Marketplace spravované aplikace
-   \"ManagedResourceGroupId\": Skupina prostředků v zákazník\'nasazená všechny prostředky, které jsou definované v applianceMainTemplate.json předplatné s.
-   \"ID balíčku vydavatele\": řetězec, který jednoznačně identifikuje balíček. 

Hodnota musí být konstruován následujícím způsobem – je tvořen \[publisherId\].\[ OfferId\]-preview\[SKUID\].\[ PackageVersion\].
Každou z těchto hodnot je možné získat, jak je znázorněno níže.

Tento balíček měl obsahovat žádné vnořené šablony nebo skripty požadované k úspěšné zřízení této aplikace. MainTemplate.json, applianceMainTemplate.json a applianceCreateUIDefinition.json se musí nacházet v kořenové složce.

**Povolení** – tato vlastnost definuje kdo získává přístup a jaké úroveň přístupu k prostředkům v předplatných zákazníků. To umožňuje vydavatele ke správě aplikace jménem zákazníka.

-   PrincipalId – Azure Active directory identifikátor uživatele, skupiny uživatelů nebo aplikaci, která bude udělen určitá oprávnění (jak je popsáno v definici Role) v prostředcích v předplatném zákazníka.

-   Definice role – seznam všech předdefinované role RBAC poskytuje Azure AD. Můžete vybrat nejvhodnější, role, které vám umožní spravovat prostředky jménem zákazníka.

Je možné přidat více autorizace. Ale doporučuje se vytvořit novou skupinu uživatelů služby Active Directory a zadejte jeho ID v \"PrincipalId\."  Tato možnost povolí přidávání více uživatelů na skupinu uživatelů, aniž byste museli aktualizovat skladovou Položku.

Další informace o RBAC najdete tady – <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Marketplace formuláře
----------------

Marketplace formuláři v rámci nabídky azure aplikace vyzve k zadání polí, která se zobrazí na [Azure Marketplace](https://azuremarketplace.microsoft.com) a na [webu Azure Portal](https://portal.azure.com/). Tady jsou některé podrobnosti pro některá pole.

#### <a name="preview-subscription-ids"></a>ID předplatných ve verzi Preview

Zadejte seznam ID předplatného Azure, které chcete mít přístup do nabídky publikovaný produkt. Tato předplatná uvedená prázdné vám umožní testovat zobrazená v náhledu nabídka dříve než přidáte za provozu. Portál pro partnery můžete do seznamu povolených až 100 předplatných.

#### <a name="suggested-categories"></a>Navrhované kategorií

Vyberte až o pěti kategorií ze zadaného seznamu, který vaše nabídka může být nejlepší přidružený. Vybrané kategorie se použije k mapování vaší nabídky na o produktových kategoriích, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [webu Azure Portal](https://portal.azure.com/).

Tady jsou některé z míst, která data, které zadáte v tomto formuláři se zobrazí v.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portál na adrese azure.com

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Loga

Postupujte podle všech loga nahrané v portál partnerů cloudu níže uvedených pokynů:

-   Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.

-   Barvy motivu portálu na adrese azure.com jsou bílý a černé. Nepoužívejte tyto barvy jako barva pozadí vašeho loga. Použijte některé barvu, která by provedla vašeho loga viditelného na portálu na azure.com.
    Doporučujeme použít jednoduché primární barvy. **Pokud používáte průhledným pozadím, ujistěte se, že loga a text nejsou bílé, černá nebo modrou.**

-   Nepoužívejte logo barevného přechodu pozadí.

-   Předejde text, dokonce i ve vaší společnosti, nebo vytvoření názvu v zobrazení loga.
    Vzhled a chování vašeho loga musí být \'plochý\' a přechody se měli vyhnout.

-   Vyhněte se roztažení loga.

##### <a name="hero-logo"></a>Hero Logo

Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. Ale jednou nahrané ikonu hero nelze odstranit. Partner v takovém případě musí postupovat podle pokynů Azure Marketplace pro Hero ikony.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Pokyny pro ikona loga Hero

-   Zobrazovaný název vydavatele, název plánu a nabídky, dlouhé shrnutí se zobrazují v barva bílé písma. Vyhněte se zachovat všechny světlé barva pozadí ikony Hero. Černou, bílou a průhledné pozadí nejsou povoleny pro Hero ikony.

-   Při nabídky získá uvedené, uvnitř Hero logo vložené prostřednictvím kódu programu zobrazovaný název vydavatele, název plánu, nabídky, dlouhé shrnutí a na tlačítko vytvořit. Není nutné zadat libovolný text při návrhu Hero logo. Ponechte prázdné mezery na pravé straně pro vydavatele zobrazí název, název plánu, nabídky, dlouhé shrnutí a atd. Jsou zahrnuty z prostřednictvím kódu programu.
    Prázdný prostory pro text by měl být 415 x 100 na pravé straně a zvýšením 370 pixelů od levého okraje.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Fórum podpory
------------

Fórum podpory je další formulář vyplnit. Tento formulář vyzve k zadání kontakty na podporu ve vaší společnosti.  Mezi příklady patří engineering kontaktní informace a kontaktní údaje podpory zákazníků.

<a name="how-to-publish-an-offer"></a>Publikování nabídky
-----------------------

Teď, když je vytvářen koncept vaší nabídky, dalším krokem je publikování nabídky na webu Azure Marketplace. Klikněte na tlačítko \"publikovat\" tlačítko zahájíte proces zpřístupnění vaší nabídky za provozu.
