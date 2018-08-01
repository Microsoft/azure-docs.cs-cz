---
title: Správa konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat existující konfigurační server pro zotavení po havárii VMware do Azure pomocí Azure Site RecoveryS.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346116"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Správa konfiguračního serveru pro virtuální počítače VMware

Nastavíte místní konfigurační server, když použijete [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místní VMware a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy serveru konfigurace po nasazení.



## <a name="modify-vmware-settings"></a>Úprava nastavení VMware

Konfigurační server je možné otevřít následujícím způsobem:
    - Přihlaste se k virtuálnímu počítači, na kterém je nasazená a spustit Azure Site Recovery Configuration Manageru ze zástupce na ploše.
    - Alternativně můžete přístup ke konfigurační server vzdáleně z **https://*ConfigurationServerName*/:44315 /**. Přihlaste se pomocí přihlašovacích údajů správce.
   
### <a name="modify-vmware-server-settings"></a>Úprava nastavení serveru VMware

1. Chcete-li přiřadit jinému serveru VMware s konfiguračním serverem, po přihlášení, vyberte **přidat vCenter serveru nebo serveru vSphere ESXi**.
2. Zadejte podrobnosti a pak vyberte **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Upravit přihlašovací údaje pro automatické zjišťování

1. Chcete-li aktualizovat přihlašovací údaje použité pro připojení k serveru VMware pro automatické zjišťování virtuálních počítačů VMware po přihlášení, vyberte **upravit**.
2. Zadejte nové přihlašovací údaje a pak vyberte **OK**.

    ![Upravit VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Upravit přihlašovací údaje pro instalaci služby Mobility

Upravte přihlašovací údaje použité pro automatickou instalaci služby Mobility na virtuální počítače VMware pro replikaci povolíte.

1. Po přihlášení vyberte **spravovat přihlašovací údaje virtuálního počítače**
2. Zadejte nové přihlašovací údaje a pak vyberte **OK**.

    ![Upravit přihlašovací údaje služby Mobility](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Upravit nastavení proxy serveru

Upravte nastavení proxy serveru konfigurace počítače serveru pro internetový přístup k Azure. Pokud máte počítače s procesu serverem kromě výchozí procesový server běží na počítači serveru konfigurace, upravte nastavení v obou počítačích.

1. Po přihlášení ke konfiguračnímu serveru, vyberte **spravovat připojení**.
2. Aktualizujte hodnoty proxy. Potom vyberte **Uložit** a aktualizujte nastavení.

## <a name="add-a-network-adapter"></a>Přidání síťového adaptéru

Formát OVF (Open Virtualization) šablona nasadí konfigurační server virtuálního počítače s jedním síťovým adaptérem.

- Je možné [přidání dalšího adaptéru k virtuálnímu počítači](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale je nutné přidat předtím, než konfigurační server zaregistrujete v trezoru.
- Chcete-li přidat adaptér po registraci konfiguračního serveru v trezoru, přidáte adaptér ve vlastnostech virtuálního počítače. Pak budete muset znovu zaregistrujte server v trezoru.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Znovu zaregistrujte konfigurační server ve stejném trezoru

Pokud je potřeba, můžete znovu zaregistrujte konfigurační server ve stejném trezoru. Pokud máte počítače serveru s další proces, kromě výchozí procesový server běží na počítači konfigurační server, zaregistrujte ho znovu oba počítače.


  1. V trezoru, otevřete **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**.
  2. V **servery**vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.
  3. Přihlaste se k počítači konfigurační server.
  4. V **%ProgramData%\ASR\home\svsystems\bin**, otevřete **cspsconfigtool.exe**.
  5. Na **registrace trezoru** kartu, vyberte možnost **Procházet**a vyhledejte souboru s přihlašovacími údaji, který jste stáhli.
  6. V případě potřeby zadejte podrobnosti o serveru proxy. Potom vyberte **Zaregistrovat**.
  7. Otevřete okno příkazového prostředí PowerShell správce a spusťte následující příkaz:
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >Za účelem **o přijetí změn nejnovější certifikáty** z konfiguračního serveru pro horizontální navýšení kapacity procesového serveru spusťte příkaz *"< Drive\Microsoft instalace Azure Recovery\agent\cdpcli.exe lokality >"--registermt*

  8. Nakonec spuštěním následujícího příkazu restartujte obengine.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault – název <name of your vault> Set AzureRmSiteRecoveryVaultSettings - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
