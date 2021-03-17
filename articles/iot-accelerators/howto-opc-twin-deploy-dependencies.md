---
title: Jak v Azure nasadit závislosti v cloudu OPC s dvojitou závislostí | Microsoft Docs
description: Tento článek popisuje, jak nasadit OPCé závislosti Azure, které jsou potřeba k místnímu vývoji a ladění.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078982"
---
# <a name="deploying-dependencies-for-local-development"></a>Nasazení závislostí pro místní vývoj

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek vysvětluje, jak nasadit jenom služby platformy Azure, které jsou potřeba k místnímu vývoji a ladění.   Na konci budete mít nasazenou skupinu prostředků, která obsahuje všechno, co potřebujete pro místní vývoj a ladění.

## <a name="deploy-azure-platform-services"></a>Nasazení služeb platformy Azure

1. Ujistěte se, že máte nainstalované rozšíření PowerShell a [AzureRM prostředí PowerShell](/powershell/azure/azurerm/install-azurerm-ps) .  Otevřete příkazový řádek nebo terminál a spusťte příkaz:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Podle zobrazených výzev přiřaďte název skupiny prostředků pro vaše nasazení.  Tento skript nasadí pouze závislosti do této skupiny prostředků ve vašem předplatném Azure, ale ne mikroslužby.  Skript také zaregistruje aplikaci v Azure AD.  To je potřeba k podpoře ověřování založeného na protokolu OAUTH.  Nasazení může trvat několik minut.

3. Po dokončení skriptu můžete vybrat možnost Uložit soubor. env.  Soubor prostředí. ENV je konfigurační soubor všech služeb a nástrojů, které chcete spustit ve vývojovém počítači.  

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení

### <a name="resource-group-name"></a>Název skupiny prostředků

Ujistěte se, že používáte krátký a jednoduchý název skupiny prostředků.  Název se používá také k pojmenování prostředků, které musí splňovat požadavky na pojmenovávání prostředků.  

### <a name="azure-active-directory-ad-registration"></a>Registrace Azure Active Directory (AD)

Skript nasazení se pokusí zaregistrovat aplikace Azure AD ve službě Azure AD.  V závislosti na vašich právech k vybranému tenantovi Azure AD to může selhat. Existují tři možnosti:

1. Pokud jste zvolili tenanta Azure AD ze seznamu tenantů, restartujte skript a zvolte jiný ze seznamu.
2. Případně můžete nasadit privátního tenanta Azure AD, restartovat skript a vybrat ho pro použití.
3. Pokračovat bez ověřování.  Vzhledem k tomu, že vaše mikroslužby spouštíte místně, je to přijatelné, ale Nenapodobují produkční prostředí.  

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně nasadili OPCé služby, které jsou v existujícím projektu, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Přečtěte si, jak nasadit OPC moduly s dvojím nasazením](howto-opc-twin-deploy-modules.md)