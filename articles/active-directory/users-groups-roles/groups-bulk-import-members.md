---
title: Nahrání hromadného importu pro přidání členů do skupiny – Azure Active Directory | Microsoft Docs
description: Přidejte členy skupiny hromadně v centru pro správu Azure Active Directory.
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
ms.openlocfilehash: 0335522b398c22fb395305b33bac5a56ba565ee2
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910851"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Členové skupiny hromadného importu (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete do skupiny přidat velký počet členů pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou import členů skupiny.

> [!NOTE]
> Hromadné operace Azure AD jsou funkcí veřejné verze Preview služby Azure AD a jsou dostupné s placeným licenčním plánem Azure AD. Další informace o tom, jak používat verzi Preview, najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="bulk-import-service-limits"></a>Omezení služby hromadného importu

Každá Hromadná aktivita pro import seznamu členů skupiny může běžet až po dobu jedné hodiny. To umožňuje import seznamu minimálně 40 000 členů.

## <a name="to-bulk-import-group-members"></a>Hromadný import členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně importovat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny** > **všechny skupiny**.
1. Otevřete skupinu, do které přidáváte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **importovat členy** pro stažení, aktualizaci a nahrání souboru CSV se seznamem členů, které chcete do skupiny importovat.

   ![Příkaz Importovat členy je na stránce profilu pro skupinu.](./media/groups-bulk-import-members/import-panel.png)

## <a name="check-import-status"></a>Ověřit stav importu

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-import-members/bulk-center.png)

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
