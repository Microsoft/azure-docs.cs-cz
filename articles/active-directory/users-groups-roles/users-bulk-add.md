---
title: Hromadné vytvoření uživatelů (Preview) na portálu Azure Active Directory | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure AD v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 08/30/2019
ms.topic: article
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: a10dfffa69652ee2b75053c04b97f6492c46811e
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174314"
---
# <a name="bulk-create-users-preview-in-azure-active-directory"></a>Hromadné vytvoření uživatelů (Preview) v Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadně vytvořené a odstranit operace hromadného uživatele, hromadnou pozvánku pro hosty a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="required-permissions"></a>Požadovaná oprávnění

Aby bylo možné hromadně vytvářet uživatele na portálu pro správu, musíte být přihlášeni jako globální správce nebo Správce uživatelů.

## <a name="to-create-users-in-bulk"></a>Hromadné vytváření uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **uživatelé** > **hromadné vytvoření**.
1. Na stránce **hromadně vytvořit uživatele** vyberte **Stáhnout** pro příjem platných souborů s hodnotami oddělenými čárkou (CSV) vlastností uživatele a pak přidejte přidat uživatele, které chcete vytvořit.

   ![Vyberte místní soubor CSV, ve kterém chcete zobrazit seznam uživatelů, které chcete přidat.](./media/users-bulk-add/upload-button.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, který chcete vytvořit. Jedinými požadovanými hodnotami jsou **jméno**, **hlavní název uživatele**, **počáteční heslo** a **zablokování přihlášení (ano/ne)** . Pak soubor uložte.

   ![Soubor CSV obsahuje jména a ID uživatelů, které se mají vytvořit.](./media/users-bulk-add/add-csv-file.png)

1. Na stránce **hromadně vytvořit uživatele (ve verzi Preview)** v části nahrát soubor CSV přejděte k souboru. Když vyberete soubor a kliknete na **Odeslat**, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru uvidíte, že se **soubor úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje nové uživatele.
1. Po dokončení operace importu se zobrazí oznámení o stavu úlohy hromadné operace.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledky hromadných operací (Preview)** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   ![Na stránce výsledků hromadných operací ověřte stav nahrávání.](./media/users-bulk-add/bulk-center.png)

Potom můžete zjistit, že uživatelé, které jste vytvořili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-users-in-the-azure-portal"></a>Ověřit uživatele v Azure Portal

1. [Přihlaste se k centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatele**.
1. V části **Zobrazit**vyberte možnost **Všichni uživatelé** a ověřte, že jsou uživatelé, které jste vytvořili, uvedeni.

### <a name="verify-users-with-powershell"></a>Ověření uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že uživatelé, které jste vytvořili, jsou uvedeni.

## <a name="bulk-import-service-limits"></a>Omezení služby hromadného importu

Každá Hromadná aktivita pro vytváření uživatelů může běžet po dobu až jedné hodiny. To umožňuje hromadné vytvoření minimálně 50 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odstranění uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Uživatelé hromadného obnovení](users-bulk-restore.md)
