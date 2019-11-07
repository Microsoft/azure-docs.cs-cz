---
title: Rychlý Start – připojení počítačů k Azure pomocí ARC Azure pro servery – portál
description: V tomto rychlém startu se dozvíte, jak připojit počítače k Azure pomocí ARC Azure pro servery z portálu.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, konfigurace požadovaného stavu, Správa aktualizací, sledování změn, inventarizace, Runbooky, Python, grafický, hybridní a integrovaný
ms.date: 08/25/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 2ae7c8545286baebc83077276e356cd2e41f0dc3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73668682"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---portal"></a>Rychlý Start: připojení počítačů k Azure pomocí ARC Azure pro servery – portál

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Přečtěte si téma Podporované klienty a požadovaná konfigurace sítě v článku [Přehled služby Azure ARC pro servery](overview.md).

## <a name="generate-the-agent-install-script-using-the-azure-portal"></a>Vygenerujte skript pro instalaci agenta pomocí Azure Portal

1. Spustit [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)
1. Klikněte na **+ Přidat** .
1. Postupujte podle pokynů průvodce k dokončení
1. Poslední stránka obsahuje skript, který můžete zkopírovat (nebo stáhnout).

Skript musí být spuštěn na cílovém počítači, který chcete připojit. Stáhne agenta, nainstaluje ho a připojí počítač jako jedinou operaci.

Na serverech mimo Azure, které chcete spravovat:

1. Přihlášení k serveru (Vzdálená komunikace pomocí SSH, RDP nebo PowerShellu)
1. Spuštění prostředí: bash v systému Linux, PowerShell jako správce ve Windows
1. Vložte skript z portálu a spusťte ho na serveru, aby se mohl připojit k Azure.
1. Výchozí ověřování pro registraci jednotlivého serveru je *interaktivní* pomocí Azure "login zařízení. Po spuštění skriptu se zobrazí zpráva podobná této:

  ```none
  To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B3V3NLWRF to authenticate.
  ```
  
   Otevřete prohlížeč a zadejte kód, který chcete ověřit. Na serveru, na kterém se připojujete, nemusí být spuštěný prohlížeč, může to být na jiném počítači, jako je třeba přenosný počítač.

1. Pokud chcete neinteraktivně ověřit službu, postupujte podle kroků v části [Vytvoření instančního objektu](quickstart-onboard-powershell.md#create-a-service-principal-for-onboarding-at-scale) a upravte skript vygenerovaný z portálu.

> [!NOTE]
> Pokud při prvním přihlášení používáte Internet Explorer na serveru, dojde k chybě. Můžete jednoduše znovu otevřít prohlížeč a znovu ho provést.

## <a name="execute-the-script-on-target-nodes"></a>Spustit skript na cílových uzlech

Přihlaste se ke každému uzlu a spusťte skript, který jste vygenerovali z portálu. Po úspěšném dokončení skriptu přejdete do Azure Portal ověřte, že se server úspěšně připojil.

![Úspěšné zprovoznění](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Vyčištění

Pokud chcete odpojit počítač od Arc Azure pro servery, musíte provést dva kroky.

1. Vyberte počítač na [portálu](https://aka.ms/hybridmachineportal), klikněte na tlačítko se třemi tečkami (`...`) a vyberte **Odstranit**.
1. Odinstalujte agenta z počítače.

   V systému Windows můžete k odinstalaci agenta použít ovládací panel aplikace & funkce.
  
  ![Aplikace & funkce](./media/quickstart-onboard/apps-and-features.png)

   Pokud chcete odinstalaci skriptovat, můžete použít následující příklad, který načte **PackageId** a odinstaluje agenta pomocí `msiexec /X`.

   Podívejte se do klíče registru `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` a vyhledejte **PackageId**. Pak můžete agenta odinstalovat pomocí `msiexec`.

   Následující příklad ukazuje odinstalaci agenta.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   V systému Linux spusťte následující příkaz k odinstalaci agenta.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přiřazení zásady k připojeným počítačům](../../governance/policy/assign-policy-portal.md)