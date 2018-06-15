---
title: Stažení nástroje Azure zásobníku z Githubu | Microsoft Docs
description: Zjistěte, jak si chcete stáhnout nástroje, které jsou požadovány pro práci s Azure zásobníku.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: a116b7a048ff95ca601a65633cdc63f98fefee9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075285"
---
# <a name="download-azure-stack-tools-from-github"></a>Stažení nástroje Azure zásobníku z Githubu

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

**Nástroje AzureStack** je úložiště GitHub, který je hostitelem moduly Powershellu pro správu a nasazení prostředků do protokolů Azure. Pokud máte v úmyslu navázat připojení VPN, můžete tyto moduly Powershellu stáhnout Development Kit zásobník Azure nebo na externí klienta se systémem Windows. K získání těchto nástrojů, naklonujte úložiště GitHub nebo stáhnout **AzureStack nástroje** složky tak, že spustíte následující skript:

```PowerShell
# Change directory to the root directory. 
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funkce poskytované službou moduly

**AzureStack nástroje** úložiště obsahuje moduly Powershellu, které podporují následující funkce pro Azure zásobníku:  

| Funkce | Popis | Tento modul, který můžete použít? |
| --- | --- | --- |
| [Možností cloudu](user/azure-stack-validate-templates.md) | Tento modul slouží k získání možností cloudu cloudu. Například můžete pomocí tohoto modulu získat cloudové funkce jako je verze rozhraní API a prostředky Azure Resource Manager. Můžete také získat rozšíření virtuálního počítače pro zásobník Azure a Azure cloudy pomocí tohoto modulu. | Operátoři cloudů a uživatelé |
| [Správce prostředků zásady pro Azure zásobníku](user/azure-stack-policy-module.md) | Tento modul slouží ke konfiguraci předplatné Azure nebo skupinu prostředků Azure pomocí stejné verze a služba dostupnosti jako zásobník Azure. | Operátoři cloudů a uživatelé |
| [Zaregistrovat Azure](azure-stack-register.md) | Tento modul slouží k registraci instance kit vývoj s Azure. Po registraci, můžete stáhnout položky marketplace z Azure a použít je v Azure zásobníku. | Operátoři cloudu |
| [Nasazení Azure zásobníku](azure-stack-run-powershell-script.md) | Tento modul slouží k přípravě hostitelský počítač zásobník Azure k nasazení a znovu nasaďte pomocí bitové kopie virtuálního pevného disku (VHD) Azure zásobníku. | Operátoři cloudu|
| [Připojení k Azure zásobníku](azure-stack-connect-powershell.md) | Tento modul slouží ke konfiguraci připojení VPN do protokolů Azure. | Operátoři cloudů a uživatelé |
| [Ověření šablony](user/azure-stack-validate-templates.md) | Tento modul slouží k ověření, pokud existující nebo nové šablony lze nasadit do protokolů Azure. | Operátoři cloudů a uživatelé|


## <a name="next-steps"></a>Další postup
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](user/azure-stack-powershell-configure-user.md)   
* [Připojení k Azure zásobníku Development Kit přes síť VPN](azure-stack-connect-azure-stack.md)  
