---
title: Vykreslení aplikace pomocí služby Azure Batch
description: Jak používat vykreslovacích aplikací pomocí služby Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 500246dc98618aead11ba539ce4485d25ac62941
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034735"
---
# <a name="rendering-applications"></a>Vykreslovací aplikace

Vykreslovací aplikace se používají tak, že vytvoříte dávkové úlohy a úkoly. Vlastnost příkazového řádku úkolu určuje příslušný příkazový řádek a parametry.  Nejjednodušší způsob, jak vytvářet úkoly úlohy je použití šablon Batch Explorer uvedená v [v tomto článku](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Šablony můžete prohlížet a upravovat verzí vytvořených v případě potřeby.

Tento článek obsahuje stručný popis toho, jak spouštět každou aplikaci vykreslování.

## <a name="rendering-with-autodesk-3ds-max"></a>Vykreslování s Autodesk 3ds Max

### <a name="renderer-support"></a>Nástroj pro vykreslování podpory

Kromě renderery součástí 3ds Max, následující renderery jsou k dispozici na vykreslování imagí virtuálních počítačů a lze odkazovat pomocí souboru 3ds Max scény:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Příkazový řádek úkolu

Vyvolat `3dsmaxcmdio.exe` aplikace provádět vykreslování příkazového řádku na uzlech fondu.  Tato aplikace je na cestě při spuštění úlohy. `3dsmaxcmdio.exe` Aplikace má k dispozici stejné parametry jako `3dsmaxcmd.exe` aplikaci, která je popsána v [3ds Max dokumentaci](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (vykreslování | Příkazový řádek vykreslování části).

Příklad:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Poznámky:

* K zajištění, že se nacházejí soubory prostředků musí věnovat pozornost.  Zajištění jsou správné a relativní cesty **Asset sledování** okna, nebo použijte `-bitmapPath` parametr příkazového řádku.
* Zkontrolujte, jestli problémy s vykreslování, jako je například neschopnost najít prostředky, tak, že zkontrolujete `stdout.txt` souboru autorem 3ds Max při spuštění úlohy.

### <a name="batch-explorer-templates"></a>Šablon služby batch Explorer

Fond a úlohu šablony je přístupný z **Galerie** v Průzkumníkovi služby Batch.  Zdrojové soubory šablony jsou k dispozici v [Batch Explorer data úložišti na Githubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Vykreslování s Autodesk Maya

### <a name="renderer-support"></a>Nástroj pro vykreslování podpory

Kromě renderery integrovaná v aplikaci Maya následující renderery jsou k dispozici na vykreslování imagí virtuálních počítačů a lze odkazovat pomocí souboru 3ds Max scény:

* Autodesk Arnold
* Chaos Group V-Ray

### <a name="task-command-line"></a>Příkazový řádek úkolu

`renderer.exe` Nástroj příkazového řádku pro vykreslování se používá v příkazovém řádku úkolu. Renderer příkazového řádku je popsána v [Nápověda Maya](http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

V následujícím příkladu úkol přípravy úlohy se použije ke zkopírování souborů scény a prostředky do pracovního adresáře přípravy úlohy, výstupní složky se používá k uložení image vykreslování a vykreslí rámec 10.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro vykreslování V-Ray souboru scény Maya obvykle zadáte V-Ray jako zobrazovací jednotky.  Lze ji také zadat na příkazovém řádku:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro Arnold vykreslování, v souboru scény Maya by normálně zadejte Arnold jako zobrazovací jednotky.  Lze ji také zadat na příkazovém řádku:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Šablon služby batch Explorer

Fond a úlohu šablony je přístupný z **Galerie** v Průzkumníkovi služby Batch.  Zdrojové soubory šablony jsou k dispozici v [Batch Explorer data úložišti na Githubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Další postup

Použití šablon fond a úlohu z [úložiště dat ve službě GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) pomocí Průzkumníka služby Batch.  V případě potřeby, vytváření nových šablon nebo změňte některou z dodané šablony.