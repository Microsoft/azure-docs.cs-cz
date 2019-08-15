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
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912328"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

V tomto článku vytvoříte službu back-end, která do vlákna zařízení přidá požadované vlastnosti, a pak se dotazuje registru identity, aby se vyhledala všechna zařízení s oznámenými vlastnostmi, která se odpovídajícím způsobem aktualizovala. Vaše služba potřebuje oprávnění **připojit službu** pro úpravu požadovaných vlastností vlákna zařízení a potřebuje oprávnění **ke čtení registru** pro dotazování registru identity. Nejsou k dispozici žádné výchozí zásady sdíleného přístupu, které obsahují pouze tato dvě oprávnění, takže je třeba ji vytvořit.
