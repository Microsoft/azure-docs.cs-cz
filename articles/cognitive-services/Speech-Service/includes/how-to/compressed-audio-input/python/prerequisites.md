---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: a715d0ece88cf2caf1cb3d20d703f550353094c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282723"
---
Zpracování komprimovaného zvuku je implementováno pomocí [`GStreamer`](https://gstreamer.freedesktop.org) . Z důvodů licencování nejsou `GStreamer` binární soubory kompilovány a propojeny se sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a modulů plug-in, viz [instalace ve Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). `GStreamer` binární soubory musí být v systémové cestě, aby sada Speech SDK mohla načíst binární soubory za běhu. Pokud je sada Speech SDK schopná najít `libgstreamer-1.0-0.dll` během běhu, znamená to, že binární soubory jsou v systémové cestě.

