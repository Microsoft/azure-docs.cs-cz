---
title: Řešení potíží s Azure Monitor pro virtuální počítačem stavem hosta (Preview)
description: Popisuje kroky, které můžete provést při potížích s Azure Monitor pro virtuální počítače stavem.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: da8097341f8499be4e28fa37c06d963d057966ea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100609718"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Řešení potíží s Azure Monitor pro virtuální počítačem stavem hosta (Preview)
Tento článek popisuje kroky, které můžete provést při potížích s Azure Monitor pro virtuální počítače stavem.

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
Ověřte, jestli je na virtuálním počítači povolená identita přiřazená systémem. Z nabídky virtuálního počítače v Azure Portal vyberte **Identita** . 

![Identita přiřazená systémem](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Ověřit pravidlo shromažďování dat
Ověřte, že pravidlo shromažďování dat určující rozšíření stavu jako zdroj dat je přidružené k virtuálnímu počítači.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Chybová zpráva pro chybný požadavek z důvodu nedostatečných oprávnění
Tato chyba označuje, že v předplatném nebyl zaregistrován poskytovatel prostředků **Microsoft. monitor zátěže byl** . Podrobnosti o registraci tohoto poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) . 

![Chybný požadavek](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Další kroky

- [Získejte přehled o funkci stavu hosta Azure Monitor pro virtuální počítače](vminsights-health-overview.md)