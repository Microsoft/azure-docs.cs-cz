---
title: Azure IoT Edge vývojové prostředí | Microsoft Docs
description: Přečtěte si o podporovaných systémech a vývojářských nástrojích pro první stranu, které vám pomůžou vytvořit IoT Edge moduly.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bee1f95e1f56b65912895fec8af2512c6cd7b85a
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300370"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Příprava vývojového a testovacího prostředí pro IoT Edge

Azure IoT Edge přesouvá stávající obchodní logiku do zařízení, která pracují na hraničních zařízeních. Chcete-li připravit aplikace a úlohy tak, aby běžely jako [IoT Edge moduly](iot-edge-modules.md), je nutné je vytvořit jako kontejnery. Tento článek poskytuje pokyny k tomu, jak nakonfigurovat vývojové prostředí, abyste mohli úspěšně vytvořit IoT Edge řešení. Po nastavení vývojového prostředí můžete zjistit, jak [vyvíjet vlastní moduly IoT Edge](module-development.md).

V jakémkoli řešení IoT Edge existují aspoň dva počítače, které je potřeba vzít v úvahu. Jedním z nich je IoT Edge zařízení (nebo zařízení), ve kterém se spouští modul IoT Edge. Druhá je vývojový počítač, který používáte k sestavování, testování a nasazování modulů. Tento článek se zaměřuje hlavně na vývojovém počítači. Pro účely testování můžou být tyto dva počítače stejné. Můžete spustit IoT Edge na svém vývojovém počítači a nasadit do něj moduly.

## <a name="operating-system"></a>Operační systém

Azure IoT Edge běží na konkrétní sadě [podporovaných operačních systémů](support.md). Pro vývoj pro IoT Edge můžete použít většinu operačních systémů, které mohou spustit kontejnerový modul. Kontejnerový modul je požadavkem na vývojovém počítači pro sestavení modulů jako kontejnerů a jejich vložení do registru kontejneru.

Pokud váš vývojový počítač nemůže spustit Azure IoT Edge, pokračujte v tomto článku, abyste se seznámili s testovacími [nástroji](#testing-tools) , které vám pomůžou místní testování a ladění.

Operační systém vašeho vývojového počítače nemusí odpovídat operačnímu systému vašeho zařízení IoT Edge. Operační systém kontejneru ale musí být konzistentní mezi vývojovým počítačem a zařízením IoT Edge. Můžete například vyvíjet moduly na počítači s Windows a nasazovat je na zařízení se systémem Linux. Počítač s Windows potřebuje ke sestavení modulů pro zařízení se systémem Linux spustit kontejnery Linux.

## <a name="container-engine"></a>Modul kontejneru

Centrálním konceptem IoT Edge je, že můžete do zařízení vzdáleně nasadit svou obchodní a cloudovou logiku, a to tak, že je zabalíte do kontejnerů. Chcete-li vytvořit kontejnery, budete potřebovat kontejnerový modul na svém vývojovém počítači.

Jediným podporovaným kontejnerovým modulem pro IoT Edgeá zařízení v produkčním prostředí je Moby. Nicméně jakýkoli modul kontejneru, který je kompatibilní s iniciativou Open container, jako je Docker, dokáže sestavovat image IoT Edge modulů.

## <a name="development-tools"></a>Vývojářské nástroje

Visual Studio i Visual Studio Code mají rozšíření doplňku, které vám pomůžou vyvíjet IoT Edge řešení. Tato rozšíření poskytují šablony specifické pro jazyk, které vám pomůžou vytvářet a nasazovat nové scénáře IoT Edge. Rozšíření Azure IoT Edge pro Visual Studio a Visual Studio Code vám pomůžou při kódování, sestavování, nasazování a ladění řešení IoT Edge. Můžete vytvořit celé IoT Edge řešení, které obsahuje více modulů, a rozšíření automaticky aktualizuje šablonu manifestu nasazení se všemi novými moduly. S rozšířeními můžete také spravovat zařízení IoT z aplikace Visual Studio nebo Visual Studio Code. Nasaďte moduly do zařízení, Sledujte stav a zobrazte zprávy při jejich doručování IoT Hub. Obě rozšíření používají [Nástroj IoT EdgeHub dev](#iot-edgehub-dev-tool) , který umožňuje také místní spuštění a ladění modulů ve vývojovém počítači.

Pokud dáváte přednost vývoji s jinými editory nebo rozhraním příkazového řádku, poskytuje nástroj Azure IoT Edge dev příkazy, abyste mohli vyvíjet a testovat z příkazového řádku. Můžete vytvářet nové scénáře IoT Edge, vytvářet bitové kopie modulu, spouštět moduly v simulátoru a monitorovat zprávy odesílané do IoT Hub.

### <a name="visual-studio-code-extension"></a>Rozšíření editoru Visual Studio Code

Rozšíření Azure IoT Edge pro Visual Studio Code poskytuje IoT Edge šablony modulů založené na programovacích jazycích, včetně jazyků C, C#, Java, Node.js a Pythonu, a také funkcí Azure Functions v jazyce C#.

Další informace a jejich stažení najdete v tématu [Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Kromě rozšíření IoT Edge může být užitečné nainstalovat další rozšíření pro vývoj. Můžete například použít [podporu Docker pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) ke správě imagí, kontejnerů a registrů. Všechny hlavní podporované jazyky navíc mají rozšíření pro Visual Studio Code, která můžou pomáhat při vývoji modulů.

#### <a name="prerequisites"></a>Požadavky

Šablony modulů pro některé jazyky a služby mají požadavky, které jsou nezbytné pro sestavení složek projektu na vývojovém počítači pomocí Visual Studio Code.

| Šablona modulu | Požadavek |
| --------------- | ------------ |
| Azure Functions | [Sada .NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [Sada .NET Core 2,1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Nastavit proměnnou prostředí JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generátor modulu Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Rozšíření sady Visual Studio 2017/2019

Azure IoT Edge nástroje pro Visual Studio poskytují šablonu IoT Edge modulu postavenou na C# a C.

Další informace a jejich stažení naleznete v tématu [Azure IoT Edge Tools for Visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) nebo [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Nástroj pro vývoj IoT Edge

Nástroj Azure IoT Edge dev zjednodušuje vývoj IoT Edge pomocí možností příkazového řádku. Tento nástroj poskytuje příkazy rozhraní příkazového řádku pro vývoj, ladění a testování modulů. Nástroj pro IoT Edge vývoj funguje s vaším vývojovým systémem bez ohledu na to, zda jste ručně nainstalovali závislosti na vašem počítači nebo používáte IoT Edge vývojového kontejneru.

Další informace a informace o tom, jak začít, najdete v tématu [IoT Edge wiki nástrojů pro vývoj](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testovací nástroje

Existuje několik testovacích nástrojů, které vám pomůžou simulovat IoT Edge zařízení nebo efektivněji ladit moduly. V následující tabulce je uvedeno porovnání na nejvyšší úrovni mezi nástroji a potom jednotlivé části popisují jednotlivé nástroje přesněji.

Pro produkční nasazení se podporuje jenom modul runtime IoT Edge, ale následující nástroje umožňují simulovat nebo snadno vytvářet IoT Edge zařízení pro účely vývoje a testování. Tyto nástroje se vzájemně nevylučují, ale můžou spolupracovat na kompletním prostředí pro vývoj.

| Nástroj | Označuje se také jako | Podporované platformy | Nejvhodnější pro |
| ---- | ------------- | ------------------- | --------- |
| Nástroj pro vývoj IoT EdgeHub  | iotedgehubdev | Windows, Linux, macOS | Simulace zařízení pro moduly ladění. |
| IoT Edge kontejner pro vývoj | Microsoft/iotedgedev | Windows, Linux, macOS | Vývoj bez instalace závislostí. |
| IoT Edge modul runtime v kontejneru | iotedgec | Windows, Linux, macOS, ARM | Testování v zařízení, které nemusí podporovat modul runtime. |
| IoT Edge kontejner zařízení | toolboc/Azure – IoT-Edge-Device-Container | Windows, Linux, macOS, ARM | Testování scénáře s velkým množstvím IoT Edge zařízení ve velkém měřítku. |

### <a name="iot-edgehub-dev-tool"></a>Nástroj pro vývoj IoT EdgeHub

Nástroj pro vývoj v Azure IoT EdgeHub poskytuje místní vývojové a ladicí prostředí. Nástroj pomáhá spustit IoT Edge moduly bez modulu runtime IoT Edge, takže můžete vytvářet, vyvíjet, testovat, spouštět a ladit IoT Edge moduly a řešení místně. Nemusíte vkládat image do registru kontejnerů a nasazovat je do zařízení pro testování.

Vývojářský nástroj IoT EdgeHub byl navržený tak, aby fungoval společně s rozšířeními sady Visual Studio a Visual Studio Code a také s nástrojem IoT Edge dev. Podporuje vývoj vnitřních smyček i testování vnějších smyček, takže se integruje i s nástroji DevOps.

Další informace a pokyny k instalaci najdete v tématu [Azure IoT EdgeHub dev Tool](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge kontejner pro vývoj

Kontejner pro vývoj Azure IoT Edge je kontejner Docker, který obsahuje všechny závislosti, které potřebujete pro vývoj IoT Edge. Tento kontejner usnadňuje začátek s libovolným jazykem, ve kterém chcete vyvíjet, včetně jazyků C#, Python, Node.js a Java. Vše, co je potřeba nainstalovat, je kontejnerový modul, jako je Docker nebo Moby, který načte kontejner do vývojového počítače.

Další informace najdete v tématu [Azure IoT Edge vývojového kontejneru](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge modul runtime v kontejneru

Modul runtime IoT Edge v kontejneru poskytuje úplný modul runtime, který jako proměnnou prostředí převezme řetězec připojení zařízení. Tento kontejner umožňuje testovat IoT Edge moduly a scénáře v systému, který nemusí nativně podporovat modul runtime, jako je macOS. Všechny moduly, které nasadíte, se spustí mimo kontejner modulu runtime. Pokud chcete, aby modul runtime a všechny nasazené moduly existovaly ve stejném kontejneru, zvažte místo toho IoT Edge kontejner zařízení.

Další informace najdete v tématu [spuštění Azure IoT Edge v kontejneru](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>IoT Edge kontejner zařízení

IoT Edge kontejner zařízení je kompletní IoT Edge zařízení, které se dá spustit na jakémkoli počítači s modulem kontejneru. Kontejner zařízení zahrnuje IoT Edge modulu runtime a samotného modulu kontejneru. Každá instance kontejneru je plně funkční zařízení IoT Edge pro samoobslužné zřizování. Kontejner zařízení podporuje vzdálené ladění modulů, pokud existuje síťová trasa k modulu. Kontejner zařízení je vhodný pro rychlé vytvoření velkého počtu zařízení IoT Edge pro testování scénářů nebo Azure Pipelines. Podporuje také nasazení do Kubernetes prostřednictvím Helm.

Další informace najdete v tématu [Azure IoT Edge kontejneru zařízení](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Nástroje DevOps

Až budete připraveni vyvíjet řešení v rozsáhlých produkčních scénářích, využijte výhod moderních DevOpsch principů, včetně automatizace, monitorování a zefektivnění procesů softwarových inženýrů. IoT Edge má rozšíření pro podporu nástrojů DevOps, včetně Azure DevOps, Azure DevOps Projects a Jenkinse. Pokud chcete přizpůsobit existující kanál nebo použít jiný nástroj DevOps jako CircleCI nebo Travis CI, můžete to udělat pomocí funkcí CLI, které jsou součástí nástroje IoT Edge dev Tool.

Další informace, doprovodné materiály a příklady najdete na následujících stránkách:

* [Průběžná integrace a průběžné nasazování pro Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Vytvoření kanálu CI/CD pro IoT Edge s využitím Azure DevOps Starter](how-to-devops-starter.md)
* [Modul plug-in Azure IoT Edge Jenkinse](https://plugins.jenkins.io/azure-iot-edge)
* [Úložiště GitHub IoT Edge DevOps](https://github.com/toolboc/IoTEdge-DevOps)
