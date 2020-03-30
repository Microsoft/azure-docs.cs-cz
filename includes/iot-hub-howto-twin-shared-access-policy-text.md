---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e81ce2743d2509ce52f3190218decfa4e518bdad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050451"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

V tomto článku vytvoříte službu back-end, která přidá požadované vlastnosti dvojčete zařízení a poté se dotazuje registru identit, aby našel všechna zařízení s ohlášenými vlastnostmi, která byla odpovídajícím způsobem aktualizována. Vaše služba potřebuje oprávnění **služby připojit** k úpravě požadovaných vlastností dvojčete zařízení a potřebuje oprávnění **ke čtení registru** k dotazování registru identit. Neexistuje žádná výchozí zásada sdíleného přístupu, která obsahuje pouze tato dvě oprávnění, takže je třeba ji vytvořit.
