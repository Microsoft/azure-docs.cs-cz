---
title: Platba za Azure App Service izolované poplatky za razítko s rezervovanou kapacitou
description: Přečtěte si, jak můžete zaplatit za Azure App Service izolované poplatky za razítko s rezervovanou kapacitou pro úspory peněz.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: f122ec2474c09cdd6c9ada4ddc59b1adb44f619f
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780014"
---
# <a name="prepay-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Platba za Azure App Service izolované poplatky za razítko s rezervovanou kapacitou

Můžete ušetřit peníze na Azure App Service izolované poplatky za razítko, a to Předplacením používání razítek po dobu tří let. Pokud si chcete koupit vyhrazenou kapacitu rezervovaného razítka, musíte zvolit oblast Azure, ve které se razítko nasadí, a počet razítek k nákupu.

Když si zakoupíte rezervaci, neúčtují se použití izolovaného razítka, které odpovídá atributům rezervace, na základě tarifů průběžných plateb. Rezervace se automaticky použije na počet izolovaných razítek, která se shodují s rozsahem rezervované kapacity a oblastí. Nemusíte přiřazovat rezervaci izolovanému razítku. Rezervace se nevztahuje na pracovníky, takže jakékoli další prostředky spojené s tímto razítkem se účtují samostatně.

Až vyprší platnost rezervované kapacity, budou izolovaná razítka nadále běžet, ale budou se účtovat podle sazby za průběžné platby. Rezervace se neobnovují automaticky.

## <a name="determine-the-right-reservation-to-purchase"></a>Určení správné rezervace k nákupu

Když si koupíte rezervaci, předem platíte za použití rezervovaných množství po dobu dalších tří let. Zkontrolujte data o využití, abyste zjistili, kolik Služba App Service v izolovaném prostředích razítek konzistentně používáte a v budoucnu se můžou používat.

Navíc se ujistěte, jak izolované razítko vysílá systém Linux nebo měřič Windows.

- Ve výchozím nastavení prázdné izolované razítko vysílá měřič razítka systému Windows. Například bez nasazení žádných pracovních procesů. I nadále tento měřič vygeneruje, pokud jsou na razítku nasazeni pracovníci Windows.
- Když nasadíte pracovní proces Linux, měřič se změní na měřič razítka systému Linux.
- V případech, kdy jsou nasazeni pracovníci systému Linux i Windows, razítko vydává měřič oken.

Měřič razítka se tedy může měnit mezi Windows a Linuxem po celou dobu životnosti razítka.

Kupte si rezervace razítka systému Windows, pokud na razítku máte jeden nebo více pracovních procesů systému Windows. Jedinou dobou, kdy byste měli zakoupit rezervaci razítka systému Linux, je, že máte v úmyslu mít _pouze_ pracovní procesy systému Linux na razítku.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Koupit vyhrazenou kapacitu izolovaného razítka

Rezervovanou kapacitu rezervovaného razítka si můžete koupit v [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Pokud si chcete koupit rezervovanou kapacitu, musíte mít roli vlastníka aspoň u jednoho podnikového předplatného nebo u každého předplatného s tarify průběžných plateb.

- V případě předplatných Enterprise musí být na [portálu EA](https://ea.azure.com/)povolená možnost **Přidat rezervované instance** . Nebo, pokud je nastavení zakázané, musíte být správce EA.
- V případě programu Cloud Solution Provider (CSP) mohou SQL Data Warehouse rezervované kapacity koupit pouze agenti správce nebo prodejní agenti.

**K nákupu:**

1. Přejít na [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Vyberte předplatné. Pomocí seznamu předplatných vyberte předplatné, které se používá k placení rezervované kapacity. Platební metodou předplatného se účtují náklady na front-end pro rezervovanou kapacitu. Typ předplatného musí být smlouva Enterprise (čísla nabídek: MS-AZR-0017P nebo MS-AZR-0148P) nebo průběžné platby (čísla nabídek: MS-AZR-0003P nebo MS-AZR-0023P) nebo předplatné CSP.
    - V případě předplatného se smlouvou Enterprise se poplatky strhávají z peněžního zůstatku v rámci dané registrace nebo se účtují jako nadlimitní využití.
    - V případě předplatného s průběžnými platbami se poplatky účtují na platební kartu nebo pomocí způsobu platby faktur určeného v předplatném.
1. Vyberte **obor** pro výběr oboru předplatného.
    - **Obor jedné skupiny prostředků** – aplikuje slevu na rezervované prostředky jenom na ty, které jsou ve vybrané skupině prostředků.
    - **Rozsah jednoho** předplatného – aplikuje slevu na rezervované prostředky ve vybraném předplatném.
    - **Sdílený rozsah** – použije slevu rezervace na odpovídající prostředky v oprávněných předplatných, která jsou v účetním kontextu. Pro zákazníky smlouva Enterprise je fakturačním kontextem registrace. U jednotlivých předplatných s tarify průběžných plateb jsou oborem fakturace všechna oprávněná předplatná vytvořená správcem účtu.
1. Vyberte **oblast** a zvolte oblast Azure, která je pokrytá rezervovanou kapacitou, a přidejte rezervaci na košík.
1. Vyberte typ izolovaného plánu a pak klikněte na **Vybrat**.  
    ![Případě](./media/billing-prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Zadejte množství Služba App Service v izolovaném prostředí razítek k vyhradení. Například množství 3 vám poskytne tři rezervované razítka oblasti. Klikněte **na další: Zkontrolovat a koupit**.
1. Zkontrolujte a klikněte na **koupit hned**.

Po zakoupení můžete zobrazit stav nákupu a kdykoli ho monitorovat po nákupu, přejít na [rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) .

## <a name="cancel-exchange-or-refund-reservations"></a>Rezervace zrušení, výměna nebo refundace

Rezervace můžete zrušit, vyměnit nebo vrátit refundaci s určitými omezeními. Další informace najdete v tématu věnovaném [výměnám samoobslužných služeb a refundacím pro Azure reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="discount-application-shown-in-usage-data"></a>Aplikace slevy zobrazená v datech o využití

Data o využití mají platnou cenu za využití, která vrací slevu za rezervaci. Data o využití zobrazují slevu rezervace pro každou instanci razítka v každé rezervaci.

Další informace o tom, jak se slevou rezervace zobrazuje v datech využití, najdete v tématu [získání smlouva Enterprise nákladů na rezervaci a využití](billing-understand-reserved-instance-usage-ea.md) v případě, že jste zákazníkem smlouva Enterprise (EA). V opačném případě se podívejte na téma [použití rezervací Azure pro vaše individuální předplatné s tarify](billing-understand-reserved-instance-usage.md)průběžných plateb.

## <a name="next-steps"></a>Další postup

- Další informace o Azure Reservations najdete v následujících článcích:
  - [Co jsou Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Vysvětlení způsobu použití Azure App Service slev izolovaného razítka](billing-reservation-discount-app-service-isolated-stamp.md)
  - [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
