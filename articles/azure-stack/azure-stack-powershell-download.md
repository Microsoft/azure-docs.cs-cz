---
title: "Stažení nástroje Azure zásobníku z Githubu | Microsoft Docs"
description: "Zjistěte, jak si chcete stáhnout nástroje, které jsou požadovány pro práci s Azure zásobníku."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E4DF77FA-F468-42B5-B44F-F10ED8049171
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.openlocfilehash: 219fd8e4e164df8c3002044719a90a7be56a9edf
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
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
| [Správa výpočtů Azure zásobníku](azure-stack-add-vm-image.md) | Tento modul slouží k přidání nebo odebrání image virtuálního počítače v zásobníku Azure Marketplace. | Operátoři cloudu |
| [Správa infrastruktury Azure zásobníku](https://github.com/Azure/AzureStack-Tools/blob/master/Infrastructure/README.md) | Tento modul slouží ke správě infrastruktury Azure zásobníku virtuálních počítačů, výstrahy, aktualizace a tak dále. |  Operátoři cloudu|
| [Správce prostředků zásady pro Azure zásobníku](user/azure-stack-policy-module.md) | Tento modul slouží ke konfiguraci předplatné Azure nebo skupinu prostředků Azure pomocí stejné verze a služba dostupnosti jako zásobník Azure. | Operátoři cloudů a uživatelé |
| [Zaregistrovat Azure](azure-stack-register.md) | Tento modul slouží k registraci instance kit vývoj s Azure. Po registraci, můžete stáhnout položky marketplace z Azure a použít je v Azure zásobníku. | Operátoři cloudu |
| [Nasazení Azure zásobníku](azure-stack-run-powershell-script.md) | Tento modul slouží k přípravě hostitelský počítač zásobník Azure k nasazení a znovu nasaďte pomocí bitové kopie virtuálního pevného disku (VHD) Azure zásobníku. | Operátoři cloudu|
| [Připojení k Azure zásobníku](azure-stack-connect-powershell.md) | Tento modul slouží k připojení k instanci zásobník Azure pomocí prostředí PowerShell a ke konfiguraci připojení VPN do protokolů Azure. | Operátoři cloudů a uživatelé |
| [Správa služby Azure zásobníku](azure-stack-create-offer.md) | Tento modul slouží k vytvoření na výchozí klienta nabídku s neomezená kvóty napříč výpočty, úložiště Azure, sítě a služby Key Vault.   | Operátoři cloudu|
| [Ověření šablony](user/azure-stack-validate-templates.md) | Tento modul slouží k ověření, pokud existující nebo nové šablony lze nasadit do protokolů Azure. | Operátoři cloudů a uživatelé|


## <a name="next-steps"></a>Další postup
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](user/azure-stack-powershell-configure-user.md)   
* [Připojení k Azure zásobníku Development Kit přes síť VPN](azure-stack-connect-azure-stack.md)  
