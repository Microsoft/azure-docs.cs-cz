---
title: 'Azure AD Domain Services: Povolení synchronizace hesel | Dokumentace Microsoftu'
description: Začínáme se službou Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2017
ms.author: ergreenl
ms.openlocfilehash: 74ad811481aea83454d7e3179652e68d4c406521
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564965"
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Povolení synchronizace hesel do služby Azure Active Directory Domain Services
V předchozích úlohách jste povolili službu Azure Active Directory Domain Services pro tenanta služby Azure Active Directory (Azure AD). Další úlohou je povolení synchronizace hodnot hash přihlašovacích údajů požadovaných pro ověřování protokolů NT LAN Manager (NTLM) a Kerberos do služby Azure AD Domain Services. Po nastavení synchronizace přihlašovacích údajů se uživatelé mohou přihlásit ke spravované doméně s použitím podnikových přihlašovacích údajů.

Potřebný postup se liší pro uživatelské účty jenom cloudu a uživatelské účty synchronizované z místního adresáře pomocí služby Azure AD Connect.

| **Typ uživatelského účtu** | **Požadovaný postup** |
| --- | --- |
| **Uživatelské účty synchronizované z místního adresáře** |**&#x2713;** [Postupujte podle pokynů v tomto článku](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) |
| **Cloudové uživatelské účty vytvořené ve službě Azure AD** |**&#x2713;** [Synchronizujte hesla pro uživatelské účty jenom cloudu do spravované domény](active-directory-ds-getting-started-password-sync.md) |

> [!TIP]
> **Možná bude nutné dokončit oba postupy.**
> Pokud je ve vašem tenantovi služby Azure AD kombinace uživatelů jenom cloudu a uživatelů z místní služby AD, musíte dokončit oba postupy.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Úkol 5: Povolení synchronizace hesel do spravované domény pro uživatelské účty synchronizované s místní službou AD
Synchronizovaný tenant služby Azure AD je nastavený na synchronizaci s místním adresářem vaší organizace pomocí služby Azure AD Connect. Azure AD Connect ve výchozím nastavení do služby Azure AD nesynchronizuje hodnoty hash přihlašovacích údajů protokolů NTLM a Kerberos. Chcete-li použít službu Azure AD Domain Services, je potřeba nakonfigurovat službu Azure AD Connect na synchronizaci hodnot hash přihlašovacích údajů požadovaných pro ověřování protokolů NTLM a Kerberos. Následujícím postupem povolíte synchronizaci požadovaných hodnot hash přihlašovacích údajů z místního adresáře do tenanta služby Azure AD.

> [!NOTE]
> **Pokud jsou ve vaší organizaci uživatelské účty synchronizované z místního adresáře, musíte povolit synchronizaci hodnot hash protokolů NTLM a Kerberos, abyste mohli používat spravovanou doménu.** Synchronizovaný uživatelský účet je účet vytvořený v místním adresáři a synchronizovaný do tenanta služby Azure AD pomocí služby Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Instalace nebo aktualizace služby Azure AD Connect
Na počítač připojený k doméně nainstalujte nejnovější doporučenou verzi služby Azure AD Connect. Máte-li existující instanci instalace služby Azure AD Connect, je nutné ji aktualizovat tak, aby používala nejnovější verzi služby Azure AD Connect. Vždy používejte nejnovější verzi služby Azure AD Connect – předejdete tak známým problémům nebo chybám, které už pravděpodobně byly opraveny.

**[Stažení služby Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)**

Doporučená verze: **1.1.614.0** – publikováno 5. září 2017.

> [!WARNING]
> Chcete-li povolit synchronizaci starších verzí přihlašovacích hesel (požadovaných pro ověřování protokolů NTLM a Kerberos) do svého tenanta služby Azure AD, JE NUTNÉ nainstalovat nejnovější doporučenou verzi služby Azure AD Connect. V předchozích verzích služby Azure AD Connect ani u starší verze nástroje DirSync není tato funkce dostupná.
>
>

Pokyny k instalaci služby Azure AD Connect jsou dostupné v následujícím článku – [Začínáme se službou Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md).

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Povolení synchronizace hodnot hash přihlašovacích údajů protokolů NTLM a Kerberos do služby Azure AD
Pro každou doménovou strukturu služby AD spusťte následující skript PowerShellu. Tento skript povolí synchronizaci místních hodnot hash uživatelských hesel protokolů NTLM a Kerberos do tenanta služby Azure AD. Skript také zahájí úplnou synchronizaci ve službě Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
```

Synchronizace hodnot hash přihlašovacích údajů do služby Azure AD bude v závislosti na velikosti adresáře (počtu uživatelů, skupin atd.) chvíli trvat. Hesla bude možné použít ve spravované doméně služby Azure AD Domain Services zanedlouho poté, co se hodnoty hash přihlašovacích údajů synchronizují do služby Azure AD.

## <a name="related-content"></a>Související obsah
* [Povolení synchronizace hesel do služby AAD Domain Services u výhradně cloudového adresáře služby Azure AD](active-directory-ds-getting-started-password-sync.md)
* [Správa spravované domény služby Azure AD Domain Services](active-directory-ds-admin-guide-administer-domain.md)
* [Připojení virtuálního počítače s Windows k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Připojení virtuálního počítače se systémem Red Hat Enterprise Linux k spravované doméně služby Azure AD Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
