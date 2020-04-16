---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 64eab53d24a14bb51633be3f979db20963677d93
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421811"
---
Manipulace s komprimovaným zvukem je realizována pomocí [GStreameru](https://gstreamer.freedesktop.org). Z licenčních důvodů gstreamer binární soubory nejsou kompilovány a propojeny s sadou Speech SDK. Vývojáři potřebují nainstalovat několik závislostí a pluginů, viz [Instalace v systému Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c). Binární soubory Gstreamer musí být v systémové cestě, aby sada SDK řeči mohla za běhu načíst binární soubory gstreamer. Pokud řeč SDK je schopen najít libgstreamer-1.0-0.dll během běhu znamená, že gstreamer binární soubory jsou v systémové cestě.

