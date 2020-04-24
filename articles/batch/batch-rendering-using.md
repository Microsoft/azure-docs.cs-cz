---
title: Použití možností vykreslování
description: Jak používat možnosti vykreslování Azure Batch. Zkuste použít aplikaci Batch Explorer, a to buď přímo, nebo vyvolanou z modulu plug-in klientské aplikace.
author: mscurrell
ms.author: markscu
ms.date: 03/05/2020
ms.topic: conceptual
ms.openlocfilehash: 6dd7d73d2b04f6e076794ee63d0a546170782fce
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115631"
---
# <a name="using-azure-batch-rendering"></a>Použití vykreslování Azure Batch

K dispozici je několik způsobů, jak Azure Batch vykreslování použít:

* Rozhraní API:
  * Pište kód pomocí kterékoli z rozhraní API pro Batch.  Vývojáři mohou integrovat Azure Batch možností do svých stávajících aplikací nebo pracovních postupů, ať už v cloudu, nebo na základě místního prostředí.
* Nástroje příkazového řádku:
  * Ke skriptování dávkového použití můžete použít [příkazový řádek Azure](https://docs.microsoft.com/cli/azure/) nebo [PowerShell](https://docs.microsoft.com/powershell/azure/overview) .
  * Konkrétně [Podpora šablon Batch CLI](https://docs.microsoft.com/azure/batch/batch-cli-templates) zjednodušuje vytváření fondů a odesílání úloh.
* Batch Explorer uživatelské rozhraní:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) je klientský nástroj pro různé platformy, který umožňuje spravovat a monitorovat účty Batch.
  * Pro každou aplikaci vykreslování je k dispozici řada šablon fondů a úloh, které lze použít ke snadnému vytváření fondů a odesílání úloh.  Sada šablon je uvedena v uživatelském rozhraní aplikace s soubory šablon, ke kterým se přistupoval z GitHubu.
  * Vlastní šablony můžete vytvářet úplně od začátku nebo můžete zkopírovat a upravit dodávané šablony z GitHubu.
* Moduly plug-in klientské aplikace:
  * K dispozici jsou moduly plug-in, které umožňují použití dávkového vykreslování přímo v rámci aplikace návrh a modelování klienta.  Moduly plug-in hlavně vyvolají aplikaci Batch Explorer s kontextové informace o aktuálním 3D model a obsahuje funkce, které vám pomůžou se správou prostředků.

Nejlepším způsobem, jak vyzkoušení Azure Batch pro koncové uživatele, kteří nejsou vývojáři a kteří nejsou odborníky na Azure, je použití aplikace Batch Explorer, a to buď přímo, nebo vyvolanou z modulu plug-in klientské aplikace.

## <a name="using-batch-explorer"></a>Použití Batch Explorer

Podrobný kurz pro použití Batch Explorer k provedení vykreslování najdete v [kurzu Blendu](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Stáhnout a nainstalovat

Batch Explorer [ke stažení jsou k dispozici](https://azure.github.io/BatchExplorer/) pro Windows, OSX a Linux.

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Používání šablon k vytváření fondů a spouštění úloh

K dispozici je komplexní sada šablon pro použití s Batch Explorer, která usnadňuje vytváření fondů a odesílání úloh pro různé aplikace pro vykreslování bez nutnosti určit všechny vlastnosti potřebné k vytváření fondů, úloh a úkolů přímo v dávce.  Šablony, které jsou dostupné v Batch Explorer, se ukládají a zobrazují v [úložišti GitHub](https://github.com/Azure/BatchExplorer-data/tree/master/ncj).

![Galerie Batch Explorer](./media/batch-rendering-using/batch-explorer-gallery.png)

K dispozici jsou šablony pro všechny aplikace, které jsou k dispozici na webu Marketplace pro vykreslování imagí virtuálních počítačů.  Pro každou aplikaci existuje více šablon, včetně šablon fondů do služby stravování pro PROCESORy a fondy GPU, Windows a Linux. šablony úlohy zahrnují kompletní nebo dlaždici vykreslování blendů a distribuované vykreslování V-Ray. Sada dodaných šablon se v průběhu času rozbalí pro další možnosti služby Batch, jako je automatické škálování fondu.

Je také možné vytvářet vlastní šablony, od začátku nebo úpravou dodaných šablon. Vlastní šablony se dají použít tak, že vyberete položku místní šablony v části galerie v Batch Explorer.

### <a name="file-system-and-data-movement"></a>Pohyb systému souborů a přesun dat

Oddíl data v Batch Explorer umožňuje kopírování souborů mezi místními systémy souborů a účty Azure Storage.

## <a name="client-application-plug-ins"></a>Moduly plug-in klientské aplikace

Moduly plug-in jsou k dispozici pro některé klientské aplikace.  Moduly plug-in umožňují vytvořit fondy a úlohy přímo z aplikace nebo vyvolat Batch Explorer.

* [Blend – 2,79](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Blends 2.8 +](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender28)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Další kroky

Příklady dávkového vykreslování si můžete vyzkoušet v těchto dvou kurzech:

* [Vykreslování pomocí Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Vykreslování scén s využitím Batch Exploreru](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)