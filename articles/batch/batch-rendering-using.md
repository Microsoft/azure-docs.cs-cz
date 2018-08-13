---
title: Azure Batch rendering
description: Jak používat možnosti vykreslování v Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: ef2abc147049d264899d227235cffe72a1a1568c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034731"
---
# <a name="using-azure-batch-rendering"></a>Pomocí Azure Batch rendering

Existuje několik způsobů, jak používat Azure Batch rendering:

* Rozhraní API:
  * Pište kód pomocí některé z rozhraní API služby Batch.  Vývojáři mohou možnosti služby Azure Batch začlenit do svých stávajících aplikací nebo pracovního postupu, určuje, zda založené na místních i cloudových.
* Nástroje příkazového řádku:
  * [Rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/) nebo [Powershellu](https://docs.microsoft.com/powershell/azure/overview) je možné skript použití služby Batch.
  * Konkrétně se [podpora šablon rozhraní příkazového řádku služby Batch](https://docs.microsoft.com/azure/batch/batch-cli-templates) usnadňuje mnohem vytvořit fondy a odesílání úloh.
* Batch Explorer uživatelského rozhraní:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) je multiplatformní klientský nástroj, který také umožňuje účtů služby Batch, které chcete spravovat a monitorovat.
  * Pro každou z aplikací vykreslování počet fond a úlohu šablon jsou k dispozici, který je možné snadno vytvořit fondy a odesílání úloh.  Sadu šablon, které je uvedené v uživatelského rozhraní, aplikace se soubory šablon současný přístup z Githubu.
  * Vlastní šablony, dají se vytvářet od začátku nebo dodané šablony z Githubu můžete kopírovat a upravovat.
* Klient aplikace moduly plug-in:
  * Moduly plug-in jsou k dispozici, která umožňují Batch rendering pro použití přímo v rámci klienta návrhu a modelování aplikace.  Moduly plug-in hlavně vyvolat aplikaci služby Batch Explorer kontextové informace o aktuální 3D modelu a obsahuje funkce pro usnadnění správy prostředků.

Nejlepší způsob, jak vyzkoušejte Azure Batch rendering a nejjednodušší způsob, jak koncoví uživatelé, kteří nejsou vývojáři a odborníci na Azure není, je použít Průzkumníka služby Batch aplikace, buď přímo nebo vyvolat z klientské aplikace modulu plug-in.

## <a name="using-batch-explorer"></a>Použití Průzkumníka služby Batch

Podrobný kurz pro použití Průzkumníka služby Batch k provedení viz vykreslování [blenderu kurzu](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Stažení a instalace

Batch Explorer [soubory ke stažení jsou k dispozici](https://azure.github.io/BatchExplorer/) pro Windows, os x a Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Pomocí šablon pro vytváření fondů a spouštění úloh

Je k dispozici pro použití se službou Batch Explorer, který umožňuje snadno vytvořit fondy a odeslání úlohy pro různé aplikace vykreslování bez nutnosti zadávat všechny vlastnosti požadované pro vytváření fondů, úloh a úloh přímo pomocí komplexní sady šablon Batch.  Šablony, které jsou k dispozici v Průzkumníkovi služby Batch jsou uložená a zobrazená v [úložiště GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie Průzkumníka služby batch](./media/batch-rendering-using/batch-explorer-gallery.png)

Šablony jsou k dispozici, který slouží různým pro všechno, co aplikace k dispozici na Tržišti imagí virtuálních počítačů vykreslování.  Pro každou aplikaci existuje několik šablon, včetně šablon fondu, aby vyhovovaly fondy; pro fondy CPU a GPU, Windows a Linux šablony úloh zahrnout úplnou rámce nebo vedle sebe blenderu pro vykreslování a V-Ray distribuované vykreslování. V čase, aby vyhovovaly pro další možnosti služby Batch, jako je například automatické škálování fondu se bude rozšiřovat sadu šablon.

Je také možné pro vlastní šablony, bude vytvořen, od začátku nebo úpravou dodané šablony. Vlastní šablony můžete použít tak, že vyberete položku "Místní šablony" v části "Galerie" Průzkumníka služby Batch.

### <a name="file-system-and-data-movement"></a>Přesun souboru systému a dat

V části "Data" v Průzkumníkovi služby Batch umožňuje soubory zkopírovány mezi místním systému souborů a účtů služby Azure Storage.

## <a name="client-application-plug-ins"></a>Klient aplikace modulů plug-in

Moduly plug-in jsou k dispozici pro některé klientské aplikace.  Moduly plug-in umožňují fondů a úloh vytvořit přímo z aplikace nebo vyvolat Průzkumníka služby Batch.

* [Blenderu](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Další postup

Pro příklady Batch rendering vyzkoušet dva kurzy:

* [Vykreslování pomocí Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Použití Průzkumníka služby Batch Rendering](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)