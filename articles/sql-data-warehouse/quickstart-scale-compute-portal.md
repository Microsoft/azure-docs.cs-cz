---
title: 'Rychlý Start: škálování výpočetních Azure Portal '
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 59d929165ac9618d68707e2f13741e7bbba7b37f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685957"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Škálování kapacity výpočetních prostředků ve službě Azure SQL Data Warehouse na Azure Portalu

Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Můžete [horizontálně navýšit kapacitu výpočetních prostředků](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo ji snížit a dosáhnout tak nižších nákladů. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlásit se na [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

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



## <a name="next-steps"></a>Další kroky
Nyní jste se naučili škálovat výpočetní výkon pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
