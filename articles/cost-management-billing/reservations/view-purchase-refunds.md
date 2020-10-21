---
title: Zobrazení nákupu rezervací Azure a transakcí refundace
description: Zjistěte, jak zobrazit nákup rezervací Azure a transakce refundace.
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: b986aa2bfce203be85adbcde8e2966c167bf7ca1
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151771"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Zobrazení nákupu rezervací a transakcí refundace

K dispozici je několik různých způsobů, jak nákup rezervací a transakce refundace zobrazit. Můžete použít Azure Portal, Power BI a rozhraní REST API.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Zobrazení transakcí rezervací na webu Azure Portal

Správce fakturace pro podnikovou registraci nebo Smlouvu se zákazníkem Microsoftu může zobrazovat transakce rezervací v modulu Správa nákladů a fakturace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte **Cost Management a fakturace**.
1. Vyberte **Transakce rezervací**.
1. Pokud chcete filtrovat výsledky, vyberte **Časový rozsah**, **Typ** nebo **Popis**.
1. Vyberte **Použít**.

[![Screenshot znázorňující transakce rezervací na webu Azure Portal](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Zobrazení transakcí rezervací v Power BI

Správce fakturace pro podnikovou registraci nebo Smlouvu se zákazníkem Microsoftu může zobrazovat transakce rezervací s využitím aplikace Power Bi Cost Management.

1. Získejte [aplikaci Power BI Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Přejděte k sestavě nákupů RI.

[![Příklad ukazující transakce rezervací](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Další informace najdete v tématu [Analýza nákladů pomocí aplikace Power BI Azure Cost Management pro smlouvy Enterprise](../costs/analyze-cost-data-azure-cost-management-power-bi-template-app.md).

## <a name="use-apis-to-get-reservation-transactions"></a>Použití rozhraní API k získání transakcí rezervací

Uživatelé se smlouvou Enterprise (EA) a Smlouvou se zákazníkem Microsoftu mohou k získání údajů o transakcích rezervací využít [rozhraní API Reservation Transactions – List](/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak spravovat rezervace, najdete v tématu [Správa rezervací Azure](manage-reserved-vm-instance.md).
- Další informace o rezervacích Azure najdete v následujících článcích:
  - [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
  - [Správa rezervací v Azure](manage-reserved-vm-instance.md)