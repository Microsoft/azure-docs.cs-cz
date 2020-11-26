---
title: Řešení potíží s Azure Monitor pro virtuální počítačem stavem hosta (Preview)
description: Popisuje kroky, které můžete provést při potížích s Azure Monitor pro virtuální počítače stavem.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 2aa83a861697882127c75eb696807f604c3061f0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184548"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Řešení potíží s Azure Monitor pro virtuální počítačem stavem hosta (Preview)
Tento článek popisuje kroky, které můžete provést při potížích s Azure Monitor pro virtuální počítače stavem.

## <a name="error-message-that-no-data-is-available"></a>Chybová zpráva, že nejsou k dispozici žádná data 

![Žádná data](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Ověřte, že virtuální počítač splňuje požadavky na konfiguraci.

1. Ověřte, jestli je virtuální počítač virtuálním počítačem Azure. Azure ARC pro servery se momentálně nepodporuje.
2. Ověřte, že virtuální počítač běží na [podporovaném operačním systému](vminsights-health-enable.md?current-limitations.md).
3. Ověřte, že je virtuální počítač nainstalovaný v [podporované oblasti](vminsights-health-enable.md?current-limitations.md).
4. Ověřte, že je pracovní prostor Log Analytics nainstalovaný v [podporované oblasti](vminsights-health-enable.md?current-limitations.md).

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Ověřte, jestli je virtuální počítač správně připojený.
Ověřte, že se na virtuálním počítači úspěšně zřídilo rozšíření agenta Azure Monitor a Agent stavu virtuálního počítače hosta. V Azure Portal v nabídce virtuálního počítače vyberte **rozšíření** . Pokud jsou uvedeni dva agenti, klikněte na odkaz []() .

![Rozšíření virtuálních počítačů](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Ověřte, že je na virtuálním počítači povolená identita přiřazená systémem.
Ověřte, jestli je na virtuálním počítači povolená identita přiřazená systémem. Z nabídky virtuálního počítače v Azure Portal vyberte **Identita** . Pokud jsou uvedeni dva agenti, klikněte na odkaz []() .

![Identita přiřazená systémem](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Ověřit pravidlo shromažďování dat
Ověřte, že pravidlo shromažďování dat určující rozšíření stavu jako zdroj dat je přidružené k virtuálnímu počítači.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Chybová zpráva pro chybný požadavek z důvodu nedostatečných oprávnění
Tato chyba označuje, že v předplatném nebyl zaregistrován poskytovatel prostředků **Microsoft. monitor zátěže byl** . Podrobnosti o registraci tohoto poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Chybný požadavek](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Další kroky

- [Získejte přehled o funkci stavu hosta Azure Monitor pro virtuální počítače](vminsights-health-overview.md)