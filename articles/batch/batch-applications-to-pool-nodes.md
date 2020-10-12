---
title: Kopírovat aplikace a data do uzlů fondu
description: Naučte se kopírovat aplikace a data do uzlů fondu.
ms.topic: how-to
ms.date: 02/17/2020
ms.openlocfilehash: e21b8551fb62c4335910fd05bb9590eaf6f7e35a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85954889"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Kopírovat aplikace a data do uzlů fondu

Azure Batch podporuje několik způsobů, jak získat data a aplikace na výpočetní uzly, aby byla data a aplikace k dispozici pro použití v úlohách. Data a aplikace můžou být potřeba ke spuštění celé úlohy, takže je potřeba je nainstalovat na každý uzel. Některé mohou být vyžadovány pouze pro konkrétní úkol nebo musí být pro úlohu nainstalovány, ale nemusí být na všech uzlech. Batch obsahuje nástroje pro každý z těchto scénářů.

- **Zdrojové soubory úlohy spuštění fondu**: pro aplikace nebo data, která je potřeba nainstalovat na každý uzel ve fondu. Tuto metodu použijte spolu s balíčkem aplikace nebo kolekcí souborů prostředků spouštěcího úkolu, aby bylo možné provést příkaz k instalaci.  

Příklady: 
- Použití příkazového řádku Start k přesunutí nebo instalaci aplikací

- Zadejte seznam konkrétních souborů nebo kontejnerů v účtu úložiště Azure. Další informace najdete [v tématu Přidání # resourceFile v dokumentaci k REST](/rest/api/batchservice/pool/add#resourcefile) .

- Každá úloha spuštěná ve fondu běží MyApplication.exe musí být nejdřív nainstalovaná s MyApplication.msi. Použijete-li tento mechanismus, je nutné nastavit vlastnost **počkat na úspěšnost pro** počáteční úkol na **hodnotu true**. Další informace najdete v tématu [Přidání # starttask v dokumentaci k REST](/rest/api/batchservice/pool/add#starttask).

- **Odkazy na balíček aplikace** ve fondu: pro aplikace nebo data, která je třeba nainstalovat na všechny uzly ve fondu. K balíčku aplikace není přidružený žádný příkaz Install, ale ke spuštění příkazu install můžete použít spouštěcí úkol. Pokud vaše aplikace nevyžaduje instalaci nebo se skládá z velkého počtu souborů, můžete použít tuto metodu. Balíčky aplikací jsou vhodné pro velké počty souborů, protože kombinují velký počet odkazů na soubory do malé datové části. Pokud se pokusíte zahrnout více než 100 samostatných souborů prostředků do jednoho úkolu, může služba Batch zacházet z interního systémového omezení pro jednu úlohu. Balíčky aplikací použijte také v případě, že máte přísnější požadavky na správu verzí, kde můžete mít mnoho různých verzí stejné aplikace a mezi nimi je potřeba vybrat. Další informace najdete v tématu [nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací služby Batch](./batch-application-packages.md).

- **Soubory prostředků pro přípravu úlohy**: pro aplikace nebo data, která musí být nainstalována pro spuštění úlohy, ale není nutné je instalovat do celého fondu. Například: Pokud váš fond má mnoho různých typů úloh a MyApplication.msi spustit pouze jeden typ úlohy, je vhodné umístit krok instalace do úlohy přípravy úlohy. Další informace o úlohách přípravy úloh najdete v tématu [spuštění úkolů přípravy úloh a uvolnění úlohy na výpočetních uzlech služby Batch](./batch-job-prep-release.md).

- **Soubory prostředků úlohy**: pro aplikaci nebo data jsou relevantní pouze pro jednotlivé úlohy. Například: máte pět úkolů, každý zpracovává jiný soubor a pak zapisuje výstup do úložiště objektů BLOB.  V takovém případě by měl být vstupní soubor zadaný v kolekci **souborů prostředků Tasks** , protože každý úkol má svůj vlastní vstupní soubor.

## <a name="determine-the-scope-required-of-a-file"></a>Určení rozsahu požadovaného pro soubor

Musíte určit rozsah souboru – je soubor požadovaný pro fond, úlohu nebo úlohu. Soubory, které jsou v oboru pro fond, by měly používat balíčky aplikací fondu nebo spouštěcí úkol. Soubory vymezené pro úlohu by měly používat úkol přípravy úlohy. Dobrým příkladem souborů vymezených ve fondu nebo na úrovni úlohy jsou aplikace. Soubory s rozsahem úkolu by měly používat soubory prostředků úkolu.

### <a name="other-ways-to-get-data-onto-batch-compute-nodes"></a>Další způsoby, jak získat data do výpočetních uzlů Batch

Existují i jiné způsoby, jak získat data do výpočetních uzlů, které nejsou oficiálně integrovány do dávkového REST API. Vzhledem k tomu, že máte kontrolu nad Azure Batch uzly a můžete spouštět vlastní spustitelné soubory, můžete načíst data z libovolného počtu vlastních zdrojů, pokud je uzel Batch připojen k cíli a máte přihlašovací údaje k tomuto zdroji na Azure Batch uzel. Mezi běžné příklady patří:

- Stahování dat z SQL
- Stahování dat z jiných webových služeb nebo vlastních umístění
- Mapování sdílené síťové složky

### <a name="azure-storage"></a>Azure Storage

Úložiště objektů BLOB má ke stažení cíle škálovatelnosti. Cíle škálovatelnosti sdílené složky ve službě Azure Storage jsou stejné jako u jednoho objektu BLOB. Velikost bude mít vliv na počet uzlů a fondů, které potřebujete.

