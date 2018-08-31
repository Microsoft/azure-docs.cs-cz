---
title: 'Rychlý start: Pozastavení a obnovení compute v Azure SQL Data Warehouse – Azure portal | Dokumentace Microsoftu'
description: Pomocí webu Azure portal k pozastavit výpočetní prostředky ve službě Azure SQL Data Warehouse vám ušetří náklady. Až budete připravení k použití datového skladu obnovit výpočty.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: a82d3cfcbc4481c970f5a31ad2de711fb1562657
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246583"
---
# <a name="quickstart-pause-and-resume-compute-for-an-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Pozastavit a obnovit výpočetní prostředky pro službu Azure SQL Data Warehouse na webu Azure Portal
Pomocí webu Azure portal k pozastavit výpočetní prostředky ve službě Azure SQL Data Warehouse vám ušetří náklady. [Obnovit výpočty](sql-data-warehouse-manage-compute-overview.md) až budete připravení použít datový sklad.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Použití [vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořit datový sklad s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky
Abyste dosáhli nižších nákladů, lze pozastavit a obnovit výpočetní prostředky na vyžádání. Například pokud nebudete používat databázi v noci a o víkendech, můžete pozastavit během těchto obdobích a obnovit během dne. Během pozastavení databáze nebude účtovat za výpočetní prostředky. Ale budou dál účtovat poplatky za úložiště. 

Postupujte podle těchto kroků se pozastavit SQL data warehouse.

1. Na webu Azure Portal klikněte vlevo na **Databáze SQL**.
2. Na stránce **Databáze SQL** vyberte **mySampleDataWarehouse**. Tím se otevře datový sklad. 
3. Na **mySampleDataWarehouse** stránce, Všimněte si, že **stav** je **Online**.

    ![Výpočetní online](media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li pozastavit datový sklad, klikněte na tlačítko **pozastavit** tlačítko. 
5. Zobrazí se dotaz potvrzení s dotazem, pokud chcete pokračovat. Klikněte na **Ano**.
6. Chvíli počkejte a pak Všimněte si, **stav** je **pozastavování**.

    ![Pozastavování](media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pause stav je **pozastaveno** a přepínač je **Start**.
8. Výpočetní prostředky pro datový sklad jsou nyní v režimu offline. Dokud se obnovení služby nebude účtovat za výpočetní výkon.

    ![Výpočetní prostředky v režimu offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Obnovit výpočetní prostředky
Následujícím postupem obnovit SQL data warehouse.

1. Na webu Azure Portal klikněte vlevo na **Databáze SQL**.
2. Na stránce **Databáze SQL** vyberte **mySampleDataWarehouse**. Tím se otevře datový sklad. 
3. Na **mySampleDataWarehouse** stránce, Všimněte si, že **stav** je **pozastaveno**.

    ![Výpočetní prostředky v režimu offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Chcete-li pokračovat v datovém skladu, klikněte na tlačítko **Start**. 
5. Zobrazí se dotaz potvrzení s dotazem, pokud chcete spustit. Klikněte na **Ano**.
6. Všimněte si, že **stav** je **obnovování**.

    ![Obnovení](media/pause-and-resume-compute-portal/resuming.png)

7. Když je datový sklad je zpátky do online režimu, je ve stavu **Online** a přepínač je **pozastavení**.
8. Výpočetní prostředky pro datový sklad jsou nyní online a můžou využívat službu. Poplatky za výpočetní prostředky mají obnovit.

    ![Výpočetní online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete zachovat data v úložišti, pozastavte výpočetní operace.
- Pokud chcete zamezit budoucím poplatkům, můžete datový sklad odstranit. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com)a klikněte na datový sklad.

    ![Vyčištění prostředků](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. Když je datový sklad pozastavený, zobrazí se tlačítko **Spustit**.  Pokud chcete obnovit výpočetní prostředky, klikněte na **Spustit**.

2. Pokud chcete odebrat datový sklad, aby se vám neúčtovaly výpočetní prostředky ani prostředky úložiště, klikněte na **Odstranit**.

3. Pokud chcete odstranit server SQL, který jste vytvořili, klikněte na tlačítko **mynewserver-20171113.database.windows.net**a potom klikněte na tlačítko **odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

4. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další postup
Máte teď pozastavená a obnovit výpočetní prostředky pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
