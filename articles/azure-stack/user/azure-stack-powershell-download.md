---
title: Stažení nástroje Azure zásobníku z Githubu | Microsoft Docs
description: Zjistěte, jak si chcete stáhnout nástroje potřebné pro práci s Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 28F360AD-789A-488D-965F-FC6E6CCF3329
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: f4c4d907fe76c5e6ee5893b5b4dd1b2d766877a7
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287249"
---
# <a name="download-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

AzureStack nástroje je úložiště GitHub, který je hostitelem moduly Powershellu, které můžete použít ke správě a nasazení prostředků do protokolů Azure.

## <a name="download-targets"></a>Stáhněte si cíle

Můžete stáhnout a použít tyto moduly Powershellu Development Kit zásobník Azure nebo externí klienta se systémem Windows, který používá připojení k síti VPN.

## <a name="how-to-get-the-tools"></a>Jak získat nástroje

Tyto nástroje získáte naklonujte úložiště GitHub AzureStack nástroje nebo nástroje AzureStack složka pro stahování spuštěním následujícího skriptu:

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

Úložiště AzureStack nástroje obsahuje moduly Powershellu, které podporují následující funkce pro Azure zásobníku:

| Funkce | Popis | Tento modul, který můžete použít? |
| --- | --- | --- |
| [Možností cloudu](azure-stack-validate-templates.md) | Tento modul slouží k získání možností cloudu cloudu. Například můžete získat možností cloudu například verze rozhraní API, prostředky Azure Resource Manager, rozšíření virtuálního počítače atd. pro zásobník Azure a Azure cloudy pomocí tohoto modulu. | Cloud správců a uživatelů. |
| [Správce prostředků zásady pro Azure zásobníku](azure-stack-policy-module.md) | Tento modul slouží ke konfiguraci předplatné Azure nebo skupinu prostředků Azure pomocí stejné verze a služba dostupnosti jako zásobník Azure. | Cloud správců a uživatelů |
| [Připojení k Azure zásobníku](azure-stack-connect-azure-stack.md) | Tento modul slouží k připojení k instanci zásobník Azure pomocí prostředí PowerShell a ke konfiguraci připojení VPN do protokolů Azure. | Cloud správců a uživatelů |
| [Ověření šablony](azure-stack-validate-templates.md) | Tento modul slouží k ověření, pokud existující nebo nové šablony lze nasadit do protokolů Azure. | Cloud správců a uživatelů |

## <a name="next-steps"></a>Další postup

* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)
* [Připojení k Azure zásobníku Development Kit přes síť VPN](azure-stack-connect-azure-stack.md)
