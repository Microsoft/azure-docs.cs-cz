---
title: Použití vykreslovacích aplikací
description: Jak používat vykreslování aplikací pomocí Azure Batch. Tento článek poskytuje stručný popis spuštění jednotlivých aplikací pro vykreslování.
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: ace3fe7aee6b9ffc7226448b455bcfea1f931458
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964868"
---
# <a name="rendering-applications"></a>Vykreslování aplikací

Vykreslování aplikací se používá při vytváření úloh a úloh Batch. Vlastnost příkazového řádku úlohy určuje příslušný příkazový řádek a parametry.  Nejjednodušší způsob, jak vytvořit úlohy úlohy, je použití šablon Batch Explorer, jak je uvedeno v [tomto článku](./batch-rendering-using.md#using-batch-explorer).  V případě potřeby lze v šablonách zobrazit a upravit verze, které byly vytvořeny.

Tento článek poskytuje stručný popis spuštění jednotlivých aplikací pro vykreslování.

## <a name="rendering-with-autodesk-3ds-max"></a>Vykreslování pomocí aplikace Autodesk 3ds Max

### <a name="renderer-support"></a>Podpora vykreslovacího modulu

Kromě vykreslovacích modulů integrovaných do aplikace 3ds Max jsou k dispozici následující zobrazovací jednotky na obrázcích pro vykreslování virtuálních počítačů, na které lze odkazovat pomocí souboru s maximální scénou 3DS:

* Autodesk Arnold
* Chaos skupina V-Ray

### <a name="task-command-line"></a>Příkazový řádek úlohy

Vyvolat `3dsmaxcmdio.exe` aplikaci k provedení vykreslování příkazového řádku na uzlu fondu.  Tato aplikace se nachází na cestě při spuštění úlohy. `3dsmaxcmdio.exe`Aplikace má stejné dostupné parametry jako `3dsmaxcmd.exe` aplikace, která je popsána v dokumentaci k nástroji [3ds Max](https://help.autodesk.com/view/3DSMAX/2018/ENU/) (rendering | Command-Line rendering section).

Například:

```
3dsmaxcmdio.exe -v:5 -rfw:0 -start:{0} -end:{0} -bitmapPath:"%AZ_BATCH_JOB_PREP_WORKING_DIR%\sceneassets\images" -outputName:dragon.jpg -w:1280 -h:720 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scenes\dragon.max"
```

Poznámky:

* Aby se zajistilo, že se soubory assetů našly, je potřeba věnovat velkou péči.  Zajistěte, aby byly cesty správné a relativní pomocí okna pro **sledování assetu** , nebo použijte `-bitmapPath` parametr na příkazovém řádku.
* Podívejte se, jestli jsou problémy s vykreslováním, jako je například neschopnost najít assety, kontrolou `stdout.txt` souboru zapsaného aplikací 3ds Max při spuštění úlohy.

### <a name="batch-explorer-templates"></a>Šablony Batch Explorer

K šablonám fondů a úloh je možné přistupovat z **Galerie** v Batch Explorer.  Zdrojové soubory šablony jsou k dispozici v [úložišti Batch Explorerch dat na GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/3dsmax).

## <a name="rendering-with-autodesk-maya"></a>Vykreslování pomocí Autodesk Maya

### <a name="renderer-support"></a>Podpora vykreslovacího modulu

Kromě vykreslovacích modulů integrovaných do Maya jsou k dispozici následující zobrazovací jednotky na obrázcích virtuálních počítačů, na kterých je možné odkazovat pomocí souboru s maximální scénou 3DS:

* Autodesk Arnold
* Chaos skupina V-Ray

### <a name="task-command-line"></a>Příkazový řádek úlohy

`renderer.exe`Vykreslovací modul příkazového řádku se používá v příkazovém řádku úlohy. Modul pro vykreslování příkazového řádku je popsán v [nápovědě k Maya](https://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=GUID-EB558BC0-5C2B-439C-9B00-F97BCB9688E4).

V následujícím příkladu se k kopírování souborů scény a prostředků do pracovního adresáře přípravy úlohy používá úloha přípravy úlohy. k uložení image vykreslování se použije výstupní složka a vykreslí se snímek 10.

```
render -renderer sw -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro vykreslování V-Ray by soubor scény Maya normálně jako zobrazovací jednotku určil V-Ray.  Dá se taky zadat na příkazovém řádku:

```
render -renderer vray -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

Pro vykreslování Arnold by soubor scény Maya normálně určil Arnold jako zobrazovací jednotku.  Dá se taky zadat na příkazovém řádku:

```
render -renderer arnold -proj "%AZ_BATCH_JOB_PREP_WORKING_DIR%" -verb -rd "%AZ_BATCH_TASK_WORKING_DIR%\output" -s 10 -e 10 -x 1920 -y 1080 "%AZ_BATCH_JOB_PREP_WORKING_DIR%\scene-file.ma"
```

### <a name="batch-explorer-templates"></a>Šablony Batch Explorer

K šablonám fondů a úloh je možné přistupovat z **Galerie** v Batch Explorer.  Zdrojové soubory šablony jsou k dispozici v [úložišti Batch Explorerch dat na GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/maya).

## <a name="next-steps"></a>Další kroky

Použijte šablony fondů a úloh z [úložiště dat v GitHubu](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) pomocí Batch Explorer.  V případě potřeby vytvořte nové šablony nebo upravte jednu ze zadaných šablon.
