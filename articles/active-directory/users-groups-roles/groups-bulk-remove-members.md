---
title: Hromadné odebrání členů skupiny odesláním souboru CSV – Azure Active Directory | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 09/11/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b153db3570f10ad5ad130dedd0bd20fe22776ed6
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910963"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Hromadné odebrání členů skupiny (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů ze skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou odebrání členů skupiny.

> [!NOTE]
> Hromadné operace Azure AD jsou funkcí veřejné verze Preview služby Azure AD a jsou dostupné s placeným licenčním plánem Azure AD. Další informace o tom, jak používat verzi Preview, najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="bulk-removal-service-limits"></a>Omezení služby hromadného odebrání

Každá Hromadná aktivita, ze které se mají odebrat seznam členů skupiny, může běžet po dobu až jedné hodiny. Tím se povolí odebrání seznamu minimálně 40 000 členů.

## <a name="to-bulk-remove-group-members"></a>Hromadné odebrání členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně odebírat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny** > **všechny skupiny**.
1. Otevřete skupinu, ze které odebíráte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte možnost **odebrat členy** pro stažení, aktualizaci a nahrání souboru CSV se seznamem členů, které chcete ze skupiny odebrat.

   ![Příkaz odebrat členy je na stránce profilu pro skupinu.](./media/groups-bulk-remove-members/remove-panel.png)

## <a name="check-removal-status"></a>Stav odebrání kontroly

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-remove-members/bulk-center.png)

## <a name="next-steps"></a>Další postup

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
