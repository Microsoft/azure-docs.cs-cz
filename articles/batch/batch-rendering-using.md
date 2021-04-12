---
title: Použití možností vykreslování
description: Jak používat možnosti vykreslování Azure Batch. Zkuste použít aplikaci Batch Explorer, a to buď přímo, nebo vyvolanou z modulu plug-in klientské aplikace.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2020
ms.topic: how-to
ms.openlocfilehash: dc3d2cc53b478b1ec955d8f4b3717b0407772849
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496622"
---
# <a name="using-azure-batch-rendering"></a>Použití vykreslování Azure Batch

> [!IMPORTANT]
> Vykreslování imagí virtuálních počítačů a licencování s platbami za použití se už [nepoužívá a vyřadí se od 29. února 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Chcete-li použít dávku pro vykreslování, je [třeba použít vlastní image virtuálního počítače a licencování standardní aplikace.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

K dispozici je několik způsobů, jak Azure Batch vykreslování použít:

* Rozhraní API:
  * Pište kód pomocí kterékoli z rozhraní API pro Batch.  Vývojáři mohou integrovat Azure Batch možností do svých stávajících aplikací nebo pracovních postupů, ať už v cloudu, nebo na základě místního prostředí.
* Nástroje příkazového řádku:
  * Ke skriptování dávkového použití můžete použít [příkazový řádek Azure](/cli/azure/) nebo [PowerShell](/powershell/azure/) .
  * Konkrétně [Podpora šablon Batch CLI](./batch-cli-templates.md) zjednodušuje vytváření fondů a odesílání úloh.
* Batch Explorer uživatelské rozhraní:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) je klientský nástroj pro různé platformy, který umožňuje spravovat a monitorovat účty Batch.
  * Pro každou aplikaci vykreslování je k dispozici řada šablon fondů a úloh, které lze použít ke snadnému vytváření fondů a odesílání úloh.  Sada šablon je uvedena v uživatelském rozhraní aplikace s soubory šablon, ke kterým se přistupoval z GitHubu.
  * Vlastní šablony můžete vytvářet úplně od začátku nebo můžete zkopírovat a upravit dodávané šablony z GitHubu.
* Moduly plug-in klientské aplikace:
  * K dispozici jsou moduly plug-in, které umožňují použití dávkového vykreslování přímo v rámci aplikace návrh a modelování klienta.  Moduly plug-in hlavně vyvolají aplikaci Batch Explorer s kontextové informace o aktuálním 3D model a obsahuje funkce, které vám pomůžou se správou prostředků.

Nejlepším způsobem, jak vyzkoušení Azure Batch pro koncové uživatele, kteří nejsou vývojáři a kteří nejsou odborníky na Azure, je použití aplikace Batch Explorer, a to buď přímo, nebo vyvolanou z modulu plug-in klientské aplikace.

## <a name="using-batch-explorer"></a>Použití Batch Explorer

Podrobný kurz pro použití Batch Explorer k provedení vykreslování najdete v [kurzu Blendu](./tutorial-rendering-batchexplorer-blender.md).

### <a name="download-and-install"></a>Stáhnout a nainstalovat

Batch Explorer [ke stažení jsou k dispozici](https://azure.github.io/BatchExplorer/) pro Windows, OSX a Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Používání šablon k vytváření fondů a spouštění úloh

K dispozici je komplexní sada šablon pro použití s Batch Explorer, která usnadňuje vytváření fondů a odesílání úloh pro různé aplikace pro vykreslování bez nutnosti určit všechny vlastnosti potřebné k vytváření fondů, úloh a úkolů přímo v dávce.  Šablony, které jsou dostupné v Batch Explorer, se ukládají a zobrazují v [úložišti GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

K dispozici jsou šablony pro všechny aplikace, které jsou k dispozici na webu Marketplace pro vykreslování imagí virtuálních počítačů.  Pro každou aplikaci existuje více šablon, včetně šablon fondů do služby stravování pro PROCESORy a fondy GPU, Windows a Linux. šablony úlohy zahrnují kompletní nebo dlaždici vykreslování blendů a distribuované vykreslování V-Ray. Sada dodaných šablon se v průběhu času rozbalí pro další možnosti služby Batch, jako je automatické škálování fondu.

Je také možné vytvářet vlastní šablony, od začátku nebo úpravou dodaných šablon. Vlastní šablony se dají použít tak, že vyberete položku místní šablony v části galerie v Batch Explorer.

### <a name="file-system-and-data-movement"></a>Pohyb systému souborů a přesun dat

Oddíl data v Batch Explorer umožňuje kopírování souborů mezi místními systémy souborů a účty Azure Storage.

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování si můžete vyzkoušet v těchto dvou kurzech:

* [Vykreslování pomocí Azure CLI](./tutorial-rendering-cli.md)
* [Vykreslování scén s využitím Batch Exploreru](./tutorial-rendering-batchexplorer-blender.md)
