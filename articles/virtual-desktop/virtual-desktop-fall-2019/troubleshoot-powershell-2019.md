---
title: Virtuální počítač s Windows (klasický) PowerShell – Azure
description: Řešení potíží s PowerShellem při nastavování klientského prostředí pro virtuální počítače s Windows (Classic)
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 08c9f7a6c5f8fd4a51c464018438bf6e7db119fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374894"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>PowerShell pro virtuální počítače s Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../troubleshoot-powershell.md).

Pomocí tohoto článku můžete vyřešit chyby a problémy při používání PowerShellu s virtuálním počítačem s Windows. Další informace o PowerShellu služby Vzdálená plocha najdete v tématu [prostředí PowerShell pro virtuální počítače s Windows](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Příkazy prostředí PowerShell použité při instalaci virtuálních počítačů s Windows

Tato část obsahuje seznam příkazů PowerShellu, které se obvykle používají při nastavování virtuální plochy Windows, a poskytuje možnosti pro řešení problémů, ke kterým může dojít při jejich používání.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Chyba: Add-RdsAppGroupUser příkaz--zadaná hodnota UserPrincipalName je již přiřazena ke skupině aplikací RemoteApp v zadaném fondu hostitelů.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Příčina:** Použité uživatelské jméno již bylo přiřazeno do skupiny aplikací jiného typu. Uživatele nelze přiřadit ke vzdálené ploše a skupině vzdálených aplikací v rámci stejného fondu hostitelů relace.

**Oprava:** Pokud uživatel potřebuje obě vzdálené aplikace i Vzdálená plocha, vytvořte různé fondy hostitelů nebo udělte uživatelům přístup ke vzdálené ploše. Tím umožníte použití jakékoli aplikace na virtuálním počítači hostitele relace.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Chyba: Add-RdsAppGroupUser příkaz--zadaná hodnota UserPrincipalName neexistuje v Azure Active Directory přidružená k tenantovi vzdálené plochy.

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Příčina:** Uživatele zadaného parametrem-UserPrincipalName nelze nalézt v Azure Active Directory vázaných na klienta virtuální plochy systému Windows.

**Oprava:** Potvrďte položky v následujícím seznamu.

- Uživatel je synchronizován na Azure Active Directory.
- Uživatel není spojený s obchodem s B2C (Business to Consumer) ani B2B (Business-to-Business).
- Tenant virtuálních počítačů s Windows je vázaný na správnou Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel nemá oprávnění k dotazování služby správy

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Příčina:** -parametr tenant

**Oprava:** Vystavení Get-RdsDiagnosticActivities s-tenant \<TenantName> .

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel nemá oprávnění k dotazování služby správy

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Příčina:** Přepínač-Deployment

**Oprava:** – přepínač nasazení může být používán pouze správci nasazení. Tito správci jsou obvykle členy týmu vzdálené plochy služby Vzdálená plocha nebo Windows. Nahraďte přepínač-Deployment parametrem-tenant \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: New-RdsRoleAssignment – uživatel nemá oprávnění k dotazování služby správy

**Příčina 1:** Použitý účet nemá oprávnění vlastníka služby Vzdálená plocha pro tenanta.

**Oprava 1:** Uživatel s oprávněním vlastníka vzdálené plochy musí provést přiřazení role.

**Příčina 2:** Použitý účet má oprávnění vlastníka vzdálené plochy, ale není součástí Azure Active Directory klienta nebo nemá oprávnění k dotazování Azure Active Directory, kde se uživatel nachází.

**Oprava 2:** Uživatel s oprávněním služby Active Directory musí provést přiřazení role.

>[!Note]
>New-RdsRoleAssignment nemůže udělit oprávnění uživateli, který neexistuje v Azure Active Directory (AD).

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview-2019.md).
- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues-2019.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration-2019.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection-2019.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](../troubleshoot-client.md) .
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup-2019.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../../azure-resource-manager/templates/deployment-history.md).
