---
title: Povolit CI/CD pomocí Jenkins – modul plug-in Azure IoT Edge | Dokumentace Microsoftu
description: Rozšíření Azure IoT Edge pro Jenkinse umožňuje integrovat do stávajícího řešení pro DevOps úlohy devlopment a nasazení IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 173e6ff91acd2ad28d7203b2b5db65e0ee0ecc43
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910824"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrace Azure IoT Edge s kanálech Jenkinse

Azure IoT Edge obsahuje integrovanou podporu pro Azure DevOps a Azure DevOps Projects, ale také poskytuje modul plug-in pro rozšíření funkcí DevOps s Jenkinsem. [Jenkins](https://jenkins.io/) je open source automatizační server, který používá moduly plug-in, které podporují mnoho typů projektů vývoje a nasazení, včetně IoT Edge. 

Modul plug-in Azure IoT Edge pro Jenkinse se zaměřuje na průběžné integrace a průběžného nasazování. Můžete vytvořit sestavení a odeslání kanál, který vytvoří moduly a předá jejich imagí kontejneru do registru kontejneru. Vytvořte kanál pro vydávání verzí, který se nasazuje do zařízení IoT Edge moduly. 

Než začnete používat modul plug-in IoT Edge pro Jenkinse, budete potřebovat centra IoT v Azure a registr kontejnerů pro uložení imagí kontejnerů. Pomocí instančního objektu služby Azure můžete udělit oprávnění Přispěvatel Jenkins do služby IoT hub, tak, aby modul plug-in můžete vytvořit nasazení IoT Edge zařízení. 

Pokud jste připravení začít, najít instalaci a podrobnosti o použití [modul plug-in Azure IoT Edge pro Jenkinse](https://plugins.jenkins.io/azure-iot-edge).