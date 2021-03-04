---
title: Monitorování diagnostiky Azure – ověření identity Azure
description: Monitorování diagnostiky Azure pro Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726474"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Nastavení diagnostiky pomocí koncového bodu čipu TPM (Trusted Platform Module) Azure Attestation

[Protokoly platforem](../azure-monitor/essentials/platform-logs-overview.md) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. [Metriky platformy](../azure-monitor/essentials/data-platform-metrics.md) se ve výchozím nastavení shromažďují a obvykle se ukládají do databáze Azure monitor metrik. Tento článek poskytuje podrobné informace o vytváření a konfiguraci nastavení diagnostiky pro odesílání metrik platforem a protokolů platforem do různých umístění. 

Služba koncového bodu TPM je povolena s nastavením diagnostiky a lze ji použít k monitorování aktivity. Pokud chcete nastavit [monitorování Azure](../azure-monitor/overview.md) pro koncový bod služby TPM pomocí prostředí PowerShell, postupujte podle následujících kroků. 

Nastavte službu Azure Attestation. 

[Nastavení ověření Azure pomocí Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Protokoly aktivit najdete v části kontejnery účtu úložiště. Podrobné informace najdete v [protokolech shromáždění zdrojů z prostředku Azure a pomocí Azure monitor – Azure monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
