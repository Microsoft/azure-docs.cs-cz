---
title: Jak nasadit závislosti cloudu správu zařízení Azure IoT OPC UA | Dokumentace Microsoftu
description: Jak nasadit Azure Dvojčete OPC závislosti.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: cdd89293328da47c8e338bb6fca8c7b93dd84f97
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759445"
---
# <a name="deploying-dependencies-for-local-development"></a>Nasazení závislosti pro místní vývoj

Tento článek vysvětluje, jak nasadit jenom potřebné služeb platformy Azure místní vývoj a ladění.   Na konci budete mít skupinu prostředků nasazení, která obsahuje všechno, co potřebujete pro místní vývoj a ladění.

## <a name="deploy-azure-platform-services"></a>Nasazení služeb platformy Azure

1. Ujistěte se, že máte prostředí PowerShell a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) nainstalovaná rozšíření.  Otevřete příkazový řádek nebo terminálu a spusťte:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Postupujte podle výzev a přiřaďte název skupiny prostředků pro vaše nasazení.  Skript nasadí jenom závislosti do této skupiny prostředků v předplatném Azure, ale ne mikroslužby.  Skript také zaregistruje aplikaci v Azure Active Directory.  To je potřeba pro podporu ověřování OAuth.  Nasazení může trvat několik minut.

3. Po dokončení skriptu můžete vybrat k uložení souboru .env.  Souboru .env prostředí je konfigurační soubor služby a nástroje, které chcete spustit na vývojovém počítači.  

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží s chybami nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte název skupiny prostředků krátce a jednoduše.  Název se používá také k název prostředkům jako takové ho musí dodržovat, s prostředkem požadavkům na názvy.  

### <a name="azure-active-directory-aad-registration"></a>Registrace služby Azure Active Directory (AAD)

Skript nasazení se pokusí registrace AAD aplikace v Azure Active Directory.  V závislosti na vašich práv k vybraného tenanta AAD to může selhat.   Existují 3 možnosti:

1. Pokud jste zvolili tenanta AAD ze seznamu tenantů, restartujte jej a zvolte jiný ze seznamu.
2. Můžete také nasadit privátní tenanta AAD, restartujte jej a vyberte jeho použití.
3. Pokračujte bez ověřování.  Protože vaše mikroslužby spouštíte místně, to je přijatelné, ale není napodobovat provozní prostředí.  

## <a name="next-steps"></a>Další postup

Teď, když jste úspěšně nasadili služeb správy zařízení OPC do existujícího projektu, je zde navrhované další krok:

> [!div class="nextstepaction"]
> [Další informace o tom, jak nasadit moduly správy zařízení OPC](howto-opc-twin-deploy-modules.md)
