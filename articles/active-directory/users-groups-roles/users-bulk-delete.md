---
title: Hromadné odstranění uživatelů na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: Hromadné odstranění uživatelů v Centru pro správu Azure ve Službě Azure Active Directory
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
ms.openlocfilehash: beb8b4f35dc5f02e59cced05a6bcfc235d42f996
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532801"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Hromadné odstranění uživatelů ve službě Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů do skupiny pomocí souboru hodnot oddělených čárkami (CSV) k hromadnému odstranění uživatelů.

## <a name="to-bulk-delete-users"></a>Hromadné odstranění uživatelů

1. [Přihlaste se k organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. Ve službě Azure AD vyberte **možnost Uživatelé** > **hromadné odstranění**.
1. Na stránce **Hromadné odstranění uživatele** vyberte **Stáhnout,** chcete-li získat platný soubor CSV s vlastnostmi uživatele.

   ![Vyberte místní soubor CSV, ve kterém zobrazíte seznam uživatelů, které chcete odstranit.](./media/users-bulk-delete/bulk-delete.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, kterého chcete odstranit. Jedinou požadovanou hodnotou je **hlavní název uživatele**. Pak soubor uložte.

   ![Soubor CSV obsahuje názvy a ID uživatelů, které mají být odstraněny.](./media/users-bulk-delete/delete-csv-file.png)

1. Na stránce **Hromadné odstranění uživatele** vyhledejte v části Upload **souboru CSV**soubor. Když vyberete soubor a klepnete na tlačítko Odeslat, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru se **soubor úspěšně nahraje**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
1. Když váš soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která odstraní uživatele.
1. Po dokončení operace odstranění se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete soubor výsledků stáhnout a zobrazit na stránce **Výsledky hromadné operace.** Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **S výsledky hromadné operace.**

   [![](media/users-bulk-delete/bulk-center.png "Check delete status in the Bulk Operations Results page")](media/users-bulk-delete/bulk-center.png#lightbox)

Dále můžete zkontrolovat, zda uživatelé, které jste odstranili, existují v organizaci Azure AD buď na webu Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Ověření odstraněných uživatelů na webu Azure Portal

1. Přihlaste se k portálu Azure pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte pouze **všichni uživatelé** a ověřte, že uživatelé, které jste odstranili, již nejsou uvedeni.

### <a name="verify-deleted-users-with-powershell"></a>Ověření odstraněných uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ověřte, zda již nejsou v seznamu uvedeni odstranění uživatelé.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
