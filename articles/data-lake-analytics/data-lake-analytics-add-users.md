---
title: Přidání uživatelů k účtu Azure Data Lake Analytics
description: Přečtěte si, jak správně přidat uživatele k účtu Data Lake Analytics pomocí Průvodce přidáním uživatele a Azure PowerShell.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/24/2018
ms.openlocfilehash: c04b1bbd62e156aeb8d3a0ebb244cfbc753dec52
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219373"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Přidání uživatele na webu Azure Portal

## <a name="start-the-add-user-wizard"></a>Spuštění Průvodce přidáním uživatele
1. Otevřete Azure Data Lake Analytics prostřednictvím https://portal.azure.com .
2. Klikněte na tlačítko **Průvodce přidáním uživatele**.
3. V kroku **Vybrat uživatele** vyhledejte uživatele, kterého chcete přidat. Klikněte na **Vybrat**.
4. v kroku **Vybrat roli** vyberte **Data Lake Analytics vývojář**. Tato role má minimální sadu oprávnění potřebných pro odesílání, monitorování a správu úloh U-SQL. Pokud skupina není určená pro správu služeb Azure, přiřaďte ji k této roli.
5. V kroku **vybrat oprávnění katalogu** vyberte všechny další databáze, ke kterým bude uživatel potřebovat přístup. K odesílání úloh se vyžaduje přístup pro čtení a zápis do výchozí statické databáze s názvem "Master". Po dokončení klikněte na **OK**.
6. V posledním kroku s názvem **přiřadit vybraná oprávnění** zkontrolujte změny provedené průvodcem. Klikněte na **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurace seznamů ACL pro složky dat
V případě potřeby udělte "R-X" nebo "RWX" ve složkách, které obsahují vstupní data a výstupní data.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Volitelně můžete přidat uživatele do role **čtecího modulu** role Azure Data Lake Storage Gen1.
1.  Najděte svůj Azure Data Lake Storage Gen1 účet.
2.  Klikněte na **Uživatelé**.
3. Klikněte na **Přidat**.
4.  Vyberte roli Azure, která tuto skupinu přiřadí.
5.  Přiřaďte roli čtenáře. Tato role má minimální sadu oprávnění potřebných pro procházení a správu dat uložených v ADLSGen1. Pokud skupina není určená pro správu služeb Azure, přiřaďte ji k této roli.
6.  Zadejte název skupiny.
7.  Klikněte na **OK**.

## <a name="adding-a-user-using-powershell"></a>Přidání uživatele pomocí prostředí PowerShell

1. Postupujte podle pokynů v této příručce: [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/).
2. Stáhněte si [Add-AdlaJobUser.ps1](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) powershellový skript.
3. Spusťte PowerShellový skript. 

Vzorový příkaz, který uživateli poskytne přístup k odesílání úloh, zobrazení nových metadat úlohy a zobrazení starých metadat:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Data Lake Analytics pomocí Azure Portal](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
