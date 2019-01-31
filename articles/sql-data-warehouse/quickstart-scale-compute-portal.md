---
title: 'Rychlý start: Horizontální navýšení kapacity výpočetních prostředků ve službě Azure SQL Data Warehouse – Azure portal | Dokumentace Microsoftu'
description: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Kapacitu výpočetních prostředků můžete horizontálně navýšit, abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 31ec1537a0075f486130a3bcf1017d35b47e499d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55463646"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Rychlý start: Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse na webu Azure Portal

Škálujte kapacitu výpočetních prostředků ve službě Azure SQL Data Warehouse pomocí Azure Portalu. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů. 

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Před zahájením

Můžete škálovat datový sklad, který už máte, nebo pomocí postupu v článku [Rychlý start: Vytvoření a připojení – portál](create-data-warehouse-portal.md) vytvořit datový sklad s názvem **mySampleDataWarehouse**.  Tento rychlý start škáluje **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Ve službě SQL Data Warehouse můžete upravit počet jednotek datového skladu a zvýšit nebo snížit tak množství výpočetních prostředků. Podle postupu v článku [Vytvoření a připojení – portál](create-data-warehouse-portal.md) jste vytvořili **mySampleDataWarehouse** a inicializovali ho se 400 jednotkami datového skladu. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

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
Nyní už víte, jak škálovat výpočetní prostředky pro datový sklad. Další informace o službě Azure SQL Data Warehouse najdete v kurzu načítání dat.

> [!div class="nextstepaction"]
>[Načtení dat do datového skladu SQL](load-data-from-azure-blob-storage-using-polybase.md)
