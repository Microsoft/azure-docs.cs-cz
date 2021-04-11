---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 1142082caef06dcc36dfd9b0aaddc44d13cf6cc8
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075553"
---
V tomto kroku budete potřebovat přihlašovací údaje předplatného Azure.

Základní prostředek, který se má nakonfigurovat pro Synchronizace souborů Azure, se nazývá *Služba synchronizace úložiště*. Doporučujeme nasadit jenom jednu pro všechny servery, které synchronizují stejnou sadu souborů v současnosti i v budoucnu. Více služeb synchronizace úložiště můžete vytvořit pouze v případě, že máte různé sady serverů, které nikdy nesmí vyměňovat data. Můžete mít třeba servery, které nikdy nesynchronizují stejnou sdílenou složku Azure. V opačném případě se jedná o jediný postup služby synchronizace úložiště.

Vyberte oblast Azure, ve které se služba synchronizace úložiště blíží vašemu umístění. Všechny ostatní cloudové prostředky musí být nasazené ve stejné oblasti. Pokud chcete zjednodušit správu, vytvořte v předplatném novou skupinu prostředků, která bude mít prostředky pro synchronizaci a úložiště.

Další informace najdete v [části o nasazení služby synchronizace úložiště](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) v článku o nasazení synchronizace souborů Azure. Postupujte pouze v této části článku. V pozdějších krocích budou odkazy na další části článku.