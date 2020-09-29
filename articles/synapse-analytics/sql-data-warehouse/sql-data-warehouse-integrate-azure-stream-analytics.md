---
title: Použít Azure Stream Analytics
description: Tipy pro použití Azure Stream Analytics s datovým skladem v Azure synapse pro vývoj řešení v reálném čase.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 60fb258fe2c6063b9b9a3ced0f4ba5f71ffd9d7c
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449509"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Použití Azure Stream Analytics se službou Azure Synapse Analytics

Azure Stream Analytics je plně spravovaná služba, která poskytuje vysoce dostupná a škálovatelná komplexní zpracování událostí přes streamovaná data v cloudu s nízkou latencí. Základní informace najdete v tématu [Úvod do Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pak můžete zjistit, jak vytvořit ucelené řešení pomocí Stream Analytics pomocí kurzu Začínáme s [Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

V tomto článku se dozvíte, jak používat datový sklad jako výstupní jímku pro příjem dat s vysokou propustností s Azure Stream Analytics úlohami.

## <a name="prerequisites"></a>Požadavky

* Azure Stream Analytics úlohy – Chcete-li vytvořit úlohu Azure Stream Analytics, postupujte podle kroků v kurzu [Začínáme s Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) :  

    1. Vytvoření vstupu centra událostí
    2. Konfigurace a spuštění aplikace generátoru událostí
    3. Zřízení Stream Analytics úlohy
    4. Zadat vstup a dotaz úlohy
* Fond SQL Azure synapse pro datový sklad – Chcete-li vytvořit nový datový sklad, postupujte podle kroků v [rychlém startu a vytvořte nový datový sklad](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Zadejte výstup streamování, který odkazuje na datový sklad.

### <a name="step-1"></a>Krok 1

V Azure Portal přejděte na Stream Analytics úlohu a v nabídce **topologie úloh** klikněte na **výstupy** .

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **Přidat** a v rozevírací nabídce vyberte **Azure synapse Analytics** .

![Výběr služby Azure synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Krok 3

Zadejte tyto hodnoty:

* *Alias pro výstup*: zadejte popisný název pro tento výstup úlohy.
* *Předplatné*:
  * Pokud je váš datový sklad ve stejném předplatném, jaké má Stream Analytics úloha, klikněte na ***Vybrat Azure synapse Analytics z vašich předplatných***.
  * Pokud je váš datový sklad v jiném předplatném, klikněte na možnost zadat nastavení Azure synapse Analytics ručně.
* *Databáze*: v rozevíracím seznamu vyberte cílovou databázi.
* *Uživatelské jméno*: zadejte uživatelské jméno účtu, který má pro databázi oprávnění k zápisu.
* *Heslo*: zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: zadejte název cílové tabulky v databázi.
* klikněte na tlačítko **Uložit** .

![Formulář Azure synapse Analytics je dokončený.](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Krok 4

Než budete moct spustit test, budete muset vytvořit tabulku v datovém skladu.  Spusťte následující skript pro vytváření tabulek pomocí SQL Server Management Studio (SSMS) nebo podle vašeho výběru nástroje pro dotazování.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Krok 5

V úloze Azure Portal for Stream Analytics klikněte na název vaší úlohy.  V podokně ***podrobností výstupu*** klikněte na tlačítko ***test*** .

![Tlačítko Test na výstupu details v ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) případě úspěšného připojení k databázi se zobrazí oznámení na portálu.

### <a name="step-6"></a>Krok 6

Klikněte na nabídku ***dotaz*** v části ***topologie úlohy*** a změňte dotaz na vložení dat do výstupního proudu, který jste vytvořili.  Kliknutím na tlačítko ***testovat vybraný dotaz*** otestujete dotaz.  Po úspěšném testu dotazu klikněte na tlačítko ***Uložit dotaz*** .

![Uložit dotaz](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Spusťte úlohu Azure Stream Analytics.  V nabídce ***Přehled*** klikněte na tlačítko ***Start*** .

![Spuštění úlohy Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klikněte na tlačítko ***Start*** v podokně úloha spuštění.

![Klikněte na tlačítko Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Další kroky

Přehled integrace najdete v tématu [integrace dalších služeb](sql-data-warehouse-overview-integrate.md).
Další tipy pro vývoj najdete v tématu věnovaném [rozhodování o návrhu a technikům kódování pro datové sklady](sql-data-warehouse-overview-develop.md).
