---
title: Stáhněte si nástroje pro Azure Stack z Githubu | Dokumentace Microsoftu
description: Zjistěte, jak chcete stáhnout nástroje potřebné pro práci s Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.openlocfilehash: 9c1e4abe50b06db58a4ca05a99e1ae4a531b2294
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187447"
---
# <a name="download-azure-stack-tools-from-github"></a>Stáhněte si nástroje pro Azure Stack z Githubu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

AzureStack nástroje je úložiště GitHub, který je hostitelem moduly Powershellu, které můžete použít ke správě a nasazování prostředků do Azure stacku.

## <a name="download-targets"></a>Stáhněte si cíle

Můžete stáhnout a použít tyto moduly Powershellu pro Azure Stack Development Kit, nebo externí klienta se systémem Windows, který používá připojení k síti VPN.

## <a name="how-to-get-the-tools"></a>Jak získat nástroje

Tyto nástroje získat, naklonujte úložiště GitHub AzureStack nástroje nebo nástroje AzureStack složka pro stahování spuštěním následujícího skriptu:

```PowerShell
# Change directory to the root directory
cd \

# Download the tools archive
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory
cd AzureStack-Tools-master

```

## <a name="functionalities-provided-by-the-modules"></a>Funkce poskytované moduly

Úložiště AzureStack nástroje obsahuje moduly Powershellu, které podporují následující funkce pro Azure Stack:

| Funkce | Popis | Tento modul, který můžete použít? |
| --- | --- | --- |
| [Cloudové funkce](https://github.com/Azure/AzureStack-Tools/tree/master/CloudCapabilities) | Tento modul slouží k získání cloudové možnosti cloudu. Například můžete získat možností cloudu, jako je například verze rozhraní API, prostředky Azure Resource Manageru, rozšíření virtuálních počítačů atd. pro Azure Stack a cloudy Azure pomocí tohoto modulu. | Uživatelé a správci cloudu. |
| [Zásady Resource Manageru pro službu Azure Stack](azure-stack-policy-module.md) | Tento modul slouží ke konfiguraci předplatné Azure nebo skupinu prostředků Azure s dostupností stejné správy verzí a služby jako Azure Stack. | Uživatelé a správci cloudu |
| [Připojení k Azure Stack](azure-stack-connect-azure-stack.md) | Tento modul slouží k připojení k instanci služby Azure Stack prostřednictvím Powershellu a ke konfiguraci připojení VPN ke službě Azure Stack. | Uživatelé a správci cloudu |
| [Program pro ověření šablony](azure-stack-validate-templates.md) | Tento modul slouží k ověření, pokud stávající nebo novou šablonu je možné nasadit do služby Azure Stack. | Uživatelé a správci cloudu |

## <a name="next-steps"></a>Další postup

- [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)
- [Připojení k Azure Stack Development Kit přes síť VPN](azure-stack-connect-azure-stack.md)
