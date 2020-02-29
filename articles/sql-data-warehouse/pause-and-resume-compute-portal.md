---
title: 'Rychlý Start: pozastavení & obnovení COMPUTE – Azure Portal '
description: Pomocí Azure Portal můžete pozastavit výpočetní výkon fondu SQL a ušetřit tak náklady. Až budete připraveni k používání datového skladu, obnovte výpočetní prostředky.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: manage
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 49684e6844c2d9f58e9b750b12991428218e4426
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200594"
---
# <a name="quickstart-pause-and-resume-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Rychlý Start: pozastavení a obnovení výpočetní služby ve fondu SQL Azure synapse Analytics v Azure Portal

Pomocí Azure Portal můžete pozastavit výpočetní výkon fondu SQL a ušetřit tak náklady. Až budete připraveni k používání datového skladu, [obnovte výpočetní](sql-data-warehouse-manage-compute-overview.md) prostředky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořte fond SQL s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete ušetřit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nebudete databázi používat během noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne. Za výpočetní prostředky se vám nebudou účtovat žádné poplatky, zatímco databáze je pozastavená. Budeme se ale nadále účtovat za úložiště. 

Pomocí těchto kroků pozastavíte fond SQL.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com/).
2. V levé navigační stránce Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete fond SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** oznámení **online**.

    ![COMPUTE online](media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li pozastavit fond SQL, klikněte na tlačítko **pozastavit** . 
5. V případě, že chcete pokračovat, se zobrazí dotaz s potvrzením. Klikněte na **Ano**.
6. Chvíli počkejte a pak si všimněte, že se **stav** **pozastavuje**.

    ![Pozastavení](media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pozastavení je stav **pozastaveno** a přepínač je **obnoven**.
8. Výpočetní prostředky pro fond SQL jsou teď offline. Za výpočetní výkon se vám nebude účtovat, dokud službu neobnovíte.

    ![COMPUTE offline](media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Pokračovat v COMPUTE

Pomocí těchto kroků obnovíte fond SQL.

1. Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete stránku fond SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** upozornění **pozastaven**.

    ![COMPUTE offline](media/pause-and-resume-compute-portal/compute-offline.png)

4. Chcete-li obnovit fond SQL, klikněte na tlačítko **obnovit**. 
5. Zobrazí se dotaz s dotazem, zda chcete spustit. Klikněte na **Ano**.
6. Všimněte si, že se **stav** **obnovuje**.

    ![Obnovování](media/pause-and-resume-compute-portal/resuming.png)

7. Když je fond SQL zase online, je stav **online** a přepínač je **pozastaven**.
8. Výpočetní prostředky pro fond SQL jsou teď online a službu můžete používat. Poplatky za výpočetní prostředky se obnovily.

    ![COMPUTE online](media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená v datovém skladu. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. 

2. Pokud chcete odebrat fond SQL, abyste vám neúčtovali výpočetní výkon nebo úložiště, klikněte na **Odstranit**.

3. Pokud chcete odebrat vytvořený SQL Server, klikněte na **sqlpoolservername.Database.Windows.NET**a pak klikněte na **Odstranit**.  S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

4. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další kroky

Nyní jste pozastavili a obnovili výpočetní výkon pro váš fond SQL. Pro další kroky pokračujte v kurzu načítání dat.

> [!div class="nextstepaction"]
> [Načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md)
