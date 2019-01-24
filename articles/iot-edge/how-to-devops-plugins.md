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
ms.openlocfilehash: 016d1c5d389cf1b9e82194e9d273863da1138d2b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830372"
---
# <a name="integrate-azure-iot-edge-with-jenkins-pipelines"></a>Integrace Azure IoT Edge s kanálech Jenkinse

Azure IoT Edge obsahuje integrovanou podporu pro Azure DevOps a Azure DevOps Projects, ale také poskytuje modul plug-in pro rozšíření funkcí DevOps s Jenkinsem. [Jenkins](https://jenkins.io/) je open source automatizační server, který používá moduly plug-in, které podporují mnoho typů projektů vývoje a nasazení, včetně IoT Edge. 

Modul plug-in Azure IoT Edge pro Jenkinse se zaměřuje na průběžné integrace a průběžného nasazování. Můžete vytvořit sestavení a odeslání kanál, který vytvoří moduly a předá jejich imagí kontejneru do registru kontejneru. Vytvořte kanál pro vydávání verzí, který se nasazuje do zařízení IoT Edge moduly. 

Než začnete používat modul plug-in IoT Edge pro Jenkinse, budete potřebovat centra IoT v Azure a registr kontejnerů pro uložení imagí kontejnerů. Pomocí instančního objektu služby Azure můžete udělit oprávnění Přispěvatel Jenkins do služby IoT hub, tak, aby modul plug-in můžete vytvořit nasazení IoT Edge zařízení. 

Pokud jste připravení začít, najít instalaci a podrobnosti o použití [modul plug-in Azure IoT Edge pro Jenkinx](https://plugins.jenkins.io/azure-iot-edge).