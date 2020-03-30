---
title: Nasazení řešení vzdáleného monitorování pomocí příkazového příkazového příkazu – Azure | Dokumenty společnosti Microsoft
description: Tento návod ukazuje, jak zřídit akcelerátor řešení vzdáleného monitorování pomocí zaokreslovacího modulu.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 501ca51a9542229a14e98a56679837950a82891e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258290"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-using-the-cli"></a>Nasazení akcelerátoru řešení vzdáleného monitorování pomocí funkce vuživatelskémi mise

Tento návod ukazuje, jak nasadit akcelerátor řešení vzdáleného monitorování. Nasadit řešení pomocí cli. Můžete také nasadit řešení pomocí webového uživatelského uživatelského nastavení na azureiotsolutions.com, abyste se dozvěděli o této možnosti, podívejte se na rychlý start [nasadit akcelerátor řešení vzdáleného monitorování.](quickstart-remote-monitoring-deploy.md)

## <a name="prerequisites"></a>Požadavky

K nasazení akcelerátoru řešení vzdáleného monitorování potřebujete aktivní předplatné Azure.

Pokud nemáte účet, můžete si během několika minut vytvořit bezplatný účet zkušební. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

Chcete-li spustit nařízení o nastavení chování uživatele, potřebujete soubor [Node.js](https://nodejs.org/) nainstalovaný v místním počítači.

## <a name="install-the-cli"></a>Instalace rozhraní příkazového řádku

Chcete-li nainstalovat příkaz příkaz příkazového řádku, spusťte v prostředí příkazového řádku následující příkaz:

```cmd/sh
npm install iot-solutions -g
```

## <a name="sign-in-to-the-cli"></a>Přihlášení k zaepisování

Než budete moci nasadit akcelerátor řešení, musíte se přihlásit k předplatnému Azure pomocí příkazového příkazového příkazu:

```cmd/sh
pcs login
```

Dokončete proces přihlášení podle pokynů na obrazovce.

## <a name="deployment-options"></a>Možnosti nasazení

Při nasazení akcelerátoru řešení existuje několik možností, které konfigurují proces nasazení:

| Možnost | Hodnoty | Popis |
| ------ | ------ | ----------- |
| Skladová jednotka (SKU)    | `basic`, `standard`, `local` | _Základní_ nasazení je určeno pro testování a ukázky, nasazuje všechny mikroslužby do jednoho virtuálního počítače. _Standardní_ nasazení je určeno pro produkční prostředí, nasazuje mikroslužby do několika virtuálních počítačů. _Místní_ nasazení konfiguruje kontejner Dockeru pro spuštění mikroslužeb v místním počítači a používá cloudové služby Azure, jako je úložiště a Cosmos DB. |
| Modul runtime | `dotnet`, `java` | Vybere implementaci jazyka mikroslužeb. |

Informace o použití možnosti místního nasazení naleznete [v tématu Spuštění řešení vzdáleného monitorování místně](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="basic-and-standard-deployments"></a>Základní a standardní nasazení

Tato část shrnuje klíčové rozdíly mezi základním a standardním nasazením.

### <a name="basic"></a>Basic

Můžete provést základní nasazení z [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators) nebo pomocí příkazového příkazu k příkazu.

Základní nasazení je zaměřeno na představení řešení. Chcete-li snížit náklady, všechny mikroslužby jsou nasazeny v jednom virtuálním počítači. Toto nasazení nepoužívá architekturu připravenou pro produkční prostředí.

Základní nasazení vytvoří následující služby ve vašem předplatném Azure:

| Počet | Prostředek                       | Typ         | Používá se pro |
|-------|--------------------------------|--------------|----------|
| 1     | [Virtuální počítač s Linuxem](https://azure.microsoft.com/services/virtual-machines/) | Standardní D1 V2  | Hostování mikroslužeb |
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                  | S1 – standardní úroveň | Správa zařízení a komunikace |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)              | Standard        | Ukládání konfiguračních dat, pravidel, výstrah a dalších chladírenských úložišť |  
| 1     | [Účet úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)  | Standard        | Úložiště pro virtuální ho svícen a kontrolní body streamování |
| 1     | [Webová aplikace](https://azure.microsoft.com/services/app-service/web/)        |                 | Hostování front-endové webové aplikace |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění datových zdrojů |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba zřizování zařízení Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizovací zařízení ve velkém měřítku |
| 1     | [Přehledy Azure Time Series](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožňuje hloubkovou telemetricko analýzu |

### <a name="standard"></a>Standard

Standardní nasazení můžete provést pouze pomocí zaokreslovacího systému.

Standardní nasazení je nasazení připravené k produkčnímu prostředí, které může vývojář přizpůsobit a rozšířit. Možnost standardního nasazení použijte, když jste připraveni přizpůsobit architekturu připravenou pro produkční prostředí, která je vytvořena pro škálování a rozšiřitelnost. Mikroslužby aplikací se nastavují jako kontejnery Dockeru a nasazují se pomocí služby Azure Kubernetes Service. Orchestrátor Kubernetes nasazuje, škáluje a spravuje mikroslužby.

Standardní nasazení vytvoří ve vašem předplatném Azure následující služby:

| Počet | Prostředek                                     | Skladová položka / velikost      | Používá se pro |
|-------|----------------------------------------------|-----------------|----------|
| 1     | [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service)| Použití plně spravované služby orchestrace kontejnerů Kubernetes, výchozí pro 3 agenty|
| 1     | [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)                     | S2 – standardní úroveň | Správa, velení a řízení zařízení |
| 1     | [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)                 | Standard        | Ukládání konfiguračních dat a telemetrie zařízení, jako jsou pravidla, výstrahy a zprávy |
| 5     | [Účty úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction#types-of-storage-accounts)    | Standard        | 4 pro úložiště virtuálních her a 1 pro body streamování |
| 1     | [App Service](https://azure.microsoft.com/services/app-service/web/)             | S1 Standard     | Aplikační brána přes TLS |
| 1     | [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)        |                 | Správa identit uživatelů a zabezpečení |
| 1     | [Azure Maps](https://azure.microsoft.com/services/azure-maps/)        | Standard                | Zobrazení umístění datových zdrojů |
| 1     | [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)        |   3 jednotky              | Povolení analýz v reálném čase |
| 1     | [Služba zřizování zařízení Azure](https://docs.microsoft.com/azure/iot-dps/)        |       S1          | Zřizovací zařízení ve velkém měřítku |
| 1     | [Přehledy Azure Time Series](https://azure.microsoft.com/services/time-series-insights/)        |   S1 – 1 jednotka              | Úložiště pro data zpráv a umožňuje hloubkovou telemetricko analýzu |

> [!NOTE]
> Informace o cenách těchto [https://azure.microsoft.com/pricing](https://azure.microsoft.com/pricing)služeb naleznete na adrese . Podrobnosti o využití a fakturaci předplatného najdete na [webu Azure Portal](https://portal.azure.com/).

## <a name="deploy-the-solution-accelerator"></a>Nasazení akcelerátoru řešení

Příklady nasazení:

### <a name="example-deploy-net-version"></a>Příklad: nasazení verze rozhraní .NET

Následující příklad ukazuje, jak nasadit základní verzi .NET akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s basic -r dotnet
```

### <a name="example-deploy-java-version"></a>Příklad: nasazení verze Java

Následující příklad ukazuje, jak nasadit standardní java verzi akcelerátoru řešení vzdáleného monitorování:

```cmd/sh
pcs -t remotemonitoring -s standard -r java
```

### <a name="pcs-command-options"></a>možnosti příkazů pcs

Když spustíte `pcs` příkaz k nasazení řešení, budete požádáni o:

- Název pro vaše řešení. Tento název musí být jedinečný.
- Předplatné Azure, které se má použít.
- Místo.
- Pověření pro virtuální počítače, které hostují mikroslužeb. Tato pověření můžete použít pro přístup k virtuálním počítačům pro řešení potíží.

Po `pcs` dokončení příkazu se zobrazí adresa URL nového akcelerátoru řešení. Příkaz `pcs` také vytvoří `{deployment-name}-output.json` soubor, který obsahuje informace, jako je například název centra IoT Hub, který vytvořil.

Další informace o parametrech příkazového řádku získáte spuštěním:

```cmd/sh
pcs -h
```

Další informace o zaokreslování, naleznete [v tématu Jak používat zapisování-konstituce](https://github.com/Azure/pcs-cli/blob/master/README.md).

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se naučili:

> [!div class="checklist"]
> * Konfigurace akcelerátoru řešení
> * Nasazení akcelerátoru řešení
> * Přihlášení k akcelerátoru řešení

Teď, když jste nasadili řešení vzdáleného monitorování, je dalším krokem [prozkoumání možností řídicího panelu řešení](./quickstart-remote-monitoring-deploy.md).

<!-- Next how-to guides in the sequence -->
