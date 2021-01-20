---
title: Monitorování diagnostiky Azure – ověření identity Azure
description: Monitorování diagnostiky Azure pro Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98606092"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Nastavení diagnostiky pomocí koncového bodu čipu TPM (Trusted Platform Module) Azure Attestation

[Protokoly platforem](/azure/azure-monitor/platform/platform-logs-overview) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. [Metriky platformy](/azure/azure-monitor/platform/data-platform-metrics) se ve výchozím nastavení shromažďují a obvykle se ukládají do databáze Azure monitor metrik. Tento článek poskytuje podrobné informace o vytváření a konfiguraci nastavení diagnostiky pro odesílání metrik platforem a protokolů platforem do různých umístění. 

Služba koncového bodu TPM je povolena s nastavením diagnostiky a lze ji použít k monitorování aktivity. Pokud chcete nastavit [monitorování Azure](/azure/azure-monitor/overview) pro koncový bod služby TPM pomocí prostředí PowerShell, postupujte podle následujících kroků. 

Nastavte službu Azure Attestation. 

[Nastavení ověření Azure pomocí Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Protokoly aktivit najdete v části kontejnery účtu úložiště. Podrobné informace najdete v [protokolech shromáždění zdrojů z prostředku Azure a pomocí Azure monitor – Azure monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
