---
title: Škálování výpočetních prostředků pro fond Synapse SQL (portál Azure)
description: Výpočetní prostředky pro fond Synapse SQL (datový sklad) můžete škálovat pomocí portálu Azure.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f92152658b9db83740ffc2de2dc6956003849e06
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350823"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Úvodní příručka: Škálování výpočetních prostředků pro fond Synapse SQL pomocí portálu Azure

Výpočetní prostředky pro fond Synapse SQL (datový sklad) můžete škálovat pomocí portálu Azure. Kapacitu výpočetních prostředků můžete [horizontálně navýšit](sql-data-warehouse-manage-compute-overview.md), abyste získali lepší výkon, nebo snížit, abyste dosáhli nižších nákladů. 

Pokud nemáte předplatné Azure, vytvořte si [bezplatný](https://azure.microsoft.com/free/) účet, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

Můžete škálovat fond SQL, který již máte, nebo použít [úvodní příručku: vytvořit a připojit - portál](create-data-warehouse-portal.md) pro vytvoření fondu SQL s názvem **mySampleDataWarehouse**. Tento rychlý start škáluje **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Váš fond SQL musí být online škálovat. 

## <a name="scale-compute"></a>Škálování výpočetního výkonu

Výpočetní prostředky fondu SQL lze škálovat zvýšením nebo snížením jednotek datového skladu. Rychlý start [create and connect - portal] (create-data-warehouse-portal.md) vytvořil **mySampleDataWarehouse** a inicializoval jej pomocí 400 DWU. V následujícím postupu upravíte jednotky datového skladu pro **mySampleDataWarehouse**.

Změna jednotek datového skladu:

1. Klikněte na **Azure Synapse Analytics (dříve SQL DW)** na levé stránce portálu Azure.
2. Na stránce **Azure Synapse Analytics (dříve SQL DW)** vyberte **mySampleDataWarehouse.** Otevře se fond SQL.
3. Klikněte na **Škálovat**.

    ![Kliknutí na Škálovat](./media/quickstart-scale-compute-portal/click-scale.png)

2. Na panelu škálování přesuňte posuvník doleva nebo doprava. Tím změníte nastavení jednotek datového skladu. Pak vyberte měřítko.

    ![Přesunutí posuvníku](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Další kroky
Další informace o fondu SQL, pokračujte na číst data do kurzu [fondu SQL.](load-data-from-azure-blob-storage-using-polybase.md) 
