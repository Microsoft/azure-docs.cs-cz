---
title: Prostředí PowerShell pro virtuální počítače s Windows – Azure
description: Řešení potíží s PowerShellem při nastavování prostředí virtuálních počítačů s Windows
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367618"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell pro Windows Virtual Desktop

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md).

Pomocí tohoto článku můžete vyřešit chyby a problémy při používání PowerShellu s virtuálním počítačem s Windows. Další informace o PowerShellu služby Vzdálená plocha najdete v tématu [prostředí PowerShell pro virtuální počítače s Windows](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Příkazy prostředí PowerShell použité při instalaci virtuálních počítačů s Windows

Tato část obsahuje seznam příkazů PowerShellu, které se obvykle používají při nastavování virtuální plochy Windows, a poskytuje možnosti pro řešení problémů, ke kterým může dojít při jejich používání.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>Chyba: New-AzRoleAssignment: poskytnuté informace nejsou namapovány na ID objektu služby AD.

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**Příčina:** Uživatele zadaného parametrem *-SignInName* nelze nalézt v Azure Active Directory vázaných na prostředí virtuálních počítačů s Windows.

**Oprava:** Ujistěte se, že máte následující věci.

- Uživatel by měl být synchronizovaný Azure Active Directory.
- Uživatel by neměl být vázaný na obchod B2C (Business-to-Consumer) ani B2B (Business-to-Business).
- Prostředí virtuálních počítačů s Windows by mělo být vázané na správné Azure Active Directory.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>Chyba: New-AzRoleAssignment: "klient s ID objektu nemá autorizaci k provedení akce nad oborem (kód: AuthorizationFailed).

**Příčina 1:** Účet, který používáte, nemá oprávnění vlastníka k tomuto předplatnému.

**Oprava 1:** Uživatel s oprávněním vlastníka musí spustit přiřazení role. Případně musí být uživatel přiřazen k roli správce přístupu uživatele, aby mohl přiřadit uživatele ke skupině aplikací.

**Příčina 2:** Použitý účet má oprávnění vlastníka, ale není součástí Azure Active Directory prostředí nebo nemá oprávnění k dotazování Azure Active Directory, kde se uživatel nachází.

**Oprava 2:** Uživatel s oprávněním služby Active Directory musí provést přiřazení role.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>Chyba: New-AzWvdHostPool--umístění není k dispozici pro typ prostředku.

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

Příčina: virtuální plocha Windows podporuje výběr umístění fondů hostitelů, skupin aplikací a pracovních prostorů k ukládání metadat služby v určitých umístěních. Možnosti jsou omezeny na místo, kde je tato funkce k dispozici. Tato chyba znamená, že tato funkce není k dispozici v umístění, které jste zvolili.

Oprava: v chybové zprávě se zveřejní seznam podporovaných oblastí. Místo toho použijte jednu z podporovaných oblastí.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>Chyba: New-AzWvdApplicationGroup musí být ve stejném umístění jako fond hostitelů.

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**Příčina:** Došlo k neshodě umístění. Všechny fondy hostitelů, skupiny aplikací a pracovní prostory mají umístění pro ukládání metadat služby. Všechny objekty, které vytvoříte, se musí nacházet ve stejném umístění. Pokud je například fond hostitelů v nástroji, musíte `eastus` také vytvořit skupiny aplikací v nástroji `eastus` . Pokud vytváříte pracovní prostor pro registraci těchto skupin aplikací do, musí být tento pracovní prostor `eastus` také v.

**Oprava:** Načtěte umístění, ve kterém byl fond hostitelů vytvořen, a potom přiřaďte skupinu aplikací, kterou vytváříte, do stejného umístění.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení potíží při nastavování prostředí virtuálních počítačů s Windows a fondů hostitelů najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md) .
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
