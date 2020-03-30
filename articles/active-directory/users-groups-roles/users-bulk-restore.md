---
title: Hromadné obnovení odstraněných uživatelů (preview) na portálu Azure Active Directory | Dokumenty společnosti Microsoft
description: Hromadné obnovení odstraněných uživatelů v Centru pro správu Azure AD ve službě Azure Active Directory
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
ms.openlocfilehash: d392ae97a8325dd4a56acd807ebfb2b951216eae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72174268"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Odstranění uživatelé (preview) hromadného obnovení ve službě Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadné uživatelské operace vytváření a odstraňování, hromadné pozvání pro hosty a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="to-bulk-restore-users"></a>Chcete-li hromadně obnovit uživatele

1. [Přihlaste se ke své organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci Azure AD.
1. Ve službě Azure AD vyberte **Položku Odstraněné uživatele** > **Deleted**.
1. Na stránce **Odstranění uživatelé** vyberte **Hromadné obnovení,** chcete-li nahrát platný soubor CSV vlastností uživatelů, které chcete obnovit.

   ![Výběr příkazu hromadného obnovení na stránce Odstranění uživatelé](./media/users-bulk-restore/bulk-restore.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, kterého chcete obnovit. Jedinou požadovanou hodnotou je **ObjectID**. Pak soubor uložte.

   ![Vyberte místní soubor CSV, do kterého uvedete seznam uživatelů, které chcete přidat.](./media/users-bulk-restore/upload-button.png)

1. Na stránce **Hromadné obnovení (náhled)** vyhledejte v části **Upload souboru CSV**soubor. Když vyberete soubor a klepnete na **tlačítko Odeslat**, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru se **soubor úspěšně nahraje**. Pokud se jedná o chyby, je nutné je před odesláním úlohy opravit.
1. Když váš soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která obnoví uživatele.
1. Po dokončení operace obnovení se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete soubor výsledků stáhnout a zobrazit na stránce **Výsledky hromadné operace.** Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Stav všech nevyřízených hromadných požadavků můžete zobrazit na stránce **Výsledky hromadné operace (preview).**

   ![Kontrola stavu nahrávání na stránce Výsledky hromadných operací](./media/users-bulk-restore/bulk-center.png)

Dále můžete zkontrolovat, zda uživatelé, které jste obnovili, existují v organizaci Azure AD buď na webu Azure Portal, nebo pomocí PowerShellu.

## <a name="view-restored-users-in-the-azure-portal"></a>Zobrazení obnovených uživatelů na webu Azure Portal

1. [Přihlaste se do Centra pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte **Možnost Všichni uživatelé** a ověřte, zda jsou v seznamu uvedeni uživatelé, které jste obnovili.

### <a name="view-users-with-powershell"></a>Zobrazení uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že uživatelé, které jste obnovili, jsou uvedeny.

## <a name="next-steps"></a>Další kroky

- [Uživatelé hromadného importu](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
