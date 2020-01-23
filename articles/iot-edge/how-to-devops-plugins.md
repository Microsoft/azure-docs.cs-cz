---
title: Povolit CI/CD pomocí modulu plug-in Jenkinse-Azure IoT Edge | Microsoft Docs
description: Rozšíření Azure IoT Edge pro Jenkinse umožňuje integrovat úlohy vývoje a nasazení IoT Edge do stávajícího řešení DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510239"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrace Azure IoT Edge s kanály Jenkinse

Azure IoT Edge obsahuje integrovanou podporu pro Azure DevOps a Azure DevOps Projects, ale také poskytuje modul plug-in pro rozšíření funkce DevOps na Jenkinse. [Jenkinse](https://jenkins.io/) je open source automatizační server, který používá moduly plug-in k podpoře mnoha typů projektů vývoje a nasazení, včetně IoT Edge.

Modul plug-in Azure IoT Edge pro Jenkinse se zaměřuje na průběžnou integraci a průběžné nasazování. Můžete vytvořit sestavení a kanál nabízených oznámení, který sestaví moduly a vloží jejich image kontejneru do registru kontejneru. Pak vytvořte kanál pro vydávání verzí, který nasadí moduly do zařízení IoT Edge.

Než začnete používat modul plug-in IoT Edge pro Jenkinse, budete potřebovat IoT Hub v Azure a registr kontejnerů pro ukládání imagí kontejneru. Pomocí instančního objektu Azure udělte Jenkinse oprávnění přispěvatelům ke službě IoT Hub, aby modul plug-in mohl vytvářet nasazení pro zařízení IoT Edge.

Pokud jste připraveni začít, najděte instalaci a použijte podrobnosti pro [modul plug-in Azure IoT Edge pro Jenkinse](https://plugins.jenkins.io/azure-iot-edge).
