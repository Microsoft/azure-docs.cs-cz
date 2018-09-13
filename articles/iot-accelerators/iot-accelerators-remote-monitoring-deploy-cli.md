---
title: Nasazení řešení vzdáleného monitorování Javy – Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak zřídit pomocí rozhraní příkazového řádku akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/12/2018
ms.topic: conceptual
ms.openlocfilehash: 56f233afed8c403d19c9b668e98ecfec45470b64
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44721615"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení akcelerátoru řešení vzdáleného monitorování, který se pomocí rozhraní příkazového řádku

V tomto kurzu se dozvíte, jak zřídit akcelerátoru řešení vzdáleného monitorování. Nasazení řešení pomocí rozhraní příkazového řádku. Můžete také nasadit řešení pomocí uživatelského rozhraní založeného na webu na webu azureiotsuite.com, další informace o této možnosti najdete v tématu [nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md).

## <a name="prerequisites"></a>Požadavky

Nasazení akcelerátoru řešení vzdáleného monitorování, budete potřebovat aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](http://azure.microsoft.com/pricing/free-trial/).

Ke spuštění rozhraní příkazového řádku, budete potřebovat [Node.js](https://nodejs.org/) nainstalované na místním počítači.

## <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku

Pokud chcete nainstalovat rozhraní příkazového řádku, spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Přihlaste se k rozhraní příkazového řádku

Před nasazením akcelerátor řešení, musíte se přihlásit ke svému předplatnému Azure pomocí rozhraní příkazového řádku takto:

```cmd/sh
pcs login
```

Použijte na obrazovce pokynů a dokončete proces přihlašování.

## <a name="deployment-options"></a>Možnosti nasazení

Při nasazení akcelerátoru řešení, máte několik možností, které konfigurace procesu nasazení:

| Možnost | Hodnoty | Popis |
| ------ | ------ | ----------- |
| Skladová jednotka (SKU)    | `basic`, `standard`, `local` | A _základní_ nasazení je určeno pro testování a předvádění, všechny mikroslužby se nasadí na jeden virtuální počítač. A _standardní_ nasazení je určená pro produkční prostředí, nasazuje mikroslužby do několika virtuálních počítačů. A _místní_ nasazení kontejneru Dockeru pro spuštění mikroslužby v místním počítači konfiguruje a používá služby Azure, jako jsou úložiště a Cosmos DB v cloudu. |
| Modul runtime | `dotnet`, `java` | Vybere implementace jazyka mikroslužeb. |

Další informace o tom, jak používat místní nasazení najdete v tématu [místně spuštěná řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-vs-standard-deployments"></a>Základní vs. Standardní nasazení

### <a name="basic"></a>Basic
Základní nasazení určeném připraveném řešení. Pokud chcete snížit náklady na této ukázce, všechny mikroslužby se nasazují v jedné virtuální počítač. To se nepovažuje za architekturu připravené pro produkční prostředí.

Naše možnost nasazení Standard by měl použít, když budete chtít přizpůsobit připravené pro produkční prostředí architektury, navržená pro škálování a rozšiřitelnost.

Vytvoření základní řešení bude znamenat, že v následujících služeb Azure, zřídí do vašeho předplatného Azure, poplatků: 

| Počet | Prostředek                       | Typ         | Používá pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuální počítač s Linuxem](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – úroveň Standard | Správa zařízení a komunikace |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat, pravidla, alarmy a dalších studeného úložiště |  
| 1     | [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Úložiště pro virtuální počítač a kontrolní body streamování |
| 1     | [Webové aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení asset umístění |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýzy v reálném čase |
| 1     | [Služby Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro analýzu telemetrie podrobné informace o data a umožňují zprávy |



### <a name="standard"></a>Standard
Standardní nasazení je nasazení připravené pro produkční prostředí vývojář může přizpůsobit a rozšířit podle svých potřeb. Spolehlivost a škálování, mikroslužby aplikací jsou vytvořené jako kontejnery Dockeru a nasazují pomocí orchestrátoru ([Kubernetes](https://kubernetes.io/) ve výchozím nastavení). Orchestrátor zodpovídá za nasazení, škálování a Správa aplikace.

Vytvoření standardní řešení bude znamenat, že v následujících služeb Azure, zřídí do vašeho předplatného Azure, poplatků:

| Počet | Prostředek                                     | SKU nebo velikosti      | Používá pro |
|-------|----------------------------------------------|-----------------|----------|
| 4     | [Virtuální počítače s Linuxem](https://azure.microsoft.com/services/virtual-machines/)   | Standard D2 V2  | hlavním uzlem 1 a 3 agenty pro hostování mikroslužeb s redundancí |
| 1     | [Azure Container Service](https://azure.microsoft.com/services/container-service/) |                 | [Kubernetes](https://kubernetes.io) nástroje orchestrator |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – úroveň Standard | Správa zařízení, příkazy a ovládání |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a telemetrii zařízení jako jsou pravidla, varování a zprávy |
| 5     | [Účty služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálního počítače a 1 pro streamování kontrolní body |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Služba Application gateway přes protokol SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení asset umístění |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýzy v reálném čase |
| 1     | [Služby Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro analýzu telemetrie podrobné informace o data a umožňují zprávy |

> Informace o cenách pro tyto služby můžete najít [tady](https://azure.microsoft.com/pricing). Množství využití a fakturace podrobnosti svého předplatného najdete v [webu Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení akcelerátoru řešení

### <a name="example-deploy-net-version"></a>Příklad: nasazení verze rozhraní .NET

Následující příklad ukazuje, jak nasadit základní, verze .NET akcelerátor řešení vzdálené monitorování:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Příklad: nasazení verze Javy

Následující příklad ukazuje, jak nasadit standard, verze Javy akcelerátor řešení vzdálené monitorování:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>možnosti příkazu počítače

Při spuštění `pcs` příkaz pro nasazení řešení, budete vyzváni k zadání:

- Název pro vaše řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Umístění.
- Přihlašovací údaje pro virtuální počítače, které hostují mikroslužby. Tyto přihlašovací údaje můžete použít k přístupu k virtuálním počítačům pro řešení potíží.

Když `pcs` příkaz dokončí, zobrazí adresu URL nové nasazení akcelerátoru řešení. `pcs` Příkaz také vytvoří soubor `{deployment-name}-output.json` společně s dalšími informacemi, jako je název služby IoT Hub, která byla zřízena za vás.

Další informace o parametrech příkazového řádku spusťte:

```cmd/sh
pcs -h
```

Další informace o rozhraní příkazového řádku najdete v tématu [jak používat rozhraní příkazového řádku](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Konfigurace akcelerátoru řešení
> * Nasazení akcelerátoru řešení
> * Přihlaste se k akcelerátoru řešení

Teď, když jste nasadili řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](./quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
