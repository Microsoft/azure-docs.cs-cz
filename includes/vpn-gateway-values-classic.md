---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/08/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 75aec9f3509881c35de9309fa1532b961fb2bc03
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875535"
---
Když v Azure Portal vytvoříte klasický virtuální sítě, název, který si zobrazíte, není úplný název, který používáte pro PowerShell. Například virtuální síť, která se na portálu zdá být pojmenovaná **virtuální sítě testvnet1** , může mít v konfiguračním souboru sítě mnohem delší název. Název může vypadat nějak takto: **Group ClassicRG virtuální sítě testvnet1**. Při vytváření připojení je důležité použít hodnoty, které vidíte v souboru konfigurace sítě.

V následujících krocích se připojíte ke svému účtu Azure a stáhnete a zobrazíte soubor konfigurace sítě, abyste získali hodnoty požadované pro vaše připojení.

1. Stáhněte a nainstalujte si nejnovější verzi rutin PowerShellu pro správu služeb Azure (SM). Většina lidí má místně nainstalované Správce prostředků moduly, ale nemá moduly pro správu služeb. Moduly pro správu služeb jsou starší a musí se instalovat samostatně. Další informace najdete v tématu [instalace rutin služby Service Management](/powershell/azure/servicemanagement/install-azure-ps).

1. Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Použijte následující příklady, které vám pomůžou se připojit. Tyto příkazy musíte spustit místně pomocí modulu pro správu služby PowerShell. Připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

   ```powershell
   Add-AzureAccount
   ```
1. Zkontrolujte předplatná pro příslušný účet.

   ```powershell
   Get-AzureSubscription
   ```
1. Máte-li více předplatných, vyberte předplatné, které chcete použít.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
1. Exportujte a zobrazte konfigurační soubor sítě. Vytvořte ve svém počítači adresář a potom do něj exportujte soubor konfigurace sítě. V tomto příkladu je konfigurační soubor sítě exportován do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
1. Otevřete soubor pomocí textového editoru a zobrazte názvy pro virtuální sítě a weby. Tyto názvy budou názvy, které použijete při vytváření připojení.<br>Názvy virtuální sítě jsou uvedené jako **VirtualNetworkSite název =**<br>Názvy webů jsou uvedené jako **LocalNetworkSiteRef název =**