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
ms.openlocfilehash: 9d384ea4749e2d0bc7edf8df7ac0508566f2f76b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72517102"
---
# <a name="bulk-remove-group-members-preview-in-azure-active-directory"></a>Hromadné odebrání členů skupiny (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů ze skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadnou odebrání členů skupiny.

## <a name="to-bulk-remove-group-members"></a>Hromadné odebrání členů skupiny

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce uživatele v organizaci. Vlastníci skupiny můžou také hromadně odebírat členy skupin, které vlastní.
1. V Azure AD vyberte **skupiny**  > **všechny skupiny**.
1. Otevřete skupinu, ze které odebíráte členy, a pak vyberte **Členové**.
1. Na stránce **Členové** vyberte **odebrat členy**.
1. Na stránce **hromadné odebrání členů skupiny (Preview)** vyberte **Stáhnout** a získejte šablonu souboru CSV s požadovanými vlastnostmi člena skupiny.

   ![Příkaz odebrat členy je na stránce profilu pro skupinu.](./media/groups-bulk-remove-members/remove-panel.png)

1. Otevřete soubor CSV a přidejte řádek pro každého člena skupiny, který chcete ze skupiny odebrat (požadované hodnoty jsou ID členského objektu nebo hlavní název uživatele). Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID pro členy, kteří mají být odebráni.](./media/groups-bulk-remove-members/csv-file.png)

1. Na stránce **hromadně odebrat členy skupiny (Preview)** v části **nahrát soubor CSV**přejděte k souboru. Po výběru souboru se spustí ověření souboru. csv.
1. Po ověření obsahu souboru se zobrazí stránka hromadného importu **úspěšně nahrané soubory**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která odebere členy skupiny ze skupiny.
1. Po dokončení operace odebrání se zobrazí oznámení, že hromadná operace byla úspěšná.

## <a name="check-removal-status"></a>Stav odebrání kontroly

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Stránka s výsledky hromadných operací zobrazuje stav hromadné žádosti](./media/groups-bulk-remove-members/bulk-center.png)

Chcete-li zobrazit podrobnosti o jednotlivých položkách řádku v rámci hromadné operace, vyberte hodnoty ve sloupcích **# úspěch**, **# selhání**nebo **Celkový počet požadavků** . Pokud dojde k chybám, zobrazí se důvody selhání.

## <a name="bulk-removal-service-limits"></a>Omezení služby hromadného odebrání

Každá Hromadná aktivita, ze které se mají odebrat seznam členů skupiny, může běžet po dobu až jedné hodiny. Tím se povolí odebrání seznamu minimálně 40 000 členů.

## <a name="next-steps"></a>Další kroky

- [Členové skupiny hromadného importu](groups-bulk-import-members.md)
- [Stažení členů skupiny](groups-bulk-download-members.md)
- [Stažení seznamu všech skupin](groups-bulk-download.md)
