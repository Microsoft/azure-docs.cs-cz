---
title: 'Rychlý start: Pozastavení a obnovení výpočtů v Azure SQL Data Warehouse-Azure Portal | Microsoft Docs'
description: Pomocí Azure Portal můžete pozastavit výpočty v Azure SQL Data Warehouse a ušetřit tak náklady. Až budete připraveni k používání datového skladu, obnovte výpočetní prostředky.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 46fb1fc6455cd35d8bb1eaf0a1b7ee499f5c1caf
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574882"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Pozastavení a obnovení výpočetní služby Azure SQL Data Warehouse v Azure Portal

Pomocí Azure Portal můžete pozastavit výpočty v Azure SQL Data Warehouse a ušetřit tak náklady. Až budete připraveni k používání datového skladu, [obnovte výpočetní](sql-data-warehouse-manage-compute-overview.md) prostředky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Před zahájením

Pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořte datový sklad s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete ušetřit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nebudete databázi používat během noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne. Za výpočetní prostředky se vám nebudou účtovat žádné poplatky, zatímco databáze je pozastavená. Budeme se ale nadále účtovat za úložiště. 

Chcete-li pozastavit SQL Data Warehouse, postupujte podle následujících kroků.

1. Na webu Azure Portal klikněte vlevo na **Databáze SQL**.
2. Na stránce **Databáze SQL** vyberte **mySampleDataWarehouse**. Tím se otevře datový sklad. 
3. Na stránce **mySampleDataWarehouse** je **stav** oznámení **online**.

    ![COMPUTE online](media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li datový sklad pozastavit, klikněte na tlačítko **pozastavit** . 
5. V případě, že chcete pokračovat, se zobrazí dotaz s potvrzením. Klikněte na **Ano**.
6. Chvíli počkejte a pak si všimněte, že se **stav** **pozastavuje**.

    ![Pozastavování](media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pozastavení je stav pozastaveno a přepínač je **spuštěn**.
8. Výpočetní prostředky pro datový sklad jsou teď offline. Za výpočetní výkon se vám nebude účtovat, dokud službu neobnovíte.

    ![COMPUTE offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Pokračovat v COMPUTE

Pomocí následujících kroků obnovíte SQL Data Warehouse.

1. Na webu Azure Portal klikněte vlevo na **Databáze SQL**.
2. Na stránce **Databáze SQL** vyberte **mySampleDataWarehouse**. Tím se otevře datový sklad. 
3. Na stránce **mySampleDataWarehouse** je **stav** upozornění **pozastaven**.

    ![COMPUTE offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Chcete-li obnovit datový sklad, klikněte na tlačítko **Start**. 
5. Zobrazí se dotaz s dotazem, zda chcete spustit. Klikněte na **Ano**.
6. Všimněte si, žese **stav** obnovuje.

    ![Obnovování](media/pause-and-resume-compute-portal/resuming.png)

7. Když je datový sklad znovu online, je stav **online** a přepínač je **pozastaven**.
8. Výpočetní prostředky pro datový sklad jsou teď online a službu můžete používat. Poplatky za výpočetní prostředky se obnovily.

    ![COMPUTE online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

2. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, klikněte na **Odstranit**.

3. Pokud chcete odebrat vytvořený SQL Server, klikněte na **MyNewServer-20171113.Database.Windows.NET**a pak klikněte na **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

4. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další postup

Nyní jste pozastavili a obnovili výpočetní výkon pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
> [Načtení dat do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
