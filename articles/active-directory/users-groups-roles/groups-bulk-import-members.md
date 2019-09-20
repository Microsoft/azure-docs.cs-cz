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
ms.openlocfilehash: 4412bc9ce8d78b5810b25b60724575af66774127
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146252"
---
# <a name="bulk-import-group-members-preview-in-azure-active-directory"></a>Členové skupiny hromadného importu (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete do skupiny přidat velký počet členů pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou import členů skupiny.

> [!NOTE]
> Hromadné operace Azure AD jsou funkcí veřejné verze Preview služby Azure AD a jsou dostupné s placeným licenčním plánem Azure AD. Další informace o tom, jak používat verzi Preview, najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="to-bulk-import-group-members"></a>Hromadný import členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně importovat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny** > **všechny skupiny**.
1. Otevřete skupinu, do které přidáváte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **importovat členy**.
1. Na stránce **Hromadný import členů skupiny (Preview)** vyberte **Stáhnout** a získejte šablonu souboru CSV s požadovanými vlastnostmi člena skupiny.

    ![Příkaz Importovat členy je na stránce profilu pro skupinu.](./media/groups-bulk-import-members/import-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete do skupiny importovat (požadované hodnoty jsou buď **ID členského objektu** , nebo **hlavní název uživatele**). Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID pro import členů.](./media/groups-bulk-import-members/csv-file.png)

1. Na stránce **Hromadný import členů skupiny (Preview)** v části **nahrát soubor CSV**přejděte k souboru. Po výběru souboru se spustí ověření souboru CSV.
1. Po ověření obsahu souboru se zobrazí stránka hromadného importu **úspěšně nahrané soubory**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje členy skupiny do skupiny.
1. Po dokončení operace importu se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-import-status"></a>Ověřit stav importu

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-import-members/bulk-center.png)

Chcete-li zobrazit podrobnosti o jednotlivých položkách řádku v rámci hromadné operace, vyberte hodnoty ve sloupcích **# úspěch**, **# selhání**nebo **Celkový počet požadavků** . Pokud dojde k chybám, zobrazí se důvody selhání.

## <a name="bulk-import-service-limits"></a>Omezení služby hromadného importu

Každá Hromadná aktivita pro import seznamu členů skupiny může běžet až po dobu jedné hodiny. To umožňuje import seznamu minimálně 40 000 členů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stažení seznamu všech skupin](groups-bulk-download.md)
