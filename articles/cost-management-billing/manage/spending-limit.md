---
title: Limit útraty Azure
description: Tento článek popisuje, jak funguje limit útraty Azure a jak ho odebrat.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: df5c5ed74a5e0a63727962fa86d261cfb588a384
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690557"
---
# <a name="azure-spending-limit"></a>Limit útraty Azure

Limit útraty v Azure zabraňuje překročení částky kreditu. Všem novým zákazníkům, kteří si zaregistrují bezplatný účet Azure nebo typy předplatných, které zahrnují kredit na několik měsíců, se standardně aktivuje limit útraty. Limit útraty odpovídá výši kreditu a není možné ho změnit. Pokud jste si například zaregistrovali bezplatný účet Azure, váš limit útraty je 200 USD a není možné ho změnit na 500 USD. Můžete ho ale kdykoliv zrušit. To znamená, že buď nemáte žádný limit, nebo máte limit odpovídající výši kreditu. Tento limit vám znemožní provádět většinu typů útraty. Limit útraty není dostupný u předplatných s plány závazků nebo s průběžnými platbami. Prohlédněte si [úplný seznam typů předplatného Azure a dostupnost limitu útraty](https://azure.microsoft.com/support/legal/offer-details/).

## <a name="reaching-a-spending-limit"></a>Dosažení limitu útraty

Jakmile poplatky spojené s využíváním služeb vyčerpají limit útraty, nasazené služby se do konce příslušného fakturačního období vypnou.

Když například utratíte veškerý kredit, který je součástí vašeho bezplatného účtu Azure, nasazené prostředky Azure se odeberou z produkčního prostředí a virtuální počítače Azure se zastaví a zruší se jejich přidělení. Data v účtech úložiště jsou dostupná jen pro čtení.

Pokud váš typ předplatného zahrnuje kredit na několik měsíců, vaše předplatné znovu automaticky aktivuje v případě na začátku dalšího fakturačního období. Potom můžete znovu nasadit prostředky Azure a získat úplný přístup k účtům úložiště a databázím.

Při dosažení limitu útraty Azure posílá e-mailová oznámení. Pokud chcete zobrazit oznámení o předplatných, u kterých došlo k dosažení limitu útraty, přihlaste se k webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Pokud jste si zaregistrovali bezplatný účet Azure a dosáhnete limitu útraty, můžete upgradovat na [průběžné](upgrade-azure-subscription.md) platby, aby se odebral limit útraty a předplatné se automaticky znovu aktivovalo.

## <a name="remove-the-spending-limit-in-azure-portal"></a>Odebrání limitu útraty na webu Azure Portal

Limit útraty můžete odebrat kdykoli za předpokladu, že je k vašemu předplatnému Azure přidružen platný způsob platby. U typů předplatného s kreditem na několik měsíců, jako jsou předplatná sady Visual Studio Enterprise nebo Visual Studio Professional, můžete zvolit odebrání limitu útraty nadobro, nebo jen pro aktuální fakturační období. Pokud zvolíte jenom aktuální fakturační období, limit útraty se na začátku dalšího fakturačního období automaticky zapne.

Pokud máte bezplatný účet Azure, přečtěte si o odebrání limitu útraty v tématu [Upgrade předplatného Azure](upgrade-azure-subscription.md). Jinak odebrání limitu útraty provedete takto:

<a id="remove"></a>

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace ](./media/spending-limit/search-bar.png)

1. V seznamu **Moje předplatná** vyberte své předplatné. Vyberte například *Visual Studio Enterprise*.

   ![Snímek obrazovky s mřížkou Moje předplatná v části Přehled](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Pokud tady nevidíte některá svá předplatná sady Visual Studio, je možné, že jste v nějakém okamžiku změnili adresář předplatného. U těchto předplatných musíte přepnout na původní adresář (adresář, ve které jste se původně zaregistrovali). Potom opakujte krok 2.

1. V přehledu předplatného odeberte limit útraty kliknutím na oranžový banner.

    ![Snímek obrazovky s bannerem pro odebrání limitu útraty](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. Zvolte, jestli chcete limit útraty odebrat trvale, nebo pouze pro aktuální fakturační období.

      ![Snímek obrazovky s oknem Odebrat limit útraty](./media/spending-limit/remove-spending-limit-blade-x.png)

      | Možnost | Účinek |
      | --- | --- |
      | Trvale odebrat limit útraty | Limit útraty se na začátku dalšího fakturačního období automaticky znovu nezapne. Můžete ho ale kdykoli znovu zapnout. |
      | Odebrat limit útraty pro aktuální fakturační období | Limit útraty se na začátku dalšího fakturačního období automaticky znovu zapne. |


1. Klikněte na **Vyberte způsob platby** a zvolte způsob platby pro vaše předplatné. Tento způsob platby se nastaví jako aktivní způsob platby pro vaše předplatné.

1. Klikněte na **Finish** (Dokončit).


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>Proč byste mohli chtít odebrat limit útraty

Limit útraty vám může bránit v nasazení nebo použití některých služeb od externích dodavatelů a společnosti Microsoft. Tady jsou situace, kdy byste měli limit útraty pro svoje předplatné odebrat.

-  Chcete nasadit image třetích stran, třeba Oracle, nebo služby, jako je Azure DevOps Services. To téměř okamžitě způsobí, že dosáhnete svého limitu útraty a vaše předplatné se deaktivuje.
- Máte služby, jejichž provoz nechcete přerušit. Když dosáhnete limitu útraty, nasazené prostředky Azure se odeberou z produkčního prostředí a virtuální počítače Azure se zastaví a zruší se jejich přidělení. Pokud máte služby, jejichž provoz nechcete přerušit, musíte limit útraty odebrat.
- Nechcete ztratit nastavení služeb a prostředků, jako jsou virtuální IP adresy. Při dosažení limitu útraty se zruší přidělení služeb a prostředků a tato nastavení se ztratí.

## <a name="turn-on-the-spending-limit-after-removing"></a>Zapnutí limitu útraty po odebrání

Tato funkce je k dispozici pouze v případě trvalého odebrání limitu útraty pro typy předplatného, které zahrnují kredit na několik měsíců. Pomocí této funkce můžete automaticky zapnout limit útraty na začátku dalšího fakturačního období.


1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce účtu.
1. Vyhledejte položku **Správa nákladů a fakturace**.

    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace ](./media/spending-limit/search-bar.png)

1. V seznamu **Moje předplatná** vyberte své předplatné. Vyberte například *Visual Studio Enterprise*.

   ![Snímek obrazovky s mřížkou Moje předplatná v části Přehled](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Pokud tady nevidíte některá svá předplatná sady Visual Studio, je možné, že jste v nějakém okamžiku změnili adresář předplatného. U těchto předplatných musíte přepnout na původní adresář (adresář, ve které jste se původně zaregistrovali). Potom opakujte krok 2.

1. V přehledu předplatného klikněte na banner v horní části stránky, abyste limit útraty znovu zapnuli.

## <a name="custom-spending-limit"></a>Vlastní limit útraty

Vlastní limity útraty nejsou k dispozici.

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>Limit útraty nezabrání všem poplatkům.

[Některé externí služby publikované na Azure Marketplace](../understand/understand-azure-marketplace-charges.md) nelze s kredity předplatného používat a mohou se účtovat samostatné poplatky, i když máte nastavený limit útraty. Mezi příklady patří licence k sadě Visual Studio, Azure Active Directory Premium, plány podpory a většina služeb jiných výrobců. Při zřizování nové externí služby se zobrazí upozornění na samostatnou fakturaci služeb:

![Upozornění související s nákupy na Azure Marketplace](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- Upgrade na plán s [průběžnými](upgrade-azure-subscription.md) platbami
