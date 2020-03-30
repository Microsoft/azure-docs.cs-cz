---
title: Jak nasadit cloudové závislosti OPC Twin v Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nasadit závislosti OPC Twin Azure potřebné k místnímu vývoji a ladění.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73824102"
---
# <a name="deploying-dependencies-for-local-development"></a>Nasazení závislostí pro místní vývoj

Tento článek vysvětluje, jak nasadit jenom služby platformy Azure potřebné k místnímu vývoji a ladění.   Na konci budete mít nasazenou skupinu prostředků, která obsahuje vše, co potřebujete pro místní vývoj a ladění.

## <a name="deploy-azure-platform-services"></a>Nasazení služeb platformy Azure

1. Ujistěte se, že máte nainstalovaná rozšíření PowerShellu a [AzureRM PowerShellu.](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)  Otevřete příkazový řádek nebo terminál a spusťte:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Podle pokynů přiřaďte název skupině prostředků pro vaše nasazení.  Skript nasadí pouze závislosti do této skupiny prostředků ve vašem předplatném Azure, ale ne mikroslužby.  Skript také zaregistruje aplikaci ve službě Azure Active Directory.  To je potřeba pro podporu ověřování na základě OAUTH.  Nasazení může trvat několik minut.

3. Po dokončení skriptu můžete vybrat uložení souboru ENV.  Soubor prostředí ENV je konfigurační soubor všech služeb a nástrojů, které chcete spustit ve vývojovém počítači.  

## <a name="troubleshooting-deployment-failures"></a>Poradce při potížích s chybami nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k názvu prostředků jako takové musí splňovat požadavky na pojmenování prostředků.  

### <a name="azure-active-directory-aad-registration"></a>Registrace služby Azure Active Directory (AAD)

Skript nasazení se pokusí zaregistrovat aplikace AAD ve službě Azure Active Directory.  V závislosti na vašich právech k vybranému tenantovi AAD to může selhat.   Existují tři možnosti:

1. Pokud jste ze seznamu klientů vybrali klienta AAD, restartujte skript a ze seznamu zvolte jiný.
2. Případně nasadit soukromého klienta AAD, restartovat skript a vybrat jeho použití.
3. Pokračujte bez ověřování.  Vzhledem k tomu, že používáte mikroslužby místně, je to přijatelné, ale nenapodobuje produkční prostředí.  

## <a name="next-steps"></a>Další kroky

Nyní, když jste úspěšně nasadili služby OPC Twin do existujícího projektu, je zde navrhovaný další krok:

> [!div class="nextstepaction"]
> [Informace o nasazení modulů OPC Twin](howto-opc-twin-deploy-modules.md)
