---
title: Vývojové prostředí Azure IoT Edge | Dokumenty společnosti Microsoft
description: Seznamte se s podporovanými systémy a nástroji pro vývoj první strany, které vám pomohou vytvořit moduly IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96078be20e8048e481a994fefc169e48ab1d8459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511089"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Příprava vývojového a testovacího prostředí pro IoT Edge

Azure IoT Edge přesune vaši existující obchodní logiku do zařízení pracujících na hraničních zařízeních. Chcete-li připravit aplikace a úlohy pro spuštění jako [moduly IoT Edge](iot-edge-modules.md), je třeba je vytvořit jako kontejnery. Tento článek obsahuje pokyny týkající se konfigurace vývojového prostředí tak, aby bylo možné úspěšně vytvořit řešení IoT Edge. Jakmile budete mít nastavené vývojové prostředí, můžete se naučit, jak [vyvinout vlastní moduly IoT Edge](module-development.md).

V každém řešení IoT Edge existují alespoň dva počítače, které je třeba zvážit. Jedním z nich je samotné zařízení IoT Edge (nebo zařízení), které spouští modul IoT Edge. Druhý je vývojový počítač, který používáte k sestavení, testování a nasazení modulů. Tento článek se zaměřuje především na vývojový stroj. Pro účely testování mohou být oba stroje stejné. IoT Edge můžete spustit ve vývojovém počítači a nasadit do něj moduly.

## <a name="operating-system"></a>Operační systém

Azure IoT Edge běží na konkrétní sadu [podporovaných operačních systémů](support.md). Pro vývoj pro IoT Edge můžete použít většinu operačních systémů, které můžete spustit modul kontejneru. Modul kontejneru je požadavek na vývojovém počítači k sestavení modulů jako kontejnery a jejich nabízení do registru kontejnerů.

Pokud váš vývojový počítač nelze spustit Azure IoT Edge, pokračujte v tomto článku se dozvíte o [testování nástrojů,](#testing-tools) které vám pomohou testovat a ladění místně.

Operační systém vývojového počítače nemusí odpovídat operačnímu systému vašeho zařízení IoT Edge. Operační systém kontejneru však musí být konzistentní mezi vývojovým počítačem a zařízením IoT Edge. Můžete například vyvíjet moduly na počítači se systémem Windows a nasadit je do zařízení s Linuxem. Počítač se systémem Windows potřebuje spustit kontejnery Linux k sestavení modulů pro zařízení Linux.

## <a name="container-engine"></a>Kontejnerový motor

Centrální koncept IoT Edge spoá ceje, že můžete vzdáleně nasadit svou obchodní a cloudovou logiku do zařízení tak, že ji zabalíte do kontejnerů. Chcete-li vytvořit kontejnery, budete potřebovat modul kontejneru na vývojovém počítači.

Jediný podporovaný modul kontejneru pro zařízení IoT Edge ve výrobě je Moby. Všechny kontejnerový modul kompatibilní s iniciativou Open Container Initiative, jako je Docker, však je schopen vytvářet image modulu IoT Edge.

## <a name="development-tools"></a>Vývojářské nástroje

Visual Studio i Visual Studio Code mají rozšíření o doplňky, která pomáhají vyvíjet řešení IoT Edge. Tato rozšíření poskytují šablony specifické pro jazyk, které vám pomohou vytvářet a nasazovat nové scénáře IoT Edge. Rozšíření Azure IoT Edge pro Visual Studio a Visual Studio Code vám pomůžou odepsat, sestavit, nasadit a ladit vaše řešení IoT Edge. Můžete vytvořit celé řešení IoT Edge, které obsahuje více modulů a rozšíření automaticky aktualizují šablonu manifestu nasazení s každým novým přidáním modulu. Pomocí rozšíření můžete také spravovat zařízení IoT z visual studia nebo kódu Sady Visual Studio. Nasazujte moduly do zařízení, sledujte stav a zobrazte zprávy při jejich doručení do služby IoT Hub. Obě rozšíření používají [dev nástroj IoT EdgeHub](#iot-edgehub-dev-tool) k povolení místního spuštění a ladění modulů ve vývojovém počítači.

Pokud dáváte přednost vývoji s jinými editory nebo z rozhraní příkazu cli, azure iot edge dev nástroj poskytuje příkazy, takže můžete vyvíjet a testovat z příkazového řádku. Můžete vytvářet nové scénáře IoT Edge, vytvářet imitace modulů, spouštět moduly v simulátoru a monitorovat zprávy odeslané do ioT hubu.

### <a name="visual-studio-code-extension"></a>Rozšíření editoru Visual Studio Code

Rozšíření Azure IoT Edge pro kód Visual Studia poskytuje šablony modulů IoT Edge postavené na programovacích jazycích, včetně C, C#, Java, Node.js a Pythonu a také funkcí Azure v jazyce C#.

Další informace a stažení najdete v [tématu Nástroje Azure IoT pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Kromě rozšíření IoT Edge může být užitečné nainstalovat další rozšíření pro vývoj. Můžete například použít [podporu Dockeru pro kód Visual Studia](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) ke správě obrázků, kontejnerů a registrů. Kromě toho všechny hlavní podporované jazyky mají rozšíření pro Visual Studio Kód, který může pomoci při vývoji modulů.

#### <a name="prerequisites"></a>Požadavky

Šablony modulu pro některé jazyky a služby mají předpoklady, které jsou nezbytné k vytvoření složek projektu ve vývojovém počítači s visual studio kód.

| Šablona modulu | Požadavek |
| --------------- | ------------ |
| Azure Functions | [Sada SDK .NET Core 2.1](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [Sada SDK .NET Core 2.1](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Vývojová sada 10](https://aka.ms/azure-jdks) <li> [Nastavení proměnné prostředí JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generátor modulů Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Rozšíření Visual Studia 2017/2019

Nástroje Azure IoT Edge pro Visual Studio poskytují šablonu modulu IoT Edge postavenou na C# a C.

Další informace a stažení najdete [v tématu Nástroje Azure IoT Edge pro Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) nebo [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>Nástroj pro dev okraje IoT

Nástroj Azure IoT Edge dev zjednodušuje vývoj ioT edge pomocí schopností příkazového řádku. Tento nástroj poskytuje příkazy rozhraní příkazu příkazy pro vývoj, ladění a testování modulů. Vývojový nástroj IoT Edge pracuje s vaším vývojovým systémem, ať už jste ručně nainstalovali závislosti na vašem počítači nebo používáte kontejner pro vývoj IoT Edge.

Další informace a informace najdete v tématu [Wiki nástrojů IoT Edge dev](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testovací nástroje

Existuje několik testovacích nástrojů, které vám pomohou simulovat zařízení IoT Edge nebo ladit moduly efektivněji. V následující tabulce je uvedeno porovnání na vysoké úrovni mezi nástroji a jednotlivé části popisují jednotlivé nástroje konkrétněji.

Pro produkční nasazení je podporována pouze runtime IoT Edge, ale následující nástroje umožňují simulovat nebo snadno vytvářet zařízení IoT Edge pro účely vývoje a testování. Tyto nástroje se vzájemně nevylučují, ale mohou spolupracovat na kompletním vývojovém prostředí.

| Nástroj | Také známý jako | Podporované platformy | Nejvhodnější pro |
| ---- | ------------- | ------------------- | --------- |
| Nástroj pro dev IoT EdgeHub  | iotedgehubdev | Windows, Linux, MacOS | Simulace zařízení pro ladění modulů. |
| Kontejner pro dev IoT Edge | Microsoft/iotedgedev | Windows, Linux, MacOS | Vývoj bez instalace závislostí. |
| Runtime IoT Edge v kontejneru | iotedgec | Windows, Linux, MacOS, ARM | Testování na zařízení, které nemusí podporovat za běhu. |
| Kontejner zařízení IoT Edge | toolboc/azure-iot-edge-device-container toolboc/azure-iot-edge-device-container toolboc/azure-iot-edge-device-container toolbo | Windows, Linux, MacOS, ARM | Testování scénáře s mnoha zařízeními IoT Edge ve velkém měřítku. |

### <a name="iot-edgehub-dev-tool"></a>Nástroj pro dev IoT EdgeHub

Vývojový nástroj Azure IoT EdgeHub poskytuje prostředí pro místní vývoj a ladění. Nástroj pomáhá spustit moduly IoT Edge bez runtime IoT Edge, takže můžete vytvářet, vyvíjet, testovat, spouštět a ladit moduly a řešení IoT Edge místně. Není potřeba vysunout image do registru kontejnerů a nasadit je do zařízení pro testování.

Nástroj IoT EdgeHub dev byl navržen tak, aby pracoval v tandemu s rozšířeními Visual Studio a Visual Studio Code, stejně jako s nástrojem pro spuštění IoT Edge. Podporuje vývoj vnitřní smyčky, stejně jako testování vnější smyčky, takže se integruje s nástroji DevOps.

Další informace a instalaci najdete v [tématu Azure IoT EdgeHub dev tool](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Kontejner pro dev IoT Edge

Kontejner pro vývoj Azure IoT Edge je kontejner Dockeru, který obsahuje všechny závislosti, které potřebujete pro vývoj IoT Edge. Tento kontejner usnadňuje začít s jazykem, ve kterém chcete vyvíjet, včetně Jazyka C#, Pythonu, Node.js a Javy. Vše, co potřebujete nainstalovat, je modul kontejneru, jako je Docker nebo Moby, chcete-li kontejner vytáhnout do vývojového počítače.

Další informace najdete v [tématu Azure IoT Edge dev kontejneru](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Runtime IoT Edge v kontejneru

Běhový čas IoT Edge v kontejneru poskytuje kompletní runtime, který přebírá připojovací řetězec zařízení jako proměnnou prostředí. Tento kontejner umožňuje testovat moduly a scénáře IoT Edge v systému, který nemusí nativně podporovat modul runtime, jako je MacOS. Všechny moduly, které nasadíte, budou spuštěny mimo kontejner runtime. Pokud chcete, aby modul runtime a všechny nasazené moduly existovaly ve stejném kontejneru, zvažte místo toho kontejner zařízení IoT Edge.

Další informace najdete [v tématu Spuštění Azure IoT Edge v kontejneru](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Kontejner zařízení IoT Edge

Kontejner zařízení IoT Edge je kompletní zařízení IoT Edge, připravené k uvedení na trh na libovolném počítači s kontejnerovým motorem. Kontejner zařízení obsahuje modul runtime IoT Edge a samotný modul kontejneru. Každá instance kontejneru je plně funkční samozřazovací zařízení IoT Edge. Kontejner zařízení podporuje vzdálené ladění modulů, pokud existuje síťová trasa k modulu. Kontejner zařízení je vhodná pro rychlé vytváření velkého počtu zařízení IoT Edge pro testování scénářů ve velkém měřítku nebo Azure Pipelines. Podporuje také nasazení kubernetes přes kormidlo.

Další informace najdete v [tématu kontejner zařízení Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Nástroje DevOps

Až budete připraveni vyvinout řešení ve velkém měřítku pro rozsáhlé produkční scénáře, využijte výhod moderních principů DevOps, včetně automatizace, monitorování a zjednodušených procesů softwarového inženýrství. IoT Edge má rozšíření pro podporu nástrojů DevOps, včetně Azure DevOps, Azure DevOps Projects a Jenkins. Pokud chcete přizpůsobit existující kanál nebo použít jiný nástroj DevOps, jako je CircleCI nebo TravisCI, můžete tak učinit s funkcemi cli zahrnutými v nástroji Pro spuštění ioT edge.

Další informace, pokyny a příklady najdete na následujících stránkách:

* [Průběžná integrace a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md)
* [Vytvoření kanálu CI/CD pro IoT Edge s azure devops projekty](how-to-devops-project.md)
* [Modul plug-in Azure IoT Edge Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [Úložiště GitHub ioT Edge DevOps](https://github.com/toolboc/IoTEdge-DevOps)
