---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 12/05/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: b1465b9bf855d592d195277b616c860a23c179f6
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504674"
---
| SKU brány | Připojení za sekundu | Počet toků | Megabajtů-bity za sekundu | Pakety za sekundu | Šířka pásma okruhu | Počet tras inzerovaných bránou (do MSEE) | Počet tras, které brána získala (z MSEE) | Počet virtuálních počítačů ve virtuální síti |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Základní SKU (zastaralé)** | N/A | N/A | 500 | N/A | N/A | N/A | N/A | N/A |
| **SKU/ErGw1AZ úrovně Standard** | 7 000 | 400 000 | >1 000 | >100 000 | 1 Gb/s |  500 | 4 000 | 2 000 (během údržby se zmenší na 1 000, obnoví se později) | 
| **SKU s vysokým výkonem/ErGw2AZ** | 14 000 | 840 000 | >2 000 | 250 000 | 1 Gb/s | 500 | ~ 9 500 (Snižte na 4 000, pokud jsou ve virtuální síti víc než 6 500 virtuálních počítačů.) | 4 500 |
| **Ultra Performance SKU/ErGw3AZ** | 16 000 | 950 000 | ~ 10 000 | 1 000 000 | 1 Gb/s | 500 | ~ 9 500 | 11 000 |
