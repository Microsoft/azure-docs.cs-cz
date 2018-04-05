---
title: Upgrade na nejnovější generování Azure SQL Data Warehouse | Microsoft Docs
description: Kroky pro upgrade na nejnovější generace architektury Azure hardware a úložiště Azure SQL Data Warehouse.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 42b716274e655bf91f72c1b3ab207b8a5f1ccee0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="upgrade-to-latest-generation-of-azure-sql-data-warehouse-in-the-azure-portal"></a>Upgrade na nejnovější generování Azure SQL Data Warehouse na portálu Azure

Použití portálu Azure k upgradu Azure SQL Data Warehouse používat nejnovější generování architektury Azure hardware a úložiště. Upgradem, můžete využít výhod vyšší výkon, větší škálovatelností a neomezené úložiště pro indexy columnstore.  

## <a name="applies-to"></a>Platí pro
Tento upgrade se vztahuje na datových skladů v optimalizovaný pro úroveň výkonu pružnost.  Podle pokynů upgrade datového skladu z optimalizovat pro úroveň výkonu pružnost na optimalizovaný pro výpočetní výkon vrstvě. 

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Než začnete

1. Když je pozastavená optimalizovaný pro pružnost datového skladu k upgradu, [obnovit datového skladu](pause-and-resume-compute-portal.md).
2. Připravte se po několika minutách nečinnosti. 
3. Proces upgradu ukončí všechny relace a zahodí všechna připojení. Před upgradem, ujistěte se, že jste dokončili své dotazy. Pokud začnete se transakce v průběhu upgradu, může být rozsáhlé čas vrácení zpět. 

## <a name="start-the-upgrade"></a>Spusťte upgrade

1. Otevřete váš datový sklad v portálu Azure a klikněte na tlačítko **Upgrade optimalizovaný pro výpočet**.
2. Všimněte si optimalizovaný pro výpočetní výkon vrstvy volby. Výchozí možností výběru je srovnatelná aktuální úroveň před upgradem.
3. Vyberte úroveň výkonu. Cena optimalizovaný pro výpočetní výkon vrstvě je aktuálně půl vypnuté během období preview.
4. Klikněte na tlačítko **upgradu**.
5. Zkontrolujte stav na portálu Azure.
6. Počkejte, než pro datový sklad změnit na Online.

## <a name="rebuild-columnstore-indexes"></a>Opětovné sestavení indexů columnstore

Jakmile datový sklad je online, můžete načíst data a spouštět dotazy. Však může být výkon zpomalit na první, protože proces na pozadí je migraci dat do nového hardwaru. 

Chcete-li vynutit dat k migraci co nejrychleji, doporučujeme opětovné sestavení indexů columnstore. To pokud chcete udělat, najdete pokyny k [nové sestavení indexů columnstore ke zlepšení kvality segment](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality). 

## <a name="next-steps"></a>Další postup
Datový sklad je online. Pokud chcete používat nové funkce výkonu, najdete v části [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md).
 