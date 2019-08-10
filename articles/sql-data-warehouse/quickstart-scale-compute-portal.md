---
title: 'Rychlý start: Horizontální navýšení kapacity Azure SQL Data Warehouse-Azure Portal | Microsoft Docs'
description: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: de91b2d681037ae67e14b16934cf72441ef9fd82
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935158"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Škálování výpočetních prostředků v Azure SQL Data Warehouse Azure Portal

Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Před zahájením

Můžete škálovat datový sklad, který už máte, nebo použít [rychlý Start: vytvoření a připojení – portál](create-data-warehouse-portal.md) pro vytvoření datového skladu s názvem **mySampleDataWarehouse**.  Tento rychlý start škáluje **mySampleDataWarehouse**.

>[!Note]
>Váš datový sklad musí být online, aby se mohlo škálovat. 

## <a name="scale-compute"></a>Škálování výpočetního výkonu

SQL Data Warehouse výpočetních prostředků se dá škálovat zvýšením nebo snížením objemu jednotek datového skladu. Průvodce rychlým startem (Create-Data-Warehouse-portal.md) vytvořil **mySampleDataWarehouse** a inicializoval ho pomocí 400 DWU. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Změna jednotek datového skladu:

1. Na webu Azure Portal klikněte vlevo na **Datové sklady SQL**.
2. Na stránce **Datové sklady SQLL** vyberte **mySampleDataWarehouse**. Otevře se datový sklad.
3. Klikněte na **Škálovat**.

    ![Kliknutí na Škálovat](media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu škálování přesuňte posuvník doleva nebo doprava. Tím změníte nastavení jednotek datového skladu.

    ![Přesunutí posuvníku](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Klikněte na **Uložit**. Zobrazí se potvrzovací zpráva. Kliknutím na **Ano** ji potvrďte nebo ji kliknutím na **Ne** zrušte.

    ![Kliknutí na Uložit](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Další postup
Nyní jste se naučili škálovat výpočetní výkon pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
