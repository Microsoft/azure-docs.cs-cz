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
ms.openlocfilehash: f53ade09c5e2e7db0499122526a1de482af9378f
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901617"
---
# <a name="bulk-restore-deleted-users-preview-in-azure-active-directory"></a>Hromadné obnovení odstraněných uživatelů (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadně vytvořené a odstranit operace hromadného uživatele, hromadnou pozvánku pro hosty a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="to-bulk-restore-users"></a>Postup hromadného obnovení uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **Uživatelé** > **odstranění**.
1. Na stránce **odstraněné uživatele** výběrem možnosti **Hromadná obnova** nahrajte platný soubor CSV s vlastnostmi uživatelů, které chcete obnovit.

   ![Na stránce odstraněné uživatele vyberte příkaz hromadné obnovení.](./media/users-bulk-restore/bulk-restore.png)

1. Po dokončení úprav souboru CSV nebo pokud máte vlastní připravená nahrávání, vyberte soubor v části **nahrát soubor CSV** , který chcete ověřit.

   ![Vyberte místní soubor CSV, ve kterém chcete zobrazit seznam uživatelů, které chcete přidat.](./media/users-bulk-restore/upload-button.png)

1. Pokud dojde k ověření obsahu souboru, opravte informace o souboru a znovu odešlete soubor, pokud dojde k chybám. Odesláním platného souboru se spustí úloha nahrávání dat automaticky.
1. Po úspěšném ověření souboru CSV vyberte **Odeslat** a spusťte úlohu Azure Batch, která uživatele obnoví. Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s výsledky hromadné operace. Soubor obsahuje důvod každé chyby.

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

## <a name="next-steps"></a>Další postup

- [Hromadný import uživatelů](users-bulk-add.md)
- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
