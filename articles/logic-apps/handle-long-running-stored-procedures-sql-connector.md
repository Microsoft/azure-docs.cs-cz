---
title: Zpracování dlouhodobě spuštěných uložených procedur v SQL Connectoru
description: Postup zpracování uložených procedur, jejichž časový limit při používání SQL Connectoru v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: camerost, logicappspm
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: f5b04c563dc81497f591788dc4890d379c0f898f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102867"
---
# <a name="handle-stored-procedure-timeouts-in-the-sql-connector-for-azure-logic-apps"></a>Zpracování časových limitů uložených procedur v konektoru SQL pro Azure Logic Apps

Když vaše aplikace logiky funguje s výsledky sady, takže [SQL Connector](../connectors/connectors-create-api-sqlazure.md) nevrátí všechny výsledky ve stejnou dobu, nebo pokud chcete mít větší kontrolu nad velikostí a strukturou sad výsledků, můžete vytvořit [uloženou proceduru](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) , která uspořádá výsledky podle vašich představ. SQL Connector poskytuje mnoho funkcí back-endu, ke kterým můžete přistupovat pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , abyste mohli snadněji automatizovat obchodní úkoly, které pracují s tabulkami SQL Database.

Například při načítání nebo vkládání více řádků aplikace logiky může iterovat přes tyto řádky pomocí [smyčky **do**](../logic-apps/logic-apps-control-flow-loops.md#until-loop) v rámci těchto [limitů](../logic-apps/logic-apps-limits-and-config.md). Pokud ale aplikace logiky potřebuje pracovat s tisíci nebo miliony řádků, chcete minimalizovat náklady vyplývající z volání databáze. Další informace najdete v tématu [zpracování hromadných dat pomocí konektoru SQL](../connectors/connectors-create-api-sqlazure.md#handle-bulk-data).

<a name="timeout-limit"></a>

## <a name="timeout-limit-on-stored-procedure-execution"></a>Časový limit pro spuštění uložené procedury

Konektor SQL má časový limit uložené procedury, který je [kratší než 2 minuty](/connectors/sql/#known-issues-and-limitations). Některé uložené procedury mohou trvat déle, než je toto omezení, což způsobí `504 Timeout` chybu. Někdy jsou tyto dlouhotrvající procesy kódované jako výslovně uložené procedury pro tento účel. V důsledku časového limitu mohou problémy způsobovat voláním těchto procedur z Azure Logic Apps. Přestože SQL Connector nativně nepodporuje asynchronní režim, můžete tento problém obejít a simulovat tento režim pomocí triggeru dokončení SQL, nativního předávacího dotazu SQL, tabulky stavu a úloh na straně serveru. Pro tuto úlohu můžete použít [agenta elastické úlohy Azure](../azure-sql/database/elastic-jobs-overview.md) pro [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md). Pro [SQL Server místní](/sql/sql-server/sql-server-technical-documentation) a [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)můžete použít [agenta SQL Server](/sql/ssms/agent/sql-server-agent).

Předpokládejme například, že máte následující dlouhotrvající uloženou proceduru, která trvá déle, než je časový limit pro dokončení běhu. Pokud tuto uloženou proceduru spustíte z aplikace logiky pomocí konektoru SQL, zobrazí se `HTTP 504 Gateway Timeout` jako výsledek chyba.

```sql
CREATE PROCEDURE [dbo].[WaitForIt]
   @delay char(8) = '00:03:00'
AS
BEGIN
   SET NOCOUNT ON;
   WAITFOR DELAY @delay
END
```

Místo toho, abyste přímo volali uloženou proceduru, můžete proceduru na pozadí asynchronně spustit pomocí *agenta úloh*. Vstupy a výstupy můžete ukládat do tabulky stavů, které pak můžete interagovat s aplikací logiky. Pokud nepotřebujete vstupy a výstupy, nebo pokud už výsledky píšete do tabulky uložené procedury, můžete tento přístup zjednodušit.

> [!IMPORTANT]
> Ujistěte se, že je uložená procedura a všechny úlohy *idempotentní*, což znamená, že můžou běžet víckrát, aniž by to ovlivnilo výsledky. Pokud dojde k chybě asynchronního zpracování nebo vypršení časového limitu, Agent úlohy může tento krok opakovat, a proto je uložená procedura několikrát. Aby se zabránilo duplikování výstupu, před vytvořením objektů si Projděte tyto [osvědčené postupy a přístupy](../azure-sql/database/elastic-jobs-overview.md#idempotent-scripts).

V další části se dozvíte, jak můžete použít agenta elastické úlohy Azure pro Azure SQL Database. Pro SQL Server a Azure SQL Managed instance můžete použít agenta SQL Server. Některé podrobnosti správy se budou lišit, ale základní postup zůstane stejný jako nastavení agenta úlohy pro Azure SQL Database.

<a name="azure-sql-database"></a>

## <a name="job-agent-for-azure-sql-database"></a>Agent úlohy pro Azure SQL Database

Pokud chcete vytvořit úlohu, která může spustit uloženou proceduru pro [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), použijte [agenta elastické úlohy Azure](../azure-sql/database/elastic-jobs-overview.md). Vytvořte agenta úloh v Azure Portal. Tento přístup přidá několik uložených procedur do databáze, kterou používá agent, označovaný také jako *databáze agenta*. Pak můžete vytvořit úlohu, která spustí uloženou proceduru v cílové databázi a po dokončení zachytí výstup.

Než budete moct vytvořit úlohu, musíte nastavit oprávnění, skupiny a cíle, jak je popsáno v [celé dokumentaci pro agenta elastické úlohy Azure](../azure-sql/database/elastic-jobs-overview.md). Také je nutné vytvořit podpůrnou tabulku v cílové databázi, jak je popsáno v následujících částech.

<a name="create-state-table"></a>

### <a name="create-state-table-for-registering-parameters-and-storing-inputs"></a>Vytvořit tabulku stavu pro registraci parametrů a ukládání vstupů

Úlohy agenta SQL neakceptují vstupní parametry. Místo toho v cílové databázi vytvořte tabulku stavu, do které zaregistrujete parametry a uložte vstupy, které se mají použít pro volání uložených procedur. Všechny kroky úlohy agenta se spouští proti cílové databázi, ale uložené procedury úlohy se spouštějí v databázi agenta. 

Chcete-li vytvořit tabulku stavů, použijte toto schéma:

```sql
CREATE TABLE [dbo].[LongRunningState](
   [jobid] [uniqueidentifier] NOT NULL,
   [rowversion] [timestamp] NULL,
   [parameters] [nvarchar](max) NULL,
   [start] [datetimeoffset](7) NULL,
   [complete] [datetimeoffset](7) NULL,
   [code] [int] NULL,
   [result] [nvarchar](max) NULL,
   CONSTRAINT [PK_LongRunningState] PRIMARY KEY CLUSTERED
      (   [jobid] ASC
      )WITH (STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF) ON [PRIMARY]
      ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
```

Tady je postup, jak Výsledná tabulka vypadá [SQL Server Management Studio (Smss)](/sql/ssms/download-sql-server-management-studio-ssms):

![Snímek obrazovky, který zobrazuje tabulku stavů, kde jsou uloženy vstupy pro uloženou proceduru.](media/handle-long-running-stored-procedures-sql-connector/state-table-input-parameters.png)

Chcete-li zajistit dobrý výkon a zajistit, aby úloha agenta mohla najít přidružený záznam, tabulka používá jako primární klíč ID spuštění úlohy ( `jobid` ). Pokud chcete, můžete také přidat jednotlivé sloupce pro vstupní parametry. Dříve popsané schéma může obecně zpracovávat více parametrů, ale je omezeno na velikost vypočtenou hodnotou `NVARCHAR(MAX)` .

<a name="create-top-level-job"></a>

### <a name="create-a-top-level-job-to-run-the-stored-procedure"></a>Vytvoření úlohy na nejvyšší úrovni pro spuštění uložené procedury

Chcete-li spustit dlouhotrvající uloženou proceduru, vytvořte tohoto agenta úloh nejvyšší úrovně v databázi agenta:

```sql
EXEC jobs.sp_add_job 
   @job_name='LongRunningJob',
   @description='Execute Long-Running Stored Proc',
   @enabled = 1
```

Nyní do úlohy Přidejte kroky, které parametrizovat, spouštějí a dokončí uloženou proceduru. Ve výchozím nastavení časový limit kroku úlohy vyprší po 12 hodinách. Pokud vaše úložná procedura potřebuje více času, nebo pokud chcete, aby byl postup dříve vyprší, můžete změnit `step_timeout_seconds` parametr na jinou hodnotu, která je zadána v sekundách. Ve výchozím nastavení má krok 10 integrovaných opakovaných pokusů s časovým limitem omezení rychlosti mezi jednotlivými opakováními, které můžete využít pro vaši výhodu.

Tady je postup, jak přidat:

1. Počkejte, než se parametry zobrazí v `LongRunningState` tabulce.

   Tento první krok čeká na zadání parametrů v `LongRunningState` tabulce, ke kterému dojde brzy po spuštění úlohy. Pokud se v tabulce nepřidá ID spuštění úlohy ( `jobid` ) `LongRunningState` , krok se pouze nezdařil a výchozí časový limit pro opakování nebo omezení rychlosti se čeká na vyřízení:

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name= 'Parameterize WaitForIt',
      @step_timeout_seconds = 30,
      @command= N'
         IF NOT EXISTS(SELECT [jobid] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
            THROW 50400, ''Failed to locate call parameters (Step1)'', 1',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Zadejte dotaz na parametry z tabulky State a předejte je uložené proceduře. Tento krok také spustí postup na pozadí. 

   Pokud uložená procedura nepotřebuje parametry, stačí přímo volat uloženou proceduru. V opačném případě pro předání `@timespan` parametru použijte `@callparams` , který lze také roztáhnout k předání dalších parametrů.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Execute WaitForIt',
      @command=N'
         DECLARE @timespan char(8)
         DECLARE @callparams NVARCHAR(MAX)
         SELECT @callparams = [parameters] FROM [dbo].[LongRunningState]
            WHERE jobid = $(job_execution_id))
         SET @timespan = @callparams
         EXECUTE [dbo].[WaitForIt] @delay = @timespan', 
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

1. Dokončete úlohu a zaznamenejte výsledky.

   ```sql
   EXEC jobs.sp_add_jobstep
      @job_name='LongRunningJob',
      @step_name='Complete WaitForIt',
      @command=N'
         UPDATE [dbo].[LongRunningState]
            SET [complete] = GETUTCDATE(),
               [code] = 200,
               [result] = ''Success''
            WHERE jobid = $(job_execution_id)',
      @credential_name='JobRun',
      @target_group_name='DatabaseGroupLongRunning'
   ```

<a name="start-job-pass-parameters"></a>

### <a name="start-the-job-and-pass-the-parameters"></a>Spusťte úlohu a předejte parametry.

Chcete-li spustit úlohu, použijte průchozí nativní dotaz s akcí [ **Spustit dotaz SQL**](/connectors/sql/#execute-a-sql-query-(v2)) a okamžitě nahrajte parametry úlohy do tabulky stavů. Chcete-li poskytnout vstup do `jobid` atributu v cílové tabulce, Logic Apps přidá **pro každou** smyčku, která projde výstupem tabulky z předchozí akce. Pro každé ID spuštění úlohy spusťte akci **Vložit řádek** , která používá dynamický výstup dat, a `ResultSets JobExecutionId` přidejte parametry pro úlohu k rozbalení a předání cílové uložené proceduře.

![Snímek obrazovky zobrazující akce, které se mají použít pro spuštění úlohy a předání parametrů do uložené procedury.](media/handle-long-running-stored-procedures-sql-connector/start-job-actions.png)

Po dokončení úlohy úloha aktualizuje `LongRunningState` tabulku, abyste mohli snadno aktivovat výsledek pomocí [triggeru **při úpravě položky**](/connectors/sql/#when-an-item-is-modified-(v2)). Pokud výstup nepotřebujete, nebo pokud už máte Trigger, který monitoruje výstupní tabulku, můžete tuto část přeskočit.

![Snímek obrazovky, který zobrazuje Trigger SQL pro změnu položky](media/handle-long-running-stored-procedures-sql-connector/trigger-on-results-after-job-completes.png)

<a name="sql-on-premises-or-managed-instance"></a>

## <a name="job-agent-for-sql-server-or-azure-sql-managed-instance"></a>Agent úlohy pro SQL Server nebo Azure SQL Managed instance

Pro stejný scénář můžete použít [agenta SQL Server](/sql/ssms/agent/sql-server-agent) pro [SQL Server místního](/sql/sql-server/sql-server-technical-documentation) prostředí a [spravované instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). I když se některé podrobnosti správy liší, základní postup zůstane stejný jako nastavení agenta úlohy pro Azure SQL Database.

## <a name="next-steps"></a>Další kroky

[Připojení k SQL Server, Azure SQL Database nebo spravované instanci Azure SQL](../connectors/connectors-create-api-sqlazure.md)

