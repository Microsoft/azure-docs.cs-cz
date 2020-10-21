---
title: Úspora za Azure App Service s využitím rezervované kapacity
description: Přečtěte si, jak můžete pomocí rezervované kapacity ušetřit náklady na poplatky za kolek služby Azure App Service v izolovaném prostředí.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: feb7be6ad5dbd0dfbd04b05836842d0476780d40
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151540"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Úspora nákladů na poplatky za kolek služby Azure App Service v izolovaném prostředí pomocí rezervované kapacity

Když potvrdíte rezervaci pro využití kolku na dobu tří let, můžete ušetřit na poplatcích za kolek služby Azure App Service v izolovaném prostředí. Pokud si chcete koupit rezervovanou kapacitu s poplatky za kolek služby Azure App Service v izolovaném prostředí, musíte zvolit oblast Azure, ve které se kolek nasadí, a počet kolků k zakoupení.

Když zakoupíte rezervaci, využití poplatků za kolek v izolovaném prostředí odpovídající atributům rezervace se už nebude účtovat podle sazeb pro průběžné platby. Rezervace se automaticky použije na počet izolovaných kolků, který odpovídá rozsahu a oblasti rezervované kapacity. Rezervaci nemusíte přiřazovat k izolovanému kolku. Rezervace se nevztahuje na pracovní procesy, takže všechny další prostředky přidružené ke kolku se účtují samostatně.

Když rezervovaná kapacita vyprší, izolované kolky poběží dál, ale budou se fakturovat podle sazeb pro průběžné platby. Rezervace se neprodlužují automaticky.

## <a name="determine-the-right-reservation-to-purchase"></a>Určení správné rezervace k zakoupení

Když si koupíte rezervaci, potvrzujete, že další tři roky budete používat rezervovaná množství. Kontrolou dat o využití zjistíte, kolik izolovaných kolků služby App Service konzistentně používáte a můžete v budoucnu používat.

Také byste měli rozumět tomu, jak izolovaný kolek vytváří měřič pro Linux nebo Windows.

- Prázdný izolovaný kolek standardně vytváří měřič kolku pro Windows. Například bez nasazení pracovních procesů. Pokud jsou na kolku nasazené pracovní procesy pro Windows, vytváří kolek dál tento měřič.
- Pokud nasadíte pracovní proces pro Linux, změní se měřič na měřič kolku pro Linux.
- V případech, kdy jsou nasazené pracovní procesy pro Linux i Windows, vytváří kolek měřič pro Windows.

Měřič kolku se tak během své existence může změnit z měřiče pro Windows na měřič pro Linux.

Rezervace s kolkem pro Windows si kupte, pokud máte na kolku jeden nebo více pracovních procesů pro Windows. Rezervaci s kolkem pro Linux si kupte jenom v případě, že se chystáte používat na kolku _výhradně_ pracovní procesy pro Linux.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Zakoupení rezervované kapacity izolovaného kolku

Rezervovanou kapacitu izolovaného kolku si můžete koupit na webu [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Za rezervaci se platí [předem nebo prostřednictvím měsíčních plateb](./prepare-buy-reservation.md). Pokud si chcete koupit rezervovanou kapacitu, musíte mít roli vlastníka alespoň u jednoho předplatného se smlouvou Enterprise nebo u individuálního předplatného se sazbami pro průběžné platby.

- U předplatných se smlouvou Enterprise musí být na portálu [EA Portal](https://ea.azure.com/) povolená možnost **Přidat rezervované instance**. Pokud je toto nastavení zakázané, musíte být správcem EA.
- U programu CSP (Cloud Solution Provider) můžou rezervovanou kapacitu služby Azure Synapse Analytics zakoupit jenom agenti správy nebo agenti prodeje.

**Postup při nákupu:**

1. Přejděte na web [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Vyberte předplatné. K výběru předplatného, které se použije k placení rezervované kapacity, použijte seznam **Předplatné**. Náklady na rezervovanou kapacitu se účtují pomocí způsobu platby zvoleného pro předplatné. Musí se jednat o předplatné se smlouvou Enterprise (číslo nabídky: MS-AZR-0017P nebo MS-AZR-0148P) nebo předplatné s průběžnými platbami (číslo nabídky: MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatné CSP.
    - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
    - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
1. Vyberte **Rozsah** a zvolte rozsah předplatného.
    - Rozsah **Jedna skupina prostředků** – sleva za rezervaci se uplatní jenom u odpovídajících prostředků ve vybrané skupině prostředků.
    - Rozsah **Jedno předplatné** – sleva za rezervaci se uplatní u odpovídajících prostředků ve vybraném předplatném.
    - Rozsah **Sdílený** – sleva za rezervaci se uplatní u odpovídajících prostředků v oprávněných předplatných v kontextu fakturace. U zákazníků se smlouvou Enterprise je kontextem fakturace prováděcí smlouva. U individuálních předplatných se sazbami pro průběžné platby jsou rozsahem fakturace všechna oprávněná předplatná vytvořená správcem účtu.
1. Vyberte **Oblast** a zvolte oblast Azure, která je pokrytá rezervovanou kapacitou. Pak přidejte rezervaci do košíku.
1. Vyberte typ Isolated Plan a pak klikněte na **Vybrat**.  
    ![Příklad ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Zadejte množství izolovaných kolků služby App Service, které chcete rezervovat. Například množství 3 vám poskytne tři rezervované kolky na oblast. Klikněte na **Další: Zkontrolovat + koupit**.
1. Zkontrolujte a klikněte na **Koupit**.

Po nákupu můžete přejít na stránku [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade), na které můžete kdykoli zobrazit a monitorovat stav nákupu.

## <a name="cancel-exchange-or-refund-reservations"></a>Zrušení, výměna nebo refundace rezervací

Rezervace je možné s určitými omezeními zrušit, vyměnit nebo refundovat. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Jak se uplatnění slevy zobrazuje v datech o využití

V datech o využití se pro využití, které získá slevu za rezervaci, zobrazuje platná nulová cena. V datech o využití se zobrazuje sleva za rezervaci pro každou instanci kolku v každé rezervaci.

Pokud jste zákazníkem se smlouvou Enterprise, najdete další informace o tom, jak se v datech o využití zobrazuje sleva za rezervaci, v tématu [jak zákazníci se smlouvou Enterprise získají informace o nákladech na rezervace a data o využití](understand-reserved-instance-usage-ea.md). Jinak se podívejte na téma [s vysvětlením využití rezervací Azure u individuálního předplatného se sazbami pro průběžné platby](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
  - [Vysvětlení způsobu uplatňování slev za rezervaci na izolované kolky služby Azure App Service](reservation-discount-app-service-isolated-stamp.md)
  - [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)