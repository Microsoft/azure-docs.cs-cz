---
title: Kopírování aplikací a dat do fondu uzlů
description: Přečtěte si, jak kopírovat aplikace a data do fondu uzlů.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/17/2020
ms.author: labrenne
ms.openlocfilehash: 226a0d69ac387142ecf580537e35f8754ac848a6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385581"
---
# <a name="copying-applications-and-data-to-pool-nodes"></a>Kopírování aplikací a dat do fondu uzlů

Azure Batch podporuje několik způsobů, jak získat data a aplikace do výpočetních uzlů tak, aby data a aplikace jsou k dispozici pro použití úlohy. Data a aplikace mohou být vyžadovány ke spuštění celé úlohy, a proto je třeba nainstalovat na každém uzlu. Některé mohou být vyžadovány pouze pro konkrétní úkol nebo je třeba nainstalovat pro úlohu, ale nemusí být na každém uzlu. Batch má nástroje pro každý z těchto scénářů.

- **Soubory prostředků úloh y zahájení fondu**: Pro aplikace nebo data, která je třeba nainstalovat do každého uzlu ve fondu. Pomocí této metody spolu s balíčkem aplikace nebo kolekce souborů prostředků úlohy spuštění k provedení příkazu instalace.  

Příklady: 
- Přesunutí nebo instalace aplikací pomocí příkazového řádku úkol zahájení

- Zadejte seznam konkrétních souborů nebo kontejnerů v účtu úložiště Azure. Další informace naleznete v [tématu add#resourcefile v dokumentaci rest](https://docs.microsoft.com/rest/api/batchservice/pool/add#resourcefile)

- Každá úloha, která běží ve fondu, spustí myApplication.exe, který musí být nejprve nainstalován pomocí myApplication.msi. Pokud použijete tento mechanismus, je třeba nastavit spuštění úkolu **čekat na úspěch** vlastnost **true**. Další informace naleznete v [tématu add#starttask v dokumentaci rest](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask).

- **Odkazy na balíček aplikace** ve fondu: Pro aplikace nebo data, která je třeba nainstalovat na každém uzlu ve fondu. K balíčku aplikace není přidružen žádný příkaz k instalaci, ale ke spuštění libovolného příkazu instalace můžete použít počáteční úlohu. Pokud vaše aplikace nevyžaduje instalaci nebo se skládá z velkého počtu souborů, můžete použít tuto metodu. Balíčky aplikací jsou vhodné pro velký počet souborů, protože kombinují velký počet odkazů na soubory do malé datové části. Pokud se pokusíte zahrnout více než 100 samostatných souborů prostředků do jednoho úkolu, služba Batch může narazit na interní systémová omezení pro jeden úkol. Balíčky aplikací použijte také v případě, že máte přísné požadavky na správu verzí, kde můžete mít mnoho různých verzí stejné aplikace a potřebujete si mezi nimi vybrat. Další informace načtete [nasazení aplikací k výpočtu uzlů pomocí balíčků aplikací Batch](https://docs.microsoft.com/azure/batch/batch-application-packages).

- **Soubory prostředků úlohy přípravy**úlohy úlohy: Pro aplikace nebo data, která musí být nainstalována pro spuštění úlohy, ale není nutné instalovat v celém fondu. Například: Pokud váš fond má mnoho různých typů úloh a pouze jeden typ úlohy potřebuje MyApplication.msi ke spuštění, má smysl umístit krok instalace do úlohy přípravy úlohy úlohy. Další informace o úlohách přípravy úloh naleznete v [tématu Spuštění úloh přípravy úloh a úloh y uvolnění úloh y v uzlech Batch compute](https://azure.microsoft.com/documentation/articles/batch-job-prep-release/).

- **Soubory prostředků úloh**: Pokud je aplikace nebo data relevantní pouze pro jednotlivé úkoly. Například: Máte pět úkolů, každý zpracuje jiný soubor a pak zapíše výstup do úložiště objektů blob.  V takovém případě by měl být vstupní soubor určen v **kolekci souborů prostředků úloh,** protože každý úkol má svůj vlastní vstupní soubor.

## <a name="determine-the-scope-required-of-a-file"></a>Určení požadovaného oboru souboru

Je třeba určit rozsah souboru - je soubor potřebný pro fond, úlohu nebo úkol. Soubory, které jsou vymezeny do fondu by měly používat balíčky aplikací fondu nebo počáteční úlohu. Soubory s vymezeným oborem úlohy by měly používat úlohu přípravy úlohy úlohy úlohy. Dobrým příkladem souborů s vymezeným oborem na úrovni fondu nebo úlohy jsou aplikace. Soubory s vymezenou oborem úkolu by měly používat soubory prostředků úlohy.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Další způsoby, jak získat data do dávkových výpočetních uzlů

Existují i jiné způsoby, jak získat data do dávkových výpočetních uzlů, které nejsou oficiálně integrovány do rozhraní API batch REST. Vzhledem k tomu, že máte kontrolu nad uzly Azure Batch a můžete spustit vlastní spustitelné soubory, můžete vyžádat data z libovolného počtu vlastních zdrojů tak dlouho, dokud uzel Batch má připojení k cíli a máte pověření k tomuto zdroji do uzlu Azure Batch. Několik běžných příkladů je:

- Stahování dat z SQL
- Stahování dat z jiných webových služeb/vlastních umístění
- Mapování sdílené síťové složky

### <a name="azure-storage"></a>Úložiště Azure

Úložiště objektů blob má cíle škálovatelnosti stahování. Cíle škálovatelnosti souboru úložiště Azure jsou stejné jako u jednoho objektu blob. Velikost bude mít vliv na počet uzlů a fondů, které potřebujete.

