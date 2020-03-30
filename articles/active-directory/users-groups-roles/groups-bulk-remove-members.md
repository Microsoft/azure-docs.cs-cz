---
title: Hromadné odebrání členů skupiny nahráním souboru CSV – Azure Active Directory | Dokumenty společnosti Microsoft
description: Přidejte uživatele hromadně v Centru pro správu Azure.
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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72517102"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Hromadné odebrání členů skupiny (preview) ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů ze skupiny pomocí souboru hodnot oddělených čárkami (CSV) k hromadnému odebrání členů skupiny.

## <a name="to-bulk-remove-group-members"></a>Hromadné odebrání členů skupiny

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupin mohou také hromadně odebrat členy skupin, které vlastní.
1. Ve službě Azure AD vyberte **Skupiny** > **všechny skupiny**.
1. Otevřete skupinu, ze které odebírat členy, a vyberte **Členové**.
1. Na stránce **Členové** vyberte **Odebrat členy**.
1. Na stránce **Hromadné odebrání členů skupiny (Náhled)** vyberte **Stáhnout,** chcete-li získat šablonu souboru CSV s požadovanými vlastnostmi členů skupiny.

   ![Příkaz Odebrat členy je na stránce profilu skupiny.](./media/groups-bulk-remove-members/remove-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete odebrat ze skupiny (požadované hodnoty jsou ID členského objektu nebo hlavní název uživatele). Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID pro členy odebrat](./media/groups-bulk-remove-members/csv-file.png)

1. Na stránce **Hromadné odebrání členů skupiny (Náhled)** v části **Nahrání souboru CSV**vyhledejte soubor. Když vyberete soubor, spustí se ověření souboru .csv.
1. Po ověření obsahu souboru se na stránce hromadného **importu zobrazí úspěšně nahraný soubor**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
1. Když váš soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která odebere členy skupiny ze skupiny.
1. Po dokončení operace odebrání se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-removal-status"></a>Zkontrolovat stav odebrání

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **Výsledky hromadné operace (preview).**

   ![Stránka Výsledky hromadných operací zobrazuje stav hromadnéžádosti](./media/groups-bulk-remove-members/bulk-center.png)

Podrobnosti o jednotlivých řádkových položkách v rámci hromadné operace vyberte hodnoty ve sloupcích **#Úspěch**, **Počet neúspěšných**nebo **Celkový počet požadavků.** Pokud došlo k chybám, budou uvedeny důvody selhání.

## <a name="bulk-removal-service-limits"></a>Limity služby hromadného odebrání

Každá hromadná aktivita pro odebrání seznamu členů skupiny může běžet až jednu hodinu. To umožňuje odebrání seznamu alespoň 40 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stáhnout seznam všech skupin](groups-bulk-download.md)
