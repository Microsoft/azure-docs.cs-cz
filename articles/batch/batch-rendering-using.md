---
title: Použití možností vykreslování – Azure Batch
description: Jak používat možnosti vykreslování Azure Batch. Zkuste použít aplikaci Batch Explorer, přímo nebo naváděnou z modulu plug-in klientské aplikace.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: f3b2e641ab187514a7900b2ab7cc75068df00252
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672013"
---
# <a name="using-azure-batch-rendering"></a>Použití vykreslování Azure Batch

Existuje několik způsobů, jak používat vykreslování Azure Batch:

* Rozhraní API:
  * Napište kód pomocí libovolného dávkového souboru API.  Vývojáři můžou integrovat funkce Azure Batch do svých stávajících aplikací nebo pracovních postupů, ať už cloudových nebo založených na místním prostředí.
* Nástroje příkazového řádku:
  * [Příkazový řádek Azure](https://docs.microsoft.com/cli/azure/) nebo [PowerShell](https://docs.microsoft.com/powershell/azure/overview) lze použít k skriptování dávkové použití.
  * Podpora šablony [dávkového uživatelského nastavení](https://docs.microsoft.com/azure/batch/batch-cli-templates) zejména usnadňuje vytváření fondů a odesílání úloh.
* Ui aplikace Batch Explorer:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) je klientský nástroj pro různé platformy, který také umožňuje spravovat a monitorovat účty Batch.
  * Pro každou z vykreslovacích aplikací je k dispozici řada šablon fondu a úloh, které lze použít ke snadnému vytváření fondů a odesílání úloh.  Sada šablon je uvedena v uživatelském rozhraní aplikace, se soubory šablon, které jsou přístupné z GitHubu.
  * Vlastní šablony lze vytvořit od začátku nebo dodané šablony z GitHubu lze kopírovat a upravovat.
* Moduly plug-in klientských aplikací:
  * K dispozici jsou moduly plug-in, které umožňují použití dávkového vykreslování přímo v aplikacích návrhu a modelování klienta.  Moduly plug-in vyvolávají především aplikaci Batch Explorer s kontextovými informacemi o aktuálním 3D modelu a obsahují funkce, které pomáhají spravovat datové zdroje.

Nejlepší způsob, jak vyzkoušet vykreslování Azure Batch a nejjednodušší způsob pro koncové uživatele, kteří nejsou vývojáři a ne odborníci na Azure, je použít aplikaci Batch Explorer, buď přímo, nebo vyvolána z modulu plug-in klientské aplikace.

## <a name="using-batch-explorer"></a>Použití Průzkumníka dávek

Podrobný návod k použití Aplikace Batch Explorer k vykreslování naleznete v [kurzu Blender .](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)

### <a name="download-and-install"></a>Stažení a instalace

Dávkové explorer [ke stažení jsou k dispozici](https://azure.github.io/BatchExplorer/) pro Windows, OSX a Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Použití šablon k vytváření fondů a spouštění úloh

Komplexní sada šablon je k dispozici pro použití s Aplikacemi Batch Explorer, která usnadňuje vytváření fondů a odesílání úloh pro různé vykreslovací aplikace, aniž byste museli specifikovat všechny vlastnosti potřebné k vytváření fondů, úloh a úloh přímo pomocí Dávkové.  Šablony dostupné v Aplikaci Batch Explorer jsou uloženy a viditelné v [úložišti GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie dávkového průzkumníka](./media/batch-rendering-using/batch-explorer-gallery.png)

Šablony jsou k dispozici, které obstarávají všechny aplikace přítomné na marketplace vykreslování image virtuálních počítačových.  Pro každou aplikaci existuje více šablon, včetně šablon fondu, které se starají o fondy CPU a GPU, fondy Windows a Linux; šablony úloh zahrnují vykreslování s plným rámem nebo kachlovým vykreslováním Blenderu a distribuované vykreslování V-Ray. Sada dodaných šablon bude v průběhu času rozšířena tak, aby vyhovovala dalším funkcím dávky, jako je automatické škálování fondu.

Je také možné, aby byly vytvořeny vlastní šablony, od začátku nebo úpravou dodaných šablon. Vlastní šablony lze použít výběrem položky Místní šablony v části Galerie v Průzkumníku dávek.

### <a name="file-system-and-data-movement"></a>Systém souborů a přesun dat

Část Data v Průzkumníkovi dávek umožňuje kopírování souborů mezi místním systémem souborů a účty Azure Storage.

## <a name="client-application-plug-ins"></a>Moduly plug-in klientských aplikací

Moduly plug-in jsou k dispozici pro některé klientské aplikace.  Moduly plug-in umožňují vytváření fondů a úloh přímo z aplikace nebo vyvolání Průzkumníka dávek.

* [Mixér 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Mixér 2.8+](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování vyzkoušejte dva kurzy:

* [Vykreslování pomocí azure cli](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Vykreslování scén s využitím Batch Exploreru](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)