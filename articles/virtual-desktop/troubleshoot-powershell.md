---
title: Prostředí Windows Virtual Desktop PowerShell – Azure
description: Řešení problémů s PowerShellem při nastavování prostředí klienta Virtuální plochy Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127467"
---
# <a name="windows-virtual-desktop-powershell"></a>PowerShell pro Windows Virtual Desktop

Tento článek slouží k řešení chyb a problémů při používání PowerShellu s virtuální plochou Windows. Další informace o prostředí PowerShell služby Vzdálená plocha naleznete v tématu [Windows Virtual Desktop Powershell](/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu virtuálníplochy windows a](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) prodiskutujte službu Windows Virtual Desktop s produktovým týmem a aktivními členy komunity.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Příkazy prostředí PowerShell používané při instalaci virtuální plochy systému Windows

V této části jsou uvedeny příkazy prostředí PowerShell, které se obvykle používají při nastavování virtuální plochy systému Windows, a způsoby řešení problémů, ke kterým může při jejich používání dojít.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Chyba: Příkaz Add-RdsAppGroupUser – Zadaný userprincipalname je již přiřazen skupině aplikací RemoteApp v určeném fondu hostitelů.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Příčina:** Použité uživatelské jméno již bylo přiřazeno skupině aplikací jiného typu. Uživatelé nemohou být přiřazeni ke vzdálené ploše i skupině vzdálených aplikací v rámci stejného fondu hostitelů relací.

**Oprava:** Pokud uživatel potřebuje vzdálené aplikace i vzdálenou plochu, vytvořte různé fondy hostitelů nebo udělte uživateli přístup ke vzdálené ploše, což umožní použití libovolné aplikace na hostitelském virtuálním počítači relace.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Chyba: Příkaz Add-RdsAppGroupUser – zadané uživatelské jméno principalname neexistuje ve službě Azure Active Directory přidružené k tenantovi vzdálené plochy.

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Příčina:** Uživatele určeného parametrem -UserPrincipalName nelze najít ve službě Azure Active Directory vázané na klienta virtuální plochy Windows.

**Oprava:** Potvrďte položky v následujícím seznamu.

- Uživatel je synchronizován se službou Azure Active Directory.
- Uživatel není vázán na podnikání na spotřebitele (B2C) nebo business-to-business (B2B) commerce.
- Klient virtuální plochy Windows je vázaný na opravu služby Azure Active Directory.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel není oprávněn dotazovat se na službu správy.

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Příčina:** Parametr -TenantName

**Oprava:** Problém Get-RdsDiagnosticActivities s \<-TenantName Název_>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: Get-RdsDiagnosticActivities – uživatel není oprávněn dotazovat se na službu správy.

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Příčina:** Použití přepínače -Deployment.

**Oprava:** -Přepínač nasazení mohou používat pouze správci nasazení. Tito správci jsou obvykle členy týmu Služby vzdálené plochy nebo virtuální plochy systému Windows. Nahraďte přepínač -Deployment \<> -TenantName TenantName.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Chyba: New-RdsRoleAssignment -- uživatel není oprávněn dotazovat služby pro správu

**Příčina 1:** Používaný účet nemá oprávnění vlastníka služby Vzdálená plocha pro klienta.

**Oprava 1:** Uživatel s oprávněními vlastníka služby Vzdálená plocha musí provést přiřazení role.

**Příčina 2:** Používaný účet má oprávnění vlastníka služby Vzdálená plocha, ale není součástí služby Azure Active Directory klienta nebo nemá oprávnění k dotazování služby Azure Active Directory, kde se uživatel nachází.

**Oprava 2:** Uživatel s oprávněními služby Active Directory musí provést přiřazení role.

>[!Note]
>New-RdsRoleAssignment nemůže udělit oprávnění uživateli, který neexistuje ve službě Azure Active Directory (AD).

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při vytváření fondu klientů a hostitelů v prostředí Virtuální plochy systému Windows najdete v [tématu Vytvoření klientského a hostitelského fondu](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta Virtuální plocha systému Windows naleznete v [tématu Připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md).
- Informace o řešení problémů s klienty vzdálené plochy naleznete [v tématu Poradce při potížích s klientem Vzdálené plochy.](troubleshoot-client.md)
- Další informace o službě naleznete v tématu [Windows Virtual Desktop environment](environment-setup.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o auditování akcí naleznete v [tématu Audit operace se Správcem prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Informace o akcích k určení chyb během nasazení najdete v [tématu Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
