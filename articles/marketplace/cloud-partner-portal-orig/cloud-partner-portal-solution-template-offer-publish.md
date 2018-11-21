---
title: Publikování šablony řešení Azure | Dokumentace Microsoftu
description: Na webu Azure Marketplace publikujte šablony řešení.
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
ms.date: 11/15/2018
ms.author: pbutlerm
ms.openlocfilehash: 333eebfa1bae919c43164572c63f2de4f7251fe0
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52261613"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Publikování šablony řešení na Azure Marketplace

Tento článek popisuje kroky pro publikování šablony řešení nabízet na webu Azure Marketplace.

## <a name="prerequisites"></a>Požadavky

Následující technická a Netechnická požadavky platí pro výpis šablonu řešení na webu Azure Marketplace.

### <a name="technical"></a>Technická

- [Pochopení šablony Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).
- Šablony rychlý start Azure:
    - [Dokumentaci k šabloně Azure pro rychlý start](https://azure.microsoft.com/documentation/templates/)
    - [Dokumentace ke službě Azure quickstart na Githubu](https://github.com/azure/azure-quickstart-templates)
 - [Vytvořit soubor rozhraní uživatele Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
 - Povolit [zákazníka využití attribution](./../azure-partner-customer-usage-attribution.md) ke sledování využití Azure pro zákaznická nasazení softwaru na Azure.

### <a name="non-technical-business-requirements"></a>Netechnická (požadavky)

- Vaše společnost (nebo její pobočka) se musí nacházet v prodeji ze země podporovanými webem Azure Marketplace.
- Váš produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace podporovanými webem Azure Marketplace.
- Zodpovídáte zpřístupnění pro technickou podporu pro zákazníky přiměřené způsobem, zda zdarma, placená nebo komunitní podpory.
- Zodpovídáte za licencování váš software a všechny závislosti softwaru třetích stran.
- Zadejte obsah, který splňuje kritéria pro vaší nabídky na webu Azure Marketplace a na webu Azure Portal.
- Souhlas s podmínkami zásady účasti na Azure Marketplace a smlouvě s vydavatelem.
- Vyjádřete souhlas s dodržováním Podmínek použití, Prohlášení o zásadách ochrany osobních údajů a Smlouvy k programu Microsoft Azure Certified.

## <a name="before-you-begin"></a>Než začnete

Po splnění všech požadavcích, můžete spustit vytváření šablony řešení nabídky. Než začnete, projděte si následující nabídky a informace o SKU.

**Nabídka**

V rámci nabídky Azure aplikace odpovídá třídu produktu, nabídky od vydavatele. Pokud máte nový typ řešení/aplikaci, která byste chtěli být k dispozici na webu Azure Marketplace, je nová nabídka nejlepším řešením. Nabídka je vlastně kolekce SKU. Každé nabídky se zobrazí jako vlastní entity na webu Azure Marketplace.

**SKU**

SKU je nejmenší zakoupitelná jednotka nabídky. V rámci stejné třídy produkt (nabídku), skladové položky vám umožní rozlišovat mezi různé podporované funkce. Například spravované nabídky nebo nespravované a různé modely fakturace jsou podporovány.

Přidání více jednotek SKU v následujících scénářích:
- Chcete zajistit podporu různými modely fakturace, jako je například používání vlastní licence (BYOL) nebo průběžných plateb (PAYG).
- Každý SKU podporuje různé funkce a každá sada funkcí se cenově liší.

Skladová položka se zobrazí v případě nadřazené nabídky na webu Azure Marketplace, se zobrazuje jako vlastní entita určené k prodeji na webu Azure portal.

## <a name="to-create-a-new-offer"></a>Chcete-li vytvořit novou nabídku

1.  Přihlaste se k [portál partnerů cloudu](http://cloudpartner.azure.com/).

2.  V levém navigačním panelu vyberte **+ nová nabídka**a pak vyberte **aplikací Azure**.

    ![Vytvořit novou nabídku](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  V části **nová nabídka**vyberte **Editor**.

    ![Nová nabídka editoru](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  V části **Editor**, budete poskytovat informace v následující zobrazení:
    - Nabídka nastavení
    - Skladové položky
    - Marketplace
    - Podpora

Každé zobrazení obsahuje sadu polí umožňující vyplnit. Povinné pole jsou označena červená hvězdička (\*)

## <a name="to-configure-offer-settings"></a>Ke konfiguraci nastavení nabídky

1. Nakonfigurujte následující **nabízejí Identity** polí v nabídky nastavení.

    **ID nabídky**

     Jedinečný identifikátor nabídky v rámci profilu vydavatele. Toto ID je viditelný v adresách URL produktu, šablon Azure Resource Manageru, a oznámí fakturace. Můžete použít pouze malé alfanumerické znaky nebo spojovníky (-). ID nesmí končit spojovníkem a nesmí být delší než 50 znaků. 
    >[!Note]
    >Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

    **ID vydavatele**

    Rozevírací seznam pro profil vydavatele. Zvolte profil, který chcete publikovat nabídku v rámci. 
    >[!Note]
    >Toto pole je uzamčen, když dostane za provozu v rámci nabídky.

    **Název**

    Zobrazovaný název nabídky. Tento název se zobrazí na webu Azure Marketplace a na webu Azure portal. Může mít maximálně 50 znaků. Použijte následující pokyny pro název nabídky:
    -  Do názvu zahrňte rozpoznatelný název značky pro váš produkt. 
    - Nezahrnujte název společnosti, pokud je jak nabídku na trh.
    - Pokud v rámci této nabídky máte marketing na svůj vlastní web, ujistěte se, že název shodný s názvem na vašem webu.

2. Vyberte **Uložit** dokončete nabídky nastavení.
pro vaši nabídku.

## <a name="to-create-skus"></a>K vytvoření skladové položky
------------------

1. Vyberte **SKU**. 

    ![Nové SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU ID je jedinečný identifikátor pro danou skladovou jednotku v rámci nabídky. Tento identifikátor se zobrazuje na adresách URL produktu, v šablonách Resource Manageru a ve fakturačních sestavách. SKU ID:
    - Pouze nesmí být delší než 50 znaků.
    - Můžete pouze obsahovat malé alfanumerické znaky nebo spojovníky (-).
    - Nemůže končit pomlčkou.

    >[!Note]
    >Po přidání SKU, se zobrazí v seznamu skladových položek v zobrazení skladové položky. Chcete-li zobrazit podrobné údaje SKU, vyberte název skladové položky. 

2. Vyberte **novou skladovou Položku** poskytnout informace zobrazené na následujícím snímku obrazovky. 

    ![Podrobné údaje SKU](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Nastavení SKU

Zadejte následující nastavení SKU.

- **Název** – název pro danou skladovou jednotku. Tento název se zobrazí v galerii pro tuto položku.
- **Souhrn** – krátký souhrnný popis skladové Položce. (Maximální délka je 100 znaků.)
- **Popis** – podrobný popis SKU.
- **Typ SKU** – rozevírací seznam s těmito hodnotami: "Spravované aplikace (Preview)" a "Šablona řešení". V tomto scénáři vyberte **šablonu řešení**.
- **Dostupnost cloudu** – umístění SKU. Výchozí hodnota je **veřejný Azure**.

### <a name="package-details"></a>Podrobnosti o balíčku

Po dokončení nastavení SKU, zadejte následující podrobnosti balíčku.

![Podrobnosti o balíčku](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Aktuální verze** – verze balíčku, který bude nahrávat. Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- **Soubor balíčku** -tento balíček obsahuje následující soubory, které jsou uloženy do souboru .zip.
    -   MainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikace a vytvořte prostředky, které jsou definovány pro řešení. Další informace najdete v tématu [jak si můžete vytvořit soubory šablony nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
    -   createUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro zřizování řešení/aplikaci na webu Azure portal. Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro spravované aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Tento balíček měl obsahovat všechny vnořené šablony nebo skripty, které jsou nutné ke zřízení této aplikace. MainTemplate.json a createUIDefinition.json musí být v kořenové složce.

## <a name="to-configure-the-marketplace"></a>Konfigurace na webu Marketplace

Použít ke konfiguraci pole, která se zobrazují pro nabídky na Marketplace zobrazení [Azure Marketplace](https://azuremarketplace.microsoft.com) a na [webu Azure portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID předplatných ve verzi Preview

Seznam ID předplatných Azure, který chcete mít přístup do nabídky při publikování nabídky. Tato předplatná uvedená prázdné využijete k otestování zobrazená v náhledu nabídka dříve než přidáte za provozu. Portál pro partnery můžete do seznamu povolených až 100 předplatných.

### <a name="suggested-categories"></a>Navrhované kategorií

Vyberte až o pěti kategorií ze zadaného seznamu, který vaše nabídka může být nejlepší přidružený. Vybrané kategorie se použije k mapování vaší nabídky na o produktových kategoriích, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [webu Azure Portal](https://portal.azure.com/).

Následující příklady ukazují informace z marketplace na webu Azure Marketplace a na webu Azure portal.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Loga

Postupujte podle následujících pokynů loga nahráli na portál partnerů cloudu:

-   Design Azure má jednoduchou paletu barev. Zachovat počet primární a sekundární barvy na vaše logo nízké.

-   Jsou bílé barvy motivu na webu Azure portal a černé. Nepoužívejte tyto barvy jako barva pozadí vašeho loga. Použijte barvu, která by provedla vašeho loga viditelného na webu Azure Portal. Doporučujeme použít jednoduché primární barvy.

    >[!Note] 
    >Pokud pracujete s průhledným pozadím, ujistěte se, že nejsou loga/text bílé, černá a modrá.

-   Nepoužívejte v logu pozadí s barevným přechodem.

-   Předejde text loga. Toto pravidlo obsahuje vaše společnost nebo název značky. Vzhled a chování vašeho loga musí být *plochý* a přechody se měli vyhnout.

-   Logo by neměl být roztažená.

#### <a name="hero-logo"></a>Hero Logo

Hero logo je volitelné. Vydavatele můžete nahrávat Hero logo. Ale po nahrání loga ho nelze odstranit. Partner musí postupovat podle pokynů Azure Marketplace pro Hero ikony.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Pokyny pro ikona loga Hero

-   Zobrazovaný název vydavatele, název plánu a nabídky, dlouhé shrnutí jsou zobrazeny pomocí bílé barvy písma. Vyhněte se použití jakékoli světlé barevné na pozadí. Černou, bílou a průhledné pozadí nejsou povoleny pro Hero ikony.

-   Vydavatel zobrazovaný název, plán, title, nabídky, dlouhé shrnutí a tlačítka pro vytvoření jsou vložené prostřednictvím kódu programu uvnitř Hero logo při jejím uvedené. Při navrhování Hero logo, nezadávejte žádný text. Ponechte prázdné místo na pravé straně loga. Tento prostor by měl být 415 × 100 pixelů a je posunut 370 pixelů od levého okraje.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Konfigurace podpory

Pomocí zobrazení podpory uveďte následující informace:

- Kontaktní údaje z vaší společnosti, jako je například technikům podpory.
- Kontakty na podporu zákazníků.

## <a name="to-publish-the-offer"></a>Publikování nabídky

Posledním krokem je publikování nabídky. Vyberte **Publikovat**.
