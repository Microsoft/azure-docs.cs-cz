---
title: Povolení ci/CD s jenkinsovým pluginem – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Rozšíření Azure IoT Edge pro Jenkins umožňuje integrovat úlohy vývoje a nasazení IoT Edge do vašeho stávajícího řešení DevOps.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 75ed5f5eda48f10776b854ce4de4d2f855a53634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510239"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrace Azure IoT Edge s kanály Jenkinse

Azure IoT Edge má integrovanou podporu pro Azure DevOps a Azure DevOps Projects, ale také poskytuje plugin pro rozšíření funkcí DevOps na Jenkinse. [Jenkins](https://jenkins.io/) je open source automatizační server, který používá pluginy pro podporu mnoha typů vývojových a nasazovacích projektů, včetně IoT Edge.

Plugin Azure IoT Edge pro Jenkins se zaměřuje na průběžnou integraci a průběžné nasazování. Můžete vytvořit sestavení a nabízený kanál, který vytvoří moduly a odešle jejich image kontejneru do registru kontejneru. Potom vytvořte kanál vydání, který nasazuje moduly do vašich zařízení IoT Edge.

Než začnete používat modul plug-in IoT Edge pro Jenkinse, potřebujete v Azure centrum IoT hub a registr kontejnerů, který bude uchovat ibi kontejnerů. Pomocí objektu zabezpečení služeb Azure udělte oprávnění přispěvatele Jenkinse do vašeho centra IoT hub, aby mohl plugin vytvářet nasazení pro vaše zařízení IoT Edge.

Pokud jste připraveni začít, vyhledejte podrobnosti o instalaci a použití [pluginu Azure IoT Edge pro Jenkinse](https://plugins.jenkins.io/azure-iot-edge).
