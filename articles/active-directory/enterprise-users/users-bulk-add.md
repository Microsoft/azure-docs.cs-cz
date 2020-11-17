---
title: Hromadné vytvoření uživatelů na portálu Azure Active Directory | Microsoft Docs
description: Hromadné přidání uživatelů do centra pro správu Azure AD v Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42e26e9b1e3548d3caeff58079ec489c2d282a63
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646878"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Hromadné vytvoření uživatelů v Azure Active Directory

Azure Active Directory (Azure AD) podporuje hromadné operace vytváření a odstraňování uživatelů a podporuje stahování seznamů uživatelů. Stačí vyplnit šablonu hodnot oddělených čárkami (CSV), kterou si můžete stáhnout z portálu Azure AD.

## <a name="required-permissions"></a>Požadovaná oprávnění

Aby bylo možné hromadně vytvářet uživatele na portálu pro správu, musíte být přihlášeni jako globální správce nebo Správce uživatelů.

## <a name="understand-the-csv-template"></a>Princip šablony sdíleného svazku clusteru

Stáhněte si a vyplňte šablonu CSV hromadného nahrání, která vám umožní hromadně vytvářet uživatele Azure AD. Šablona sdíleného svazku clusteru, kterou stáhnete, může vypadat jako v tomto příkladu:

![Tabulka pro nahrávání a volání s vysvětlením účelu a hodnot pro každý řádek a sloupec](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> Pokud přidáváte jenom jednu položku pomocí šablony sdíleného svazku clusteru, musíte zachovat řádek 3 a přidat novou položku do řádku 4.

### <a name="csv-template-structure"></a>Struktura šablony CSV

Řádky ve stažené šabloně CSV jsou následující:

- **Číslo verze**: první řádek obsahující číslo verze musí být zahrnut do souboru CSV pro nahrávání.
- **Záhlaví sloupců**: formát záhlaví sloupců je &lt; *název položky* &gt; [PropertyName] &lt; *povinný nebo prázdný* &gt; . Například, `Name [displayName] Required`. Některé starší verze šablony mohou mít drobné variace.
- **Řádek příklady**: v šabloně jsme zahrnuli řádek příkladů přípustných hodnot pro každý sloupec. Řádek příklady musíte odebrat a nahradit ho vlastními položkami.

### <a name="additional-guidance"></a>Další doprovodné materiály

- První dva řádky šablony nahrávání se nesmí odebrat ani změnit, jinak se nahrávání nedá zpracovat.
- Požadované sloupce jsou uvedeny jako první.
- Nedoporučujeme přidávat do šablony nové sloupce. Všechny další sloupce, které přidáte, se ignorují a nezpracovávají.
- Doporučujeme si stáhnout nejnovější verzi šablony CSV, jak je to možné.
- Nezapomeňte zkontrolovat, že není k dispozici žádný nezamýšlený prázdný znak před nebo za libovolným polem. V případě **hlavního názvu uživatele** by tyto prázdné znaky způsobily selhání importu.

## <a name="to-create-users-in-bulk"></a>Hromadné vytváření uživatelů

1. [Přihlaste se ke svojí organizaci Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V Azure AD vyberte **Uživatelé**  >  **hromadně vytvořit**.
1. Na stránce **hromadně vytvořit uživatele** vyberte **Stáhnout** pro příjem platných souborů s hodnotami oddělenými čárkou (CSV) vlastností uživatele a pak přidejte přidat uživatele, které chcete vytvořit.

   ![Vyberte místní soubor CSV, ve kterém chcete zobrazit seznam uživatelů, které chcete přidat.](./media/users-bulk-add/upload-button.png)

1. Otevřete soubor CSV a přidejte řádek pro každého uživatele, který chcete vytvořit. Jedinými požadovanými hodnotami jsou **jméno**, **hlavní název uživatele**, **počáteční heslo** a **zablokování přihlášení (ano/ne)**. Pak soubor uložte.

   [![Soubor CSV obsahuje jména a ID uživatelů, které se mají vytvořit.](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. Na stránce **hromadně vytvořit uživatele** v části nahrát soubor CSV přejděte k souboru. Když vyberete soubor a kliknete na **Odeslat**, spustí se ověření souboru CSV.
1. Po ověření obsahu souboru uvidíte, že se **soubor úspěšně nahrál**. Pokud dojde k chybám, musíte je opravit předtím, než budete moct úlohu odeslat.
1. Když soubor projde ověřením, vyberte **Odeslat** a spusťte hromadnou operaci Azure, která importuje nové uživatele.
1. Po dokončení operace importu se zobrazí oznámení o stavu úlohy hromadné operace.

Pokud dojde k chybám, můžete si stáhnout a zobrazit soubor výsledků na stránce s **výsledky hromadné operace** . Soubor obsahuje důvod každé chyby. Odeslání souboru se musí shodovat s poskytnutou šablonou a zahrnout přesné názvy sloupců.

## <a name="check-status"></a>Zkontrolování stavu

Na stránce **výsledků hromadných operací** můžete zobrazit stav všech vašich nevyřízených hromadných požadavků.

   [![Na stránce výsledků hromadných operací se podívejte na vytvořit stav.](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

Potom můžete zjistit, že uživatelé, které jste vytvořili, existují v organizaci Azure AD, a to buď v Azure Portal, nebo pomocí PowerShellu.

## <a name="verify-users-in-the-azure-portal"></a>Ověřit uživatele v Azure Portal

1. [Přihlaste se k centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který je správcem uživatele v organizaci.
1. V navigačním podokně vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Uživatelé**.
1. V části **Zobrazit** vyberte možnost **Všichni uživatelé** a ověřte, že jsou uživatelé, které jste vytvořili, uvedeni.

### <a name="verify-users-with-powershell"></a>Ověření uživatelů pomocí PowerShellu

Spusťte následující příkaz:

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

Měli byste vidět, že uživatelé, které jste vytvořili, jsou uvedeni.

## <a name="bulk-import-service-limits"></a>Omezení služby hromadného importu

Každá Hromadná aktivita pro vytváření uživatelů může běžet po dobu až jedné hodiny. To umožňuje hromadné vytvoření minimálně 50 000 uživatelů.

## <a name="next-steps"></a>Další kroky

- [Hromadné odstraňování uživatelů](users-bulk-delete.md)
- [Stáhnout seznam uživatelů](users-bulk-download.md)
- [Hromadné obnovování uživatelů](users-bulk-restore.md)
