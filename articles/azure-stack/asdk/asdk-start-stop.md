---
title: Spuštění a zastavení Azure zásobníku Development Kit (ASDK) | Microsoft Docs
description: Zjistěte, jak na spuštění a ukončení dolů Azure zásobníku Development Kit (ASDK).
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
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426899"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>Spuštění a zastavení Azure zásobníku Development Kit (ASDK)
Není doporučeno jednoduše restartujte ASDK hostitelský počítač. Místo toho by měla podle postupů v tomto článku správně vypnutí a restartování služby ASDK. 

## <a name="stop-azure-stack"></a>Zastavit Azure zásobníku 
Chcete-li správně vypnut služeb Azure zásobníku a ASDK hostitelský počítač, použijte následující příkazy prostředí PowerShell:

1. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (není prostředí PowerShell ISE).
3. Spusťte následující příkazy k vytvoření relace privilegované koncový bod (období): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Dále v této relaci období, použijte **Stop-AzureStack** rutiny zastavení služeb Azure zásobníku a vypnout ASDK hostitelského počítače:

   ```powershell
   Stop-AzureStack
   ```
5. Zkontrolujte výstup prostředí PowerShell zajistit, že všechny služby Azure zásobníku se úspěšně vypnul před ASDK hostitelský počítač vypne. Proces vypnutí trvá několik minut.

## <a name="start-azure-stack"></a>Spustit Azure zásobníku 
ASDK služby by měl spustit automaticky při spuštění na hostitelském počítači. Však ASDK infrastruktury služby spuštění se liší v závislosti na výkonu v konfiguraci hardwaru počítače ASDK hostitele. Může trvat několik hodin pro všechny služby úspěšně restartování v některých případech.

Bez ohledu na to, jak byl ASDK vypnout byste měli způsobem následujících kroků ověřte, zda jsou všechny služby Azure zásobníku spuštěna a je plně funkční, po hostitelský počítač je zapnutý: 

1. Zapnutí ASDK hostitelský počítač. 
2. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
3. Otevřete PowerShell jako správce (není prostředí PowerShell ISE).
4. Spusťte následující příkazy k vytvoření relace privilegované koncový bod (období):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. Dále v této relaci období, spusťte následující příkazy a zkontrolujte stav spuštění služeb Azure zásobníku:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Zkontrolujte výstup zajistit, že služby Azure zásobníku úspěšně restartovány.

Další informace o doporučených postupů správně vypnutí a restartování služby zásobník Azure najdete v tématu [zahájení a ukončení zásobník Azure](.\.\azure-stack-start-and-stop.md). 

## <a name="troubleshoot-startup-and-shutdown"></a>Řešení potíží s spuštění a vypnutí 
Pokud nemáte Azure zásobníku služby úspěšně spustit během dvou hodin po zapnutí hostitelského počítače ASDK, proveďte tyto kroky:

1. Přihlaste se jako AzureStack\CloudAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (není prostředí PowerShell ISE).
3. Spusťte následující příkazy k vytvoření relace privilegované koncový bod (období):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Dále v této relaci období, spusťte následující příkazy a zkontrolujte stav spuštění služeb Azure zásobníku:

   ```powershell
   Test-AzureStack
   ```
5. Zkontrolujte výstup a vyřešte případné chyby. Další informace najdete v tématu [spustit test pro ověření Azure zásobníku](.\.\azure-stack-diagnostic-test.md).
6. Restartujte služby Azure zásobníku z v rámci relace období spuštěním **Start-AzureStack** rutiny:

   ```powershell
   Start-AzureStack
   ```

Pokud systém **počáteční AzureStack** má za následek selhání, přejděte [fórum podpory Azure zásobníku](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) získat ASDK řešení potíží s podpory. 

## <a name="next-steps"></a>Další postup 
Další informace o Azure zásobníku nástroj pro diagnostiku a vystavování protokolování, najdete v části [diagnostické nástroje Azure zásobníku](.\.\azure-stack-diagnostics.md).
