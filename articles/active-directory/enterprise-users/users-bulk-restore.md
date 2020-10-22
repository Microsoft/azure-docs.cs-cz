---
title: Hromadné obnovení odstraněných uživatelů na portálu Azure Active Directory | Microsoft Docs
description: Obnovte odstraněné uživatele hromadně v centru pro správu Azure AD v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 04/27/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4031356c3a2ff51f6f3da8b53dd0dcc9fd5a426
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92374805"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Hromadné obnovení odstraněných uživatelů v Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadné operace obnovení uživatelů a podporuje stahování seznamů uživatelů, skupin a členů skupiny.

## <a name="understand-the-csv-template"></a>Princip šablony sdíleného svazku clusteru

Stáhněte si a vyplňte šablonu sdíleného svazku clusteru, která vám umožní úspěšně obnovit uživatele Azure AD. Šablona sdíleného svazku clusteru, kterou stáhnete, může vypadat jako v tomto příkladu:

![Tabulka pro nahrávání a volání s vysvětlením účelu a hodnot pro každý řádek a sloupec](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>Struktura šablony CSV

Řádky ve stažené šabloně CSV jsou následující:

- **Číslo verze**: první řádek obsahující číslo verze musí být zahrnut do souboru CSV pro nahrávání.
- **Záhlaví sloupců**: formát záhlaví sloupců je &lt; *název položky* &gt; [PropertyName] &lt; *povinný nebo prázdný* &gt; . Například, `Object ID [objectId] Required`. Některé starší verze šablony mohou mít drobné variace.
- **Řádek příklady**: v šabloně jsme zahrnuli řádek příkladů přípustných hodnot pro každý sloupec. Řádek příklady musíte odebrat a nahradit ho vlastními položkami.

### <a name="additional-guidance"></a>Další doprovodné materiály

- První dva řádky šablony nahrávání se nesmí odebrat ani změnit, jinak se nahrávání nedá zpracovat.
- Požadované sloupce jsou uvedeny jako první.
- Nedoporučujeme přidávat do šablony nové sloupce. Všechny další sloupce, které přidáte, se ignorují a nezpracovávají.
- Doporučujeme si stáhnout nejnovější verzi šablony CSV, jak je to možné.

## <a name="to-bulk-restore-users"></a>Postup hromadného obnovení uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci Azure AD.
1. V Azure AD vyberte **Uživatelé**  >  **odstranění**.
1. Na stránce **odstraněné uživatele** výběrem možnosti **Hromadná obnova** nahrajte platný soubor CSV s vlastnostmi uživatelů, které chcete obnovit.

    ![Na stránce odstraněné uživatele vyberte příkaz hromadné obnovení.](./media/users-bulk-restore/bulk-restore.png)

1. Otevřete šablonu sdíleného svazku clusteru a přidejte řádek pro každého uživatele, který chcete obnovit. Jediná požadovaná hodnota je **objectID**. Pak soubor uložte.

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="Vyberte místní soubor CSV, ve kterém chcete zobrazit seznam uživatelů, které chcete přidat.":::

1. Na stránce **hromadné obnovení** v části **nahrát soubor CSV**přejděte k souboru. Když vyberete soubor a kliknete na **Odeslat**, spustí se ověření souboru CSV.
1. Když se obsah souboru ověří, zobrazí se soubor se **úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která uživatele obnoví.
1. Po dokončení operace obnovení se zobrazí oznámení, že hromadná operace byla úspěšná.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

[![Ověřte stav na stránce výsledků hromadných operací.](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

V dalším kroku můžete zjistit, že uživatelé, které jste obnovili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="view-restored-users-in-the-azure-portal"></a>Zobrazit obnovené uživatele v Azure Portal

1. [Přihlaste se k centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. V části **Zobrazit**vyberte možnost **Všichni uživatelé** a ověřte, že jsou v seznamu uvedeni uživatelé, které jste obnovili.

### <a name="view-users-with-powershell"></a>Zobrazení uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že jsou uvedeni uživatelé, které jste obnovili.

## <a name="next-steps"></a>Další kroky

- [Hromadný import uživatelů](users-bulk-add.md)
- [Hromadné odstraňování uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
