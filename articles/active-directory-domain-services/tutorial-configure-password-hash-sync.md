---
title: Povolení synchronizace hash hesel pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: V tomto kurzu se dozvíte, jak povolit synchronizaci hodnot hash hesel pomocí azure ad připojení do spravované domény Služby Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: 93e5ee9b46fb3387b70dd5092f72efcaa8a2bc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239165"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Kurz: Povolení synchronizace hesel ve službě Azure Active Directory Domain Services pro hybridní prostředí

Pro hybridní prostředí lze klienta Azure Active Directory (Azure AD) nakonfigurovat tak, aby se synchronizoval s místním prostředím služby AD DS (AD DS) pomocí služby Azure AD Connect. Ve výchozím nastavení Azure AD Connect nesynchronizuje starší NT LAN Manager (NTLM) a hodnoty hash hesla kerberos, které jsou potřeba pro Azure Active Directory Domain Services (Azure AD DS).

Chcete-li používat Azure AD DS s účty synchronizovanými z místního prostředí služby AD DS, je třeba nakonfigurovat Azure AD Connect pro synchronizaci těchto hodnot hash hesel požadovaných pro ověřování NTLM a Kerberos. Po nakonfigurované Azure AD Connect, místní vytvoření účtu nebo změny hesla událost také také synchronizuje starší heslo hashe s Azure AD.

Tyto kroky nemusíte provádět, pokud používáte účty pouze pro cloud bez místního prostředí služby AD DS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Proč jsou potřeba starší hash hesel NTLM a Kerberos
> * Jak nakonfigurovat starší synchronizaci hodnot hash hesla pro Azure AD Connect

Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující zdroje:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, který je synchronizovaný s místním adresářem pomocí Služby Azure AD Connect.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
    * V případě potřeby [povolte synchronizaci hash hesel službou Azure AD Connect][enable-azure-ad-connect].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby [vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronizace hash hesla pomocí služby Azure AD Connect

Azure AD Connect se používá k synchronizaci objektů, jako jsou uživatelské účty a skupiny z místního prostředí Služby AD DS do klienta Azure AD. V rámci procesu synchronizace hodnot hash hesla umožňuje účtům používat stejné heslo v prostředí služby AD DS on-prem a ve službě Azure AD.

K ověření uživatelů ve spravované doméně potřebuje služba Azure AD DS hodnotu hash hesel ve formátu, který je vhodný pro ověřování protokolech NTLM a Kerberos. Azure AD neukládá hodnotu hashe hesla ve formátu, který je vyžadován pro ověřování NTLM nebo Kerberos, dokud nepovolíte Azure AD DS pro vašeho tenanta. Z bezpečnostních důvodů Azure AD také neukládá žádné přihlašovací údaje hesla ve formě prostého textu. Azure AD proto nemůže automaticky generovat tyto hashe ntlm nebo kerberos hesla na základě existujících přihlašovacích údajů uživatelů.

Azure AD Connect lze nakonfigurovat tak, aby synchronizovat požadované hashe hesla NTLM nebo Kerberos pro Azure AD DS. Ujistěte se, že jste dokončili kroky k [povolení synchronizace hash hesel Azure AD Connect pro heslo][enable-azure-ad-connect]. Pokud jste měli existující instanci Azure AD Connect, [stáhněte a aktualizujte na nejnovější verzi][azure-ad-connect-download] a ujistěte se, že můžete synchronizovat starší hodnotu hash hesel pro NTLM a Kerberos. Tato funkce není k dispozici v raných verzích Služby Azure AD Connect nebo s nástrojem DirSync starší verze. Je vyžadována verze Azure AD Connect *verze 1.1.614.0* nebo novější.

> [!IMPORTANT]
> Azure AD Connect by se měl nainstalovat a nakonfiguroval jenom pro synchronizaci s místními prostředími služby AD DS. Není podporováno instalace Azure AD Connect ve spravované doméně Azure AD DS k synchronizaci objektů zpět do Azure AD.

## <a name="enable-synchronization-of-password-hashes"></a>Povolit synchronizaci hodnot hashe hesel

S nainstalovanou a nakonfigurovanou službou Azure AD pro synchronizaci se službou Azure AD teď nakonfigurujte synchronizaci hash staršíverze hesla pro NTLM a Kerberos. Skript Prostředí PowerShell se používá ke konfiguraci požadovaných nastavení a potom spustit úplnou synchronizaci hesel do Azure AD. Po dokončení procesu synchronizace hash hesel služby Azure AD Connect se uživatelé můžou přihlásit k aplikacím prostřednictvím služby Azure AD DS, které používají starší hodnotu hash hesel NTLM nebo Kerberos.

1. V počítači s nainstalovaným Azure AD Connect otevřete v nabídce Start službu **Synchronizace připojení Azure AD > Connect**.
1. Vyberte kartu **Konektory.** Informace o připojení, které slouží k vytvoření synchronizace mezi místním prostředím služby AD DS a službou Azure AD, jsou uvedeny.

    **Typ** označuje buď *Windows Azure Active Directory (Microsoft)* pro konektor Azure AD nebo *Služby Active Directory Domain Services* pro místní konektor Služby AD DS. Poznamenejte si názvy spojnic, které se mají použít ve skriptu Prostředí PowerShell v dalším kroku.

    ![Seznam názvů konektorů ve Správci synchronizačních služeb](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    V tomto příkladu obrazovky se používají následující konektory:

    * Konektor Azure AD s názvem *aaddscontoso.onmicrosoft.com – AAD*
    * Místní konektor AD DS se jmenuje *onprem.contoso.com*

1. Zkopírujte a vložte následující skript PowerShellu do počítače s nainstalovaným Azure AD Connect. Skript aktivuje úplnou synchronizaci hesel, která obsahuje starší hodnoty hashe hesla. Aktualizujte `$azureadConnector` `$adConnector` a proměnné s názvy spojnic z předchozího kroku.

    Spusťte tento skript v každé doménové struktuře služby AD a synchronizujte místní účet NTLM a kerberos heslové hodnoty hash do azure ad.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    
    Import-Module "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    V závislosti na velikosti adresáře z hlediska počtu účtů a skupin může synchronizace starších hodnot hash hesla do služby Azure AD nějakou dobu trvat. Hesla se pak synchronizují se spravovanou doménou Azure AD DS po synchronizaci se službou Azure AD.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Proč jsou potřeba starší hash hesel NTLM a Kerberos
> * Jak nakonfigurovat starší synchronizaci hodnot hash hesla pro Azure AD Connect

> [!div class="nextstepaction"]
> [Zjistěte, jak funguje synchronizace ve spravované doméně služby Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
