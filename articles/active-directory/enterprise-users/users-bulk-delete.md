---
title: Hromadné odstranění uživatelů na portálu Azure Active Directory | Microsoft Docs
description: Hromadné odstranění uživatelů v centru pro správu Azure v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574459"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Hromadné odstranění uživatelů v Azure Active Directory

Pomocí portálu Azure Active Directory (Azure AD) můžete odebrat velký počet členů do skupiny pomocí souboru hodnot oddělených čárkami (CSV) pro hromadné odstranění uživatelů.

## <a name="understand-the-csv-template"></a>Princip šablony sdíleného svazku clusteru

Stáhněte si a vyplňte šablonu sdíleného svazku clusteru, abyste mohli úspěšně odstraňovat uživatele Azure AD. Šablona sdíleného svazku clusteru, kterou stáhnete, může vypadat jako v tomto příkladu:

![Tabulka pro nahrávání a volání s vysvětlením účelu a hodnot pro každý řádek a sloupec](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>Struktura šablony CSV

Řádky ve stažené šabloně CSV jsou následující:

- **Číslo verze**: první řádek obsahující číslo verze musí být zahrnut do souboru CSV pro nahrávání.
- **Záhlaví sloupců**: formát záhlaví sloupců je &lt; *název položky* &gt; [PropertyName] &lt; *povinný nebo prázdný* &gt; . Například, `User name [userPrincipalName] Required`. Některé starší verze šablony mohou mít drobné variace.
- **Řádek příklady**: v šabloně jsme zahrnuli řádek příkladů přípustných hodnot pro každý sloupec. Řádek příklady musíte odebrat a nahradit ho vlastními položkami.

### <a name="additional-guidance"></a>Další doprovodné materiály

- První dva řádky šablony nahrávání se nesmí odebrat ani změnit, jinak se nahrávání nedá zpracovat.
- Požadované sloupce jsou uvedeny jako první.
- Nedoporučujeme přidávat do šablony nové sloupce. Všechny další sloupce, které přidáte, se ignorují a nezpracovávají.
- Doporučujeme si stáhnout nejnovější verzi šablony CSV, jak je to možné.

## <a name="to-bulk-delete-users"></a>Hromadné odstranění uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **Uživatelé**  >  **hromadného odstranění**.
1. Na stránce **hromadné odstranění uživatele** vyberte **Stáhnout** pro příjem platného souboru CSV vlastností uživatele.

   ![Vyberte místní soubor CSV, ve kterém chcete vypsat uživatele, které chcete odstranit.](./media/users-bulk-delete/bulk-delete.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, kterého chcete odstranit. Jediná požadovaná hodnota je **hlavní název uživatele**. Pak soubor uložte.

   ![Soubor CSV obsahuje jména a ID uživatelů, kteří se mají odstranit.](./media/users-bulk-delete/delete-csv-file.png)

1. Na stránce **hromadné odstranění uživatele** v části **nahrát soubor CSV** přejděte k souboru. Když vyberete soubor a kliknete na Odeslat, spustí se ověření souboru CSV.
1. Když se obsah souboru ověří, zobrazí se soubor se **úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která uživatele odstraní.
1. Po dokončení operace odstranění se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   [![Na stránce výsledky hromadných operací ověřte stav odstranění.](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

V dalším kroku se můžete podívat, že uživatelé, které jste odstranili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-deleted-users-in-the-azure-portal"></a>Ověřit odstraněné uživatele v Azure Portal

1. Přihlaste se k Azure Portal pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. V části **Zobrazit** vyberte možnost **Všichni uživatelé** a ověřte, že uživatelé, které jste odstranili, již nejsou uvedeni.

### <a name="verify-deleted-users-with-powershell"></a>Ověření odstraněných uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Ověřte, že uživatelé, které jste odstranili, již nejsou uvedeni.

## <a name="next-steps"></a>Další kroky

- [Hromadné přidání uživatelů](users-bulk-add.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Hromadné obnovování uživatelů](users-bulk-restore.md)
