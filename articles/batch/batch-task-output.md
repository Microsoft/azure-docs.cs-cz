---
title: Zachovat výsledků nebo protokoly z dokončených úloh a úloh k úložišti dat – Azure Batch | Microsoft Docs
description: Další informace o různé možnosti pro zachování dat výstup z úlohy a úkoly služby Batch. Můžete zachovat data do úložiště Azure nebo do jiného úložiště dat.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="persist-job-and-task-output"></a>Trvalý výstup úloh a funkcí

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Několik běžných příkladů výstupu úlohy patří:

- Soubory vytvořené při procesy úloh vstupní data.
- Soubory protokolu přidružené k provedení úlohy. 

Tento článek popisuje různé možnosti pro zachování výstup úlohy a scénáře, pro které je nejvhodnější jednotlivých možností.   

## <a name="about-the-batch-file-conventions-standard"></a>O standardní konvence dávkového souboru

Batch definuje volitelná sada konvence pro pojmenování souborů výstupní úloh ve službě Azure Storage. [Dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) popisuje tyto konvence. Standardní konvence souboru určuje názvy cílovou kontejnerů a objektů blob cestu ve službě Azure Storage pro danou výstupní soubor založený na názvy úloh a úloh.

Ho je na vás, jestli se rozhodnete použít standardní soubor konvence pro pojmenování výstupní datové soubory. Můžete také název cílový kontejner a objektů blob, ale nechcete. Pokud používáte standard souboru konvence pro pojmenování výstupní soubory, pak jsou k dispozici pro zobrazení v výstupní soubory [portál Azure][portal].

Existuje několik různých způsobů, které můžete použít standardní soubor konvence:

- Pokud používáte rozhraní API služby Batch udržení výstupní soubory, můžete název cílové kontejnery a objekty BLOB podle standardu názvů souborů. Rozhraní API služby Batch umožňuje zachovat výstupní soubory z kódu klienta beze změny aplikace úkolů.
- Pokud vyvíjíte pomocí rozhraní .NET, můžete použít [konvence souboru Azure Batch library pro .NET][nuget_package]. Výhodou použití této knihovny je, že podporuje dotazování vaše výstupní soubory podle jejich ID nebo účel. Integrované funkce dotazování usnadňuje přístup k souborům výstup z klientské aplikace, nebo z jiných úloh. Úloha aplikace musí však upravit tak, aby volání souboru konvence knihovny. Další informace najdete v tématu referenční informace pro [souboru konvence knihovna pro .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Pokud vyvíjíte s jiném jazyce než v rozhraní .NET, můžete implementovat standard názvů souboru v aplikaci.

## <a name="design-considerations-for-persisting-output"></a>Aspekty návrhu pro zachování výstup 

Při navrhování řešení Batch, zvažte následující okolnosti související s výstupy úlohy a úlohy.

* **Výpočetní uzel životnost**: výpočetní uzly jsou často přechodný, zejména ve fondech škálování povolené. Výstup z úlohy, která běží na uzlu je k dispozici pouze tehdy, když existuje uzlu a pouze v rámci doby uchování souboru jste nastavili pro úlohu. Pokud úloha vytváří výstup, který může být potřeba po dokončení úlohy, potom úlohu musíte nahrát jeho výstupní soubory do odolná úložiště, jako je například Azure Storage.

* **Výstup úložiště**: Azure Storage se doporučuje jako úložiště dat pro výstup úlohy, ale můžete použít jakékoli odolné úložiště. Zápis výstupu úlohy do úložiště Azure je integrována do rozhraní API služby Batch. Pokud používáte jinou formu odolná úložiště, budete muset psát logiku aplikace k uchování úloh výstup sami.   

* **Výstup načtení**: výstup úlohy můžete načíst přímo z výpočetních uzlů ve vašem fondu nebo z Azure Storage nebo jiného úložiště dat, pokud držena formou výstup úlohy. Chcete-li načíst výstup úkolu přímo z výpočetním uzlu, musíte název souboru a jeho výstupní umístění na uzlu. Pokud jste potrvají výstup úlohy do služby Azure Storage, potřebujete úplná cesta k souboru ve službě Azure Storage stáhnout výstup soubory pomocí sady SDK úložiště Azure.

* **Zobrazení výstupu**: když přejdete do dávky úloh ve službě Azure portálu a vyberte možnost **soubory na uzlu,**, zobrazí se všechny soubory přidružené k úkolu, ne jenom výstupní soubory vás zajímá. Soubory na výpočetních uzlech znovu, jsou k dispozici pouze tehdy, když existuje uzlu a pouze v rámci dobu uchovávání souboru jste nastavili pro úlohu. Chcete-li zobrazit výstup úlohy, které jste trvalé do služby Azure Storage, můžete použít portál Azure nebo klienta aplikace Azure Storage, jako [Azure Storage Explorer][storage_explorer]. Chcete-li zobrazit výstupní data ve službě Azure Storage pomocí portálu nebo jiný nástroj, musí znát umístění souboru a přejděte do ji přímo.

## <a name="options-for-persisting-output"></a>Možnosti pro zachování výstup

V závislosti na vašem scénáři existuje několik různých přístupů, které můžete použít k uchování výstup úlohy:

- Použijte rozhraní API služby Batch.  
- V knihovně konvence souboru Batch pro .NET.  
- Implementujte standard dávkového souboru konvence ve vaší aplikaci.
- Implementujte řešení přesun vlastního souboru.

Následující části popisují každý přístup podrobněji.

### <a name="use-the-batch-service-api"></a>Použít rozhraní API služby Batch

S verzí 2017-05-01, služba Batch přidává podporu pro zadání výstupní soubory ve službě Azure Storage pro data úloh když jste [přidání úkolu do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) nebo [přidat kolekcí úkolů do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Rozhraní API služby Batch podporuje zachování dat úloh na účet úložiště Azure z fondů vytvořené pomocí konfigurace virtuálního počítače. Pomocí rozhraní API služby Batch můžete zachovat data úloh beze změny aplikace, která spustí úlohu. Volitelně můžete dodržovat [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) pro pojmenovávání souborů, které zachovat do služby Azure Storage. 

Pomocí rozhraní API služby Batch lze zachovat úkolů, když výstup:

- Chcete zachovat data z úlohy Batch a úkolech Správce úloh ve fondech vytvořené pomocí konfigurace virtuálního počítače.
- Chcete zachovat data ke kontejneru úložiště Azure libovolný název.
- Chcete zachovat data do Azure Storage kontejner s názvem podle [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> Rozhraní API služby Batch nepodporuje zachování dat z úloh spuštěných ve fondech vytvořen s konfigurací cloudové služby. Informace o zachování úloh výstup z fondů systémem konfiguraci cloudových služeb najdete v tématu [zachovat úloh a úkolů data do služby Azure Storage pomocí knihovny Batch souboru konvence pro .NET k uchování ](batch-task-output-file-conventions.md)
> 
> 

Další informace o zachování výstup úlohy s rozhraním API služby Batch najdete v tématu [zachovat data úloh do služby Azure Storage službou Batch služby API](batch-task-output-files.md). Viz také [PersistOutputs] [github_persistoutputs] ukázkového projektu na Githubu, které ukazuje, jak pomocí klientské knihovny Batch pro .NET lze zachovat výstup úlohy do trvalého úložiště.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Použití knihovny konvence souboru Batch pro .NET

Umožňují vývojářům vytvářet řešení Batch s C# a rozhraní .NET [souboru konvence knihovna pro .NET] [ nuget_package] k uchování dat úloh do Azure Storage účet, podle k [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Soubor konvence knihovny zpracovává přesunutí výstupní soubory do úložiště Azure a pojmenování cílové kontejnery a objekty BLOB dobře známé způsobem.

Soubor konvence knihovny podporuje dotazování výstupní soubory podle ID nebo účel, což usnadňuje umisťovat bez nutnosti dokončení soubor identifikátory URI. 

Použijte knihovně konvence souboru Batch pro .NET k uchování úkolů, když výstup:

- Chcete data datového proudu do služby Azure Storage při běhu úlohy.
- Chcete zachovat data z fondů vytvořené pomocí konfigurace cloudové služby nebo konfigurace virtuálního počítače.
- Klientské aplikace nebo jiné úkoly v úloze musí vyhledat a stáhnout úloh výstupní soubory podle ID nebo podle účelu. 
- Chcete provést kontrolu odkazující nebo časná nahrávání počáteční výsledků.
- Chcete zobrazit výstup úlohy na portálu Azure.

Další informace o zachování výstup úlohy s knihovnou souboru konvence pro rozhraní .NET najdete v tématu [zachovat úloh a úkolů data do služby Azure Storage pomocí knihovny Batch souboru konvence pro technologii .NET zachovat ](batch-task-output-file-conventions.md). Viz také [PersistOutputs] [github_persistoutputs] ukázkového projektu na Githubu, které ukazuje, jak pomocí souboru konvence knihovna pro .NET lze zachovat výstup úlohy do trvalého úložiště.

Ukázkový projekt [PersistOutputs] [github_persistoutputs] na Githubu ukazuje, jak pomocí klientské knihovny Batch pro .NET lze zachovat výstup úlohy do odolné úložiště.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementovat standard konvence dávkového souboru

Pokud používáte jiný jazyk než rozhraní .NET, můžete implementovat [dávkového souboru konvence standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) ve vaší vlastní aplikaci. 

Můžete implementovat standardní pojmenování souboru konvence, sami když chcete Principy schéma pojmenování, nebo když chcete zobrazit výstup úlohy na portálu Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementace řešení přesun vlastního souboru

Můžete také implementovat vlastní řešení Přesun celého souboru. Použít tato metoda při:

- Chcete zachovat data úloh k úložišti dat než Azure Storage. K nahrání souborů do úložiště dat jako Azure SQL nebo Azure DataLake, můžete vytvořit vlastní skript nebo spustitelný soubor nahrát do tohoto umístění. Potom ji můžete volat po spuštění vaší primární spustitelný soubor na příkazovém řádku. V uzlu systému Windows, například může volat tyto dva příkazy: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcete provést kontrolu odkazující nebo časná nahrávání počáteční výsledků.
- Chcete zachovat podrobnou kontrolu nad zpracování chyb. Například můžete implementovat vlastní řešení, pokud chcete použít k provádění určitých akcí nahrávání založené na konkrétní úkol ukončovací kód akce závislosti úkolů. Další informace o Akce závislosti úkolů najdete v tématu [vytvoření závislosti úkolů ke spouštění úloh, které jsou závislé na dalších úkolech,](batch-task-dependencies.md). 

## <a name="next-steps"></a>Další postup

- Prozkoumat pomocí nové funkce v rozhraní API služby Batch uchovávat data úloh v [zachovat data úloh do služby Azure Storage službou Batch služby API](batch-task-output-files.md).
- Další informace o použití knihovny Batch souboru konvence pro .NET v [zachovat úloh a úkolů data do služby Azure Storage pomocí knihovny Batch souboru konvence pro technologii .NET zachovat ](batch-task-output-file-conventions.md).
- Ukázkový projekt [PersistOutputs] [github_persistoutputs] najdete na Githubu, které ukazuje, jak pomocí obou klientské knihovny Batch pro .NET a soubor konvence knihovna pro .NET lze zachovat výstup úlohy do trvalého úložiště.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
