---
title: 'Rychlý start: Horizontální navýšení kapacity výpočetních prostředků ve službě Azure SQL Data Warehouse – Azure Portal | Microsoft Docs'
description: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d93ea939a938fa88615161d3d048ee1881dd319a
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Škálování kapacity výpočetních prostředků ve službě Azure SQL Data Warehouse na Azure Portalu

Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Můžete [horizontálně navýšit kapacitu výpočetních prostředků](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo ji snížit a dosáhnout tak nižších nákladů. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Můžete škálovat datový sklad, který už máte, nebo pomocí postupu v článku [Rychlý start: Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořit datový sklad s názvem **mySampleDataWarehouse**.  Tento rychlý start škáluje **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve službě SQL Data Warehouse můžete upravit počet jednotek datového skladu a tím zvýšit nebo snížit množství výpočetních prostředků. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Změna jednotek datového skladu:

1. Na levé stránce webu Azure Portal klikněte na **Databáze SQL**.
2. Na stránce **Databáze SQL** vyberte **mySampleDataWarehouse**. Otevře se datový sklad.
3. Klikněte na **Škálovat**.

    ![Kliknutí na Škálovat](media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu škálování přesuňte posuvník doleva nebo doprava. Tím změníte nastavení jednotek datového skladu.

    ![Přesunutí posuvníku](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

    ![Kliknutí na Uložit](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Další kroky
Teď už víte, jak škálovat výpočetní prostředky pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
