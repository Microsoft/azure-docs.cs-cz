---
title: Povolit synchronizaci hodnot hash hesel pro Azure AD Domain Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak povolit synchronizaci hodnot hash hesel pomocí Azure AD Connect do Azure Active Directory Domain Services spravované domény.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: e83d8941d1be7fd36f53a881a21716252ad01954
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618344"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Kurz: povolení synchronizace hesel v Azure Active Directory Domain Services pro hybridní prostředí

U hybridních prostředí je možné nakonfigurovat klienta Azure Active Directory (Azure AD) na synchronizaci s místním prostředím Active Directory Domain Services (služba AD DS) pomocí Azure AD Connect. Ve výchozím nastavení Azure AD Connect nesynchronizuje starší verze systému NT LAN Manager (NTLM) a hodnoty hash hesla protokolu Kerberos, které jsou potřeba pro Azure Active Directory Domain Services (Azure služba AD DS).

Pokud chcete používat Azure služba AD DS s účty synchronizovanými z místního prostředí služba AD DS, je nutné nakonfigurovat Azure AD Connect pro synchronizaci hodnot hash hesel vyžadovaných pro ověřování protokolem NTLM a protokolem Kerberos. Po nakonfigurování Azure AD Connect dojde k místnímu vytvoření účtu nebo události změny hesla a pak se do Azure AD synchronizují starší hodnoty hash hesel.

Pokud používáte jenom cloudové účty bez místního prostředí služba AD DS nebo pokud používáte *doménovou strukturu prostředků*, nemusíte tyto kroky provádět. U spravovaných domén, které používají doménovou strukturu prostředků, se hodnoty hash místních hesel nikdy nesynchronizují. Ověřování pro místní účty používají vztahy důvěryhodnosti v doménové struktuře zpátky na vlastní služba AD DS řadiče domény.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Proč jsou nutné starší hodnoty hash hesla NTLM a Kerberos
> * Postup konfigurace synchronizace hodnot hash zastaralých hesel pro Azure AD Connect

Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující zdroje:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, který se synchronizuje s místním adresářem pomocí Azure AD Connect.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
    * V případě potřeby [povolte Azure AD Connect pro synchronizaci hodnot hash hesel][enable-azure-ad-connect].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Synchronizace hodnot hash hesel pomocí Azure AD Connect

Azure AD Connect slouží k synchronizaci objektů, jako jsou uživatelské účty a skupiny z místního prostředí služba AD DS do tenanta Azure AD. V rámci procesu umožňuje synchronizace hodnot hash hesel, aby účty používaly stejné heslo v prostředí Prem služba AD DS a v Azure AD.

K ověřování uživatelů ve spravované doméně služba AD DS Azure potřebuje hodnoty hash hesel ve formátu, který je vhodný pro ověřování protokolem NTLM a protokolem Kerberos. Služba Azure AD neukládá hodnoty hash hesel ve formátu požadovaném pro ověřování protokolem NTLM nebo Kerberos, dokud u svého tenanta nepovolíte službu Azure služba AD DS. Z bezpečnostních důvodů Azure AD také neukládá přihlašovací údaje hesla ve formě nešifrovaných textů. Proto služba Azure AD nemůže automaticky generovat tyto hodnoty hash hesla NTLM nebo Kerberos na základě stávajících přihlašovacích údajů uživatelů.

Azure AD Connect se dá nakonfigurovat tak, aby synchronizoval požadované hodnoty hash hesla protokolu NTLM nebo Kerberos pro Azure služba AD DS. Ujistěte se, že jste dokončili kroky pro [povolení Azure AD Connect pro synchronizaci hodnot hash hesel][enable-azure-ad-connect]. Pokud jste již existující instanci Azure AD Connect, [Stáhněte a aktualizujte nejnovější verzi][azure-ad-connect-download] , abyste se ujistili, že je možné synchronizovat hodnoty hash hesel starší verze pro protokol NTLM a Kerberos. Tato funkce není k dispozici v počátečních verzích Azure AD Connect nebo pomocí starší verze nástroje DirSync. Vyžaduje se Azure AD Connect verze *1.1.614.0* nebo novější.

> [!IMPORTANT]
> Azure AD Connect by měl být nainstalovaný a nakonfigurovaný jenom pro synchronizaci s místními služba AD DS prostředími. Pro synchronizaci objektů zpět do Azure AD se nepodporuje instalace Azure AD Connect ve spravované doméně Azure služba AD DS.

## <a name="enable-synchronization-of-password-hashes"></a>Povolit synchronizaci hodnot hash hesel

Když je služba Azure AD Connect nainstalovaná a nakonfigurovaná pro synchronizaci se službou Azure AD, teď nakonfigurujte starší synchronizaci hodnot hash hesel pro NTLM a Kerberos. Pomocí skriptu PowerShellu se nakonfigurují požadovaná nastavení a pak se spustí Úplná synchronizace hesel do Azure AD. Když je proces synchronizace hodnot hash hesel Azure AD Connect dokončený, můžou se uživatelé přihlásit k aplikacím prostřednictvím Azure služba AD DS, které používají starší hodnoty hash hesla NTLM nebo Kerberos.

1. V počítači s nainstalovanou Azure AD Connect v nabídce Start otevřete **synchronizační službu Azure AD Connect >**.
1. Vyberte kartu **konektory** . Zobrazí se informace o připojení, které slouží k navázání synchronizace mezi místním prostředím služba AD DS a službou Azure AD.

    **Typ** označuje buď *Windows Azure Active Directory (Microsoft)* pro konektor Azure AD nebo *Active Directory Domain Services* pro konektor on-premises služba AD DS. Poznamenejte si názvy konektorů, které se ve skriptu PowerShellu použijí v dalším kroku.

    ![Vypíše názvy konektorů v Service Manager synchronizace.](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    V tomto ukázkovém snímku obrazovky se používají následující konektory:

    * Konektor Azure AD má název *contoso.onmicrosoft.com-AAD* .
    * Konektor on-premises služba AD DS má název *OnPrem.contoso.com* .

1. Zkopírujte následující skript prostředí PowerShell a vložte ho do počítače s nainstalovaným Azure AD Connect. Skript spustí úplnou synchronizaci hesla, která zahrnuje starší hodnoty hash hesel. Aktualizujte `$azureadConnector` `$adConnector` proměnné a pomocí názvů konektorů z předchozího kroku.

    Spuštěním tohoto skriptu v každé doménové struktuře AD synchronizujete hodnoty hash hesla protokolu NTLM a Kerberos do služby Azure AD.

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

    V závislosti na velikosti adresáře na základě počtu účtů a skupin může synchronizace hodnot hash starších hesel do služby Azure AD nějakou dobu trvat. Hesla se pak po synchronizaci do služby Azure AD synchronizují se spravovanými doménami.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Proč jsou nutné starší hodnoty hash hesla NTLM a Kerberos
> * Postup konfigurace synchronizace hodnot hash zastaralých hesel pro Azure AD Connect

> [!div class="nextstepaction"]
> [Informace o tom, jak synchronizace funguje ve spravované doméně Azure AD Domain Services](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
