---
title: Spuštění a zastavení Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Zjistěte, jak ke spuštění a vypnutí dolů Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 0073427028d20ab969b8d0135b1c831d7fdf8c73
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191961"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Spuštění a zastavení Azure Stack Development Kit (ASDK)
Není doporučeno jednoduše Restartujte hostitelský počítač ASDK. Místo toho by měla podle postupů v tomto článku správně vypne a restartuje ASDK služby. 

## <a name="stop-azure-stack"></a>Zastavení služby Azure Stack 
Vypnout správně ASDK hostitelského počítače a služby Azure stacku, použijte následující příkazy Powershellu:

1. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne prostředí PowerShell ISE).
3. Spusťte následující příkazy k vytvoření relace privileged koncový bod (období): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Potom se v relaci období pomocí **Stop-AzureStack** k zastavení služby Azure Stack a vypnout počítač ASDK hostitele:

   ```powershell
   Stop-AzureStack
   ```
5. Zkontrolujte výstup prostředí PowerShell zajistit, že všechny služby Azure Stack se úspěšně vypnul před ASDK hostitelský počítač vypne. Vypnutí procesu trvá několik minut.

## <a name="start-azure-stack"></a>Spuštění služby Azure Stack 
ASDK služby by měl spustit automaticky při spuštění v hostitelském počítači. Ale čas spuštění služby infrastruktury ASDK se liší v závislosti na výkonu hardwarová konfigurace ASDK hostitelského počítače. Může trvat několik hodin pro všechny služby, se úspěšně znovu spustit v některých případech.

Bez ohledu na to, jak byl ASDK vypnutý používejte následující postup ověření, že všechny služby Azure Stack spuštěn a plně funkční po hostitelský počítač je zapnutý: 

1. Zapnutí ASDK hostitelský počítač. 
2. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
3. Otevřete PowerShell jako správce (ne prostředí PowerShell ISE).
4. Spusťte následující příkazy k vytvoření relace privileged koncový bod (období):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. V dalším kroku v této relaci období, spusťte následující příkazy ke kontrole stavu spuštění služby Azure Stack:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Zkontrolujte výstup zajistit, že jste úspěšně restartovat služby Azure Stack.

Další informace o doporučených postupů řádně vypnout a restartovat služby Azure Stack, najdete v článku [spouštění a zastavování služby Azure Stack](../azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Řešení potíží s spuštění a vypnutí 
Pokud během dvou hodin po zapnutí ASDK hostitelský počítač není úspěšně spustit služby Azure Stack, proveďte tyto kroky:

1. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne prostředí PowerShell ISE).
3. Spusťte následující příkazy k vytvoření relace privileged koncový bod (období):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. V dalším kroku v této relaci období, spusťte následující příkazy ke kontrole stavu spuštění služby Azure Stack:

   ```powershell
   Test-AzureStack
   ```
5. Zkontrolujte výstup a vyřešte všechny chyby. Další informace najdete v tématu [spustit test pro ověření služby Azure Stack](../azure-stack-diagnostic-test.md).
6. Restartujte služby Azure Stack z v rámci relace období spuštěním **Start AzureStack** rutiny:

   ```powershell
   Start-AzureStack
   ```

Pokud systém **Start AzureStack** důsledkem chyby, přejděte [fórum podpory služby Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) zobrazíte ASDK podporu při řešení potíží. 

## <a name="next-steps"></a>Další postup 
Další informace o službě Azure Stack pro diagnostické nástroje a vydávání protokolování, najdete v článku [diagnostické nástroje služby Azure Stack](../azure-stack-diagnostics.md).
