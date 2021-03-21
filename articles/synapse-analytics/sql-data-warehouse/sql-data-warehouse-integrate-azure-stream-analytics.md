---
title: Použít Azure Stream Analytics ve vyhrazeném fondu SQL
description: Tipy pro použití Azure Stream Analytics s vyhrazeným fondem SQL ve službě Azure synapse pro vývoj řešení v reálném čase.
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 023cf55a01f34277dd5c5707d0d123f54c1674df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600084"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Použití Azure Stream Analytics s vyhrazeným fondem SQL ve službě Azure synapse Analytics

Azure Stream Analytics je plně spravovaná služba, která poskytuje vysoce dostupná a škálovatelná komplexní zpracování událostí přes streamovaná data v cloudu s nízkou latencí. Základní informace najdete v tématu [Úvod do Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Pak můžete zjistit, jak vytvořit ucelené řešení pomocí Stream Analytics pomocí kurzu Začínáme s [Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

V tomto článku se dozvíte, jak použít vyhrazený fond SQL jako výstupní jímku pro příjem dat s vysokou propustností s Azure Stream Analytics úlohami.

## <a name="prerequisites"></a>Předpoklady

* Azure Stream Analytics úlohy – Chcete-li vytvořit úlohu Azure Stream Analytics, postupujte podle kroků v kurzu [Začínáme s Azure Stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) :  

    1. Vytvoření vstupu centra událostí
    2. Konfigurace a spuštění aplikace generátoru událostí
    3. Zřízení Stream Analytics úlohy
    4. Zadat vstup a dotaz úlohy
* Vyhrazený fond SQL – Chcete-li vytvořit nový vyhrazený fond SQL, postupujte podle kroků v [rychlém startu: vytvoření vyhrazeného fondu SQL](../quickstart-create-sql-pool-portal.md).

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Zadejte výstup streamování, který odkazuje na vyhrazený fond SQL.

### <a name="step-1"></a>Krok 1

V Azure Portal přejděte na Stream Analytics úlohu a v nabídce **topologie úloh** klikněte na **výstupy** .

### <a name="step-2"></a>Krok 2

Klikněte na tlačítko **Přidat** a v rozevírací nabídce vyberte **Azure synapse Analytics** .

![Výběr služby Azure synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Krok 3

Zadejte tyto hodnoty:

* *Alias pro výstup*: zadejte popisný název pro tento výstup úlohy.
* *Předplatné*:
  * Pokud je vyhrazený fond SQL ve stejném předplatném, jaké má Stream Analytics úloha, klikněte na ***Vybrat Azure synapse Analytics z vašich předplatných***.
  * Pokud je vyhrazený fond SQL v jiném předplatném, klikněte na možnost zadat nastavení Azure synapse Analytics ručně.
* *Databáze*: v rozevíracím seznamu vyberte cílovou databázi.
* *Uživatelské jméno*: zadejte uživatelské jméno účtu, který má pro databázi oprávnění k zápisu.
* *Heslo*: zadejte heslo pro zadaný uživatelský účet.
* *Tabulka*: zadejte název cílové tabulky v databázi.
* klikněte na tlačítko **Uložit** .

![Formulář Azure synapse Analytics je dokončený.](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Krok 4

Než budete moct spustit test, budete muset vytvořit tabulku ve vyhrazeném fondu SQL.  Spusťte následující skript pro vytváření tabulek pomocí SQL Server Management Studio (SSMS) nebo podle vašeho výběru nástroje pro dotazování.

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

V úloze Azure Portal for Stream Analytics klikněte na název vaší úlohy.  Klikněte na tlačítko ***test** _ v podokně *_Podrobnosti výstupu_* _ *.

![Tlačítko Test na výstupu details v ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) případě úspěšného připojení k databázi se zobrazí oznámení na portálu.

### <a name="step-6"></a>Krok 6

Klikněte na nabídku ***dotaz** _ v části _*_topologie úlohy_*_ a změňte dotaz na vložení dat do výstupního proudu, který jste vytvořili.  Kliknutím na tlačítko _*_testovat vybraný dotaz_*_ otestujete dotaz.  Po úspěšném testu dotazu klikněte na tlačítko _ *_Uložit dotaz_**.

![Uložit dotaz](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Krok 7

Spusťte úlohu Azure Stream Analytics.  Klikněte na tlačítko ***Start** _ v nabídce _ *_Overview_**.

![Spuštění úlohy Stream Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klikněte na tlačítko ***Start*** v podokně úloha spuštění.

![Klikněte na tlačítko Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Další kroky

Přehled integrace najdete v tématu [integrace dalších služeb](sql-data-warehouse-overview-integrate.md).
Další tipy pro vývoj najdete v tématu [věnovaném rozhodování a technikům kódování pro vyhrazený fond SQL](sql-data-warehouse-overview-develop.md).
