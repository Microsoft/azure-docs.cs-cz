---
title: Hromadné obnovení odstraněných uživatelů (Preview) na portálu Azure Active Directory | Microsoft Docs
description: Obnovte odstraněné uživatele hromadně v centru pro správu Azure AD v Azure Active Directory
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
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174268"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Hromadné obnovení odstraněných uživatelů (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadně vytvořené a odstranit operace hromadného uživatele, hromadnou pozvánku pro hosty a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="to-bulk-restore-users"></a>Postup hromadného obnovení uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci Azure AD.
1. V Azure AD vyberte **uživatelé** > **odstraněno**.
1. Na stránce **odstraněné uživatele** výběrem možnosti **Hromadná obnova** nahrajte platný soubor CSV s vlastnostmi uživatelů, které chcete obnovit.

   ![Na stránce odstraněné uživatele vyberte příkaz hromadné obnovení.](./media/users-bulk-restore/bulk-restore.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, který chcete obnovit. Jediná požadovaná hodnota je **objectID**. Pak soubor uložte.

   ![Vyberte místní soubor CSV, ve kterém chcete zobrazit seznam uživatelů, které chcete přidat.](./media/users-bulk-restore/upload-button.png)

1. Na stránce **Hromadná obnova (Preview)** v části **nahrát soubor CSV**přejděte k souboru. Když vyberete soubor a kliknete na **Odeslat**, spustí se ověření souboru CSV.
1. Když se obsah souboru ověří, zobrazí se soubor se **úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která uživatele obnoví.
1. Po dokončení operace obnovení se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Na stránce výsledků hromadných operací ověřte stav nahrávání.](./media/users-bulk-restore/bulk-center.png)

V dalším kroku můžete zjistit, že uživatelé, které jste obnovili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="view-restored-users-in-the-azure-portal"></a>Zobrazit obnovené uživatele v Azure Portal

1. [Přihlaste se k centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte možnost **Všichni uživatelé** a ověřte, že jsou v seznamu uvedeni uživatelé, které jste obnovili.

### <a name="view-users-with-powershell"></a>Zobrazení uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že jsou uvedeni uživatelé, které jste obnovili.

## <a name="next-steps"></a>Další kroky

- [Hromadný import uživatelů](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
