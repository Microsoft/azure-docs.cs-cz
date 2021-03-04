---
title: Řešení potíží se stavem hosta virtuálních počítačů (Preview)
description: Popisuje postup řešení potíží, který můžete provést v případě problémů se stavem pro virtuální počítače s přehledem.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 6ae0a9e20d6aad7200729419ece333d80e652c3c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703915"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>Řešení potíží se stavem hosta virtuálních počítačů (Preview)
Tento článek popisuje kroky, které můžete provést, když máte problémy se stavem pro virtuální počítače s přehledem.

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

## <a name="next-steps"></a>Další kroky

- [Přehled funkce stavu hosta v rámci virtuálních počítačů Insights](vminsights-health-overview.md)