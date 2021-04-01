---
title: 'Registrace připojení ke službě peering Service – Azure PowerShell '
description: V tomto kurzu se dozvíte, jak zaregistrovat připojení ke službě peering Service pomocí PowerShellu.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
Customer intent: Customer wants to measure their connection telemetry per prefix to Microsoft services with Azure Peering Service .
ms.openlocfilehash: a12bfa042c8741814cedc70f1dcb67dedbfd331e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400430"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Kurz: registrace připojení ke službě peering Service pomocí Azure PowerShell

V tomto kurzu se naučíte registrovat službu peering Service pomocí Azure PowerShell.

Azure peering Service je síťová služba, která vylepšuje připojení zákazníků ke cloudovým službám Microsoftu, jako jsou Microsoft 365, Dynamics 365, služby software jako služba (SaaS), Azure nebo jakékoli služby Microsoftu přístupné prostřednictvím veřejného Internetu. V tomto článku se dozvíte, jak zaregistrovat připojení služby partnerského vztahu pomocí Azure PowerShell.

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) hned teď.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně místo toho, musíte použít Azure PowerShell modulu verze 1.0.0 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Informace o instalaci a upgradu najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Nakonec, pokud používáte PowerShell místně, budete také muset spustit `Connect-AzAccount` . Tento příkaz vytvoří připojení k Azure.

Použijte modul Azure PowerShell k registraci a správě služby peering Service. Službu partnerského vztahu můžete zaregistrovat nebo spravovat z příkazového řádku PowerShellu nebo ve skriptech.


## <a name="prerequisites"></a>Požadavky  
Musíte mít následující:

### <a name="azure-account"></a>Účet Azure

Musíte mít platný a aktivní účet Microsoft Azure. Tento účet je nutný k nastavení připojení služby partnerského vztahu. Peering Service je prostředek v rámci předplatných Azure.

### <a name="connectivity-provider"></a>Poskytovatel připojení

Můžete pracovat s partnerem poskytovatele internetových služeb nebo s internetovým Exchangem a získat službu peering Service pro připojení vaší sítě k síti Microsoftu.

Ujistěte se, že jsou poskytovatelé připojení partneři s Microsoftem.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Registrace předplatného pomocí příznaku funkce a poskytovatele prostředků

Než přejdete k postupu registrace služby peering Service, zaregistrujte své předplatné pomocí poskytovatele prostředků a příznaku funkce pomocí Azure PowerShell. Azure PowerShell jsou zde uvedeny následující příkazy:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>Načtení umístění a poskytovatele služeb 

Spusťte následující příkazy v Azure PowerShell k získání umístění a poskytovatele služeb, na který má být povolena služba partnerského vztahu. 

Získat umístění služby partnerského vztahu:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Získat poskytovatele služby partnerských vztahů:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>Registrace připojení ke službě peering Service

Zaregistrujte připojení ke službě peering Service pomocí následující sady příkazů prostřednictvím Azure PowerShell. Tento příklad zaregistruje službu partnerského vztahu s názvem myPeeringService.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Registrace předpony služby partnerského vztahu

Zaregistrujte předponu, kterou poskytuje poskytovatel připojení, spuštěním následujících příkazů prostřednictvím Azure PowerShell. Tento příklad registruje předponu s názvem myPrefix.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Zobrazit seznam všech připojení ke službě peering Services

Pokud chcete zobrazit seznam všech služeb partnerského vztahu, spusťte následující příkaz:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Vypsat všechny předpony služby partnerského vztahu

Pokud chcete zobrazit seznam předpon služby partnerského vztahu, spusťte následující příkaz:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Odebrat předponu služby partnerského vztahu

Pokud chcete odebrat předponu služby partnerského vztahu, spusťte následující příkaz:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Další kroky

- Další informace o připojení ke službě peering Service najdete v tématu [připojení ke službě peering Service](connection.md).
- Další informace o telemetrie připojení ke službě peering Service najdete v tématu [telemetrie připojení ke službě peering Service](connection-telemetry.md).
- Pokud chcete zaregistrovat připojení ke službě peering Service pomocí Azure Portal, přečtěte si téma [registrace připojení služby peering Service-Azure Portal](azure-portal.md).
- Pokud chcete zaregistrovat připojení ke službě partnerského vztahu pomocí Azure CLI, přečtěte si téma [registrace připojení ke službě peering Service – Azure CLI](cli.md).
