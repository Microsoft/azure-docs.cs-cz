---
title: Použití Azure Stream Analytics
description: Tipy pro používání Azure Stream Analytics s datovým skladem v Azure Synapse pro vývoj řešení v reálném čase.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350443"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Používání Azure Stream Analytics s Azure Synapse Analytics

Azure Stream Analytics je plně spravovaná služba poskytující vysoce dostupné a škálovatelné zpracování složitých událostí s nízkou latencí prostředpou streamování dat v cloudu. Základy se můžete naučit na [úvodním úvodu do Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md). Potom se můžete dozvědět, jak vytvořit komplexní řešení s Stream Analytics podle [pokynů začínáme používat Kurz Azure Stream Analytics.](../../stream-analytics/stream-analytics-real-time-fraud-detection.md)

V tomto článku se dozvíte, jak použít datový sklad jako výstupní jímku pro úlohy Azure Stream Analytics.

## <a name="prerequisites"></a>Požadavky

* Úloha Azure Stream Analytics – pokud chcete vytvořit úlohu Azure Stream Analytics, postupujte podle pokynů v kurzu [Začínáme používat Azure Stream Analytics:](../../stream-analytics/stream-analytics-real-time-fraud-detection.md)  

    1. Vytvoření vstupu centra událostí
    2. Konfigurace a spuštění aplikace generátoru událostí
    3. Zřízení úlohy Stream Analytics
    4. Určení vstupu úlohy a dotazu
* Datový sklad fondu AZURE Synapse SQL – chcete-li vytvořit nový datový sklad, postupujte podle pokynů v [rychlém startu a vytvořte nový datový sklad](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Určení výstupu streamování, který má být přejděte na datový sklad.

### <a name="step-1"></a>Krok 1

Na portálu Azure přejděte na úlohu Stream Analytics a klikněte na **výstupy** v nabídce **Topologie úlohy.**

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **Přidat** a z rozbalovací nabídky zvolte **SQL Database.**

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>Krok 3

Zadejte následující hodnoty:

* *Alias výstupu*: Zadejte popisný název pro tento výstup úlohy.
* *Předplatné*:
  * Pokud je váš datový sklad ve stejném předplatném jako úloha Stream Analytics, klikněte na ***Vybrat databázi SQL z vašich předplatných***.
  * Pokud je databáze v jiném předplatném, klikněte ručně na Poskytnout nastavení databáze SQL.
* *Databáze*: V rozevíracím seznamu vyberte cílovou databázi.
* *Uživatelské jméno*: Zadejte uživatelské jméno účtu, který má oprávnění k zápisu pro databázi.
* *Heslo*: Zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: Zadejte název cílové tabulky v databázi.
* klikněte na tlačítko **Uložit**

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>Krok 4

Před spuštěním testu budete muset vytvořit tabulku v datovém skladu.  Spusťte následující skript pro vytváření tabulek pomocí aplikace SQL Server Management Studio (SSMS) nebo pomocí nástroje pro dotaz.

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

Na azure portálu pro stream analytics úlohy klikněte na název úlohy.  Klikněte na tlačítko ***Testovat*** v podokně ***Podrobnosti o výstupu.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)Po úspěšném připojení k databázi se na portálu zobrazí oznámení.

### <a name="step-6"></a>Krok 6

Klikněte na nabídku ***Dotaz*** v části ***Topologie úlohy*** a změňte dotaz tak, aby vkládal data do výstupu datového proudu, který jste vytvořili.  Kliknutím na tlačítko ***Testovat vybraný dotaz*** otestujete dotaz.  Po úspěšném testu dotazu klepněte na tlačítko ***Uložit dotaz.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Spusťte úlohu Azure Stream Analytics.  Klikněte na tlačítko ***Start*** v nabídce ***Přehled.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

klikněte na tlačítko ***Start*** v podokně úloh y Start.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Další kroky

Přehled integrace najdete v tématu [Integrace dalších služeb](sql-data-warehouse-overview-integrate.md).
Další tipy pro vývoj najdete v [tématu Rozhodnutí o návrhu a techniky kódování pro datové sklady](sql-data-warehouse-overview-develop.md).
