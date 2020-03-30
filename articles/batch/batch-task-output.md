---
title: Zachování úlohy a výstupu úloh y do úložiště dat – Azure Batch
description: Seznamte se s různými možnostmi pro uchování výstupních dat z dávkových úloh a úloh. Data můžete uchovávat ve službě Azure Storage nebo do jiného úložiště dat.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0809a838f1d34491eb4e276ce356eded9b98756e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022830"
---
# <a name="persist-job-and-task-output"></a>Trvalý výstup úloh a funkcí

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Mezi běžné příklady výstupu úloh patří:

- Soubory vytvořené při zpracování vstupních dat úlohou.
- Soubory protokolu přidružené k provádění úloh.

Tento článek popisuje různé možnosti pro trvalý výstup úlohy.

## <a name="options-for-persisting-output"></a>Možnosti pro trvalý výstup

V závislosti na scénáři existuje několik různých přístupů, které můžete provést k zachování výstupu úlohy:

- [Použijte rozhraní API služby Batch](batch-task-output-files.md).  
- [Slouží knihovně Konvence dávkových souborů pro rozhraní .NET](batch-task-output-file-conventions.md).  
- Implementujte standard Konvence dávkových souborů ve vaší aplikaci.
- Implementujte vlastní řešení pro přesun souborů.

Následující části stručně popisují každý přístup, jakož i obecné aspekty návrhu pro trvalý výstup.

### <a name="use-the-batch-service-api"></a>Použití rozhraní API dávkové služby

Služba Batch podporuje určení výstupních souborů ve službě Azure Storage pro data úloh, když [přidáte úlohu do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) nebo [přidáte kolekci úkolů do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Další informace o trvalém výstupu úloh pomocí rozhraní API služby Batch najdete v [tématu Persist task data to Azure Storage with the Batch service API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Použití knihovny Dávkových souborů pro rozhraní .NET

Batch definuje volitelnou sadu konvencí pro pojmenování výstupních souborů úloh ve službě Azure Storage. [Standard Konvence dávkových souborů](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) popisuje tyto konvence. Standard File Conventions určuje názvy cílového kontejneru a cesty k objektům blob ve službě Azure Storage pro daný výstupní soubor na základě názvů úlohy a úlohy.

Je jen na vás, zda se rozhodnete použít standard File Conventions pro pojmenování výstupních datových souborů. Cílový kontejner a objekt blob můžete také pojmenovat, jak chcete. Pokud používáte standard File Conventions pro pojmenování výstupních souborů, jsou vaše výstupní soubory k dispozici pro zobrazení na [webu Azure Portal][portal].

Vývojáři, kteří vytvářejí dávková řešení s c# a .NET, můžou pomocí [knihovny File Conventions for .NET][nuget_package] zachovat data úloh v účtu úložiště Azure podle [standardu Batch FileConventions](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). Knihovna File Conventions zpracovává přesouvání výstupních souborů do služby Azure Storage a pojmenování cílových kontejnerů a objektů BLOB známým způsobem.

Další informace o trvalém výstupu úloh v knihovně Konvencí souborů pro rozhraní .NET najdete v [tématu Persist job and task data to Azure Storage with the Batch File Conventions library for .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementace standardu Konvence dávkových souborů

Pokud používáte jiný jazyk než .NET, můžete implementovat [standard Konvence dávkových souborů](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) ve vlastní aplikaci.

Můžete chtít implementovat file conventions pojmenování standard u sebe, když chcete osvědčené schéma pojmenování, nebo pokud chcete zobrazit výstup úlohy na webu Azure portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementace vlastního řešení pro přesun souborů

Můžete také implementovat vlastní kompletní řešení pohybu souborů. Tento postup použijte, pokud:

- Chcete zachovat data úloh do jiného úložiště dat než Azure Storage. Chcete-li nahrát soubory do úložiště dat, jako je Azure SQL nebo Azure DataLake, můžete vytvořit vlastní skript nebo spustitelný soubor pro nahrání do tohoto umístění. Po spuštění primárního spustitelného souboru jej pak můžete volat na příkazovém řádku. Například v uzlu systému Windows můžete volat tyto dva příkazy:`doMyWork.exe && uploadMyFilesToSql.exe`
- Chcete provést check-pointing nebo včasné nahrání počátečních výsledků.
- Chcete zachovat podrobnou kontrolu nad zpracováním chyb. Můžete například implementovat vlastní řešení, pokud chcete pomocí akcí závislostí mezi úkoly provést určité akce nahrávání na základě konkrétních kódů ukončení úkolu. Další informace o akcích závislostí mezi úkoly naleznete v [tématu Vytvoření závislostí mezi úkoly pro spuštění úloh závislých na jiných úkolech](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Aspekty návrhu pro trvalý výstup

Při navrhování řešení Batch zvažte následující faktory související s výstupy úloh a úloh.

- **Životnost výpočetního uzlu**: Výpočetní uzly jsou často přechodné, zejména ve fondech s automatickým škálování. Výstup z úlohy, která je spuštěna na uzlu, je k dispozici pouze v době, kdy uzel existuje, a pouze v rámci doby uchování souborů, kterou jste pro úlohu nastavili. Pokud úloha vytvoří výstup, který může být potřeba po dokončení úlohy, pak úloha musí nahrát své výstupní soubory do trvalého úložiště, jako je azure storage.

- **Výstupní úložiště**: Azure Storage se doporučuje jako úložiště dat pro výstup úlohy, ale můžete použít libovolné trvalé úložiště. Zápis výstupu úlohy do Azure Storage je integrovaný do rozhraní API dávkové služby. Pokud používáte jinou formu trvalého úložiště, budete muset napsat aplikační logiku, abyste si sami uchovávají výstup úlohy.

- **Načtení výstupu**: Výstup úlohy můžete načíst přímo z výpočetních uzlů ve fondu nebo z Azure Storage nebo jiného úložiště dat, pokud máte trvalý výstup úlohy. Chcete-li načíst výstup úlohy přímo z výpočetního uzlu, potřebujete název souboru a jeho výstupní umístění v uzlu. Pokud nadále výstup úlohy do Služby Azure Storage, pak budete potřebovat úplnou cestu k souboru v Úložišti Azure ke stažení výstupních souborů s Azure Storage SDK.

- **Zobrazení výstupu**: Když přejdete na dávkovou úlohu na webu Azure Portal a vyberete **Soubory v uzlu**, zobrazí se všechny soubory přidružené k úloze, nejen výstupní soubory, které vás zajímají. Soubory na výpočetních uzlech jsou opět k dispozici pouze v případě, že uzel existuje, a pouze v rámci doby uchovávání souborů, kterou jste pro úlohu nastavili. Chcete-li zobrazit výstup úlohy, který jste událi ve službě Azure Storage, můžete použít portál Azure nebo klientskou aplikaci Azure Storage, jako je [Průzkumník úložiště Azure][storage_explorer]. Chcete-li zobrazit výstupní data ve službě Azure Storage pomocí portálu nebo jiného nástroje, musíte znát umístění souboru a přejít na něj přímo.

## <a name="next-steps"></a>Další kroky

- Prozkoumejte pomocí nových funkcí v rozhraní API dávkové služby k zachování dat úloh v [úložišti zachovat data úloh do Úložiště Azure pomocí rozhraní API služby Batch](batch-task-output-files.md).
- Další informace o použití knihovny Dávkových konvencí souborů pro rozhraní .NET v [úložišti Persist a úloh do úložiště Azure s knihovnou Dávkových konvencí souborů pro rozhraní .NET](batch-task-output-file-conventions.md).
- Podívejte se na ukázkový projekt [PersistOutputs][github_persistoutputs] na GitHubu, který ukazuje, jak používat knihovnu klienta Batch pro rozhraní .NET a knihovnu Konvencí souborů pro rozhraní .NET k zachování výstupu úloh y do trvalého úložiště.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
