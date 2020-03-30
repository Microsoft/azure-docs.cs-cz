---
title: Pozastavení a obnovení výpočetních prostředků ve fondu Synapse SQL prostřednictvím portálu Azure
description: Pomocí portálu Azure pozastavte výpočetní prostředky pro fond SQL, abyste ušetřili náklady. Pokračujte v výpočtu, až budete připraveni použít datový sklad.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/18/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 55e3d5bf4fb63c35d484e4a764c7eeb2e2484fcf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350962"
---
# <a name="quickstart-pause-and-resume-compute-in-synapse-sql-pool-via-the-azure-portal"></a>Úvodní příručka: Pozastavení a obnovení výpočetních prostředků ve fondu Synapse SQL prostřednictvím portálu Azure

Na portálu Azure můžete pozastavit a obnovit výpočetní prostředky fondu Synapse SQL (datového skladu). Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Pomocí [portálu Vytvořit a připojit –](create-data-warehouse-portal.md) vytvořte fond SQL s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavení výpočtu

Chcete-li snížit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nebudete databázi používat v noci a o víkendech, můžete ji během těchto časů pozastavit a během dne ji obnovit. 
>[!NOTE]
>Nebude se vám účtovat výpočetní prostředky, když je databáze pozastavena. Bude vám však nadále účtovánpoplatek za úložiště. 

Chcete-li pozastavit fond SQL, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte na **Azure Synapse Analytics (dříve SQL DW)** na levé navigační stránce portálu Azure.
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure Synapse Analytics (dříve SQL DW)** a otevřete fond SQL. 
3. Na stránce **mySampleDataWarehouse** si všimněte, **že stav** je **online**.

    ![Výpočetní výkon online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li fond SQL pozastavit, klepněte na tlačítko **Pozastavit.** 
5. Zobrazí se potvrzovací otázka s dotazem, zda chcete pokračovat. Klepněte na tlačítko **Ano**.
6. Počkejte chvíli a pak si všimněte, **že stav** je **pozastavení**.

    ![Pozastaveno](./media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pozastavení je stav **pozastaven** a přepínač **je Pokračovat**.
8. Výpočetní prostředky pro fond SQL jsou teď offline. Dokud službu neobnovíte, nebude vám účtován poplatek za výpočetní prostředky.

    ![Výpočetní výkon offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Pokračovat v výpočtu

Chcete-li obnovit fond SQL, postupujte takto.

1. Klikněte na **Azure Synapse Analytics (dříve SQL DW)** na levé stránce portálu Azure.
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure Synapse Analytics (dříve SQL DW)** a otevřete stránku fondu SQL. 
3. Na stránce **mySampleDataWarehouse** upozornění, že **stav** je **pozastaven**.

    ![Výpočetní výkon offline](././media/pause-and-resume-compute-portal/compute-offline.png)

4. Chcete-li obnovit fond SQL, klepněte na tlačítko **Pokračovat**. 
5. Zobrazí se potvrzovací otázka s dotazem, zda chcete začít. Klepněte na tlačítko **Ano**.
6. Všimněte si, že **stav** se **obnovuje**.

    ![Obnovování](./media/pause-and-resume-compute-portal/resuming.png)

7. Když je fond SQL zpět online, stav je **Online** a přepínač je **Pozastavit**.
8. Výpočetní prostředky pro fond SQL jsou teď online a můžete použít službu. Poplatky za výpočetní výkon byly obnoveny.

    ![Výpočetní výkon online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jsou účtovány za jednotky datového skladu a data uložená ve fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete data uchovávat v úložišti, pozastavte výpočetní výkon.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit fond SQL. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a klikněte na svůj fond SQL.

    ![Vyčištění prostředků](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. 

2. Pokud chcete fond SQL odebrat, abyste se neúčtovali za výpočetní prostředky nebo úložiště, klikněte na **odstranit**.

3. Chcete-li odebrat vytvořený server SQL, klepněte na **tlačítko sqlpoolservername.database.windows.net**a potom klepněte na tlačítko **Odstranit**.  

   > [!CAUTION]
   > S tímto odstraněním buďte opatrní, protože odstraněním serveru se odstraní také všechny databáze k tomuto serveru přiřazené.

5. Pokud chcete odebrat skupinu prostředků, klikněte na **myResourceGroup** a pak klikněte na **Odstranit skupinu prostředků**.


## <a name="next-steps"></a>Další kroky

Nyní jste pozastavili a obnovili výpočetní prostředky pro fond SQL. Pokračujte k dalšímu článku a dozvíte se více o tom, jak [načíst data do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md). Další informace o správě výpočetních funkcí najdete v článku [Správa přehledu výpočetních prostředků.](sql-data-warehouse-manage-compute-overview.md) 

