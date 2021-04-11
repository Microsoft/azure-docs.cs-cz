---
title: Řešení potíží se stavem hosta virtuálních počítačů (Preview)
description: Popisuje postup řešení potíží, který můžete provést v případě problémů se stavem pro virtuální počítače s přehledem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 834c70e02ab25fa6dcadb5f6c997be09aaf5e353
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932773"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Řešení potíží se stavem hosta virtuálních počítačů (Preview)
Tento článek popisuje kroky, které můžete provést, když máte problémy se stavem pro virtuální počítače s přehledem.


## <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>Po upgradu stavu hosta se stále zobrazuje dostupná zpráva o upgradu. 

- Ověřte, že je virtuální počítač spuštěný v globálním Azure. Servery s podporou Arc se zatím nepodporují.
- Ověřte, jestli je virtuální počítač v oblasti a verze operačního systému podporovaný, jak je popsáno v tématu [Povolení stavu hosta Azure monitor pro virtuální počítače (Preview)](vminsights-health-enable.md).
- Ověřte, zda je rozšíření stavu hosta úspěšně nainstalováno s 0 ukončovacím kódem.
- Ověřte, zda je rozšíření agenta Azure Monitor úspěšně nainstalováno.
- Ověřte, jestli je pro virtuální počítač povolená spravovaná identita přiřazená systémem.
- Ověřte, že pro virtuální počítač nejsou zadané žádné spravované identity přiřazené uživatelem.
- Ověřte, jestli jsou virtuální počítače s Windows v *angličtině*. Agent Azure Monitor v tuto chvíli nepodporuje lokalizaci.
- Ověřte, že virtuální počítač nepoužívá síťový proxy server. Agent Azure Monitor v současné době nepodporuje proxy servery.
- Ověřte, zda byl Agent rozšíření stavu spuštěn bez chyb. Pokud se Agent nemůže spustit, může být stav agenta poškozený. Odstraňte obsah složky stavu agenta a restartujte agenta.
  - Pro Linux: démon je *vmGuestHealthAgent*. Stavová složka je */var/opt/vmGuestHealthAgent/**
  - Pro Windows: služba je *Agent stavu hosta virtuálního počítače*. Stavová složka _je \\ * %ProgramData%\Microsoft\VMGuestHealthAgent_.
- Ověřte, že agent Azure Monitor má síťové připojení. 
  - Z virtuálního počítače se pokuste o příkaz testation _<region> . Handler.Control.monitor.Azure.com_. Například pro virtuální počítač v westeurope se pokuste odeslat test pomocí testu pro _westeurope.Handler.Control.monitor.Azure.com:443_.
- Ověřte, jestli má virtuální počítač přidružení s pravidlem shromažďování dat ve stejné oblasti jako pracovní prostor Log Analytics.
  -  Chcete-li zajistit správnost struktury sady DCR, přečtěte si téma **Vytvoření pravidla shromažďování dat (DCR)** v tématu [Povolení Azure monitor pro virtuální počítačeho stavu hosta (Preview)](vminsights-health-enable.md) . Věnujte zvláštní pozornost *čítače výkonu* části zdroje dat, která je nastavená na ukázky tři čítače a přítomnost *inputDataSources* oddílu v konfiguraci rozšíření stavu pro odesílání čítačů do rozšíření.
-  Podívejte se na virtuální počítač s chybami rozšíření stavu hosta.
   -  Pro Linux: Zaregistrujte protokoly v _/var/log/Azure/Microsoft.Azure.monitor.VirtualMachines.GuestHealthLinuxAgent/*. log_.
   -  Pro Windows: Zaregistrujte protokoly v _C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.monitor.VirtualMachines.GuestHealthWindowsAgent \{ rozšíření verze} \* . log_.
-  Ověřte, zda virtuální počítač neobsahuje chyby agenta Azure Monitor.
   -  Pro Linux: Zaregistrujte protokoly na adrese _/var/log/MDSD. *_.
   -  Pro Windows: Zaregistrujte protokoly na adrese _C:\WindowsAzure\Resources \* {vmName}. AMADataStore_.
 



## <a name="error-message-that-no-data-is-available"></a>Chybová zpráva, že nejsou k dispozici žádná data 

![Žádná data](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Ověřte, že virtuální počítač splňuje požadavky na konfiguraci.

1. Ověřte, že je virtuální počítač virtuálním počítačem Azure. Azure Arc pro servery se v současné době nepodporuje.
2. Ověřte, že je na virtuálním počítači [podporovaný operační systém](vminsights-health-enable.md?current-limitations.md).
3. Ověřte, že je virtuální počítač nainstalovaný v [podporované oblasti](vminsights-health-enable.md?current-limitations.md).
4. Ověřte, že je pracovní prostor služby Log Analytics nainstalovaný v [podporované oblasti](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Ověřte, jestli je virtuální počítač správně připojený.
Ověřte, že se na virtuálním počítači úspěšně zřídilo rozšíření agenta Azure Monitor a Agent stavu virtuálního počítače hosta. V Azure Portal v nabídce virtuálního počítače vyberte **rozšíření** a ujistěte se, že jsou uvedené dva agenti.

![Rozšíření virtuálních počítačů](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Ověřte, že je na virtuálním počítači povolená identita přiřazená systémem.
Ověřte, jestli je na virtuálním počítači povolená identita přiřazená systémem. Z nabídky virtuálního počítače v Azure Portal vyberte **Identita** . Pokud je povolená identita spravované uživatelem bez ohledu na stav identity spravované systémem, Azure Monitor Agent nebude moct komunikovat se službou konfigurace a rozšíření stavu hosta nebude fungovat.

![Identita přiřazená systémem](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Ověřit pravidlo shromažďování dat
Ověřte, že pravidlo shromažďování dat určující rozšíření stavu jako zdroj dat je přidružené k virtuálnímu počítači.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Chybová zpráva pro chybný požadavek z důvodu nedostatečných oprávnění
Tato chyba označuje, že v předplatném nebyl zaregistrován poskytovatel prostředků **Microsoft. monitor zátěže byl** . Podrobnosti o registraci tohoto poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Chybný požadavek](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>Po povolení stavu hosta se zobrazuje stav "neznámé".

### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>Ověřte, že čítače výkonu v uzlech Windows fungují správně. 
Stav hosta spoléhá na to, že agent může shromažďovat čítače výkonu z uzlu. Základní sada knihoven čítače výkonu může být poškozena a může být nutné ji znovu sestavit. Postupujte podle pokynů v tématu [Ruční sestavení hodnot knihovny čítače výkonu](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values) a znovu sestavte čítače výkonu.





## <a name="next-steps"></a>Další kroky

- [Přehled funkce stavu hosta v rámci virtuálních počítačů Insights](vminsights-health-overview.md)