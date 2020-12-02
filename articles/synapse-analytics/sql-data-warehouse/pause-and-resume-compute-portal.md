---
title: 'Rychlý Start: pozastavení a obnovení výpočetní kapacity ve vyhrazeném fondu SQL (dřív SQL DW) prostřednictvím Azure Portal'
description: Pomocí Azure Portal můžete pozastavit výpočetní výkon fondu SQL a ušetřit tak náklady. Až budete připraveni k používání datového skladu, obnovte výpočetní prostředky.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88f6782a0f81b94977b37bd8f87bf26a0d4c8a72
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457671"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-via-the-azure-portal"></a>Rychlý Start: pozastavení a obnovení výpočetní kapacity ve vyhrazeném fondu SQL (dřív SQL DW) prostřednictvím Azure Portal

Můžete použít Azure Portal k pozastavení a obnovení vyhrazeného fondu SQL (dříve SQL DW) výpočetních prostředků. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Pomocí [Vytvoření a připojení – portál](../quickstart-create-sql-pool-portal.md) Vytvořte vyhrazený fond SQL s názvem **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pozastavit výpočetní prostředky

Pokud chcete snížit náklady, můžete pozastavit a obnovit výpočetní prostředky na vyžádání. Pokud například nebudete databázi používat během noci a na víkendech, můžete ji během těchto časů pozastavit a obnovit během dne.
 
>[!NOTE]
>Za výpočetní prostředky se vám nebudou účtovat žádné poplatky, zatímco databáze je pozastavená. Budeme se ale nadále účtovat za úložiště. 

Pomocí těchto kroků pozastavíte vyhrazený fond SQL:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Na levé navigační stránce Azure Portal klikněte na **vyhrazený fond SQL (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** z **vyhrazeného fondu SQL (dříve SQL DW)** a otevřete fond SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** oznámení **online**.

    ![COMPUTE online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Chcete-li pozastavit vyhrazený fond SQL, klikněte na tlačítko **pozastavit** . 
5. V případě, že chcete pokračovat, se zobrazí dotaz s potvrzením. Klikněte na **Ano**.
6. Chvíli počkejte a pak si všimněte, že se **stav** **pozastavuje**.

    ![Snímek obrazovky ukazuje Azure Portal pro ukázkový datový sklad s hodnotou stavu pozastaveno.](./media/pause-and-resume-compute-portal/pausing.png)

7. Po dokončení operace pozastavení je stav **pozastaveno** a přepínač je **obnoven**.
8. Výpočetní prostředky pro vyhrazený fond SQL jsou teď offline. Za výpočetní výkon se vám nebude účtovat, dokud službu neobnovíte.

    ![COMPUTE offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Pokračovat v COMPUTE

Pomocí těchto kroků obnovíte vyhrazený fond SQL.

1. Na levé straně Azure Portal klikněte na **vyhrazený fond SQL (dřív SQL DW)** .
2. Na stránce **vyhrazený fond SQL (dřív SQL DW)** vyberte **mySampleDataWarehouse** a otevřete stránku fondu SQL. 
3. Na stránce **mySampleDataWarehouse** je **stav** upozornění **pozastaven**.

    ![COMPUTE offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Chcete-li obnovit fond SQL, klikněte na tlačítko **obnovit**. 
1. Zobrazí se dotaz s dotazem, zda chcete spustit. Klikněte na **Ano**.
1. Všimněte si, že se **stav** **obnovuje**.

    ![Snímek obrazovky se zobrazí Azure Portal pro ukázkový datový sklad s vybraným tlačítkem Start a stavovou hodnotou obnovení.](./media/pause-and-resume-compute-portal/resuming.png)

1. Když je fond SQL zase online, je stav **online** a přepínač je **pozastaven**.
1. Výpočetní prostředky pro fond SQL jsou teď online a službu můžete používat. Poplatky za výpočetní prostředky se obnovily.

    ![COMPUTE online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem vyhrazeném fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně. 

- Pokud chcete uchovávat data v úložišti, pozastavte výpočetní prostředí.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit vyhrazený fond SQL. 

Pomocí tohoto postupu podle potřeby vyčistěte prostředky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a vyberte vyhrazený fond SQL.

    ![Vyčištění prostředků](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Pokud chcete pozastavit výpočetní prostředky, klikněte na tlačítko **Pozastavit**. 

1. Pokud chcete odebrat vyhrazený fond SQL, abyste vám neúčtovali výpočetní výkon nebo úložiště, klikněte na **Odstranit**.



## <a name="next-steps"></a>Další kroky

Nyní jste pozastavili a obnovili výpočetní výkon pro vyhrazený fond SQL. V dalším článku se dozvíte další informace o tom, jak [načíst data do vyhrazeného fondu SQL](load-data-from-azure-blob-storage-using-polybase.md). Další informace o správě výpočetních funkcí najdete v článku [Správa výpočetních přehledů](sql-data-warehouse-manage-compute-overview.md) . 

