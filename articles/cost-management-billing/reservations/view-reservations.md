---
title: Zobrazení rezervací pro prostředky Azure
description: Zjistěte, jak na webu Azure Portal zobrazit rezervace Azure. Rezervace a informace o jejich využití můžete zobrazit pomocí rozhraní API, PowerShellu, rozhraní příkazového řádku a Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 12/15/2020
ms.author: banders
ms.openlocfilehash: 8c69f477f363654b8bd707949f0a5b4c46a4e8df
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561100"
---
# <a name="view-azure-reservations"></a>Zobrazení rezervací Azure

Tento článek vysvětluje, jak zobrazovat rezervace Azure na webu Azure Portal. Zakoupené rezervace můžete zobrazit a spravovat na webu Azure Portal.

## <a name="who-can-manage-a-reservation-by-default"></a>Kdo může ve výchozím nastavení spravovat rezervaci

Ve výchozím nastavení mohou rezervace zobrazovat a spravovat následující uživatelé:

- Do objednávky rezervace se přidá osoba, která rezervaci koupí, a správce účtu pro fakturační předplatné použité k nákupu rezervace.
- Správci fakturace pro smlouvy Enterprise a Smlouvy se zákazníkem Microsoftu

Pokud chcete správu rezervací umožnit ostatním, máte dvě možnosti:

- Delegování správy přístupu pro individuální objednávku rezervace:
    1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
    1. Výběrem **Všechny služby** > **Rezervace** zobrazte seznam rezervací, ke kterým máte přístup.
    1. Vyberte rezervaci, ke které chcete delegovat přístup jiným uživatelům.
    1. V části Podrobnosti rezervace vyberte objednávku požadované rezervace.
    1. Vyberte **Řízení přístupu (IAM)** .
    1. Vyberte **Přidat přiřazení role** > **Role** > **Vlastník**. Pokud chcete udělit omezený přístup, vyberte jinou roli.
    1. Zadejte e-mailovou adresu uživatele, kterého chcete přidat jako vlastníka.
    1. Vyberte uživatele a pak vyberte **Uložit**.

- Přidání uživatele jako správce fakturace pro smlouvu Enterprise nebo Smlouvu se zákazníkem Microsoftu:
    - V případě smlouvy Enterprise přidejte uživatele s rolí _Podnikový správce_, kteří mohou zobrazovat a spravovat všechny objednávky rezervací související se smlouvou Enterprise. Uživatelé s rolí _Podnikový správce (jen pro čtení)_ mohou rezervaci jenom zobrazit. Správci oddělení a vlastníci účtů mohou rezervace zobrazovat _jenom v případě_, že je k nim explicitně přidáte pomocí řízení přístupu (IAM). Další informace najdete v článku [Správa rolí Azure Enterprise](../manage/understand-ea-roles.md).

        _Podnikoví správci můžou převzít vlastnictví objednávky rezervace a mohou k rezervaci přidávat další uživatele pomocí řízení přístupu (IAM)._
    - V případě Smlouvy se zákazníkem Microsoftu mohou všechny nákupy rezervací realizované prostřednictvím konkrétního fakturačního profilu spravovat uživatelé s rolí vlastníka nebo přispěvatele tohoto fakturačního profilu. Čtenáři fakturačního profilu a správci faktur mohou zobrazovat všechny rezervace, které se pro daný fakturační profil platí. Nemohou ale v rezervacích dělat změny.
    Podrobnosti najdete v části [Role a úlohy související s fakturačním profilem](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="how-billing-administrators-view-or-manage-reservations"></a>Jak správci fakturace zobrazují nebo spravují rezervace

1. Přejděte na **Cost Management + Billing** a na levé straně stránky vyberte **Transakce rezervací**.
2. Pokud máte požadovaná oprávnění k fakturaci, můžete zobrazit a spravovat rezervace. Pokud se vám žádné rezervace nezobrazují, zkontrolujte, že jste přihlášení pomocí tenanta Azure AD, ve kterém byly rezervace vytvořeny.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Zobrazení rezervace a informací o využití na webu Azure Portal

Zobrazení rezervace jako vlastník nebo čtenář

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do části [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. V seznamu se zobrazí všechny rezervace, u kterých máte roli Vlastník nebo Čtenář. U každé rezervace se zobrazí poslední známé procento využití.
4. Výběrem procenta využití zobrazíte historii a podrobnosti využití. Podrobnosti najdete v následujícím videu.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Získání informací o rezervacích a využití pomocí rozhraní API, PowerShellu a rozhraní příkazového řádku

Seznam všech rezervací můžete získat s využitím následujících zdrojů informací:

- [Rozhraní API: Objednávka rezervace – Výpis](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Objednávka rezervace – Výpis](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Rozhraní příkazového řádku: Objednávka rezervace – Výpis](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Pomocí rozhraní API pro využití rezervovaných instancí můžete získat také informace o [využití rezervací](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Zobrazení rezervací a využití v Power BI

Pro uživatele Power BI jsou k dispozici dvě možnosti.
- Balíček obsahu: Informace o datu nákupu rezervací a údaje o využití jsou k dispozici v [balíčku obsahu Power BI pro Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Pomocí tohoto balíčku obsahu můžete vytvářet libovolné sestavy. 
- Aplikace Cost Management: V [aplikaci Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) můžete využít předem vytvořené sestavy, které si můžete dál přizpůsobit.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)

