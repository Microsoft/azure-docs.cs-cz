---
title: Nasazení řešení vzdáleného monitorování Java - Azure | Microsoft Docs
description: V tomto kurzu se dozvíte, jak zřídit pomocí rozhraní příkazového řádku akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 603ce00a036822fd0c7411b47cf3fe630671cc5a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628119"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení pomocí rozhraní příkazového řádku akcelerátoru řešení vzdáleného monitorování

V tomto kurzu se dozvíte, jak zřídit akcelerátoru řešení vzdáleného monitorování. Nasazení řešení pomocí rozhraní příkazového řádku. Můžete taky nasadit řešení pomocí rozhraní založené na webu na azureiotsuite.com, další informace o této možnosti najdete [nasazení akcelerátoru řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li nasadit akcelerátoru řešení vzdáleného monitorování, potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

Chcete-li spustit rozhraní příkazového řádku, musíte [Node.js](https://nodejs.org/) nainstalována na místním počítači.

## <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku

Pokud chcete nainstalovat rozhraní příkazového řádku, spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Přihlaste se k rozhraní příkazového řádku

Před nasazením akcelerátoru řešení, musíte se přihlásit k vašemu předplatnému Azure pomocí rozhraní příkazového řádku takto:

```cmd/sh
pcs login
```

Postupujte podle na obrazovce pokyny k dokončení procesu přihlášení.

## <a name="deployment-options"></a>Možnosti nasazení

Když nasadíte akcelerátoru řešení, máte několik možností, které konfigurace procesu nasazení:

| Možnost | Hodnoty | Popis |
| ------ | ------ | ----------- |
| Skladová jednotka (SKU)    | `basic`, `standard`, `local` | A _základní_ nasazení je určený pro testovací a ukázky, nasazování všechny mikroslužeb do jediného virtuálního počítače. A _standardní_ nasazení je určeno v produkčním prostředí se nasadí mikroslužeb do více virtuálních počítačů. A _místní_ nasazení nakonfiguruje kontejner Docker ke spuštění mikroslužeb na místním počítači a používá služby Azure, jako je například úložiště a Cosmos databáze, v cloudu. |
| Modul runtime | `dotnet`, `java` | Vybere jazyk provádění mikroslužeb. |

Další informace o tom, jak použít místní nasazení najdete v tématu [místního spuštění řešení vzdáleného monitorování](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Running-the-Remote-Monitoring-Solution-Locally#deploy-azure-services-and-set-environment-variables).

## <a name="basic-vs-standard-deployments"></a>Základní vs. Standardní nasazení

### <a name="basic"></a>Basic
Základní nasazení je zaměřeno předvádění řešení. Abyste snížili náklady na této ukázce, všechny mikroslužeb nasazených v jeden virtuální počítač; Toto není považováno za architektura produkční prostředí.

Naše standardní nasazení možnost byste měli použít, až budete připraveni k přizpůsobení produkční prostředí architektury, vytvořené pro škálování a rozšíření.

Vytvoření základní řešení, bude mít za následek následující služby Azure se zřídí do vašeho předplatného Azure, náklady na: 

| Počet | Prostředek                       | Typ         | Použít pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuální počítač Linux](https://azure.microsoft.com/services/virtual-machines/) | Standardní D1 V2  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – základní vrstvě | Správa zařízení a komunikace |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat a zařízení telemetrie jako pravidel, výstrahy a zpráv |  
| 1     | [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Úložiště pro virtuální počítač a vysílání datového proudu kontrolní body |
| 1     | [Webové aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |

### <a name="standard"></a>Standard
Standardní nasazení je takové nasazení produkční prostředí vývojář může přizpůsobit a rozšířit podle jejich potřeb. Spolehlivost a škálování aplikace mikroslužeb jsou vytvořené jako kontejnery Docker a nasazují pomocí orchestrator ([Kubernetes](https://kubernetes.io/) ve výchozím nastavení). Orchestrator je zodpovědná za nasazení, škálování a správu aplikace.

Vytváření standardní řešení, bude mít za následek následující služby Azure se zřídí do vašeho předplatného Azure, náklady na:

| Počet | Prostředek                                     | SKU / Size      | Použít pro |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Virtuální počítače s Linuxem](https://azure.microsoft.com/services/virtual-machines/)   | Standardní D2 V2  | hlavní 1 a 3 agentů pro hostování mikroslužeb s nadbytečností |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) orchestrator |
| 1     | [Azure IoT Hub] [https://azure.microsoft.com/services/iot-hub/]                     | S1 – základní vrstvě | Správa zařízení, příkazy a ovládání |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a zařízení telemetrie jako pravidel, výstrahy a zpráv |
| 5     | [Účty úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálního počítače a 1 pro streamování kontrolní body |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Aplikační brána přes protokol SSL |

> Informace o cenách pro tyto služby můžete nalézt [zde](https://azure.microsoft.com/pricing). Využití objemy a fakturační údaje pro vaše předplatné najdete v [portálu Azure](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení řešení akcelerátoru

### <a name="example-deploy-net-version"></a>Příklad: nasazení verze rozhraní .NET

Následující příklad ukazuje, jak nasadit základní, rozhraní .NET verze akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Příklad: nasazení verzi Javy

Následující příklad ukazuje, jak nasadit standard verze Java akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>Parametry příkazu počítače

Při spuštění `pcs` příkazu Nasaďte řešení, budete vyzváni k:

- Název pro vaše řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Umístění.
- Přihlašovací údaje pro virtuální počítače, které hostují mikroslužeb. Tyto přihlašovací údaje můžete použít k přístupu k virtuálním počítačům pro řešení potíží.

Když `pcs` příkaz skončí, zobrazí adresu URL nového nasazení akcelerátoru řešení. `pcs` Příkaz také vytvoří soubor `{deployment-name}-output.json` s další informace, jako je název služby IoT Hub, který byl pro vás zřízený.

Další informace o parametrech příkazového řádku spusťte příkaz:

```cmd/sh
pcs -h
```

Další informace o rozhraní příkazového řádku najdete v tématu [použití rozhraní příkazového řádku](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace řešení akcelerátoru
> * Nasazení řešení akcelerátoru
> * Přihlaste se k řešení akcelerátoru

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumejte možnosti řídicí panel řešení](./iot-accelerators-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->