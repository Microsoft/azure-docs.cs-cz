---
title: Ukázkový skript Azure PowerShellu – Omezení webového provozu | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Vytvoření aplikační brány s Firewallem webových aplikací a škálovací sadou virtuálních počítačů. K omezení provozu se používají pravidla OWASP.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 7232d7d3bfad23a91bf93adb679973f5a419aa76
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457752"
---
# <a name="restrict-web-traffic-using-azure-powershell"></a>Omezení webového provozu s využitím Azure Powershellu

Tento skript vytvoří aplikační bránu s Firewallem webových aplikací. Pro back-endové servery se použije škálovací sada virtuálních počítačů. Firewall webových aplikací používá k omezení webového provozu pravidla OWASP. Po spuštění skriptu můžete otestovat aplikační bránu použitím její veřejné IP adresy.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss-waf.ps1 "Create application gateway with WAF")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud chcete skupinu prostředků, aplikační bránu a všechny související prostředky odebrat, spusťte následující příkaz.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nasazení. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Nová-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Vytvoří konfiguraci podsítě. |
| [Nová virtuální síť Az](/powershell/module/az.network/new-azvirtualnetwork) | Vytvoří virtuální síť pomocí konfigurace podsítě. |
| [Nová adresa AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Vytvoří veřejnou IP adresu aplikační brány. |
| [Nová konfigurace AzApplicationGatewayIP](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Vytvoří konfiguraci, která k aplikační bráně přidruží podsíť. |
| [Nová-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Vytvoří konfiguraci, která k aplikační bráně přiřadí veřejnou IP adresu. |
| [Nový-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Přiřadí port, který se bude používat k přístupu k aplikační bráně. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Vytvoří back-endový fond pro aplikační bránu. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Nakonfiguruje nastavení pro back-endový fond. |
| [Nový azaplikační httplistener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Vytvoří naslouchací proces. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Vytvoří pravidlo směrování. |
| [Nový-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Určí úroveň a kapacitu brány Application Gateway. |
| [Nová-AzApplicationGatewayWebApplicationFirewallKonfigurace](/powershell/module/az.network/new-azapplicationgatewaywebapplicationfirewallconfiguration) | Vytvoří konfiguraci Firewallu webových aplikací. |
| [Nová azaplikační brána](/powershell/module/az.network/new-azapplicationgateway) | Vytvoří aplikační bránu. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Vytvoří profil úložiště pro škálovací sadu. |
| [Set-AzVmssOsProfil](/powershell/module/az.compute/set-azvmssosprofile) | Definuje operační systém pro škálovací sadu. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Definuje síťové rozhraní pro škálovací sadu. |
| [Nový-AzVmss](/powershell/module/az.compute/new-azvm) | Vytvoří škálovací sadu virtuálních počítačů. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Vytvoří účet úložiště. |
| [Nastavení set-azdiagnosticsetting](/powershell/module/az.monitor/set-azdiagnosticsetting) | Nakonfiguruje diagnostiku pro záznam dat. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | Získá veřejnou IP adresu aplikační brány. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odebere skupinu prostředků a všechny prostředky, které obsahuje. | 
## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro aplikační brány najdete v [dokumentaci služby Azure Application Gateway](../powershell-samples.md).
