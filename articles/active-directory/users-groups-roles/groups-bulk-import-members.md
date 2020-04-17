---
title: Hromadné nahrávání importu pro přidání členů do skupiny – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přidejte hromadně členy skupiny v Centru pro správu Služby Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/16/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15960caa55274f06159263c1af4a6c8280e83f4e
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533464"
---
# <a name="bulk-import-group-members-in-azure-active-directory"></a>Členové skupiny hromadného importu ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete do skupiny přidat velký počet členů pomocí souboru hodnot oddělených čárkami (CSV) pro hromadné členy skupiny importu.

## <a name="to-bulk-import-group-members"></a>Chcete-li hromadně importovat členy skupiny

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupin mohou také hromadně importovat členy skupin, které vlastní.
1. Ve službě Azure AD vyberte **Skupiny** > **všechny skupiny**.
1. Otevřete skupinu, do které přidáváte členy, a vyberte **Členové**.
1. Na stránce **Členové** vyberte **Importovat členy**.
1. Na stránce **Členové skupiny hromadného importu** vyberte **Stáhnout,** chcete-li získat šablonu souboru CSV s požadovanými vlastnostmi členů skupiny.

    ![Příkaz Importovat členy je na stránce profilu skupiny.](./media/groups-bulk-import-members/import-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete importovat do skupiny (požadované hodnoty jsou Buď **ID členského objektu,** nebo **hlavní název uživatele**). Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID pro členy k importu](./media/groups-bulk-import-members/csv-file.png)

1. Na stránce **Členové skupiny hromadného importu** v části **Nahrání souboru CSV**vyhledejte soubor. Když vyberete soubor, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru se na stránce hromadného **importu zobrazí úspěšně nahraný soubor**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje členy skupiny do skupiny.
1. Po dokončení operace importu se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-import-status"></a>Zkontrolovat stav importu

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **S výsledky hromadné operace.**

[![](media/groups-bulk-import-members/bulk-center.png "Check status in the Bulk Operations Results page")](media/groups-bulk-import-members/bulk-center.png#lightbox)

Podrobnosti o jednotlivých řádkových položkách v rámci hromadné operace vyberte hodnoty ve sloupcích **#Úspěch**, **Počet neúspěšných**nebo **Celkový počet požadavků.** Pokud došlo k chybám, budou uvedeny důvody selhání.

## <a name="bulk-import-service-limits"></a>Limity služeb hromadného importu

Každá hromadná aktivita pro import seznamu členů skupiny může běžet až jednu hodinu. To umožňuje dovoz seznamu nejméně 40.000 členů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odebrání členů skupiny](groups-bulk-remove-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stáhnout seznam všech skupin](groups-bulk-download.md)
