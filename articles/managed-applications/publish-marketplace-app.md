---
title: "Azure spravované aplikace na webu Marketplace | Microsoft Docs"
description: "Popisuje Azure spravované aplikace, které jsou dostupné přes Marketplace."
services: azure-resource-manager
author: tfitzmac
manager: timlt
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/15/2018
ms.author: tomfitz
ms.openlocfilehash: 61815098a4935983f19b3dcea6e001230aee4771
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Azure spravovaných aplikací na webu Marketplace

Dodavatelé můžete použít Azure spravované aplikace na nabídku svá řešení pro všechny zákazníky využívající Azure Marketplace. Tyto dodavatelé mohou zahrnovat zprostředkovatelé spravované služby (MSPs), nezávislí dodavatelé softwaru (ISV) a systémových integrátorech (si). Spravované aplikace snížit údržby a režijní náklady na údržbu zákazníků. Dodavatelé prodeje infrastruktury a software přes marketplace. Službám a podpoře provozní se můžete připojit k spravovaných aplikací. Další informace najdete v tématu [spravované aplikace přehled](overview.md).

Tento článek vysvětluje, jak můžete publikovat aplikaci na Marketplace s cílem a co nejvíce zpřístupnili zákazníkům.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Předpoklady pro publikování spravované aplikace

K dokončení tohoto článku, musíte už mít soubor .zip vaší spravované aplikaci definice. Další informace najdete v tématu [vytvořit aplikaci služby katalogu](publish-service-catalog-app.md).

Kromě toho existuje několik předpokladů firmy. Jsou to tyto:

* Vaše společnost nebo její dceřiné společnosti, musí být umístěny v určité zemi, kde je podporováno prodeje podle marketplace.
* Tento produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace nepodporuje marketplace.
* Zpřístupní se na technickou podporu zákazníkům vyvineme způsobem. Podporu může být volná, placené, nebo prostřednictvím komunity podporovat.
* Licence softwaru a všechny závislosti software třetích stran.
* Zadejte obsah, který splňuje kritéria pro vaši nabídku uveden v Marketplace. a na portálu Azure.
* Souhlas s podmínkami Azure Marketplace zapojení zásady a vydavatele smlouvy.
* Souhlasit s podmínky použití, prohlášení o ochraně osobních údajů Microsoft a certifikované smlouvy programu služby Microsoft Azure.

## <a name="become-a-publisher"></a>Stát vydavatelem

Chcete-li začnou vydavatele v Azure Marketplace, postupujte takto:

1. Vytvoření Microsoft ID – vytvoření účtu Microsoft pomocí e-mailovou adresu, která patří do domény vaší společnosti, ale nechcete jeden uživatel. Tato e-mailová adresa se používá pro Microsoft Developer Center a cloudu portál pro partnery. Další informace najdete v tématu [Azure Marketplace vydavatele průvodce](https://aka.ms/sellerguide).
1. Odeslání [Azure Marketplace navrženém formuláře](https://aka.ms/ampnomination) – **řešení, které chcete publikovat?**, vyberte **spravované aplikace**. Po odeslání formuláře tým registrace Marketplace aplikace zkontroluje a ověří žádost. Schválení proces může trvat jednu až tři dny. Vaše navrženém schválena, obdržíte propagační kód vzdát poplatek za zápis pro středisku pro vývojáře. V takovém případě **není** vyplněním formuláře navrženém Marketplace, jste vyzváni k zaplacení poplatků registrace $99.
1. Zaregistrovat v [středisku pro vývojáře](http://dev.windows.com/registration?accountprogram=azure) -Microsoft ověří, jestli vaše organizace platný právní subjekt s platnou DAŇOVÉ číslo pro země, ve kterém je zaregistrován. Schválení proces může trvat 5 až 10 dní. Abyste se vyhnuli poplatek za zápis, použijte propagační kód, který jste obdrželi v e-mailu z procesu navrženém. Další informace najdete v tématu [Azure Marketplace vydavatele průvodce](https://aka.ms/sellerguide).
1. Přihlaste se k [portál pro partnery cloudu](https://cloudpartner.azure.com) – v profilu vydavatele přidružení účtu středisku pro vývojáře s profilem vydavatele Marketplace. Další informace najdete v tématu [Azure Marketplace vydavatele průvodce](https://aka.ms/sellerguide).

## <a name="create-a-new-azure-application-offer"></a>Vytvořit novou aplikaci Azure nabídka

Po vytvoření účtu partnera portálu, jste připraveni vytvořit nabídku spravované aplikace.

### <a name="set-up-an-offer"></a>Nastavit nabídky

Nabídka pro spravované aplikace odpovídá na třídu produktu nabídky od vydavatele. Pokud máte nový typ aplikace, kterou chcete zpřístupnit v marketplace, můžete ho nastavit jako novou nabídku. Nabídka je kolekce SKU. Všechny nabídky se zobrazí jako vlastní entity v marketplace.

1. Přihlaste se k [portál Cloud Partner](https://cloudpartner.azure.com/).

1. V navigačním podokně na levé straně vyberte **+ nové nabídky** > **aplikací Azure**.

1. V **Editor** zobrazení, najdete v části požadované formuláře. Každý formulář je popsán dále v tomto článku.

## <a name="offer-settings-form"></a>Nabídka nastavení formuláře

Pole pro **nabízejí nastavení** formuláře jsou:

* **ID nabídky**: Tento jedinečný identifikátor identifikuje nabídku v rámci profilu vydavatele. Toto ID je viditelná v adresách URL produktu, šablony Resource Manageru a fakturace sestavy. Může být složené jenom malé alfanumerické znaky nebo pomlčky (-). ID nemůže končit pomlčkou. Jeho omezeno na maximálně 50 znaků. Po nabídku přejde za provozu, toto pole je uzamčené.
* **ID vydavatele**: pomocí tohoto rozevíracího seznamu vyberte vydavatele profil, který chcete publikovat v rámci této nabídky. Po nabídku přejde za provozu, toto pole je uzamčené.
* **Název**: Tento název zobrazení pro danou nabídku se zobrazí v Marketplace. a na portálu. Může mít maximálně 50 znaků. Zahrnují srozumitelný název značky pro svůj produkt. Nezadávejte název společnosti, pokud to není, jak je na trh. Pokud jste marketingové v rámci této nabídky na vlastní web, ujistěte se, zda je název přesně jak se zobrazuje na vašem webu.

Až budete hotoví, vyberte **Uložit** k rozdělanou práci uložit.

## <a name="skus-form"></a>SKU formuláře

Dalším krokem je přidání SKU pro vaši nabídku.

SKU je nejmenší nabízené ke koupi jednotkou nabídku. Skladová položka v rámci stejné třídy produktu (nabídka) můžete použít k rozlišení mezi:

* Různé funkce, které jsou podporovány
* Jestli je nabídka spravovaných nebo nespravovaných
* Modely fakturace, které jsou podporovány

V případě nadřazené nabídky na Marketplace se zobrazí SKU. Zobrazí se jako vlastní nabízené ke koupi entity na portálu Azure.

1. Vyberte **SKU** > **nové SKU**.

1. Zadejte **SKU ID**. SKU ID je jedinečný identifikátor pro SKU v rámci nabídku. Toto ID je viditelná v adresách URL produktu, šablony Resource Manageru a fakturace sestavy. Může být složené jenom malé alfanumerické znaky nebo pomlčky (-). ID nemůže končit pomlčkou a jeho omezeno na maximálně 50 znaků. Po nabídku přejde za provozu, toto pole je uzamčené. Můžete mít více SKU v rámci nabídku. Je nutné SKU pro každé bitové kopie, které chcete publikovat.

1. Vyplňte **SKU podrobnosti** část v následující podobě:

   Vyplňte následující pole:

   * **Název**: Zadejte název pro tento SKU. Tento název se zobrazí v galerii pro tuto položku.
   * **Souhrn**: Zadejte krátký souhrn pro tento SKU. Tento text se zobrazí pod názvem.
   * **Popis**: Zadejte podrobný popis o verze SKU.
   * **Typ SKU**: povolené hodnoty jsou *spravované aplikace* a *šablony řešení*. Pro tento případ, vyberte *spravované aplikace*.
   * **Země nebo oblast dostupnosti**: Vyberte zemí, kde je k dispozici spravované aplikace.
   * **Ceny**: Zadejte cenu pro správu aplikace. Vyberte dostupný zemí před nastavením cenu.

1. Přidejte nový balíček. Vyplňte **podrobnosti balíčku** část v následující podobě:

   Vyplňte následující pole:

   * **Aktuální verze**: Zadejte verzi balíčku můžete nahrávat na server. Musí být ve formátu `{number}.{number}.{number}{number}`.
   * **Vyberte soubor balíčku**: Tento balíček obsahuje dvě požadované soubory komprimované do balíčku .zip. Jeden soubor je šablony Resource Manageru, který definuje prostředky pro nasazení pro spravované aplikace. Definuje další soubor [uživatelské rozhraní](create-uidefinition-overview.md) pro spotřebitele nasazení spravované aplikace prostřednictvím portálu. V uživatelském rozhraní zadejte elementy, které umožňují příjemcům zadáním hodnot parametru.
   * **PrincipalId**: Tato vlastnost je Azure Active Directory (Azure AD) identifikátor uživatele, skupiny uživatelů nebo aplikací, kteří mají udělen přístup k prostředkům v rámci předplatného zákazníka. Definice Role jsou popsána oprávnění.
   * **Definice role**: Tato vlastnost je seznam všech uvedené vestavěné role řízení přístupu na základě Role (RBAC) poskytované Azure AD. Můžete vybrat roli, která je nejvhodnější používat ke správě prostředků jménem zákazníka.

Můžete přidat více oprávnění. Doporučujeme vytvořit skupinu uživatele AD a zadejte své ID v **PrincipalId**. Tímto způsobem můžete přidat další uživatele do skupiny uživatelů, aniž by bylo nutné aktualizovat verze SKU.

Další informace o RBAC najdete v tématu [začít pracovat s RBAC na portálu Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulář Marketplace.

Ve formuláři Marketplace je dotaz pro pole, které se zobrazí na [Azure Marketplace](https://azuremarketplace.microsoft.com) a na [portál Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID odběru Preview

Zadejte seznam předplatného Azure ID, kteří mohou přistupovat k nabídku po publikování. Tyto odběry uvedené prázdné můžete použít k testování nabídku náhled před jeho provedením za provozu. Seznam povolených až 100 předplatná v portálu pro partnery můžete zkompilovat.

### <a name="suggested-categories"></a>Navrhované kategorií

Vyberte až pěti kategorií ze seznamu, který může být nejlépe přidružený vaši nabídku. Tyto kategorie se používají pro mapování vaši nabídku do kategorie produktů, které jsou k dispozici v [Azure Marketplace](https://azuremarketplace.microsoft.com) a [portál Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Souhrn spravované aplikace zobrazí následující pole:

![Souhrn Marketplace](./media/publish-marketplace-app/publishvm10.png)

**Přehled** kartě pro spravované aplikace zobrazí následující pole:

![Přehled webu Marketplace](./media/publish-marketplace-app/publishvm11.png)

**Plány + cenová** kartě pro spravované aplikace zobrazí následující pole:

![Plány Marketplace.](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

Souhrn spravované aplikace zobrazí následující pole:

![Portál souhrn](./media/publish-marketplace-app/publishvm12.png)

Přehled pro spravované aplikace zobrazí následující pole:

![Přehled portálu](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Logu

Postupujte podle těchto pokynů pro všechny logo, který nahrajete na portálu Cloud Partner:

*   Azure návrh obsahuje paletu barev jednoduché. Omezte počet primární a sekundární barev v loga.
*   Barev motivů portálu jsou bílé a černé. Nepoužívejte tyto barvy jako barvu pozadí pro loga. Použijte barvu, která umožňuje loga viditelného na portálu. Doporučujeme, abyste jednoduché primární barvy. *Pokud používáte průhledné pozadí, ujistěte se, že nejsou bílé, logem a text černé nebo blue.*
*   Nepoužívejte barevného přechodu pozadí na logo.
*   Neukládejte text do logo, ani vaše společnost nebo název značky. Vzhled a chování vaše logo musí být plochý a vyhnout se přechody.
*   Zajistěte, aby že logo není roztažená.

#### <a name="hero-logo"></a>Logo nejdůležitější

Logo nejdůležitější je volitelný. Vydavatele můžete vypnout nahrávat logo nejdůležitější. Po odeslání ikonu hrdina nelze odstranit. V ten moment se musí partnerovi postupujte podle pokynů Marketplace pro nejdůležitější ikony.

Postupujte podle těchto pokynů pro ikonu logo nejdůležitější:

*   Zobrazovaný název vydavatele, název plánu a nabídky dlouhé shrnutí jsou zobrazeny v prázdné. Proto nepoužívejte světla barvu pozadí ikonu nejdůležitější. Černé, bílé nebo průhledné pozadí není povolena pro nejdůležitější ikony.
*   Po nabídka je uvedena, jsou elementy prostřednictvím kódu programu vložená do logo nejdůležitější. Vložené prvky patří zobrazovaný název vydavatele, název plánu, nabídku dlouhé shrnutí a **vytvořit** tlačítko. V důsledku toho není zadat libovolný text při návrhu logo nejdůležitější. Vzhledem k tomu, že je text do tohoto místa zahrnuto prostřednictvím kódu programu, ponechte prázdné místo na pravé straně. Prázdné místo pro text by mělo být 415 × 100 pixelů na pravé straně. Posunut 370 pixelů od levého okraje.

    ![Příklad logo nejdůležitější](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Podpora formuláře

Vyplňte **podporu** formuláře s podporou kontaktuje z vaší společnosti. Tyto informace může technici, kontakty a kontakty podpory zákazníků.

## <a name="publish-an-offer"></a>Publikování nabídky

Po vyplnění všech oddílů, vyberte **publikovat** ke spuštění procesu, který zpřístupní vaši nabídku pro zákazníky.

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Informace o publikování aplikace spravované katalogu služeb najdete v tématu [vytvoření a publikování aplikace spravované katalogu služeb](publish-service-catalog-app.md).