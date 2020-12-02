---
title: Rezervované instance virtuálních počítačů Azure EA
description: Tento článek shrnuje, jak vám rezervace Azure umožní ušetřit peníze za rezervované instance virtuálních počítačů v rámci smlouvy Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 7c8a35e7c8ecd0952f8e02b66c5071253d9b0445
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348921"
---
# <a name="azure-ea-vm-reserved-instances"></a>Rezervované instance virtuálních počítačů Azure EA

Tento článek shrnuje, jak vám rezervace Azure umožní ušetřit peníze za rezervované instance virtuálních počítačů v rámci smlouvy Enterprise. Další informace o rezervacích najdete v tématu [Co jsou rezervace Azure?](../reservations/save-compute-costs-reservations.md).

## <a name="reservation-exchanges-and-refunds"></a>Výměny rezervací a vrácení peněz

Rezervaci si můžete vyměnit za jinou rezervaci stejného typu. Rezervaci je také možné refundovat, pokud už ji nepotřebujete, a to až do 50 000 USD za rok. Výměnu nebo refundaci rezervace je možné provést na webu Azure Portal. Další informace najdete v tématu [Samoobslužné výměny a vrácení peněz za rezervace Azure](../reservations/exchange-and-refund-azure-reservations.md).

### <a name="partial-refunds"></a>Částečné refundace

Částečné refundace vydáváme, když zákazníci EA vrací rezervace zakoupené prostřednictvím nadlimitního využití, nikoli peněžního závazku.

Refundace se zobrazí na portálu EA jako záporné vyrovnání v předchozím měsíci a kladné vyrovnání v aktuálním měsíci. Bude vypadat podobně jako výměna rezervací. V dobropisu bude uvedené číslo původní faktury. Proto pokud chcete odsouhlasit původní nákup s dobropisem, použijte číslo původní faktury.

## <a name="reservation-costs-and-usage"></a>Náklady na rezervace a jejich využití

Zákazníci se smlouvou Enterprise si mohou data o nákladech a využití zobrazit přes Azure Portal a rozhraní REST API. V souvislosti s daty o nákladech na rezervace a jejich využití můžete:

- Získat údaje o nákupech rezervací
- Zjistit, které předplatné, skupina prostředků nebo prostředek rezervaci využil
- Udělat vratku za využití rezervace
- Vypočítat úspory vyplývající z rezervace
- Získat údaje o nedostatečném využití rezervací
- Amortizovat náklady na rezervace

Další informace o nákladech na rezervace a jejich využití najdete v tématu [Získání nákladů na rezervace a jejich využití v rámci smlouvy Enterprise](../reservations/understand-reserved-instance-usage-ea.md).

Informace o cenách najdete v tématu [Ceny služby Virtual Machines s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Ceny Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

## <a name="reserved-instances-api-support"></a>Podpora rozhraní API pro rezervované instance

Pomocí rozhraní API pro Azure můžete informace o rezervacích služeb nebo softwaru Azure ve vaší organizaci získávat prostřednictvím programu. Rozhraní API můžete například použít k těmto akcím:

- Vyhledání rezervací k zakoupení
- Nákup rezervace
- Zobrazení zakoupených rezervací
- Zobrazení a správa přístupu k rezervacím
- Rozdělení nebo sloučení rezervací
- Změna rozsahu rezervací

Další informace najdete v tématu [Rozhraní API pro automatizaci rezervací Azure](../reservations/reservation-apis.md).

## <a name="azure-reserved-virtual-machine-instances"></a>Rezervované instance virtuálních počítačů Azure

Rezervované instance mohou oproti průběžným platbám snížit náklady na všechny virtuální počítače až o 72 %. V kombinaci se Zvýhodněným hybridním využitím Azure mohou úspory dosáhnout až 82 %. Rezervované instance s platbou předem na jeden nebo tři roky pomáhají s lepší správou úloh, rozpočtů a prognóz. Rezervace můžete také vyměnit nebo zrušit podle toho, jak se budou měnit obchodní potřeby.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Možnosti nákupu rezervovaných instancí virtuálních počítačů

Pokud si chcete zakoupit rezervovanou instanci virtuálního počítače Azure, podnikový správce registrace v Azure musí povolit možnost nákupu _Rezervované instance_. Tuto možnost najdete v části _Podrobnosti registrace_ na kartě _Registrace_ na [portálu Azure EA](https://ea.azure.com/).

Po povolení přidávání rezervovaných instancí v registraci EA může jakýkoli vlastník účtu s aktivním předplatným přidruženým k dané registraci EA zakoupit rezervované instance virtuálních počítačů na webu [Azure Portal](https://aka.ms/reservations). Další informace najdete v tématu [Předplacení virtuálních počítačů a úspory s využitím rezervovaných instancí virtuálních počítačů](../../virtual-machines/prepay-reserved-vm-instances.md).

### <a name="how-to-view-reserved-instance-purchase-details"></a>Zobrazení podrobností o nákupu rezervovaných instancí

Podrobnosti o nákupu rezervovaných instancí můžete zobrazit prostřednictvím nabídky _Rezervace_ na levé straně webu [Azure Portal](https://aka.ms/reservations) nebo na [portálu Azure EA](https://ea.azure.com/). V nabídce vlevo vyberte **Sestavy** a na kartě _Souhrn využití_ se posuňte dolů do části _Poplatky podle služeb_. Posuňte se na konec této části, kde se zobrazí výpis zakoupených rezervovaných instancí a jejich využití – vedle názvu služby mají označení `1 year` nebo `3 years`, například `Standard_DS1_v2 eastus 1 year` nebo `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Jak můžu změnit předplatné přidružené k rezervované instanci nebo převést výhody rezervovaných instancí na jiné předplatné v rámci stejného účtu?

Předplatné, které získá výhody rezervovaných instancí, můžete změnit následujícím způsobem:

- Přihlaste se k webu [Azure Portal](https://aka.ms/reservations).
- Aktualizujte použitý rozsah předplatného tím, že přidružíte jiné předplatné v rámci stejného účtu.

Další informace o změně rozsahu rezervace najdete v tématu [Změna rozsahu rezervace](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope).

### <a name="how-to-view-reserved-instance-usage-details"></a>Zobrazení podrobností o využití rezervovaných instancí

Podrobnosti o využití rezervovaných instancí můžete zobrazit na webu [Azure Portal](https://aka.ms/reservations) nebo na [portálu Azure EA](https://ea.azure.com/) (v případě zákazníků se smlouvou Enterprise, kteří mají přístup k zobrazení fakturačních údajů) v části _Sestavy_ > _Souhrn využití_ > _Poplatky podle služeb_. Rezervované instance poznáte podle toho, že název služby obsahuje slovo Reservation (Rezervace), například `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Další informace o využití rezervovaných instancí najdete v souboru CSV s podrobnostmi o využití a rozšířenou sestavou ke stažení. Pole _Další informace_ vám pomůže zjistit využití rezervovaných instancí.

Pokud jste k nákupu rezervovaných instancí virtuálních počítačů Azure nepoužili Zvýhodněné hybridní využití Azure, rezervované instance budou generovat údaje pro dva měřiče (hardwarový a softwarový). Pokud jste k nákupu rezervované instance použili Zvýhodněné hybridní využití Azure, v podrobnostech o využití rezervovaných instancí se nezobrazí softwarový měřič.

### <a name="reserved-instance-billing"></a>Fakturace rezervovaných instancí

U podnikových zákazníků se k nákupu rezervovaných instancí virtuálních počítačů Azure používá záloha na Azure. Pokud je u vaší registrace dostatečný zůstatek zálohy na Azure na pokrytí nákupu rezervované instance, příslušná částka se odečte od zůstatku zálohy Azure a za nákup neobdržíte fakturu.

Pokud zákazníci se smlouvou Azure Enterprise využijí celou svoji zálohu na Azure, stále si můžou koupit rezervované instance, ale tyto nákupy se budou fakturovat na další faktuře za nadlimitní využití. Případné nadlimitní využití rezervovaných instancí bude součástí normální faktury za nadlimitní využití.

### <a name="reserved-instance-expiration"></a>Vypršení platnosti rezervované instance

30 dnů před vypršením platnosti rezervace a při vypršení její platnosti obdržíte e-mailová oznámení. Po vypršení platnosti rezervace zůstanou nasazené virtuální počítače spuštěné a budou se účtovat podle tarifů průběžných plateb. Další informace najdete v [nabídce rezervovaných instancí virtuálních počítačů](https://azure.microsoft.com/pricing/reserved-vm-instances/).

## <a name="next-steps"></a>Další kroky

- Další informace o rezervacích Azure najdete v tématu [Co jsou rezervace Azure?](../reservations/save-compute-costs-reservations.md).
- Další informace o nákladech na rezervace a jejich využití v rámci smlouvy Enterprise najdete v tématu [Získání nákladů na rezervace a jejich využití v rámci smlouvy Enterprise](../reservations/understand-reserved-instance-usage-ea.md).
- Informace o cenách najdete v tématu [Ceny služby Virtual Machines s Linuxem](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) nebo [Ceny Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).