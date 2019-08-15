---
title: Jak v Azure nasadit závislosti v cloudu OPC s dvojitou závislostí | Microsoft Docs
description: Postup nasazení OPCch závislostí Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: cb07899b51280cff8613d637640c0da23debbc8e
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016520"
---
# <a name="deploying-dependencies-for-local-development"></a>Nasazení závislostí pro místní vývoj

Tento článek vysvětluje, jak nasadit jenom služby platformy Azure, které jsou potřeba k místnímu vývoji a ladění.   Na konci budete mít nasazenou skupinu prostředků, která obsahuje všechno, co potřebujete pro místní vývoj a ladění.

## <a name="deploy-azure-platform-services"></a>Nasazení služeb platformy Azure

1. Ujistěte se, že máte nainstalované rozšíření PowerShell a [AzureRM prostředí PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) .  Otevřete příkazový řádek nebo terminál a spusťte příkaz:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Podle zobrazených výzev přiřaďte název skupiny prostředků pro vaše nasazení.  Tento skript nasadí pouze závislosti do této skupiny prostředků ve vašem předplatném Azure, ale ne mikroslužby.  Skript také zaregistruje aplikaci v Azure Active Directory.  To je potřeba k podpoře ověřování založeného na protokolu OAUTH.  Nasazení může trvat několik minut.

3. Po dokončení skriptu můžete vybrat možnost Uložit soubor. env.  Soubor prostředí. ENV je konfigurační soubor všech služeb a nástrojů, které chcete spustit ve vývojovém počítači.  

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k pojmenování prostředků, které musí splňovat požadavky na pojmenovávání prostředků.  

### <a name="azure-active-directory-aad-registration"></a>Registrace Azure Active Directory (AAD)

Skript nasazení se pokusí zaregistrovat aplikace AAD v Azure Active Directory.  V závislosti na vašich právech vybraného tenanta AAD to může selhat.   Existují tři možnosti:

1. Pokud jste zvolili tenanta AAD ze seznamu tenantů, restartujte skript a zvolte jiný ze seznamu.
2. Případně můžete nasadit privátního tenanta AAD, restartovat skript a vybrat ho pro použití.
3. Pokračovat bez ověřování.  Vzhledem k tomu, že vaše mikroslužby spouštíte místně, je to přijatelné, ale Nenapodobují produkční prostředí.  

## <a name="next-steps"></a>Další postup

Teď, když jste úspěšně nasadili OPCé služby, které jsou v existujícím projektu, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Přečtěte si, jak nasadit OPC moduly s dvojím nasazením](howto-opc-twin-deploy-modules.md)
