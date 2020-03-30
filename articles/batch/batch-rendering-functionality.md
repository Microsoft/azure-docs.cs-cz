---
title: Možnosti vykreslování – Azure Batch
description: Standardní funkce Azure Batch se používají ke spuštění úloh y vykreslování a aplikací. Batch obsahuje specifické funkce pro podporu úloh vykreslování.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 697e2640b7215e0bbb9202c672f936535831eb99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449712"
---
# <a name="azure-batch-rendering-capabilities"></a>Možnosti vykreslování Azure Batch

Standardní funkce Azure Batch se používají ke spuštění úloh y vykreslování a aplikací. Batch také obsahuje specifické funkce pro podporu vykreslování úloh.

Přehled konceptů dávek, včetně fondů, úloh a úkolů, naleznete v [tomto článku](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Dávkové fondy

### <a name="rendering-application-installation"></a>Instalace aplikace vykreslování

Image virtuálního počítače Azure Marketplace lze zadat v konfiguraci fondu, pokud je potřeba použít jenom předinstalované aplikace.

K dispozici je obrázek Windows 2016 a obrázek CentOS.  Na [Azure Marketplace](https://azuremarketplace.microsoft.com)najdete image virtuálních počítače hledáním "dávkové vykreslování".

Příklad konfigurace fondu najdete v tématu [vykreslování Azure CLI kurzu](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Portál Azure a Batch Explorer poskytují nástroje grafického uživatelského rozhraní pro výběr vykreslovací image virtuálního počítače při vytváření fondu.  Pokud používáte dávkové rozhraní API, zadejte následující hodnoty vlastností pro [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) při vytváření fondu:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Version |
|---------|---------|---------|--------|
| dávka | rendering-centos73 | Vykreslování | nejnovější |
| dávka | vykreslování windows2016 | Vykreslování | nejnovější |

Další možnosti jsou k dispozici, pokud jsou požadovány další aplikace ve virtuálních počítačích fondu:

* Vlastní obrázek z Galerie sdílených obrázků:
  * Pomocí této možnosti můžete na virtuálním počítači nakonfigurovat konkrétní aplikace a verze, které požadujete. Další informace naleznete [v tématu Vytvoření fondu pomocí Galerie sdílených obrázků](batch-sig-images.md). Společnosti Autodesk a Chaos Group upravily společnosti Arnold a V-Ray tak, aby ověřovaly licenční službu Azure Batch. Ujistěte se, že máte verze těchto aplikací s touto podporou, jinak nebude fungovat licencování za použití. Aktuální verze Maya nebo 3ds Max nevyžadují licenční server při spuštění bezhlavý (v režimu dávky / příkazového řádku). Pokud si nejste jistí, jak s touto možností postupovat, obraťte se na podporu Azure.
* [Balíčky aplikací](https://docs.microsoft.com/azure/batch/batch-application-packages):
  * Zabalte soubory aplikace pomocí jednoho nebo více souborů ZIP, nahrajte je přes portál Azure a zadejte balíček v konfiguraci fondu. Při vytvoření virtuálních aplikací fondu se stáhnou soubory ZIP a extrahují je.
* Soubory prostředků:
  * Soubory aplikací se nahrávají do úložiště objektů blob Azure a v [úloze spuštění fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)zadáte odkazy na soubory . Při vytvoření virtuálních počítačích fondu se soubory prostředků stáhnou do každého virtuálního počítače.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licencování plateb za použití pro předinstalované aplikace

Aplikace, které budou použity a mají licenční poplatek, musí být specifikovány v konfiguraci fondu.

* Zadejte `applicationLicenses` vlastnost při [vytváření fondu](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  Následující hodnoty mohou být zadány v poli řetězců - "vray", "arnold", "3dsmax", "maya".
* Když zadáte jednu nebo více aplikací, náklady na tyto aplikace se přidají k nákladům na virtuální chod.  Ceny aplikací jsou uvedeny na [stránce ceny Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Pokud se místo toho připojíte k licenčnímu serveru `applicationLicenses` a použijete vykreslovací aplikace, nezadávejte vlastnost.

K výběru aplikací a zobrazení cen aplikací můžete použít portál Azure nebo Průzkumník dávek.

Pokud je proveden pokus o použití aplikace, ale aplikace nebyla zadána ve `applicationLicenses` vlastnosti konfigurace fondu nebo se netýká licenčního serveru, spuštění aplikace se nezdaří s chybou licencování a nenulovým ukončovacím kódem.

### <a name="environment-variables-for-pre-installed-applications"></a>Proměnné prostředí pro předinstalované aplikace

Aby bylo možné vytvořit příkazový řádek pro vykreslovací úlohy, musí být určeno umístění instalace spustitelných souborů vykreslovací aplikace.  Proměnné systémového prostředí byly vytvořeny na image virtuálního počítače Azure Marketplace, které lze použít místo nutnosti určit skutečné cesty.  Tyto proměnné prostředí jsou navíc ke [standardním proměnným prostředí dávka vytvořeným](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) pro každou úlohu.

|Aplikace|Spustitelný soubor aplikace|Proměnná prostředí|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Skupina V-Ray samostatný|vray.exe|VRAY_3.60.4_EXEC|
Příkazový řádek Arnold 2017|kick.exe|ARNOLD_2017_EXEC|
|Příkazový řádek Arnold 2018|kick.exe|ARNOLD_2018_EXEC|
|Mixér|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Rodiny virtuálních počítačů Azure

Stejně jako u jiných úloh se požadavky na systém vykreslování liší a požadavky na výkon se u úloh a projektů liší.  V Azure je k dispozici celá řada rodin virtuálních zařízení v závislosti na vašich požadavcích – nejnižší cena, nejlepší cena/výkon, nejlepší výkon a tak dále.
Některé vykreslovací aplikace, například Arnold, jsou založeny na procesoru; jiné, jako jsou Cykly V-Ray a Blender, mohou používat procesory a/nebo GPU.
Popis dostupných rodin virtuálních počítačů a velikostí virtuálních počítačů [najdete v tématu Typy a velikosti virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Virtuální počítače s nízkou prioritou

Stejně jako u jiných úloh lze virtuální aplikace s nízkou prioritou využít ve fondech dávek pro vykreslování.  Virtuální počítače s nízkou prioritou fungují stejně jako běžné vyhrazené virtuální počítače, ale využívají přebytečnou kapacitu Azure a jsou k dispozici s velkou slevou.  Kompromis pro použití virtuálních počítačů s nízkou prioritou je, že tyto virtuální počítače nemusí být k dispozici k přidělení nebo mohou být kdykoli v závislosti na dostupné kapacitě vyzývavé. Z tohoto důvodu virtuální chodů s nízkou prioritou nebudou vhodné pro všechny úlohy vykreslování. Například pokud obrázky trvat mnoho hodin k vykreslení pak je pravděpodobné, že s vykreslování těchto obrazů přerušena a restartována z důvodu virtuálních můřic je preempted by nebylo přijatelné.

Další informace o charakteristikách virtuálních počítače s nízkou prioritou a o různých způsobech jejich konfigurace pomocí batch, najdete v tématu [Použití virtuálních počítače s nízkou prioritou s batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Pracovní místa a úkoly

Pro úlohy a úkoly není vyžadována žádná podpora specifická pro vykreslování.  Hlavní položkou konfigurace je příkazový řádek úlohy, který musí odkazovat na požadovanou aplikaci.
Při použití image virtuálního počítače Azure Marketplace, pak je osvědčeným postupem použít proměnné prostředí k určení cesty a spustitelný soubor aplikace.

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování vyzkoušejte dva kurzy:

* [Vykreslování pomocí azure cli](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Vykreslování scén s využitím Batch Exploreru](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
