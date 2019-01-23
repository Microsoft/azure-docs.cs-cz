---
title: Nasazení řešení vzdáleného monitorování pomocí rozhraní příkazového řádku – Azure | Dokumentace Microsoftu
description: Tato příručka ukazuje, jak zřídit pomocí rozhraní příkazového řádku akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/30/2018
ms.topic: conceptual
ms.openlocfilehash: 61da17d1dc59a08128671da163d8b3ea27f83994
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463214"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení akcelerátoru řešení vzdáleného monitorování, který se pomocí rozhraní příkazového řádku

Tato příručka ukazuje, jak nasazení akcelerátoru řešení vzdáleného monitorování. Nasazení řešení pomocí rozhraní příkazového řádku. Můžete také nasadit řešení pomocí uživatelského rozhraní založeného na webu na webu azureiotsuite.com, další informace o této možnosti najdete[nasazení akcelerátoru řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md) rychlý start.

## <a name="prerequisites"></a>Požadavky

Nasazení akcelerátoru řešení vzdáleného monitorování, budete potřebovat aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

Ke spuštění rozhraní příkazového řádku, budete potřebovat [Node.js](https://nodejs.org/) nainstalované na místním počítači.

## <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku

Pokud chcete nainstalovat rozhraní příkazového řádku, spusťte následující příkaz v prostředí příkazového řádku:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Přihlaste se k rozhraní příkazového řádku

Před nasazením akcelerátor řešení, musíte se přihlásit ke svému předplatnému Azure pomocí rozhraní příkazového řádku:

```cmd/sh
pcs login
```

Použijte na obrazovce pokynů a dokončete proces přihlašování.

## <a name="deployment-options"></a>Možnosti nasazení

Při nasazení akcelerátoru řešení, máte několik možností, které konfigurace procesu nasazení:

| Možnost | Hodnoty | Popis |
| ------ | ------ | ----------- |
| Skladová jednotka (SKU)    | `basic`, `standard`, `local` | A _základní_ nasazení je určeno pro testování a předvádění, všechny mikroslužby se nasadí na jeden virtuální počítač. A _standardní_ nasazení je určená pro produkční prostředí, nasazuje mikroslužby do několika virtuálních počítačů. A _místní_ nasazení kontejneru Dockeru pro spuštění mikroslužby v místním počítači konfiguruje a používá Azure cloud services, jako jsou úložiště a Cosmos DB. |
| Modul runtime | `dotnet`, `java` | Vybere implementace jazyka mikroslužeb. |

Zjistěte, jak použít možnost místního nasazení, najdete v článku [místně spuštěná řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Základní a standardní nasazení

Tento oddíl shrnuje hlavní rozdíly mezi základní a standardní nasazení.

### <a name="basic"></a>Basic

Nasazení Basic slouží k předvádění řešení. Pokud chcete snížit náklady, všechny mikroslužby se nasazují v jednom virtuálním počítači. Toto nasazení nepoužívá architektura připravené pro produkční prostředí.

Základní nasazení vytvoří tyto služby ve vašem předplatném Azure:

| Počet | Prostředek                       | Typ         | Používá pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Linux Virtual Machine](https://azure.microsoft.com/services/virtual-machines/) | Standard D1 V2  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – úroveň Standard | Správa zařízení a komunikace |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat, pravidla, oznámení a další studeného úložiště |  
| 1     | [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Úložiště pro virtuální počítač a kontrolní body streamování |
| 1     | [Webové aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení asset umístění |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýzy v reálném čase |
| 1     | [Služby Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro analýzu telemetrie podrobné informace o data a umožňují zprávy |

### <a name="standard"></a>Standard

Standardní nasazení je nasazení připravené pro produkční prostředí, které vývojář může přizpůsobit a rozšířit. Až budete připraveni k přizpůsobení připravené pro produkční prostředí architektury, navržená pro škálování a rozšiřitelnost, použijte možnost nasazení standard. Aplikace mikroslužeb jsou vytvořené jako kontejnery Dockeru a nasazují pomocí služby Azure Kubernetes Service. Orchestrátor Kubernetes nasadí, škálování a spravuje mikroslužby.

Standardní nasazení vytvoří tyto služby ve vašem předplatném Azure:

| Počet | Prostředek                                     | SKU nebo velikosti      | Používá pro |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Použití plně spravovanou službu Orchestrace kontejnerů Kubernetes, výchozí hodnota je 3 agentů|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – úroveň Standard | Správa zařízení, příkazy a ovládání |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a telemetrii zařízení jako jsou pravidla, oznámení a zpráv |
| 5     | [Azure Storage Accounts](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálního počítače a 1 pro streamování kontrolní body |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Služba Application gateway přes protokol SSL |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení asset umístění |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýzy v reálném čase |
| 1     | [Služby Azure Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizování zařízení ve velkém měřítku |
| 1     | [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro analýzu telemetrie podrobné informace o data a umožňují zprávy |

> [!NOTE]
> Můžete najít informace o těchto služeb v cenách [ https://azure.microsoft.com/pricing ](https://azure.microsoft.com/pricing). Využití a fakturace podrobnosti svého předplatného v můžete najít [webu Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení akcelerátoru řešení

Příklady nasazení:

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

Při spuštění `pcs` příkaz pro nasazení řešení, zobrazí se dotaz pro:

- Název pro vaše řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Umístění.
- Přihlašovací údaje pro virtuální počítače, které hostují mikroslužby. Tyto přihlašovací údaje můžete použít k přístupu k virtuálním počítačům pro řešení potíží.

Když `pcs` příkaz dokončí, zobrazí adresu URL nový akcelerátor řešení. `pcs` Příkaz také vytvoří soubor `{deployment-name}-output.json` , který obsahuje informace, jako je název služby IoT Hub, který je vytvořen.

Další informace o parametrech příkazového řádku spusťte:

```cmd/sh
pcs -h
```

Další informace o rozhraní příkazového řádku najdete v tématu [jak používat rozhraní příkazového řádku](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak:

> [!div class="checklist"]
> * Konfigurace akcelerátoru řešení
> * Nasazení akcelerátoru řešení
> * Přihlaste se k akcelerátoru řešení

Teď, když nasadíte řešení vzdáleného monitorování, dalším krokem je [prozkoumat možnosti řídicího panelu řešení](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
