---
title: Nasazení služby synchronizace úložiště
description: Nasazení cloudového prostředku Azure File Sync. Služba synchronizace úložiště. Běžný textový blok sdílený mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fe74bae95784dcb63a80ee7e280c02de2eadeb5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124720"
---
V tomto kroku potřebujete přihlašovací údaje předplatného Azure.

Základní prostředek pro konfiguraci Azure File Sync se nazývá "Služba synchronizace úložiště".
Doporučujeme nasadit pouze jeden pro všechny servery ve společnosti, které synchronizují stejnou sadu souborů nyní nebo v budoucnu. Synchronizační služby úložiště vytvořte pouze v případě, že máte odlišné sady serverů, které si nikdy nesmí vyměňovat data. (například: synchronizace stejné sdílené složky Azure). V opačném případě je nejvhodnější jedna služba synchronizace úložiště.

Vyberte oblast Azure pro službu synchronizace úložiště, která se nachází v blízkosti vašeho umístění v kanceláři. Všechny ostatní prostředky cloudu musí být nasazeny ve stejné oblasti.
Chcete-li zjednodušit správu, vytvořte ve svém předplatném novou skupinu prostředků, ve které jsou umístěny prostředky synchronizace a úložiště.

Následující článek popisuje, jak nasadit službu synchronizace úložiště. Následujte jen tuhle část doktora. V pozdějších krocích budou odkazy na další podsekce tohoto dokumentu.

[Přečtěte si, jak nasadit službu synchronizace úložiště.](../articles/storage/files/storage-sync-files-deployment-guide.md#deploy-the-storage-sync-service)
