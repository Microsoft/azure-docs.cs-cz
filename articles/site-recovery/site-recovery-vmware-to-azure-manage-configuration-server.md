---
title: "Spravovat konfigurační server pro obnovení po havárii VMware s Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje, jak spravovat existující konfigurační server pro obnovení po havárii VMware do Azure s Azure Site Recovery."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 80426b076481aaf2001644498da8cdce6f0311ab
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Spravovat konfigurační server pro virtuální počítače VMware

Můžete nastavení konfigurace serveru místní, při použití [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii virtuálních počítačů VMware a fyzické servery do Azure. Konfigurační server koordinuje komunikaci mezi místními VMware a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy konfigurační server po jeho nasazení.


## <a name="modify-vmware-settings"></a>Upravit nastavení VMware

Upravte nastavení pro server VMware, ke kterému se připojí konfigurační server.

1. Přihlaste se k počítači s konfigurační server.
2. Spusťte Azure Site Recovery Configuration Manageru z zástupce na ploše. Můžete také otevřít [tento odkaz](https://configuration-server-name/IP:44315).
3. Vyberte **spravovat server ESXi Server vSPhere vCenter**, a poté proveďte následující:

    * Chcete-li přidružit jiný server VMware konfigurační server, vyberte **server přidat vCenter Server vSphere ESXi**. Zadejte podrobnosti serveru.

    * Chcete-li aktualizovat pověření použitá k připojení k serveru VMware pro automatické zjišťování virtuálních počítačů VMware, vyberte **upravit**. Zadejte nové přihlašovací údaje a potom vyberte **OK**.

    ![Upravit VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Upravit přihlašovací údaje pro instalaci služby Mobility

Změňte pověření používaná k automatické instalaci služby Mobility na virtuální počítače VMware, povolíte pro replikaci.

1. Přihlaste se k počítači s konfigurační server.
2. Spusťte Správce konfigurace obnovení lokality ze zástupce na ploše. Můžete také otevřít [tento odkaz](https://configuration-server-name/IP:44315).
3. Vyberte **spravovat virtuální počítač pověření**a zadejte nové přihlašovací údaje. Potom vyberte **OK** se aktualizovat nastavení.

    ![Upravit přihlašovací údaje služby Mobility](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Upravte nastavení proxy serveru

Upravte nastavení proxy serveru používá počítačem konfigurace serveru pro přístup k Internetu do Azure. Pokud máte počítač serveru proces kromě výchozí server proces spuštěn na serveru, konfigurace, změňte nastavení na obou počítačích.

1. Přihlaste se k počítači s konfigurační server.
2. Spusťte Správce konfigurace obnovení lokality ze zástupce na ploše. Můžete také otevřít [tento odkaz](https://configuration-server-name/IP:44315).
3. Vyberte **spravovat připojení**a aktualizujte hodnoty proxy. Potom vyberte **Uložit** se aktualizovat nastavení.

## <a name="add-a-network-adapter"></a>Přidání síťového adaptéru

Otevřete virtualizace formát OVF () šablony nasadí konfigurační server virtuálních počítačů s jedním síťovým adaptérem. Můžete [přidat další adaptér k virtuálnímu počítači)](how-to-deploy-configuration-server.md#add-an-additional-adapter), ale je potřeba jej přidat před zaregistrujte konfigurační server v trezoru.

Chcete-li přidat adaptér po zaregistrujte konfigurační server v trezoru, přidáte adaptér ve vlastnostech virtuálního počítače. Pak zaregistrujte server v trezoru.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Znovu zaregistrujte konfigurační server v ke stejnému trezoru

Pokud potřebujete, můžete zaregistrujte server konfigurace v ke stejnému trezoru. Pokud máte server další proces počítač kromě výchozí proces serveru spuštěna v počítači konfigurace serveru, znovu zaregistrujte obou počítačů.


  1. V úložišti, otevřete **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**.
  2. V **servery**, vyberte **stáhnout registrační klíč** ke stažení souboru s přihlašovacími údaji.
  3. Přihlaste se k počítači konfigurace serveru.
  4. V **%ProgramData%\ASR\home\svagent\bin**, otevřete **cspsconfigtool.exe**.
  5. Na **registrace trezoru** vyberte **Procházet** a vyhledejte soubor s přihlašovacími údaji jste si stáhli.
  6. V případě potřeby zadejte podrobnosti o serveru proxy. Potom vyberte **Zaregistrovat**.
  7. Otevřete příkazové okno prostředí PowerShell správce a spusťte následující příkaz:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Upgrade na konfiguračním serveru

Spuštění aktualizace kumulativní aktualizace konfigurace serveru. Aktualizace můžete použít pro až N-4 verze. Příklad:

- Pokud spustíte 9.7 9.8, 9.9 nebo 9.10, můžete upgradovat přímo do 9.11.
- Pokud spustíte 9,6 nebo starší a chcete provést upgrade na 9.11, je nutné nejprve upgradovat na verzi 9.7. před 9.11.

Odkazy na kumulativní aktualizace pro upgrade na všechny verze serveru konfigurace jsou k dispozici v [wiki aktualizace stránky](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade serveru následujícím způsobem:

1. Stáhněte si soubor instalačního programu aktualizace na konfiguračním serveru.
2. Dvakrát klikněte na panel spusťte instalační program.
3. Instalační program zjistí aktuální verze na počítači spuštěna.
4. Vyberte **OK** potvrďte a spustíte upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Odstranit nebo zrušit registraci konfigurační server

1. Zakázat [zakažte](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v rámci konfigurace serveru.
2. [Zrušit přidružení](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) a [odstranit](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) všechny zásady replikace z konfiguračního serveru.
3. [Odstranit](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) všichni hostitelé vCenter vSphere servery, které jsou spojeny s konfiguračním serverem.
4. V úložišti, otevřete **infrastruktura Site Recovery** > **konfigurační servery**.
5. Vyberte konfigurační server, který chcete odebrat. Potom na **podrobnosti** vyberte **odstranit**.

    ![Odstraňte konfigurační server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Odstranit pomocí prostředí PowerShell

Volitelně můžete odstranit konfigurační server pomocí prostředí PowerShell.

1. [Nainstalujte](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) modulu Azure PowerShell.
2. Přihlaste se k účtu Azure pomocí tohoto příkazu:
    
    `Login-AzureRmAccount`
3. Vyberte předplatné trezoru.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Nastavte kontext úložiště.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Načtěte konfigurační server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstraňte konfigurační server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Můžete použít **-Force** v AzureRmSiteRecoveryFabric odebrat možnost pro Vynucené odstranění konfigurační server.
 


## <a name="renew-ssl-certificates"></a>Obnovení certifikátů SSL

Konfigurační server má integrované webový server, která orchestruje činnosti služby Mobility, proces servery a hlavních cílových serverů, které jsou k němu připojená. Webový server používá certifikát SSL k ověřování klientů. Platnost certifikátu vyprší po tři roky a můžete obnovit kdykoli.

### <a name="check-expiry"></a>Kontrola vypršení platnosti

Pro nasazení serveru konfigurace před může 2016 vypršení platnosti certifikátu byla nastavena na jeden rok. Pokud máte certifikát, který vyprší, dojde k následující položky:

- Když datum vypršení platnosti je dvou měsíců nebo méně, spuštění služby odesílání oznámení na portálu a e-mailem (Pokud se přihlášení k odběru oznámení Site Recovery).
- Na stránce prostředků úložiště se zobrazí nápis informující o oznámení. Další informace vyberte informační zprávě.
- Pokud se zobrazí **upgradovat nyní** tlačítko, znamená to, že některé součásti ve vašem prostředí nebyly aktualizovány na verzi 9.4.xxxx.x nebo vyšší verze. Upgradujte součásti nástroje před obnovováním certifikátu. Nelze obnovit na starší verze.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V úložišti, otevřete **infrastruktura Site Recovery** > **konfigurační Server**. Vyberte požadované konfigurační server.
2. Datum vypršení platnosti se zobrazí pod **stav konfigurace serveru**.
3. Vyberte **obnovení certifikátů**. 


## <a name="next-steps"></a>Další postup

Přečtěte si podrobné pokyny pro nastavení zotavení po havárii [virtuální počítače VMware](tutorial-vmware-to-azure.md) do Azure.
