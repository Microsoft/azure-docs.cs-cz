---
title: Přidání uživatelů do účtu Azure Data Lake Analytics
description: Přečtěte si, jak správně přidat uživatele do účtu Data Lake Analytics pomocí Průvodce přidáním uživatele a Azure PowerShellu.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672734"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Přidání uživatele na webu Azure Portal

## <a name="start-the-add-user-wizard"></a>Spuštění Průvodce přidáním uživatele
1. Otevřete Azure Data Lake https://portal.azure.comAnalytics přes .
2. Klepněte na **tlačítko Přidat Průvodce uživatelem**.
3. V kroku **Vybrat uživatele** vyhledejte uživatele, kterého chcete přidat. Klepněte na **tlačítko Vybrat**.
4. v kroku **Vybrat roli** vyberte **vývojáře Data Lake Analytics**. Tato role má minimální sadu oprávnění potřebných k odeslání/monitorování/správě úloh U-SQL. Přiřazení k této roli, pokud skupina není určena pro správu služeb Azure.
5. V kroku **Vybrat oprávnění katalogu** vyberte všechny další databáze, ke kterým bude uživatel potřebovat přístup. Pro odeslání úloh je nutný přístup pro čtení a zápis do hlavní databáze. Po dokončení klikněte na **OK**.
6. V posledním kroku s názvem **Přiřadit vybraná oprávnění** zkontrolujte změny, které průvodce provede. Klikněte na tlačítko **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurace seznamu AC pro datové složky
Udělit "R-X" nebo "RWX", podle potřeby, na složky obsahující vstupní data a výstupní data.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Volitelně můžete přidat uživatele do role **čtečky role Azure** Data Lake Storage Gen1.
1.  Najděte si svůj účet Azure Data Lake Storage Gen1.
2.  Klikněte na **Uživatelé**.
3. Klikněte na **Přidat**.
4.  Vyberte roli Azure RBAC pro přiřazení této skupiny.
5.  Přiřazení k roli Čtenáře. Tato role má minimální sadu oprávnění potřebných k procházení nebo správě dat uložených v ADLSGen1. Tuto roli přiřaďte, pokud skupina není určena pro správu služeb Azure.
6.  Zadejte název skupiny.
7.  Klikněte na tlačítko **OK**.

## <a name="adding-a-user-using-powershell"></a>Přidání uživatele pomocí PowerShellu

1. Postupujte podle pokynů v této příručce: [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Stáhněte si skript prostředí PowerShell [Add-AdlaJobUser.ps1.](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1)
3. Spusťte skript prostředí PowerShell. 

Ukázkový příkaz, který uživateli poskytuje přístup k odesílání úloh, zobrazení nových metadat úlohy a zobrazení starých metadat je následující:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Další kroky

* [Přehled Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Začínáme s Analýzou datových jezer pomocí portálu Azure](data-lake-analytics-get-started-portal.md)
* [Správa Azure Data Lake Analytics pomocí Azure PowerShellu](data-lake-analytics-manage-use-powershell.md)

