---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/17/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: f97ab0a0bb91af90daaf60c5c2c85f0ede56a5e7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403955"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

V tomto článku vytvoříte službu back-end, která do vlákna zařízení přidá požadované vlastnosti, a pak se dotazuje registru identity, aby se vyhledala všechna zařízení s oznámenými vlastnostmi, která se odpovídajícím způsobem aktualizovala. Vaše služba potřebuje oprávnění **připojit službu** pro úpravu požadovaných vlastností vlákna zařízení a potřebuje oprávnění **ke čtení registru** pro dotazování registru identity. Nejsou k dispozici žádné výchozí zásady sdíleného přístupu, které obsahují pouze tato dvě oprávnění, takže je třeba ji vytvořit.
