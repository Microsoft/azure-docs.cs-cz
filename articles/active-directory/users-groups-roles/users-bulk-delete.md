---
title: Hromadné odstranění uživatelů (Preview) na portálu Azure Active Directory | Microsoft Docs
description: Hromadné odstranění uživatelů v centru pro správu Azure v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/15/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7c47887c12c8bf9be7a0c5b11dfb3f099965cb7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174388"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Hromadné odstranění uživatelů (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů do skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadné odstranění uživatelů.

## <a name="to-bulk-delete-users"></a>Hromadné odstranění uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **uživatelé** > **hromadné odstranění**.
1. Na stránce **hromadné odstranění uživatele** vyberte **Stáhnout** pro příjem platného souboru CSV vlastností uživatele.

   ![Vyberte místní soubor CSV, ve kterém chcete vypsat uživatele, které chcete odstranit.](./media/users-bulk-delete/bulk-delete.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, kterého chcete odstranit. Jediná požadovaná hodnota je **hlavní název uživatele**. Pak soubor uložte.

   ![Soubor CSV obsahuje jména a ID uživatelů, kteří se mají odstranit.](./media/users-bulk-delete/delete-csv-file.png)

1. Na stránce **hromadné odstranění uživatele (Preview)** v části **nahrání souboru CSV**přejděte k souboru. Když vyberete soubor a kliknete na Odeslat, spustí se ověření souboru CSV.
1. Když se obsah souboru ověří, zobrazí se soubor se **úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která uživatele odstraní.
1. Po dokončení operace odstranění se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Na stránce výsledků hromadných operací ověřte stav nahrávání.](./media/users-bulk-delete/bulk-center.png)

V dalším kroku se můžete podívat, že uživatelé, které jste odstranili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Ověřit odstraněné uživatele v Azure Portal

1. Přihlaste se k Azure Portal pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte možnost **Všichni uživatelé** a ověřte, že uživatelé, které jste odstranili, již nejsou uvedeni.

### <a name="verify-deleted-users-with-powershell"></a>Ověření odstraněných uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ověřte, že uživatelé, které jste odstranili, již nejsou uvedeni.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
