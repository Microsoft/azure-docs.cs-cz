---
title: Windows virtuální plochy PowerShell – Azure
description: Řešení potíží s prostředím PowerShell při nastavování prostředí virtuálního klienta Windows tenanta.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927504"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows virtuální plochy prostředí PowerShell

Použijte tento článek vyřešit chyby a problémy při použití Powershellu s virtuální plochy Windows. Další informace o Powershellu služby vzdálené plochy najdete v tématu [Windows Powershellu virtuální plochy](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Příkazy prostředí PowerShell použít během instalace virtuálního klienta Windows

Tato část obsahuje seznam příkazů prostředí PowerShell, které se běžně používají při nastavování virtuální plochy Windows a poskytuje způsoby, jak vyřešit problémy, které mohou nastat při jejich používání.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Chyba: Příkaz Přidat RdsAppGroupUser--zadaný atribut UserPrincipalName je už přiřazený k skupinu aplikace RemoteApp v zadaném fondu hostitele

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Příčina:** Uživatelské jméno používané byla již přiřazena do jiného typu skupiny aplikací. Uživatele nelze přiřadit oba do skupiny Vzdálená plocha a Vzdálená aplikace v rámci stejného fondu hostitele relace.

**Oprava:** Pokud uživatel potřebuje vzdálené aplikace a vzdálené plochy, vytvořte fondy jiného hostitele nebo udělit uživatelům přístup k vzdálené plochy, která umožní použití libovolné aplikace na virtuální počítač hostitele relace.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Chyba: Příkaz Přidat RdsAppGroupUser – zadaným UserPrincipalName neexistuje v Azure Active Directory přidružené k tenantovi vzdálené plochy

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**Příčina:** Uživatel určený parametrem - UserPrincipalName nebyl nalezen v Azure Active Directory, které jsou vázány na tenanta virtuální plochy Windows.

**Oprava:** Zkontrolujte položky v následujícím seznamu.

- Uživatel je synchronizovat do Azure Active Directory.
- Uživatel není vázaný na a zákazníky (B2C) nebo obchodování business-to-business (B2B).
- Virtuální plochy Windows tenanta se váže na správné Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – Uživatel nemá oprávnění k dotazování na službu správy

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Příčina:** parametr - TenantName

**Oprava:** Vydávání s - TenantName Get-RdsDiagnosticActivities <TenantName>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel nemá oprávnění k dotazování na službu správy

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Příčina:** Pomocí parametru - nasazení přepínače.

**Oprava:** – nasazení přepínač lze použít pouze správci nasazení. Tito správci jsou obvykle členové týmu vzdálené plochy virtuální služby nebo Windows Desktop. Nahraďte přepínač-nasazení - TenantName <TenantName>.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Nové – RdsRoleAssignment – uživatel nemá oprávnění k dotazování na službu správy

**1. příčina:** Použitý účet nemá oprávnění vlastníka služby Vzdálená plocha v tenantovi.

**Oprava 1:** Uživatel s oprávněními vlastníka služby Vzdálená plocha je potřeba provést přiřazení role.

**2. příčina:** Použitý účet má oprávnění vlastníka služby Vzdálená plocha, ale není součástí vašeho tenanta Azure Active Directory nebo nemá oprávnění k dotazování Azure Active Directory, kde se uživatel zdržuje.

**Oprava 2:** Uživatel s oprávnění služby Active Directory je potřeba provést přiřazení role.

>[!Note]
>Nové RdsRoleAssignment nelze udělit oprávnění pro uživatele, který neexistuje v Azure Active Directory (AD).

## <a name="next-steps"></a>Další postup

- Přehled o řešení potíží virtuální plochy Windows a sleduje eskalace, naleznete v tématu [řešení potíží s přehled, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Řešení potíží při vytváření fondu tenanta a hostitele v prostředí virtuálního klienta Windows, naleznete v tématu [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md).
- Řešení potíží při konfiguraci virtuálního počítače (VM) v virtuální plochy Windows, naleznete v tématu [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Řešení potíží s připojeními klientů virtuální plochy Windows, naleznete v tématu [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Absolvovat kurz řešení potíží, najdete v článku [kurzu: Řešení potíží s nasazení šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).