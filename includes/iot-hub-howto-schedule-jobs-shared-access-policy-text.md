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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "70049043"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-schedule-jobs.md files-->

V tomto článku vytvoříte back-end službu, která naplánuje úlohu na vyvolání přímé metody v zařízení, naplánuje úlohu, aby se zařízení aktualizovala na vlákna, a monitoruje průběh jednotlivých úloh. K provedení těchto operací potřebuje vaše služba oprávnění **ke čtení** a **zápisu do registru** . Ve výchozím nastavení se každé centrum IoT vytvoří se zásadami sdíleného přístupu s názvem **registryReadWrite** , které udělují tato oprávnění.
