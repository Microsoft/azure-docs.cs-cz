---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b1287f9c7e946c7b4d035b2ad6301947ffad3cea
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522623"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: návrh pro vysoký výkon

Tento článek poskytuje pokyny pro vytváření aplikací s vysokým výkonem pomocí Azure Premium Storage. Pokyny uvedené v tomto dokumentu můžete použít v kombinaci s osvědčenými postupy pro výkon, které platí pro technologie používané vaší aplikací. Pro ilustraci pokynů jsme použili SQL Server spuštěnou v Premium Storage jako příklad v celém tomto dokumentu.

I když řešíte scénáře výkonu pro vrstvu úložiště v tomto článku, budete muset optimalizovat aplikační vrstvu. Pokud například hostete farmu služby SharePoint v Azure Premium Storage, můžete použít příklady SQL Server z tohoto článku k optimalizaci databázového serveru. Kromě toho Optimalizujte webový server farmy služby SharePoint a aplikační server, abyste získali maximální výkon.

Tento článek vám pomůže zodpovědět následující otázky týkající se optimalizace výkonu aplikace v Azure Premium Storage.

* Jak změřit výkon aplikace?  
* Proč se vám nezobrazuje očekávaný vysoký výkon?  
* Které faktory ovlivňují výkon vaší aplikace na Premium Storage?  
* Jak tyto faktory ovlivňují výkon aplikace na Premium Storage?  
* Jak se dá optimalizovat pro IOPS, šířku pásma a latenci?  

Tyto pokyny poskytujeme konkrétně pro Premium Storage, protože úlohy spuštěné v Premium Storage jsou vysoce výkonná. V případě potřeby jsme zadali příklady. Některé z těchto pokynů můžete také použít pro aplikace běžící na virtuálních počítačích s IaaS se standardními disky úložiště.