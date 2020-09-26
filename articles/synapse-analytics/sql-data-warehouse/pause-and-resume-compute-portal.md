---
title: Pozastavení a obnovení výpočetní kapacity ve fondu synapse SQL prostřednictvím Azure Portal
description: Pomocí Azure Portal můžete pozastavit výpočetní výkon fondu SQL a ušetřit tak náklady. Až budete připraveni k používání datového skladu, obnovte výpočetní prostředky.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: aa86161bce0b52eb1043d792fb2f4770df0992ee
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91369412"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Rychlý Start: pozastavení a obnovení výpočetní kapacity ve fondu synapse SQL prostřednictvím Azure Portal

Azure Portal můžete použít k pozastavení a obnovení výpočetních prostředků fondu SQL synapse (datový sklad). Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Pomocí [Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořte fond SQL s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete snížit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nebudete databázi používat během noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne. 
>[!NOTE]
>Za výpočetní prostředky se vám nebudou účtovat žádné poplatky, zatímco databáze je pozastavená. Budeme se ale nadále účtovat za úložiště. 

Pomocí těchto kroků pozastavíte fond SQL:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V levé navigační stránce Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete fond SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** oznámení **online**.

    ![COMPUTE online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li pozastavit fond SQL, klikněte na tlačítko **pozastavit** . 
5. V případě, že chcete pokračovat, se zobrazí dotaz s potvrzením. Klikněte na **Ano**.
6. Chvíli počkejte a pak si všimněte, že se **stav** **pozastavuje**.

    ![Snímek obrazovky ukazuje Azure Portal pro ukázkový datový sklad s hodnotou stavu pozastaveno.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pozastavení je stav **pozastaveno** a přepínač je **obnoven**.
8. Výpočetní prostředky pro fond SQL jsou teď offline. Za výpočetní výkon se vám nebude účtovat, dokud službu neobnovíte.

    ![COMPUTE offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Pokračovat v COMPUTE

Pomocí těchto kroků obnovíte fond SQL.

1. Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** a otevřete stránku fond SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** upozornění **pozastaven**.

    ![COMPUTE offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Chcete-li obnovit fond SQL, klikněte na tlačítko **obnovit**. 
5. Zobrazí se dotaz s dotazem, zda chcete spustit. Klikněte na **Ano**.
6. Všimněte si, že se **stav** **obnovuje**.

    ![Snímek obrazovky se zobrazí Azure Portal pro ukázkový datový sklad s vybraným tlačítkem Start a stavovou hodnotou obnovení.](./media/pause-and-resume-compute-portal/resuming.png)

7. Když je fond SQL zase online, je stav **online** a přepínač je **pozastaven**.
8. Výpočetní prostředky pro fond SQL jsou teď online a službu můžete používat. Poplatky za výpočetní prostředky se obnovily.

    ![COMPUTE online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. 

2. Pokud chcete odebrat fond SQL, abyste vám neúčtovali výpočetní výkon nebo úložiště, klikněte na **Odstranit**.

3. Pokud chcete odebrat vytvořený SQL Server, klikněte na **sqlpoolservername.Database.Windows.NET**a pak klikněte na **Odstranit**.  

   > [!CAUTION]
   > S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další kroky

Nyní jste pozastavili a obnovili výpočetní výkon pro váš fond SQL. V dalším článku se dozvíte další informace o tom, jak [načíst data do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md). Další informace o správě výpočetních funkcí najdete v článku [Správa výpočetních přehledů](sql-data-warehouse-manage-compute-overview.md) . 

