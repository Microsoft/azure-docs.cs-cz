---
title: Zobrazení rezervací prostředků Azure | Microsoft Docs
description: Zjistěte, jak na webu Azure Portal zobrazit rezervace Azure. Rezervace a informace o jejich využití můžete zobrazit pomocí rozhraní API, PowerShellu, rozhraní příkazového řádku a Power BI.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 926e0c8bc0fdff580b4d1e84be5940e0945d52ae
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92146282"
---
# <a name="view-azure-reservations"></a>Zobrazení rezervací Azure

Zakoupené rezervace můžete zobrazit a spravovat na webu Azure Portal.

## <a name="permissions-to-view-a-reservation"></a>Oprávnění k zobrazení rezervace

Pokud chcete zobrazit nebo spravovat rezervaci, musíte k ní mít oprávnění čtenáře nebo vlastníka. Když zakoupíte rezervaci, ve výchozím nastavení vy a správce účtu automaticky získáte roli vlastníka objednávky rezervace a samotné rezervace. Pokud chcete umožnit ostatním uživatelům zobrazit rezervaci, musíte je přidat jako **vlastníka** nebo **čtenáře** objednávky rezervace nebo samotné rezervace. Přidáním uživatele do předplatného zadaného pro fakturaci rezervace se uživatel nepřidá automaticky do rezervace. 

Další informace najdete v tématu [Přidání nebo změna uživatelů, kteří můžou spravovat rezervaci](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Zobrazení rezervace a informací o využití na webu Azure Portal

Zobrazení rezervace jako vlastník nebo čtenář

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do části [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
3. V seznamu se zobrazí všechny rezervace, u kterých máte roli vlastníka nebo čtenáře. U každé rezervace se zobrazí poslední známé procento využití.
4. Kliknutím na procento využití zobrazíte historii a podrobnosti využití. Podrobnosti najdete v následujícím videu.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-cli"></a>Získání informací o rezervacích a využití pomocí rozhraní API, PowerShellu nebo rozhraní příkazového řádku

Seznam všech rezervací můžete získat s využitím následujících zdrojů informací.
- [Rozhraní API: Objednávka rezervace – Výpis](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Objednávka rezervace – Výpis](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Rozhraní příkazového řádku: Objednávka rezervace – Výpis](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

Pomocí rozhraní API pro využití rezervovaných instancí můžete získat také informace o [využití rezervací](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Zobrazení rezervací a využití v Power BI

Pro uživatele Power BI jsou k dispozici dvě možnosti.
- Balíček obsahu: Informace o nákupech rezervací a data o využití jsou k dispozici v [balíčku obsahu Power BI pro Consumption Insights](/power-bi/desktop-connect-azure-cost-management). Pomocí tohoto balíčku obsahu můžete vytvářet libovolné sestavy. 
- Aplikace Cost Management: V [aplikaci Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) můžete využít předem vytvořené sestavy, které si můžete dál přizpůsobit.

## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).