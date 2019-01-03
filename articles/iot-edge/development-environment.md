---
title: Azure IoT Edge vývojového prostředí | Dokumentace Microsoftu
description: Přečtěte si o podporovaných systémů a nástroje pro vývoj první strany, které vám pomůžou že vytvářet moduly IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 46b007cfa7156c0c7718b7f4bd4f735a58c722fc
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797927"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Připravte vývojové a testovací prostředí pro IoT Edge

Azure IoT Edge přesouvá stávající obchodní logiky na zařízení provoz na hraničních zařízeních. Příprava vašich aplikací a úloh, aby se spouštěly jako [moduly IoT Edge](iot-edge-modules.md), budete muset sestavit jako kontejnery. Tento článek obsahuje pokyny k jeho konfigurace vývojového prostředí, takže můžete úspěšně vytvořit řešení IoT Edge. Jakmile budete mít nastavení vývojového prostředí, pak můžete získat informace tom, jak [vyvíjet vlastní moduly IoT Edge](module-development.md).

V žádném řešení IoT Edge se minimálně dva počítače, které byste měli zvážit. Jedna je zařízení IoT Edge (nebo zařízení) samostatně, který spustí modul IoT Edge. Druhá je vývojovém počítači, který použijete k sestavení, testování a nasazení modulů. Tento článek se zaměřuje především na vývojovém počítači. Pro účely testování dvě počítače můžou být stejné. Můžete spustit IoT Edge na počítači pro vývoj a nasadíme do ní moduly.

## <a name="operating-system"></a>Operační systém

Azure IoT Edge běží na konkrétní sadu [podporované operační systémy](support.md). Při vývoji pro IoT Edge, můžete použít Většina operačních systémů, které lze spustit modul kontejneru. Modul kontejneru je požadavek na vývojovém počítači k vytvoření své moduly jako kontejnery a vložit je do registru kontejneru. 

Pokud vývojovém počítači nelze spustit Azure IoT Edge, pokračujte v tomto článku se dozvíte o [testovací nástroje](#testing-tools) , které vám pomohou testování a ladění místně. 

Operační systém ze svého vývojového počítače nemusí odpovídat operačnímu systému vašeho zařízení IoT Edge. Operační systém kontejneru však musí být konzistentní vzhledem k aplikacím mezi počítači pro vývoj a zařízení IoT Edge. Můžete například vývoj modulů na počítači s Windows a nasaďte je u zařízení s Linuxem. Počítače Windows potřebuje ke spuštění kontejnerů Linuxu vytvářet moduly pro Linux zařízení. 

## <a name="container-engine"></a>Modul kontejneru

Centrální konceptu IoT Edge je, že můžete logiky podnikání a cloudu vzdáleně nasadit do zařízení balení do kontejnerů. K vytvoření kontejnerů, musíte modul kontejneru na vývojovém počítači. 

Modul kontejneru pouze podporované pro zařízení IoT Edge v produkčním prostředí je Moby. Žádné kompatibilní s Open Initiative kontejneru, jako je Docker, kontejner modulu je však schopna vytváření bitové kopie modulu IoT Edge. 

## <a name="development-tools"></a>Vývojářské nástroje

Visual Studio a Visual Studio Code jsou doplňku rozšíření pro vývoj řešení IoT Edge. Tato rozšíření poskytují specifické pro jazyk šablony vám pomůže vytvořit a nasadit nové scénáře IoT Edge. Rozšíření Azure IoT Edge pro Visual Studio a Visual Studio Code nápovědy kód, vytvořit, nasadit a ladit řešení služby IoT Edge. Můžete vytvořit celé řešení IoT Edge, který obsahuje více modulů a rozšíření automaticky aktualizovat šablonu manifestu nasazení každé přidání nového modulu. Pomocí rozšíření můžete také spravovat zařízení IoT z v rámci sady Visual Studio nebo Visual Studio Code. Do zařízení nasadit moduly, monitorování stavu a zobrazení zprávy při jejich doručení na IoT Hub. Obě rozšíření použijte [IoT EdgeHub vývojového nástroje](#iot-edgehub-dev-tool) umožňující místní spouštění a ladění modulů na svém vývojovém počítači. 

Pokud chcete vyvíjet pomocí jiné editory nebo z rozhraní příkazového řádku, nástroj pro vývojáře Azure IoT Edge poskytuje příkazy, takže můžete vyvíjet a testovat z příkazového řádku. Můžete vytvořit nový IoT Edge scénáře, vytváření bitové kopie modulu, spusťte moduly v simulátoru a sledování zpráv odeslaných do služby IoT Hub. 

### <a name="visual-studio-code-extension"></a>Rozšíření editoru Visual Studio Code

Rozšíření Azure IoT Edge pro Visual Studio Code poskytuje IoT Edge module šablony založená na programovací jazyky, včetně C, C#, Java, Node.js a Python, jakož i služba Azure functions v C#. 

Další informace a ke stažení najdete na stránce [Azure IoT Edge pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Kromě rozšíření IoT Edge možná bude užitečné, chcete-li nainstalovat další rozšíření pro vývoj. Například můžete použít [podporu Dockeru pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) ke správě bitových kopií, kontejnery a registry. Všechny hlavní podporované jazyky kromě toho mají rozšíření pro Visual Studio Code, který vám pomůže, když vyvíjíte moduly. 

#### <a name="prerequisites"></a>Požadavky

Modul šablon pro některé jazyky a služby mají požadavky, které jsou potřebné k vývoji projektu složek na svém vývojovém počítači s Visual Studio Code.

| Šablona modulu | Požadavek |
| --------------- | ------------ |
| Azure Functions | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [.NET core 2.1 SDK](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](https://aka.ms/azure-jdks) <li> [Nastavte proměnnou prostředí JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Generátor modulu Azure IoT Edge Node.js](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [PIP](https://pip.pypa.io/en/stable/installing/#installation) <li> [Cookiecutter](https://cookiecutter.readthedocs.io/en/latest/installation.html) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-2017-extension"></a>Rozšíření sady Visual Studio 2017

Nástroje Azure IoT Edge pro Visual Studio poskytují IoT Edge šablona modulu založená na C#. 

Další informace a ke stažení najdete na stránce [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

### <a name="iot-edge-dev-tool"></a>Nástroj pro vývojáře IoT Edge

Nástroj pro vývojáře Azure IoT Edge zjednodušuje vývoj aplikací IoT Edge pomocí možnosti příkazového řádku. Tento nástroj poskytuje příkazy rozhraní příkazového řádku pro vývoj, ladění a testování moduly. Nástroj pro vývojáře IoT Edge spolupracuje s váš vývojový systém, ať už jste nainstalovali ručně závislosti na vašem počítači nebo používáte kontejneru vývoj IoT Edge. 

Další informace a mohli začít, najdete v části [wiki nástroj pro vývojáře IoT Edge](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Testovací nástroje

Několik testovacích nástrojů existovat pro simulaci zařízení IoT Edge nebo ladění modulů efektivněji. V následující tabulce jsou uvedeny vysoké úrovně srovnání nástroje a potom jednotlivé části popisují každý nástroj přesněji. 

Pro nasazení v produkčním prostředí je podporován pouze modul runtime IoT Edge, ale tyto nástroje umožňují simulaci nebo snadno vytvořit IoT Edge zařízení pro účely vývoje a testování. Tyto nástroje se vzájemně nevylučují, ale můžou spolupracovat kompletní vývojové prostředí. 

| Nástroj | Označuje se taky jako | Podporované platformy | Nejvhodnější pro |
| ---- | ------------- | ------------------- | --------- |
| Nástroj pro vývojáře IoT EdgeHub  | iotedgehubdev | Windows, Linuxu a MacOS | Simulace zařízení za účelem ladění modulů. |
| Kontejner vývoj IoT Edge | Microsoft/iotedgedev | Windows, Linuxu a MacOS | Vývoj bez instalování závislostí. |
| Modul runtime IoT Edge v kontejneru | iotedgec | Windows, Linux, MacOS, ARM | Testování na zařízení, které nemusí podporovat modulu runtime. |
| Kontejner zařízení IoT Edge | toolboc/azure-iot-edge zařízení – container | Windows, Linux, MacOS, ARM | Testuje scénář s mnoha hraniční zařízení IoT ve velkém měřítku. |

### <a name="iot-edgehub-dev-tool"></a>Nástroj pro vývojáře IoT EdgeHub

Azure IoT EdgeHub vývojového nástroje poskytuje místního prostředí pro vývoj a ladění. Nástroj umožňuje spuštění moduly IoT Edge bez modul runtime IoT Edge tak, že můžete vytvořit, vývoj, testování, spouštění a ladění řešení místně a moduly IoT Edge. Není nutné nahrávání imagí do registru kontejneru a nasaďte je u zařízení pro účely testování.

Nástroj pro vývoj IoT EdgeHub byla navržena pro spolupráci v kombinaci s rozšířeními sady Visual Studio a Visual Studio Code, a také pomocí nástroje pro vývoj IoT Edge. Podporuje vývoje vnitřní smyčky, jakož i testování vnější smyčky, proto se příliš integruje pomocí nástrojů DevOps. 

Další informace a nainstalovat, naleznete v tématu [vývojového nástroje Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>Kontejner vývoj IoT Edge

Kontejner vývojáře Azure IoT Edge je kontejner Dockeru, který má všechny závislosti, které potřebujete pro vývoj IoT Edge. Tento kontejner umožňuje snadno a rychle začít s libovolný preferovaný jazyk chcete vyvíjet, včetně C#, Python, Node.js nebo Java. Vše, co potřebujete k instalaci je modul kontejneru, jako je Docker nebo Moby, k vyžádání kontejneru do svého vývojového počítače. 

Další informace najdete v tématu [kontejneru pro vývojáře Azure IoT Edge](https://hub.docker.com/r/microsoft/iotedgedev/).

### <a name="iot-edge-runtime-in-a-container"></a>Modul runtime IoT Edge v kontejneru

Modul runtime IoT Edge v kontejneru poskytuje kompletní modulu runtime, který přebírá připojovací řetězec zařízení jako proměnnou prostředí. Tento kontejner umožňuje testovat moduly IoT Edge a scénáře v systému, který nemusí podporovat nativně, modul runtime jako s MacOS. Spustí se všechny moduly, které nasadíte mimo svůj kontejner modulu runtime. Pokud chcete modul runtime a všechny nasazené moduly, které existují v rámci stejného kontejneru, zvažte kontejneru zařízení IoT Edge.

Další informace najdete v tématu [systémem Azure IoT Edge v kontejneru](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Kontejner zařízení IoT Edge

Kontejner zařízení IoT Edge je kompletní zařízení IoT Edge, jste připravení spustit na jakýkoli počítač s modulem kontejneru. Kontejner zařízení zahrnuje modul runtime IoT Edge a samotný modul kontejneru. Každá instance kontejneru je plně funkční samoobslužné zřizování zařízení IoT Edge. Kontejner zařízení podporuje vzdálené ladění modulů, jako je síťová trasa do modulu. Kontejner zařízení je dobrá pro rychlé vytváření velký počet zařízení IoT Edge k otestování scénářů ve velkém měřítku nebo kanály DevOps. Také podporuje nasazování do kubernetes pomocí helm. 

Další informace najdete v tématu [kontejneru zařízení Azure IoT Edge](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Nástroje DevOps

Jakmile budete připraveni k vývoji řešení ve velkém měřítku pro rozsáhlé produkčních scénářů, využijte moderní DevOps zásady, včetně služby automation, monitorování a procesy zjednodušené softwarového inženýrství. IoT Edge je rozšíření pro podporu nástrojů DevOps, včetně Azure DevOps, projekty Azure DevOps a Jenkinse. Pokud chcete upravit existující kanál nebo použít jiné nástroje DevOps, jako je CircleCI nebo travis ci, udělat s funkcemi rozhraní příkazového řádku zahrnutý v nástroji pro vývoj IoT Edge.

Další informace, pokyny a příklady naleznete na následujících stránkách:
* [Průběžná integrace a průběžné nasazování do Azure IoT Edge](how-to-ci-cd.md)
* [Vytvoření kanálu CI/CD pro IoT Edge s projekty Azure DevOps](how-to-devops-project.md)
* [Modul plug-in Azure IoT Edge Jenkinse](https://plugins.jenkins.io/azure-iot-edge)
* [Úložiště IoT Edge DevOps GitHub](https://github.com/toolboc/IoTEdge-DevOps)
