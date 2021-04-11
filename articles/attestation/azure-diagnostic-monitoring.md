---
title: Monitorování diagnostiky Azure pro Azure Attestation
description: Monitorování diagnostiky Azure pro Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d2773be4bc67e125c18d5d38c951685e4f4fceaf
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168344"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Nastavení diagnostiky pomocí koncového bodu čipu TPM (Trusted Platform Module) Azure Attestation

Tento článek vám pomůže vytvořit a nakonfigurovat nastavení diagnostiky pro odesílání metrik platforem a protokolů platforem do různých umístění. [Protokoly platforem](/azure/azure-monitor/platform/platform-logs-overview) v Azure, včetně protokolů aktivit Azure a protokolů prostředků, poskytují podrobné informace o diagnostice a auditování pro prostředky Azure a platformu Azure, na které jsou závislé. [Metriky platformy](/azure/azure-monitor/platform/data-platform-metrics) se ve výchozím nastavení shromažďují a ukládají se do databáze Azure monitor metrik.

Než začnete, ujistěte se, že jste [nastavili Azure Attestation s Azure PowerShell](quickstart-powershell.md).

V nastavení diagnostiky je povolená služba čipu TPM (Trusted Platform Module), která se dá použít k monitorování aktivity. Pomocí následujícího kódu nastavte [monitorování Azure](/azure/azure-monitor/overview) pro koncový bod služby TPM.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Protokoly aktivit jsou v části **kontejnery** účtu úložiště. Další informace najdete v tématu [shromáždění a analýza protokolů prostředků z prostředku Azure](/azure/azure-monitor/learn/tutorial-resource-logs).
