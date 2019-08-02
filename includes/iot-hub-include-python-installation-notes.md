---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640457"
---
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

* **Windows**

    * [Python 2. x nebo 3. x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).

    * Pokud používáte operační systém Windows, ujistěte se, že máte správnou verzi [balíčku Visual C++ Redistributable](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) , aby bylo možné použít nativní knihovny DLL z Pythonu. Doporučujeme použít nejnovější verzi.

    * V případě potřeby nainstalujte balíček [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) pomocí příkazu.`pip install azure-iothub-device-client`

    * V případě potřeby nainstalujte balíček [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) pomocí příkazu`pip install azure-iothub-service-client`

* **Mac OS**

    Pro Mac OS potřebujete Python 3.7.0 (nebo 2,7) + libboost-1,67 + 7.61.1 (vše nainstalované prostřednictvím homebrew). Jakákoli jiná distribuce/operační systém bude pravděpodobně vkládat různé verze zvyšování & závislostí, které nebudou fungovat a výsledkem bude chyba při importu za běhu.

    Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Informace pro Linux a Mac OS najdete v oddílech týkajících se Linux a Mac OS na stránce [Příprava vývojového prostředí pro Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .

> [!NOTE]
> Při importu iothub_client do ukázky došlo k několika sestavám chyb. Další informace o řešení problémů s **Chyba při importu** najdete v článku [řešení problémů s Chyba při importu](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
