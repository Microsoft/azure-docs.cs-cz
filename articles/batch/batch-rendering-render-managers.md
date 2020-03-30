---
title: Podpora správce vykreslení – Azure Batch
description: Použití integrace správce vykreslení Azure Batch. Přečtěte si o integrované podpoře nebo doplňcích pro oblíbené správce vykreslení.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 246907b16534d1a91833cab633a1973c97429f47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75449683"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Použití Azure Batch s manažery vykreslovací farmy

Pokud používáte existující místní vykreslovací farmu, je vysoce pravděpodobné, že správce vykreslení řídí kapacitu vykreslovací farmy a vykreslení úloh.

Azure poskytuje integrovanou podporu nebo doplňky pro oblíbené správce vykreslení. Pak můžete přidat a odebrat virtuální počítače Azure, včetně virtuálních počítačů s licencováním aplikací s platbami za použití a virtuálními počítači s nízkou prioritou.

Podporováni jsou následující správci vykreslení:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Královský render](https://www.royalrender.de/)
* [Thinkbox Uzávěrka](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Centrum vykreslování Azure

Azure Render Hub zjednodušuje vytváření a správu vykreslovacích farem Azure.  Render Hub má nativní podporu pro PipelineFx Qube a Deadline 10.  Další informace a podrobné pokyny najdete [v úložišti GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Používání Azure s PipelineFX Qube

Azure Render Hub podporuje oblíbené správce vykreslení včetně konečného termínu.  Pokyny k nasazení a používání centra Render Hub najdete [v úložišti GitHub](https://github.com/Azure/azure-render-hub).

Skripty a pokyny, které umožňují virtuální počítače fondu Azure Batch, které mají být použity jako pracovníci Qube, jsou také k dispozici v [úložišti GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Používání Azure s královským vykreslením

Royal Render má integrovanou integraci Azure a Azure Batch, která umožňuje rozšířit vykreslovací farmu pomocí virtuálních počítačů založených na Azure. Shrnutí naleznete [v souborech nápovědy](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Příklad zákazníka Royal Render využívajícího integraci Azure najdete v [článku o zákaznících Medúzy Pictures](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Použití Azure s termínem Thinkbox

Azure Render Hub podporuje oblíbené správce vykreslení včetně konečného termínu.  Pokyny k nasazení a používání centra Render Hub najdete [v úložišti GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte integraci Azure Batch pro správce vykreslování pomocí příslušného modulu plug-in a případně pokynů na GitHubu.
