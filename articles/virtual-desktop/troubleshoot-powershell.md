---
title: Prostředí PowerShell pro virtuální počítače s Windows – Azure
description: Řešení potíží s PowerShellem při nastavování prostředí klienta virtuální plochy Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 73fd9a989c72cb33baccefdfa512f34de5bb88bd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876735"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell pro Windows Virtual Desktop

Pomocí tohoto článku můžete vyřešit chyby a problémy při používání PowerShellu s virtuálním počítačem s Windows. Další informace o PowerShellu služby Vzdálená plocha najdete v tématu [prostředí PowerShell pro virtuální počítače s Windows](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

V současné době nepřijímáme případy podpory, ale virtuální počítač s Windows je ve verzi Preview. Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Příkazy prostředí PowerShell použité při instalaci virtuálních počítačů s Windows

Tato část obsahuje seznam příkazů PowerShellu, které se obvykle používají při nastavování virtuální plochy Windows, a poskytuje možnosti pro řešení problémů, ke kterým může dojít při jejich používání.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Chyba: Příkaz Add-RdsAppGroupUser – zadaná hodnota UserPrincipalName je již přiřazena ke skupině aplikací RemoteApp v zadaném fondu hostitelů.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Způsobit** Použité uživatelské jméno již bylo přiřazeno do skupiny aplikací jiného typu. Uživatele nelze přiřadit ke vzdálené ploše a skupině vzdálených aplikací v rámci stejného fondu hostitelů relace.

**Opravit** Pokud uživatel potřebuje obě vzdálené aplikace i Vzdálená plocha, vytvořte různé fondy hostitelů nebo udělte uživatelům přístup ke vzdálené ploše. Tím umožníte použití jakékoli aplikace na virtuálním počítači hostitele relace.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Chyba: Příkaz Add-RdsAppGroupUser – zadaná hodnota UserPrincipalName neexistuje v Azure Active Directory přidružených k tenantovi vzdálené plochy.

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Způsobit** Uživatele zadaného parametrem-UserPrincipalName nelze nalézt v Azure Active Directory vázaných na klienta virtuální plochy systému Windows.

**Opravit** Potvrďte položky v následujícím seznamu.

- Uživatel je synchronizován na Azure Active Directory.
- Uživatel není spojený s obchodem s B2C (Business to Consumer) ani B2B (Business-to-Business).
- Tenant virtuálních počítačů s Windows je vázaný na správnou Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities--User není autorizován pro dotazování služby správy.

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Příčina:** -parametr tenant

**Opravit** Vydejte příkaz Get-RdsDiagnosticActivities s- \<tenant tenant >.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel nemá oprávnění k dotazování služby správy.

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Způsobit** Přepínač-Deployment

**Oprava:** – přepínač nasazení může být používán pouze správci nasazení. Tito správci jsou obvykle členy týmu vzdálené plochy služby Vzdálená plocha nebo Windows. Nahraďte přepínač-Deployment parametrem-tenant \<>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: New-RdsRoleAssignment--uživatel nemá oprávnění k dotazování na službu správy.

**Příčina 1:** Použitý účet nemá oprávnění vlastníka služby Vzdálená plocha pro tenanta.

**Oprava 1:** Uživatel s oprávněním vlastníka vzdálené plochy musí provést přiřazení role.

**Příčina 2:** Použitý účet má oprávnění vlastníka vzdálené plochy, ale není součástí Azure Active Directory klienta nebo nemá oprávnění k dotazování Azure Active Directory, kde se uživatel nachází.

**Oprava 2:** Uživatel s oprávněním služby Active Directory musí provést přiřazení role.

>[!Note]
>Příkaz New-RdsRoleAssignment nemůže udělit oprávnění uživateli, který neexistuje v Azure Active Directory (AD).

## <a name="next-steps"></a>Další postup

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta k virtuální ploše Windows najdete v tématu [připojení klientů vzdálené plochy](troubleshoot-client-connection.md).
- Další informace o službě verze Preview najdete v tématu [prostředí verze Preview pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Postup při řešení potíží najdete v [kurzu: Řešení potíží s nasazeními](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)šablon Správce prostředků.
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
