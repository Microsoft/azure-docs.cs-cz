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
ms.openlocfilehash: 47d46cf4b400e29de8c526f750a10f57d6400220
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402665"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

V tomto článku vytvoříte back-end službu, která naplánuje úlohu k vyvolání přímé metody v zařízení, naplánuje úlohu k aktualizaci vlákna na zařízení a monitoruje průběh jednotlivých úloh. K provedení těchto operací potřebuje vaše služba oprávnění **ke čtení** a **zápisu do registru** . Ve výchozím nastavení se všechny IoT Hub vytvoří se zásadami sdíleného přístupu s názvem **registryReadWrite** , které udělují tato oprávnění.
