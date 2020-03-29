---
title: Použití vykreslovacích aplikací – Azure Batch
description: Jak používat vykreslovací aplikace s Azure Batch. Tento článek obsahuje stručný popis, jak spustit každou aplikaci vykreslování.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: dc0ce23c90a4ba6575ba26b37d97f94ba8fa1f63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390480"
---
# <a name="rendering-applications"></a>Vykreslovací aplikace

Vykreslovací aplikace se používají při vytváření dávkových úloh a úloh. Vlastnost příkazového řádku úkolu určuje příslušný příkazový řádek a parametry.  Nejjednodušší způsob, jak vytvořit úlohy projektu, je použít šablony aplikace Batch Explorer, jak je uvedeno v [tomto článku](https://docs.microsoft.com/azure/batch/batch-rendering-using#using-batch-explorer).  Šablony lze v případě potřeby zobrazit a upravit verze.

Tento článek obsahuje stručný popis, jak spustit každou aplikaci vykreslování.

## <a name="rendering-with-autodesk-3ds-max"></a>Vykreslování s 3ds Max od služby Autodesk

### <a name="renderer-support"></a>Podpora rendereru

Kromě rendererů zabudovaných do 3ds Max jsou na vykreslovacích obrázcích virtuálního počítači k dispozici následující renderery, na které lze odkazovat v souboru scény 3ds Max:

* Autodesk Arnold
* Chaos Skupina V-Ray

### <a name="task-command-line"></a>Příkazový řádek úkolu

Vyvolat `3dsmaxcmdio.exe` aplikace k provedení vykreslování příkazového řádku v uzlu fondu.  Tato aplikace je na cestě při spuštění úlohy. Aplikace `3dsmaxcmdio.exe` má stejné dostupné parametry `3dsmaxcmd.exe` jako aplikace, která je popsána v [dokumentaci nápovědy 3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (Rendering | vykreslovací sekce příkazového řádku).

Například:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Poznámky:

* Je třeba věnovat velkou pozornost tomu, aby byly nalezeny soubory datových zdrojů.  Ujistěte se, že cesty jsou správné a relativní `-bitmapPath` pomocí okna **Sledování majetku,** nebo použijte parametr na příkazovém řádku.
* Zkontrolujte, zda existují problémy s rendrováním, například `stdout.txt` neschopnost najít datové zdroje, kontrolou souboru napsaného společností 3ds Max při spuštění úlohy.

### <a name="batch-explorer-templates"></a>Šablony dávkového průzkumníka

K šablonám fondu a úloh lze přistupovat z **Galerie** v Průzkumníku dávek.  Zdrojové soubory šablony jsou k dispozici v [datovém úložišti Batch Explorer na GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Vykreslování s Autodesk Maya

### <a name="renderer-support"></a>Podpora rendereru

Kromě rendererů zabudovaných do Mayy jsou na vykreslovacích obrázcích virtuálních počítačích k dispozici následující renderery, na které lze odkazovat v souboru scény 3ds Max:

* Autodesk Arnold
* Chaos Skupina V-Ray

### <a name="task-command-line"></a>Příkazový řádek úkolu

Vykreslovací `renderer.exe` modul příkazového řádku se používá v příkazovém řádku úkolu. Vykreslovač příkazového řádku je popsán v [nápovědě Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

V následujícím příkladu se úloha přípravy úlohy používá ke zkopírování souborů scény a datových zdrojů do pracovního adresáře přípravy úloh, výstupní složka se používá k uložení vykreslovacího obrazu a snímek 10 se vykreslí.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro vykreslování V-Ray by soubor scény Maya normálně specifikoval V-Ray jako vykreslovač.  To může být také zadán na příkazovém řádku:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro vykreslování Arnold, maya scény soubor by normálně určit Arnold jako vykreslovač.  To může být také zadán na příkazovém řádku:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Šablony dávkového průzkumníka

K šablonám fondu a úloh lze přistupovat z **Galerie** v Průzkumníku dávek.  Zdrojové soubory šablony jsou k dispozici v [datovém úložišti Batch Explorer na GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Další kroky

Pomocí fondu a šablon úloh z [úložiště dat v GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) pomocí Dávkového průzkumníka.  V případě potřeby vytvořte nové šablony nebo upravte jednu z dodaných šablon.