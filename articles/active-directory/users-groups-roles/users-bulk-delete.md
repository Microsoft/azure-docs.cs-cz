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
ms.openlocfilehash: eb01b46d61b6ba99c3ec9c537dccc350074f5e05
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146440"
---
# <a name="bulk-delete-users-preview-in-azure-active-directory"></a>Hromadné odstranění uživatelů (Preview) v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů do skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadné odstranění uživatelů.

## <a name="to-bulk-delete-users"></a>Hromadné odstranění uživatelů

1. Přihlaste se ke svojí organizaci Azure AD pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **Uživatelé** > **hromadného odstranění**.
1. Na stránce **hromadné odstranění uživatele** vyberte **Stáhnout** pro příjem platného souboru CSV vlastností uživatele a pak přidejte uživatele, které chcete odstranit.

   ![Soubor CSV obsahuje jména a ID uživatelů, kteří se mají odstranit.](./media/users-bulk-delete/delete-csv-file.png)

1. Po dokončení úprav souboru CSV vyberte soubor v části **nahrání souboru CSV** , který se má ověřit.

   ![Vyberte místní soubor CSV, ve kterém chcete vypsat uživatele, které chcete odstranit.](./media/users-bulk-delete/bulk-delete.png)

1. Po ověření obsahu souboru je nutné před odesláním úlohy opravit všechny chyby.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte úlohu Azure Batch, která uživatele odstraní. Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

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
