---
title: Zachovat výsledky nebo protokoly z dokončené úlohy a úkoly do úložiště dat – Azure Batch | Dokumentace Microsoftu
description: Další informace o různých možnostech trvalá data výstup z úlohy a úkoly služby Batch. Je možné zachovat data do služby Azure Storage nebo do jiného.
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
ms.openlocfilehash: b578abfa6fc0a10edc5daab40f8a0eea5e6653d9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39115058"
---
# <a name="persist-job-and-task-output"></a>Trvalý výstup úloh a funkcí

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Mezi běžné příklady výstup úlohy patří:

- Soubory vytvořené při procesy úloh vstupní data.
- Soubory protokolu přidružené k provedení úlohy. 

Tento článek popisuje různé možnosti pro zachování výstup úlohy a scénáře, pro které je nejvhodnější jednotlivých možností.   

## <a name="about-the-batch-file-conventions-standard"></a>O standardních Batch File Conventions

Batch definuje volitelná sada konvence pro pojmenovávání souborů výstup úlohy ve službě Azure Storage. [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) popisuje tyto konvence. Standardní File Conventions Určuje název kontejneru a objektu blob cesta k cílové ve službě Azure Storage pro danou výstupní soubor podle názvu úlohy a úkolů.

To je na vás, jestli rozhodnete použít standardní File Conventions pro pojmenování výstupních datových souborů. Můžete také pojmenujte cílový kontejner a objektů blob, ale chcete. Pokud použijete standardní soubor konvence pro pojmenování výstupních souborů, pak výstupní soubory jsou k dispozici pro zobrazení v [webu Azure portal][portal].

Existují různé způsoby, které můžete použít standardní konvence souboru:

- Pokud používáte rozhraní API služby Batch k trvalému ukládání výstupních souborů, můžete název cílového kontejnerům a objektům BLOB podle standardu File Conventions. Rozhraní API služby Batch můžete zachovat výstupní soubory z klientského kódu beze změny vaší aplikaci úkolu.
- Pokud vyvíjíte pomocí .NET, můžete použít [knihovny Azure Batch File Conventions pro .NET][nuget_package]. Výhodou této knihovny je, že podporuje dotazování výstupní soubory podle jejich ID nebo účel. Integrované funkce dotazování usnadňuje přístup k souborům výstup z klientské aplikace nebo od ostatních úloh. Úloha aplikace však musí být změněna na volání knihovny File Conventions. Další informace najdete v tématu referenční informace pro [knihovny File Conventions pro .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Pokud vyvíjíte pomocí jiného jazyka než .NET, můžete implementovat standard File Conventions ve vaší aplikaci.

## <a name="design-considerations-for-persisting-output"></a>Aspekty návrhu pro zachování výstup 

Při návrhu řešení pro Batch, vezměte v úvahu následující faktory související s výstupy úloh a úkolů.

* **Výpočetní uzel životnost**: výpočetní uzly jsou často přechodné, zejména v povolené automatické škálování fondů. Výstup z úlohy, která běží na uzlu je k dispozici, pouze když existuje uzlu, a pouze v rámci doby uchování souboru jste nastavili pro úlohu. Pokud úloha vytvoří výstup, který může být potřeba po dokončení úkolu, potom úloha musíte nahrát jeho výstupní soubory do odolného úložiště, jako je Azure Storage.

* **Výstupní úložiště**: Azure Storage se doporučuje jako úložiště dat pro výstup úlohy, ale můžete použít libovolný trvalého úložiště. Zápis výstup úlohy do Azure Storage je integrována do rozhraní API služby Batch. Pokud používáte jiná forma trvalého úložiště, budete muset psát logiku aplikace a zachová sami výstup úlohy.   

* **Výstup načítání**: výstup úlohy můžete načíst přímo z výpočetních uzlů ve fondu nebo z úložiště Azure nebo jiného úložiště dat, pokud mají trvalý výstup úloh. Načíst výstup úkolu přímo z výpočetního uzlu, budete potřebovat název souboru a jeho umístění výstupu na uzlu. Pokud je trvalý výstup úloh do služby Azure Storage, potřebujete úplná cesta k souboru ve službě Azure Storage ke stažení výstupních souborů s využitím Azure Storage SDK.

* **Zobrazení výstupu**: když přejdete na úlohu služby Batch v Azure portal a vyberte **soubory v uzlu**, zobrazí se všechny soubory spojené s úlohou, nejen výstupní soubory vás zajímá. Znovu jsou k dispozici soubory na výpočetních uzlech, pouze když existuje uzlu a pouze v rámci dobu uchovávání souboru jste nastavili pro úlohu. Pokud chcete zobrazit výstup úlohy, které jste ukládají do Azure Storage, můžete použít na webu Azure portal nebo aplikaci klienta služby Azure Storage, jako [Průzkumníka služby Azure Storage][storage_explorer]. Chcete-li zobrazit výstupní data ve službě Azure Storage pomocí portálu nebo jiný nástroj, musíte znát umístění souboru a přejít k němu přímo.

## <a name="options-for-persisting-output"></a>Možnosti pro zachování výstup

V závislosti na scénáři existuje několik různých přístupů, které si můžete zachovat výstup úlohy:

- Pomocí rozhraní API služby Batch.  
- Pomocí knihovny Batch File Conventions pro .NET.  
- Implementace standardu Batch File Conventions ve vaší aplikaci.
- Implementujte řešení pro přesun vlastního souboru.

Následující části popisují jednotlivým přístupům podrobněji.

### <a name="use-the-batch-service-api"></a>Použití rozhraní API služby Batch

Verze 2017-05-01, služba Batch přidá podporu pro určení výstupních souborů ve službě Azure Storage pro data úloh při vám [přidání úkolu do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) nebo [Přidání kolekce úkolů do úlohy](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Rozhraní API služby Batch podporuje zachování dat úloh na účet služby Azure Storage z fondy vytvořené s konfigurací virtuálního počítače. Pomocí rozhraní API služby Batch můžete zachovat data úlohy beze změny aplikací, na kterém běží vaše úlohy. Volitelně můžete dodržovat [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) pro pojmenovávání souborů, které se zachovávají do služby Azure Storage. 

Rozhraní API služby Batch použijte k uchování při výstup úlohy:

- Chcete zachovat data z úlohy služby Batch a úkolech Správce úloh v fondy vytvořené s konfigurací virtuálního počítače.
- Chcete zachovat data ke kontejneru služby Azure Storage libovolný název.
- Chcete zachovat data do kontejneru služby Azure Storage s názvem podle [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> Rozhraní API služby Batch nepodporuje zachovávání dat. z úloh spuštěných v fondy vytvořené s konfigurací cloudové služby. Informace o zachování úloh, výstup z fondů spuštění konfigurace služby cloud services najdete v tématu [uchování úloh a dat do služby Azure Storage pomocí knihovny Batch File Conventions pro .NET k uchování ](batch-task-output-file-conventions.md)
> 
> 

Další informace o zachování výstup úlohy s rozhraním API služby Batch najdete v tématu [trvalého úkolů data do služby Azure Storage pomocí služby Batch služby API](batch-task-output-files.md). Viz také [PersistOutputs] [ github_persistoutputs] ukázkového projektu na Githubu, který ukazuje, jak používat klientskou knihovnu služby Batch pro .NET k uchování výstup úlohy do trvalého úložiště.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Použití služby Batch File Conventions knihovny pro .NET

Vývojářům vyvíjejícím řešení Batch C# a .NET můžete použít [knihovny File Conventions pro .NET] [ nuget_package] k uchování úloh dat do služby Azure Storage account, podle k [dávkového souboru Konvence standardní](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Knihovny File Conventions zpracovává přesunutí výstupní soubory do služby Azure Storage a dobře známý způsob pojmenování cílové kontejnerům a objektům BLOB.

Knihovny File Conventions podporuje dotazování výstupní soubory podle ID nebo účel, což usnadňuje vyhledání bez nutnosti celý soubor identifikátorů URI. 

Pomocí knihovny Batch File Conventions pro .NET k uchování při výstup úlohy:

- Chcete Streamovat data do služby Azure Storage při běhu úlohy.
- Chcete zachovat data z fondy vytvořené s konfigurací cloudové služby nebo konfigurace virtuálního počítače.
- Klientská aplikace nebo další úkoly v úloze musí vyhledat a stáhnout výstupní soubory úloh podle ID nebo podle účelu. 
- Chcete provést verze nebo dřívější bodového nahrávání počáteční výsledky.
- Chcete zobrazit výstup úlohy na webu Azure Portal.

Další informace o zachování úlohy výstup pomocí knihovny File Conventions pro rozhraní .NET najdete v tématu [uchování úloh a dat do služby Azure Storage pomocí knihovny Batch File Conventions pro .NET k uchování ](batch-task-output-file-conventions.md). Viz také [PersistOutputs] [ github_persistoutputs] ukázkového projektu na Githubu, který ukazuje, jak pomocí knihovny File Conventions pro .NET k uchování výstup úlohy do trvalého úložiště.

[PersistOutputs] [ github_persistoutputs] ukázkového projektu na Githubu ukazuje, jak používat klientskou knihovnu služby Batch pro .NET k uchování výstup úlohy do trvalého úložiště.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementace standardu Batch File Conventions

Pokud používáte jiný jazyk než .NET, můžete implementovat [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) ve své aplikaci. 

Můžete provádět standardní pojmenování File Conventions sami když chcete prověřené schéma pojmenování, nebo když chcete zobrazit výstup úlohy na webu Azure Portal.

### <a name="implement-a-custom-file-movement-solution"></a>Implementovat řešení pro přesun vlastního souboru

Můžete také implementovat svoje vlastní řešení pro přesun celý soubor. Použijte tento přístup, když:

- Chcete zachovat data úlohy do úložiště dat než Azure Storage. Postup nahrání souborů do úložiště dat jako Azure SQL nebo Azure DataLake, můžete vytvořit vlastní skript nebo spustitelný soubor k nahrání do tohoto umístění. Potom ji můžete volat po spuštění primární spustitelný soubor na příkazovém řádku. Například v uzlu Windows, může volat tyto dva příkazy: `doMyWork.exe && uploadMyFilesToSql.exe`
- Chcete provést verze nebo dřívější bodového nahrávání počáteční výsledky.
- Chcete udržovat detailní kontrolu nad tím zpracování chyb. Například můžete implementovat svoje vlastní řešení, pokud chcete použít akce závislosti úkolů určité nahrávání akce podle konkrétního úkolu ukončovací kód. Další informace o Akce závislosti úkolů, naleznete v tématu [vytváření závislostí ke spouštění úloh, které jsou závislé na jiných úkolech](batch-task-dependencies.md). 

## <a name="next-steps"></a>Další postup

- Prozkoumejte použití nových funkcí v rozhraní API služby Batch k uchování dat úloh v [trvalého úkolů data do služby Azure Storage pomocí služby Batch služby API](batch-task-output-files.md).
- Další informace o použití knihovny File Conventions služby Batch pro .NET v [uchování úloh a dat do služby Azure Storage pomocí knihovny File Conventions služby Batch pro .NET](batch-task-output-file-conventions.md).
- Zobrazit [PersistOutputs] [ github_persistoutputs] ukázkového projektu na Githubu, který ukazuje, jak používat i klientské knihovny Batch .NET a knihovny File Conventions pro .NET k trvalý výstup úloh do trvalého úložiště .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
