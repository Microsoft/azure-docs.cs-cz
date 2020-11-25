---
title: Migrace místních služba SSIS (SQL Server Integration Services) úloh (SSIS) do Azure Data Factory
description: Tento článek popisuje, jak migrovat úlohy služba SSIS (SQL Server Integration Services) (SSIS) na Azure Data Factory kanály/aktivity/triggery pomocí SQL Server Management Studio.
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
ms.openlocfilehash: 5566717387f6da375129a0e70c9ad825198d66b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005703"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>Migrace úloh agenta SQL Server do ADF pomocí SSMS

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při [migraci místních služba SSIS (SQL Server Integration Services) úloh (SSIS) na SSIS v ADF](scenario-ssis-migration-overview.md)se po migraci balíčků SSIS dá dávková migrace úloh SQL Server agentů s typem kroku úlohy služba SSIS (SQL Server Integration Services) balíčku do Azure Data Factory (ADF) kanálů/aktivit/naplánovat aktivační události prostřednictvím SQL Server Management Studio (SSMS) **SSIS Průvodce migrací úloh**.

Obecně platí, že pro vybrané úlohy agenta SQL s použitelnými typy kroků úlohy může **Průvodce migrací úlohy SSIS** :

- namapujte místní umístění balíčku SSIS na místo, kam se balíčky migrují, které jsou přístupné SSIS v ADF.
    > [!NOTE]
    > Umístění balíčku systému souborů je podporováno pouze.
- Migrujte příslušné úlohy s příslušnými kroky úlohy do odpovídajících prostředků ADF, jak je uvedeno níže:

|Objekt úlohy agenta SQL  |Prostředek ADF  |Poznámky|
|---------|---------|---------|
|Úloha agenta SQL|kanálu     |Název kanálu bude *vygenerován pro \<job name>*. <br> <br> Předdefinované úlohy agenta nejsou k dispozici: <li> Úloha údržby serveru SSIS <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|Krok úlohy SSIS|Aktivita provádění balíčku SSIS|<li> Název aktivity bude \<step name> . <li> Proxy účet použitý v kroku úlohy bude migrován jako ověřování systému Windows této aktivity. <li> *Možnosti spuštění* s výjimkou *použití 32ho modulu runtime* definovaného v kroku úlohy budou při migraci ignorovány. <li> *Ověřování* definované v kroku úlohy bude při migraci ignorováno.|
|schedule      |trigger plánu        |Název aktivační události plánovače, *pro \<schedule name>* kterou bude vytvořen. <br> <br> Níže uvedené možnosti v plánu úlohy agenta SQL se budou při migraci ignorovat: <li> Interval druhé úrovně. <li> *Spustit automaticky při spuštění agenta SQL Server* <li> *Spustit pokaždé, když se procesory stanou nečinné* <li> den v *týdnu* a *víkend*<time zone> <br> Níže jsou uvedeny rozdíly po migraci plánu úlohy agenta SQL do aktivační události naplánování ADF: <li> Následné spuštění triggeru na základě plánu ADF je nezávisle na stavu spuštění v předchůdci aktivovaném spuštěním. <li> Konfigurace opakování aktivační události plánu ADF se v úloze agenta SQL liší od každodenní frekvence.|

- Vygenerujte šablony Azure Resource Manager (ARM) v místní výstupní složce a přímo nebo později nasaďte do objektu pro vytváření dat. Další informace o šablonách ADF Správce prostředků najdete v tématu [typy prostředků Microsoft. DataFactory](/azure/templates/microsoft.datafactory/allversions).

## <a name="prerequisites"></a>Požadavky

Funkce popsaná v tomto článku vyžaduje SQL Server Management Studio verze 18,5 nebo vyšší. Pokud chcete získat nejnovější verzi SSMS, přečtěte si téma [stažení SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15).

## <a name="migrate-ssis-jobs-to-adf"></a>Migrace úloh SSIS do ADF

1. V SSMS v Průzkumník objektů vyberte SQL Server Agent, vyberte úlohy, klikněte pravým tlačítkem a vyberte **migrovat úlohy SSIS na ADF**.
![Snímek obrazovky ukazuje SQL Server Management Studio Průzkumník objektů, kde můžete vybrat úlohy a pak migrovat úlohy S s I s na D F.](media/how-to-migrate-ssis-job-ssms/menu.png)

1. Přihlaste se k Azure, vyberte předplatné Azure, Data Factory a Integration Runtime. Azure Storage je volitelná, která se používá v kroku mapování umístění balíčku, pokud mají migrovány úlohy SSIS balíčky systému souborů SSIS.
![nabídce](media/how-to-migrate-ssis-job-ssms/step1.png)

1. Namapujte cesty balíčků SSIS a konfiguračních souborů v úlohách SSIS na cílové cesty, ke kterým mají migrované kanály přístup. V tomto kroku mapování můžete:

    1. Vyberte zdrojovou složku a pak **Přidat mapování**.
    1. Aktualizujte cestu ke zdrojové složce. Platné cesty jsou cesty ke složkám nebo cesty nadřazených složek balíčků.
    1. Aktualizujte cestu k cílové složce. Výchozí hodnota je relativní cesta k výchozímu účtu úložiště, který je vybraný v kroku 1.
    1. Odstraní vybrané mapování prostřednictvím **mapování pro odstranění**.
![Snímek obrazovky se stránkou balíčku mapy s I S cestami ke konfiguraci, kde můžete přidat mapování. ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![ Snímek obrazovky se stránkou balíčku mapy s a cestami konfigurace, kde můžete aktualizovat cestu ke zdrojové a cílové složce.](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. Vyberte příslušné úlohy, které chcete migrovat, a nakonfigurujte nastavení odpovídající *spouštěné aktivity balíčku SSIS*.

    - *Výchozí nastavení* platí pro všechny vybrané kroky ve výchozím nastavení. Další informace o jednotlivých vlastnostech naleznete v tématu *Karta nastavení* pro [aktivitu spustit SSIS balíčku](how-to-invoke-ssis-package-ssis-activity.md) , když je umístění balíčku *systém souborů (Package)*.
    ![Snímek obrazovky se zobrazí na stránce S úlohami výběru S, kde můžete nakonfigurovat nastavení odpovídající spouštěné aktivity balíčku SSIS.](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *Nastavení kroku*, nakonfigurujte nastavení pro vybraný krok.
        
        **Použít výchozí nastavení**: je vybraná možnost výchozí. Zrušte výběr ke konfiguraci nastavení pouze pro vybraný krok.  
        Další informace o dalších vlastnostech naleznete v tématu *Karta nastavení* pro [aktivitu spustit SSIS balíčku](how-to-invoke-ssis-package-ssis-activity.md) , když je umístění balíčku *systém souborů (Package)*.
    ![Snímek obrazovky se zobrazí na stránce S úlohami výběru S s, kde můžete použít výchozí nastavení.](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. Vygenerujte a nasaďte šablonu ARM.
    1. Vyberte nebo zadejte výstupní cestu šablon ARM migrovaných kanálů ADF. Složka se vytvoří automaticky, pokud neexistuje.
    2. Vyberte možnost **Nasazení šablon ARM do objektu pro vytváření dat**:
        - Výchozí hodnota není vybraná. Vygenerované šablony ARM můžete nasadit později ručně.
        - Tuto možnost vyberte, pokud chcete nasadit vygenerované šablony ARM přímo do objektu pro vytváření dat.
    ![Snímek obrazovky se zobrazí stránka konfigurace migrace, kde můžete vybrat nebo zadat výstupní cestu pro šablony ARM migrovaných kanálů ADF a vybrat možnost nasazení šablon ARM do datové továrny.](media/how-to-migrate-ssis-job-ssms/step4.png)

1. Migrujte a pak zkontrolujte výsledky.
![Snímek obrazovky ukazuje stránku výsledek migrace, která zobrazuje průběh migrace.](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>Další kroky

[Spustit a monitorovat kanál](how-to-invoke-ssis-package-ssis-activity.md)