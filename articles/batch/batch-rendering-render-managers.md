---
title: Podpora správce vykreslování – Azure Batch
description: Použití Azure k vykreslování pomocí integrace správce vykreslování Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: eb3ce47e5ffed697392065a1faacbbfaec19f4d1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983670"
---
# <a name="using-azure-batch-with-render-farm-managers"></a>Použití Azure Batch s vykreslováním manažerů farmy

Pokud používáte existující místní farmu vykreslování, je velmi pravděpodobnější, že správce vykreslování řídí kapacitu a úlohy vykreslování farmy vykreslování.

Azure poskytuje integrovanou podporu nebo doplňky pro oblíbené správce vykreslování. Pak můžete přidat a odebrat virtuální počítače Azure, včetně virtuálních počítačů s licencováním aplikací pro platby za použití s nízkou prioritou.

Podporují se následující správce vykreslování:

* [PipelineFX Qube!](https://www.pipelinefx.com/)
* [Královská vykreslování](https://www.royalrender.de/)
* [Konečný termín Thinkbox](https://deadline.thinkboxsoftware.com/)

## <a name="azure-render-hub"></a>Centrum vykreslování Azure

Centrum vykreslování Azure zjednodušuje vytváření a správu Farm pro vykreslování Azure.  Centrum vykreslování má nativní podporu pro PipelineFx Qube a konečný termín 10.  Další informace a podrobné pokyny najdete [v úložišti GitHub](https://github.com/Azure/azure-render-hub).

## <a name="using-azure-with-pipelinefx-qube"></a>Používání Azure s PipelineFX Qube

Centrum vykreslování Azure podporuje oblíbené správce vykreslování včetně termínu.  Pokyny k nasazení a použití centra vykreslování najdete v [úložišti GitHub](https://github.com/Azure/azure-render-hub).

Skripty a pokyny pro povolení použití virtuálních počítačů fondu Azure Batch jako Qube pracovní procesy jsou k dispozici také v [úložišti GitHub](https://github.com/Azure/azure-qube).

## <a name="using-azure-with-royal-render"></a>Použití Azure s vykreslováním královská

Královská vykreslování má integrovanou integraci Azure a Azure Batch, která umožňuje rozšiřování farmy vykreslování pomocí virtuálních počítačů založených na Azure. Souhrn najdete [v souborech s nápovědu](https://www.royalrender.de/help8/index.html?Cloudrendering.html).

Příklad zákazníka vykrálovská vykreslit pomocí Integrace Azure najdete v článku o příběhi [Jellyfish snímků](https://customers.microsoft.com/story/jellyfishpictures).

## <a name="using-azure-with-thinkbox-deadline"></a>Používání Azure s konečným termínem Thinkbox

Centrum vykreslování Azure podporuje oblíbené správce vykreslování včetně termínu.  Pokyny k nasazení a použití centra vykreslování najdete v [úložišti GitHub](https://github.com/Azure/azure-render-hub).

## <a name="next-steps"></a>Další kroky

Vyzkoušejte integraci Azure Batch pro správce vykreslování pomocí vhodného modulu plug-in a pokynů na GitHubu, kde je to možné.
