---
title: Migrace úloh místních služb SQL Server Integration Services (SSIS) do Azure Data Factory
description: Tento článek popisuje, jak migrovat úlohy SQL Server Integration Services (SSIS) do kanálu Azure Data Factory/aktivity/aktivační události pomocí SQL Server Management Studio.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: ee51be1d994c3b81765266e95c48d321a2f43b14
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80989438"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrace úloh agenta serveru SQL Server do adf s SSMS

Při [migraci místních úloh služby SQL Server Integration Services (SSIS) do ssis v adf](scenario-ssis-migration-overview.md)můžete po migraci balíčků SSIS provést dávkovou migraci úloh agenta SQL Server s typem kroku úlohy SQL Server Integration Services Package do kanálu/aktivit/spouštění aktivit/plánu Azure Data Factory (ADF) prostřednictvím **Průvodce migrace úloh SSIS**(SQL Server Management Studio) SSIS .

Obecně platí, že pro vybrané úlohy agenta SQL s příslušnými typy kroků úlohy může **Průvodce migrací úloh SSIS:**

- mapovat umístění balíčku SSIS do místa, kam jsou balíčky migrovány, které jsou přístupné SSIS v ADF.
    > [!NOTE]
    > Umístění balíčku systému souborů je podporováno pouze.
- migrujte příslušné úlohy s příslušnými kroky úlohy do odpovídajících zdrojů ADF, jak je uvedeno níže:

|Objekt úlohy agenta SQL  |Zdroj ADF  |Poznámky|
|---------|---------|---------|
|Úloha agenta SQL|Potrubí     |Název kanálu bude *generován pro \<>názvu úlohy *. <br> <br> Integrované úlohy agenta nejsou použitelné: <li> Úloha údržby serveru SSIS <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|Krok úlohy SSIS|Spustit aktivitu balíčku SSIS|<li> Název aktivity bude \<název kroku>. <li> Účet proxy použitý v kroku úlohy bude migrován jako ověřování této aktivity systémem Windows. <li> *Možnosti spuštění* s výjimkou *Použití 32bitového běhu definovaného* v kroku úlohy bude při migraci ignorováno. <li> *Ověření* definované v kroku úlohy bude při migraci ignorováno.|
|schedule      |trigger plánu        |Název aktivační události plánu bude *generován pro \<>názvu plánu *. <br> <br> Níže uvedené možnosti v plánu úloh agenta SQL budou při migraci ignorovány: <li> Interval druhé úrovně. <li> *Automatické spuštění při spuštění agenta serveru SQL Server* <li> *Spusťte vždy, když se procesory stanou nečinnými* <li> *den v týdnu* a *víkendový den*<time zone> <br> Níže jsou uvedeny rozdíly po migraci plánu úloh agenta SQL na aktivační událost plánu ADF: <li> Následné spuštění spuštění plánu ADF je nezávislé na stavu spuštění předchozího spuštění. <li> Konfigurace opakování aktivační události plánu adf se liší od denní frekvence v úloze agenta SQL.|

- generujte šablony Azure Resource Manager (ARM) v místní výstupní složce a nasaďte je přímo nebo později ručně do datové továrny. Další informace o šablonách Správce prostředků ADF naleznete v [tématu Typy prostředků Microsoft.DataFactory](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions).

## <a name="prerequisites"></a>Požadavky

Funkce popsaná v tomto článku vyžaduje SQL Server Management Studio verze 18.5 nebo vyšší. Nejnovější verzi služby SSMS naleznete v [tématu Stažení aplikace SQL Server Management Studio (SSMS).](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)

## <a name="migrate-ssis-jobs-to-adf"></a>Migrace úloh SSIS do adf

1. V SSMS vyberte v Průzkumníku objektů agenta serveru SQL Server, vyberte Úlohy, klikněte pravým tlačítkem myši a **vyberte Migrovat úlohy SSIS do adf**.
![Nabídky](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Přihlaste se do Azure, vyberte předplatné Azure, Data Factory a integration runtime. Azure Storage je volitelné, který se používá v kroku mapování umístění balíčku, pokud úlohy SSIS, které mají být migrovány, mají balíčky systému souborů SSIS.
![Nabídky](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Namapujte cesty balíčků SSIS a konfiguračních souborů v úlohách SSIS na cílové cesty, kam mají přístup migrované kanály. V tomto kroku mapování můžete:

    1. Vyberte zdrojovou složku a **pak přidejte mapování**.
    1. Aktualizovat cestu ke zdrojové složce. Platné cesty jsou cesty složek nebo cesty nadřazených složek balíčků.
    1. Aktualizujte cestu cílové složky. Výchozí je relativní cesta k výchozímu účtu úložiště, který je vybrán v kroku 1.
    1. Odstranit vybrané mapování pomocí **příkazu Odstranit mapování**.
![krok2](media/how-to-migrate-ssis-job-ssms/step2.png)
![krok2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Vyberte příslušné úlohy k migraci a nakonfigurujte nastavení odpovídající *aktivity balíčku SSIS*.

    - *Výchozí nastavení*se ve výchozím nastavení vztahuje na všechny vybrané kroky. Další informace o jednotlivých vlastnostech naleznete v *tématu Nastavení karty* [pro spuštění aktivity balíčku SSIS,](how-to-invoke-ssis-package-ssis-activity.md) pokud je umístění balíčku *systém souborů (balíček).*
    ![krok3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Nastavení kroku*, konfigurace nastavení pro vybraný krok.
        
        **Použít výchozí nastavení**: Je vybráno výchozí nastavení. Zrušením zaškrtnutí nakonfigurujte nastavení pouze pro vybraný krok.  
        Další informace o dalších vlastnostech naleznete v *tématu Nastavení karty* [pro aktivitu Spustit balíček SSIS,](how-to-invoke-ssis-package-ssis-activity.md) pokud je umístění balíčku *systém souborů (balíček).*
    ![krok3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Vygenerujte a nasaďte šablonu ARM.
    1. Vyberte nebo zadejte výstupní cestu pro šablony ARM migrovaných kanálů ADF. Složka bude vytvořena automaticky, pokud neexistuje.
    2. Vyberte možnost **Nasazení šablon ARM do vaší datové továrny**:
        - Výchozí hodnota není vybrána. Vygenerované šablony ARM můžete nasadit později ručně.
        - Tuto možnost vyberte, chcete-li nasadit generované šablony ARM přímo do datové továrny.
    ![krok4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrujte a zkontrolujte výsledky.
![krok5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Další kroky

[Spuštění a monitorování kanálu](how-to-invoke-ssis-package-ssis-activity.md)
