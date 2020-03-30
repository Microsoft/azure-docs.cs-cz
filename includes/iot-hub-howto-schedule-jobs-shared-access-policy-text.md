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
ms.openlocfilehash: e493d1c4f5851ee510ea83e706afce5fbb6f487e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70049043"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

V tomto článku vytvoříte back-endovou službu, která naplánuje úlohu k vyvolání přímé metody na zařízení, naplánuje úlohu k aktualizaci dvojčete zařízení a sleduje průběh každé úlohy. K provedení těchto operací potřebuje vaše služba oprávnění ke čtení a **zápisu** **registru.** Ve výchozím nastavení je každé centrum IoT vytvořeno pomocí zásady sdíleného přístupu s názvem **registryReadWrite,** která uděluje tato oprávnění.
