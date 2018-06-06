---
title: Přidání uživatelů k účtu Azure Data Lake Analytics
description: Naučte se správně přidání uživatelů do účtu Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34717226"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Přidání uživatele na portálu Azure

## <a name="start-the-add-user-wizard"></a>Spuštění Průvodce přidáním uživatele
1. Otevřete Azure Data Lake Analytics pomocí https://portal.azure.com.
2. Klikněte na tlačítko **Průvodce přidáním uživatele**.
3. V **vyberte uživatele** krok, vyhledejte uživatele, který chcete přidat. Klikněte na **Vybrat**.
4. **vyberte role** kroku, vyberte **Data Lake Analytics vývojáře**. Tato role má minimální sadu oprávnění potřebná k odeslání a monitorování nebo spravovat úloh U-SQL. Přiřadíte k této roli, pokud není určena skupině pro správu služby Azure.
5. V **vyberte oprávnění katalogu** kroku, vyberte všechny další databáze, tento uživatel bude potřebovat přístup k. Oprávnění ke čtení a zápisu do hlavní databáze se vyžaduje k odesílání úloh. Až to budete mít, klikněte na **OK**.
6. V posledním kroku názvem **přiřadit vybrané oprávnění** zkontrolovat změny, bude průvodce. Klikněte na **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurace seznamů řízení přístupu pro složek s daty
Udělit "R-X" nebo "RWX", podle potřeby na složek, které neobsahují data vstupní a výstupní data.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Volitelně lze přidat uživatele k roli Azure Data Lake Store **čtečky** role.
1.  Najít účtu Azure Data Lake Store.
2.  Klikněte na **uživatelé**.
3. Klikněte na tlačítko **Add** (Přidat).
4.  Vyberte Role RBAC služby Azure přiřadit této skupiny.
5.  Přiřadíte role Čtenář. Tato role má minimální sadu oprávnění potřebná k procházení a spravovat data uložená v ADLS. Přiřadíte k této roli, pokud není určena skupině pro správu služby Azure.
6.  Zadejte název skupiny.
7.  Klikněte na **OK**.

## <a name="adding-a-user-using-powershell"></a>Přidání uživatele pomocí prostředí PowerShell

1. Postupujte podle pokynů v této příručce: [postup instalace a konfigurace prostředí Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Stažení [přidat AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) skript prostředí PowerShell.
3. Spusťte skript prostředí PowerShell. 

Příkaz Ukázka umožnit přístup uživatelů k odesílání úloh, zobrazit metadata nových úloh a zobrazení, které je starší metadata:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Další postup

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

