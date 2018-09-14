---
title: Přidat uživatele k účtu Azure Data Lake Analytics
description: Zjistěte, jak správně přidání uživatelů do vašeho účtu Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: f48dc07e27c6cb01a842f1f6d720ed6476028ef7
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45542308"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Přidání uživatele na webu Azure Portal

## <a name="start-the-add-user-wizard"></a>Začněte Průvodce přidáním uživatele
1. Otevřete Azure Data Lake Analytics prostřednictvím https://portal.azure.com.
2. Klikněte na tlačítko **Průvodce přidáním uživatele**.
3. V **vybrat uživatele** krok, vyhledejte uživatele, které chcete přidat. Klikněte na **Vybrat**.
4. **vybrat roli** kroku, vyberte **Data Lake Analytics Developer**. Tato role má minimální sadu oprávnění potřebná k odeslání/monitorování a Správa úloh U-SQL. Přiřadíte k této roli, pokud není určena skupině pro správu služeb Azure.
5. V **vybrat oprávnění katalogu** kroku, vyberte všechny další databáze potřebovat přístup k tomuto uživateli. Čtení a zápis k hlavní databázi se vyžaduje k odesílání úloh. Až to budete mít, klikněte na **OK**.
6. Volá se, v posledním kroku **přiřadit vybraná oprávnění** zkontrolovat změny, které průvodce provede. Klikněte na **OK**.


## <a name="configure-acls-for-data-folders"></a>Nakonfigurujte seznamy ACL pro složek s daty
Udělit "R-X" nebo "RWX", podle potřeby na složky obsahující vstupní data a výstupní data.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Volitelně můžete přidat uživatele do role Azure Data Lake Storage Gen1 **čtečky** role.
1.  Vyhledejte svůj účet Azure Data Lake Storage Gen1.
2.  Klikněte na **uživatelé**.
3. Klikněte na tlačítko **Add** (Přidat).
4.  Vyberte roli Azure RBAC k přiřazení této skupiny.
5.  Přiřadíte role Čtenář. Tato role má minimální sadu oprávnění potřebná k procházení a spravovat data uložená v ADLSGen1. Přiřadíte k této roli, pokud není určena skupině pro správu služeb Azure.
6.  Zadejte název skupiny.
7.  Klikněte na **OK**.

## <a name="adding-a-user-using-powershell"></a>Přidání uživatele pomocí Powershellu

1. Postupujte podle pokynů v tomto průvodci: [instalace a konfigurace Azure Powershellu](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Stáhněte si [přidat AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) skript prostředí PowerShell.
3. Spusťte skript prostředí PowerShell. 

Ukázkový příkaz poskytnout přístup uživatelů k odesílání úloh, zobrazit nová metadata úloh a zobrazení, které je původní metadata:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Další postup

* [Přehled služby Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí webu Azure portal](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure Powershellu](data-lake-analytics-manage-use-powershell.md)

