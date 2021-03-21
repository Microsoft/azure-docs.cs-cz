---
title: Kopírovat aplikace a data do uzlů fondu
description: Naučte se kopírovat aplikace a data do uzlů fondu.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703643"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Kopírovat aplikace a data do uzlů fondu

Azure Batch podporuje několik způsobů, jak získat data a aplikace na výpočetní uzly, aby byly k dispozici pro použití v úlohách.

Zvolená metoda může záviset na rozsahu vašeho souboru nebo aplikace. Data a aplikace můžou být potřeba ke spuštění celé úlohy, takže je potřeba je nainstalovat na každý uzel. Některé soubory nebo aplikace mohou být vyžadovány pouze pro konkrétní úlohu. Pro úlohu může být potřeba nainstalovat jiné, ale nemusíte být na každém uzlu. Batch obsahuje nástroje pro každý z těchto scénářů.

## <a name="determine-the-scope-required-of-a-file"></a>Určení rozsahu požadovaného pro soubor

Musíte určit rozsah souboru – je soubor požadovaný pro fond, úlohu nebo úlohu. Soubory, které jsou v oboru pro fond, by měly používat balíčky aplikací fondu nebo spouštěcí úkol. Soubory vymezené pro úlohu by měly používat úkol přípravy úlohy. Dobrým příkladem souborů vymezených ve fondu nebo na úrovni úlohy jsou aplikace. Soubory s rozsahem úkolu by měly používat soubory prostředků úkolu.

## <a name="pool-start-task-resource-files"></a>Zdrojové soubory úlohy spuštění fondu

Pro aplikace nebo data, která je potřeba nainstalovat na každý uzel ve fondu, použijte soubory prostředků spouštěcí úlohy fondu. Tuto metodu použijte spolu s [balíčkem aplikace](batch-application-packages.md) nebo kolekcí souborů prostředků spouštěcího úkolu, aby bylo možné provést příkaz k instalaci.  

Můžete například přesunout nebo nainstalovat aplikace pomocí příkazového řádku spustit úlohu. Můžete také zadat seznam souborů nebo kontejnerů v účtu úložiště Azure. Další informace najdete v tématu [Přidání # ResourceFile v dokumentaci k REST](/rest/api/batchservice/pool/add#resourcefile).

Pokud Každá úloha spuštěná ve fondu spustí aplikaci (. exe), která musí být nejdřív nainstalovaná se souborem. msi, budete muset nastavit vlastnost **počkat** na spuštění úlohy na **hodnotu true**. Další informace najdete v tématu [Přidání # StartTask v dokumentaci k REST](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Odkazy na balíček aplikace

Pro aplikace nebo data, která je potřeba nainstalovat na každý uzel ve fondu, zvažte použití [balíčků aplikací](batch-application-packages.md). K balíčku aplikace není přidružený žádný příkaz Install, ale ke spuštění příkazu install můžete použít spouštěcí úkol. Pokud vaše aplikace nevyžaduje instalaci nebo se skládá z velkého počtu souborů, můžete použít tuto metodu.

Balíčky aplikací jsou užitečné v případě, že máte velký počet souborů, protože mohou zkombinovat mnoho odkazů na soubory do malé datové části. Pokud se pokusíte zahrnout více než 100 samostatných souborů prostředků do jednoho úkolu, může služba Batch zacházet z interního systémového omezení pro jednu úlohu. Balíčky aplikací jsou užitečné také v případě, že máte mnoho různých verzí stejné aplikace a je třeba mezi nimi volit.

## <a name="job-preparation-task-resource-files"></a>Soubory prostředků přípravné úlohy

Pro aplikace nebo data, která je třeba nainstalovat, aby bylo možné úlohu spustit, ale není nutné ji instalovat na celý fond, zvažte použití [souborů prostředků úkolu přípravy úlohy](./batch-job-prep-release.md).

Například pokud váš fond má mnoho různých typů úloh a pouze jeden typ úlohy potřebuje ke spuštění soubor. msi, je vhodné umístit krok instalace do úlohy přípravy úlohy.

## <a name="task-resource-files"></a>Soubory prostředků úkolu

Soubory prostředků úlohy jsou vhodné, pokud je vaše aplikace nebo data relevantní pouze pro jednotlivé úlohy.

Například můžete mít pět úkolů, každé zpracování jiného souboru a potom zápis výstupu do úložiště objektů BLOB v tomto případě by měl být vstupní soubor zadán v kolekci soubory prostředků úlohy, protože každá úloha má vlastní vstupní soubor.

## <a name="additional-ways-to-get-data-onto-nodes"></a>Další způsoby, jak získat data do uzlů

Vzhledem k tomu, že máte kontrolu nad Azure Batch uzly a můžete spouštět vlastní spustitelné soubory, můžete načíst data z libovolného počtu vlastních zdrojů. Ujistěte se, že uzel Batch má připojení k cíli a že máte v uzlu přihlašovací údaje k tomuto zdroji.

Několik příkladů způsobů přenosu dat do uzlů Batch:

- Stahování dat z SQL
- Stahování dat z jiných webových služeb nebo vlastních umístění
- Mapování sdílené síťové složky

## <a name="azure-storage"></a>Azure Storage

Mějte na paměti, že úložiště objektů BLOB má ke stažení cíle škálovatelnosti. Cíle škálovatelnosti sdílené složky ve službě Azure Storage jsou stejné jako u jednoho objektu BLOB. Velikost bude mít vliv na počet uzlů a fondů, které potřebujete.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak používat [balíčky aplikací se službou Batch](batch-application-packages.md).
- Přečtěte si další informace o [práci s uzly a fondy](nodes-and-pools.md).
