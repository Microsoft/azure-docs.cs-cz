---
title: Nasazení služby synchronizace úložiště
description: Nasazení cloudového prostředku Azure File Sync. Služba synchronizace úložiště. Společný textový blok, který je sdílen mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 4468d8c6b57b38bdbaaa01208cfc4915f734f6bb
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209451"
---
V tomto kroku budete potřebovat přihlašovací údaje předplatného Azure. Předplatné Azure, které použijete, může být jiné než ten, který používáte pro StorSimple.

Základní prostředek, který se má nakonfigurovat Azure File Sync, se nazývá služba synchronizace úložiště.
Doporučujeme pro všechny servery ve společnosti nasadit jenom jeden, který teď synchronizuje stejnou sadu souborů, nebo v budoucnu. Pokud máte více než jedno zařízení StorSimple, můžete zvážit vytvoření prostředku služby synchronizace úložiště pro každou z nich. Měli byste ale vytvořit jenom více služeb synchronizace úložiště, pokud máte různé sady serverů, které musí data nikdy vyměňovat. V opačném případě je osvědčeným postupem jediná služba synchronizace úložiště.

Vyberte oblast Azure pro službu synchronizace úložiště, která se blíží umístění vaší kanceláře. Všechny ostatní cloudové prostředky musí být nasazené ve stejné oblasti.
Osvědčeným postupem je vytvořit novou skupinu prostředků ve vašem předplatném, abyste mohli spravovat prostředky pro synchronizaci a úložiště pro snadnější správu.

Následující článek popisuje, jak nasadit službu synchronizace úložiště. Postupujte pouze v této části dokumentu. V pozdějších krocích budou odkazy na další pododdíly tohoto dokumentu.

[Přečtěte si, jak nasadit službu synchronizace úložiště.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
