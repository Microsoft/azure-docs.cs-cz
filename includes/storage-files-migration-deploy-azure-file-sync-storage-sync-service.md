---
title: Nasazení služby synchronizace úložiště
description: Nasazení cloudového prostředku Azure File Sync, služby synchronizace úložiště. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 248b61530b80a00aa10272dcb3d28b85c6ee04e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143609"
---
V tomto kroku budete potřebovat přihlašovací údaje předplatného Azure.

Základní prostředek, který se má nakonfigurovat pro Azure File Sync, se nazývá *Služba synchronizace úložiště*. Doporučujeme nasadit jenom jednu pro všechny servery, které synchronizují stejnou sadu souborů v současnosti i v budoucnu. Pokud máte různé sady serverů, které nikdy nesmí vyměňovat data (například synchronizovat stejnou sdílenou složku Azure), vytvořte více služeb synchronizace úložiště. V opačném případě se jedná o jediný postup služby synchronizace úložiště.

Vyberte oblast Azure, ve které se služba synchronizace úložiště blíží vašemu umístění. Všechny ostatní cloudové prostředky musí být nasazené ve stejné oblasti.
Pokud chcete zjednodušit správu, vytvořte v předplatném novou skupinu prostředků, která bude mít prostředky pro synchronizaci a úložiště.

Další informace najdete v [části o nasazení služby synchronizace úložiště](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service) v článku o nasazení Azure File Sync. Postupujte pouze v této části článku. V pozdějších krocích budou odkazy na další části článku.