---
title: Nasazení platformy Azure pro průmyslové IoT
description: V tomto kurzu se naučíte, jak nasadit platformu IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787682"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Kurz: nasazení platformy Azure pro průmyslové IoT

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * O hlavních součástech platformy IIoT
> * O různých typech instalace
> * Jak nasadit platformu pro průmyslový IoT

## <a name="prerequisites"></a>Předpoklady

- Je potřeba vytvořit předplatné Azure.
- Stáhnout [Git](https://git-scm.com/downloads)
- Registrace aplikací Azure Active Directory (AAD) používané pro ověřování vyžadují oprávnění správce globálního správce, aplikace nebo správce cloudové aplikace k poskytnutí souhlasu správce na úrovni tenanta (viz níže pro další možnosti).
- Podporovanými operačními systémy pro nasazení jsou Windows, Linux a Mac.
- IoT Edge podporuje Windows 10 IoT Enterprise LTSC a Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Hlavní součásti

- Minimální závislosti: IoT Hub, Cosmos DB, Service Bus, centrum událostí, Key Vault, úložiště
- Standardní závislosti: minimálně + služba signálu, registrace aplikací AAD, Služba Device Provisioning, Time Series Insights, sešit, Log Analytics, Application Insights
- Mikroslužby: plán App Service App Service
- Uživatelské rozhraní (webová aplikace): plán App Service (sdílený s mikroslužbami), App Service
- Simulace: virtuální počítač, virtuální síť, IoT Edge
- Azure Kubernetes Service

## <a name="installation-types"></a>Typy instalace

- Minimum: Minimální závislosti
- Místní: minimální a standardní závislosti
- Služby: místní a mikroslužby
- Simulace: Minimální závislosti a simulace komponenty
- Aplikace: služby a uživatelské rozhraní
- Vše (výchozí): aplikace a simulace

## <a name="deployment"></a>Nasazení

1. Pokud chcete začít s nasazením platformy IIoT, naklonujte úložiště z příkazového řádku nebo terminálu.

    Klonovaný https://github.com/Azure/Industrial-IoT  Industrial-IoT CD Git

2. Spusťte nasazení s asistencí, skript bude shromažďovat požadované informace, jako je například účet Azure, předplatné, cílový prostředek a název aplikace.

Ve Windows:
    ```
    .\deploy
    ```

V systému Linux nebo Mac:
    ```
    ./deploy.sh
    ```

3. Mikroslužby a uživatelské rozhraní jsou webové aplikace, které vyžadují ověřování. to vyžaduje tři registrace aplikací v AAD. Pokud chybí požadovaná práva, existují dvě možná řešení:

- Požádejte správce AAD, aby pro aplikaci udělil souhlas správce na úrovni tenanta.
- Správce AAD může vytvořit aplikace AAD. Složka Deploy/Scripts obsahuje skript AAD-register.ps1, který provede registraci AAD odděleně od nasazení. Výstupem skriptu je soubor obsahující relevantní informace, které se mají použít jako součást nasazení, a musí být předány do skriptu deploy.ps1 ve stejné složce pomocí argumentu-aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

V produkčních nasazeních, která vyžadují přípravu, vrácení zpět, škálování a odolnost, se dá platforma nasadit do [služby Azure Kubernetes Service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md) .

Odkazy
- [Nasazení platformy Azure pro průmyslové IoT](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Jak nasadit vše v jednom](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Nasazení platformy do AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Další kroky
Teď, když jste nasadili platformu IIoT, se můžete seznámit s postupem přizpůsobení konfigurace těchto komponent:

> [!div class="nextstepaction"]
> [Přizpůsobení konfigurace komponent](tutorial-configure-industrial-iot-components.md)
