---
title: 'Rychlý Start: škálování výpočetních Azure Portal '
description: Škálování výpočetní kapacity ve fondu SQL ve Azure Portal. Výpočetní prostředky můžete škálovat na více instancí, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200331"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Rychlý Start: škálování výpočetních prostředků ve fondu SQL Azure synapse Analytics v Azure Portal

Škálování výpočetní kapacity ve fondu SQL ve Azure Portal. Můžete [horizontálně navýšit kapacitu výpočetních prostředků](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo ji snížit a dosáhnout tak nižších nákladů. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Můžete škálovat fond SQL, který už máte, nebo můžete použít [rychlý Start: vytvoření a připojení – portál](create-data-warehouse-portal.md) pro vytvoření fondu SQL s názvem **mySampleDataWarehouse**.  Tento rychlý start škáluje **mySampleDataWarehouse**.

>[!Note]
>Váš fond SQL musí být online, aby se mohlo škálovat. 

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Výpočetní prostředky fondu SQL se dají škálovat zvýšením nebo snížením jednotek datového skladu. Průvodce rychlým startem (Create-Data-Warehouse-portal.md) vytvořil **mySampleDataWarehouse** a inicializoval ho pomocí 400 DWU. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Změna jednotek datového skladu:

1. Na levé straně Azure Portal klikněte na **Azure synapse Analytics (dřív SQL DW)** .
2. Vyberte **mySampleDataWarehouse** ze stránky **Azure synapse Analytics (dříve SQL DW)** . Otevře se fond SQL.
3. Klikněte na **Škálovat**.

    ![Kliknutí na Škálovat](media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu škálování přesuňte posuvník doleva nebo doprava. Tím změníte nastavení jednotek datového skladu. Pak vyberte škálovat.

    ![Přesunutí posuvníku](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Další kroky
Nyní jste se naučili škálovat výpočetní výkon pro fond SQL. Pokud chcete získat další informace o fondu SQL, pokračujte v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do fondu SQL](load-data-from-azure-blob-storage-using-polybase.md)
