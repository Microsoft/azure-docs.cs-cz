---
title: Trvalý výstup úloh a úloh do úložiště dat
description: Přečtěte si o různých možnostech, jak zachovat výstupní data z úloh a úloh služby Batch. Data můžete ukládat do Azure Storage nebo do jiného úložiště dat.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85965106"
---
# <a name="persist-job-and-task-output"></a>Trvalý výstup úloh a funkcí

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Mezi běžné příklady výstupu úlohy patří:

- Soubory vytvořené při zpracovávání vstupních dat úlohou.
- Soubory protokolu přidružené ke spuštění úlohy.

Tento článek popisuje různé možnosti pro zachování výstupu úlohy.

## <a name="options-for-persisting-output"></a>Možnosti pro zachování výstupu

V závislosti na vašem scénáři existuje několik různých přístupů, které můžete provést, abyste zachovali výstup úlohy:

- [Použijte rozhraní API služby Batch](batch-task-output-files.md).  
- [Použijte knihovnu konvence souborů Batch pro .NET](batch-task-output-file-conventions.md).  
- Implementujte standard pro dávkové soubory ve vaší aplikaci.
- Implementujte vlastní řešení pro přesun souborů.

V následujících částech jsou stručně popsány jednotlivé postupy a také Obecné faktory návrhu pro zachování výstupu.

### <a name="use-the-batch-service-api"></a>Použití rozhraní API služby Batch

Služba Batch podporuje určení výstupních souborů v Azure Storage pro data úlohy při [Přidání úlohy do úlohy](/rest/api/batchservice/add-a-task-to-a-job) nebo [Přidání kolekce úkolů do úlohy](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Další informace o zachování výstupu úlohy pomocí rozhraní API služby Batch najdete v tématu [zachování dat úkolu pro Azure Storage pomocí rozhraní API služby Batch](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Použití knihovny konvence souborů Batch pro .NET

Batch definuje volitelnou sadu konvencí pro pojmenování výstupních souborů úloh v Azure Storage. Tyto konvence Standard popisují tyto konvence pro [dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) . Standardní konvence souborů určuje názvy cílového kontejneru a cesty objektů BLOB v Azure Storage pro daný výstupní soubor na základě názvů úlohy a úlohy.

Nezáleží na tom, jestli se rozhodnete použít pro pojmenování výstupních datových souborů standardní konvence souborů. Cílový kontejner a objekt blob můžete také pojmenovat, ale chcete. Pokud pro pojmenování výstupních souborů použijete standardní konvence souborů, jsou vaše výstupní soubory k dispozici pro zobrazení v [Azure Portal][portal].

Vývojáři, kteří sestavují řešení Batch pomocí C# a .NET, můžou pomocí [knihovny konvence souborů pro .NET][nuget_package] uchovávat data úloh na účet Azure Storage v závislosti na [standardu pro dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). Knihovna konvence souborů zpracovává přesun výstupních souborů do Azure Storage a pojmenování cílových kontejnerů a objektů BLOB dobře známým způsobem.

Další informace o zachování výstupu úlohy pomocí knihovny konvence souborů pro rozhraní .NET najdete v tématu [zachování úloh a dat úloh pro Azure Storage s knihovnou konvence souborů Batch pro .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementace standardu Souborová konvence pro dávkové soubory

Pokud používáte jiný jazyk než .NET, můžete implementovat [Standard pro dávkové soubory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) ve vaší vlastní aplikaci.

Můžete chtít implementovat konvence souborů sami, pokud chcete osvědčené schéma pojmenování, nebo když chcete zobrazit výstup úlohy v Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementace vlastního řešení pro přesun souborů

Můžete také implementovat vlastní kompletní řešení pro přesun souborů. Tento postup použijte v těchto případech:

- Chcete zachovat data úkolu do jiného úložiště dat než Azure Storage. Pokud chcete nahrát soubory do úložiště dat, jako je Azure SQL nebo Azure datalake, můžete vytvořit vlastní skript nebo spustitelný soubor, který se odešle do tohoto umístění. Pak ji můžete zavolat na příkazovém řádku po spuštění vašeho primárního spustitelného souboru. V uzlu Windows například můžete zavolat tyto dva příkazy: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcete provést příkaz k vrácení se změnami nebo prvotní nahrání počátečních výsledků.
- Chcete udržovat detailní kontrolu nad zpracováním chyb. Například můžete chtít implementovat vlastní řešení, pokud chcete použít akce závislosti úloh k provedení určitých akcí odeslání na základě kódů ukončení úlohy. Další informace o akcích závislostí úkolů najdete v tématu [Vytváření závislostí úloh pro spouštění úloh, které jsou závislé na jiných úkolech](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Faktory návrhu pro zachování výstupu

Při návrhu řešení Batch Vezměte v úvahu následující faktory, které souvisejí s výstupem úloh a úloh.

- **Doba života výpočetního uzlu**: výpočetní uzly jsou často přechodné, zejména v fondech s podporou automatického škálování. Výstup úlohy, která je spuštěna na uzlu, je k dispozici pouze v případě, že uzel existuje a pouze v rámci doby uchování souboru, kterou jste pro úkol nastavili. Pokud úloha vytvoří výstup, který může být potřeba po dokončení úkolu, pak musí odeslat své výstupní soubory do trvalého úložiště, jako je Azure Storage.

- **Výstupní úložiště**: Azure Storage se doporučuje jako úložiště dat pro výstup úlohy, ale můžete použít jakékoli trvalé úložiště. Zápis výstupu úlohy do Azure Storage je integrovaný do rozhraní API služby Batch. Pokud použijete jinou formu trvalého úložiště, budete muset napsat logiku aplikace, abyste zachovali výstup úlohy sami.

- **Načtení výstupu**: výstup úlohy můžete načíst přímo z výpočetních uzlů ve fondu nebo z Azure Storage nebo jiného úložiště dat, pokud máte uložený výstup úlohy. Chcete-li načíst výstup úlohy přímo z výpočetního uzlu, budete potřebovat název souboru a jeho výstupní umístění na uzlu. Pokud zachová výstup úlohy do Azure Storage, budete potřebovat úplnou cestu k souboru v Azure Storage ke stažení výstupních souborů pomocí sady Azure Storage SDK.

- **Zobrazení výstupu**: Když přejdete na úlohu Batch v Azure Portal a vyberete **soubory na uzlu**, zobrazí se všechny soubory přidružené k této úloze, nikoli jenom výstupní soubory, které vás zajímají. Soubory na výpočetních uzlech jsou zase k dispozici pouze v době, kdy uzel existuje a je pouze v čase uchování souboru, který jste nastavili pro úlohu. Chcete-li zobrazit výstup úlohy, který jste zachovali Azure Storage, můžete použít Azure Portal nebo Azure Storage klientské aplikace, jako je například [Průzkumník služby Azure Storage][storage_explorer]. Chcete-li zobrazit výstupní data v Azure Storage s portálem nebo jiným nástrojem, je nutné znát umístění souboru a přejít přímo na něj.

## <a name="next-steps"></a>Další kroky

- Seznamte se s využitím nových funkcí v rozhraní API služby Batch, abyste zachovali data úloh v [datech trvalého úkolu, abyste je Azure Storage pomocí rozhraní API služby Batch](batch-task-output-files.md).
- Přečtěte si o použití knihovny konvence souborů služby Batch pro .NET v části [trvalá úloha a data úkolu k Azure Storage s knihovnou konvence souborů Batch pro .NET](batch-task-output-file-conventions.md).
- Podívejte se na vzorový projekt [PersistOutputs][github_persistoutputs] na GitHubu, který ukazuje, jak použít jak knihovnu klienta Batch pro .NET, tak knihovnu pro konvence souborů pro .NET, aby se výstup úlohy zachoval do trvalého úložiště.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
