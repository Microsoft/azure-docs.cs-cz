---
title: Správa konfiguračního serveru pro zotavení po havárii VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak spravovat existující konfigurační server pro zotavení po havárii VMware do Azure pomocí Azure Site RecoveryS.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 1d2f194eb6a2186fc1e8451a7022d26cd1013bb2
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022392"
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
  ```

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru pomocí jiného trezoru

> [!WARNING]
> Následující krok zruší přidružení konfiguračního serveru z aktuálního úložiště a zastaví se replikace všech chráněných virtuálních počítačů v rámci konfigurace serveru.

1. Přihlaste se ke konfiguračnímu serveru.
2. Otevřete okno příkazového prostředí PowerShell správce a spusťte následující příkaz:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Spusťte portál pro zařízení prohlížeče konfigurační server pomocí zástupce na ploše.
4. Proveďte kroky registrace, podobně jako na nový server konfigurace [registrace](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Upgradujte konfigurační server

Spuštění kumulativní aktualizace se aktualizovat konfigurační server. Aktualizace můžete použít pro až N-4 verze. Příklad:

- Pokud spustíte 9.7, 9.8, 9.9 nebo 9.10, můžete upgradovat přímo na 9.11.
- Pokud chcete provést upgrade 9.11 spustíte 9,6 nebo starší, musíte nejprve upgradovat na verzi 9.7. před 9.11.

Odkazy na kumulativní aktualizace pro upgrade pro všechny verze konfiguračního serveru jsou k dispozici v [aktualizace wikistránka](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade serveru následujícím způsobem:

1. V trezoru, přejděte na **spravovat** > **infrastruktura Site Recovery** > **konfigurační servery**.
2. Pokud je k dispozici aktualizace, odkaz se zobrazí v **verze agenta** > sloupce.
    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update2.png)
3. Stáhněte si instalační soubor aktualizace ke konfiguračnímu serveru.

    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dvakrát klikněte na panel spusťte instalační program.
5. Instalační program zjistí aktuální verze, která běží na počítači. Klikněte na tlačítko **Ano** spusťte upgrade.
6. Po dokončení upgradu ověřuje se konfigurace serveru.

    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update3.png)

7. Klikněte na tlačítko **Dokončit** zavřete Instalační služby.

## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v rámci konfigurace serveru.
2. [Zrušit přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranit](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všechny zásady replikace z konfiguračního serveru.
3. [Odstranit](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všichni hostitelé vCenter servery pro/vSphere, které jsou spojeny s konfiguračním serverem.
4. V trezoru, otevřete **infrastruktura Site Recovery** > **konfigurační servery**.
5. Vyberte konfigurační server, který chcete odebrat. Potom na **podrobnosti** stránce **odstranit**.

    ![Odstranění konfiguračního serveru](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Odstranit pomocí Powershellu

Volitelně můžete odstranit konfigurační server pomocí prostředí PowerShell.

1. [Nainstalujte](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) modulu Azure PowerShell.
2. Přihlaste se ke svému účtu Azure pomocí tohoto příkazu:

    `Connect-AzureRmAccount`
3. Vyberte předplatné trezoru.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Nastavte kontext trezoru.

    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Načtení konfiguračního serveru.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstraňte konfigurační server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Můžete použít **– platnost** možnost odebrat AzureRmSiteRecoveryFabric pro Vynucené odstranění konfiguračního serveru.

## <a name="generate-configuration-server-passphrase"></a>Vygenerovat heslo konfiguračního serveru

1. Přihlaste se ke konfiguračnímu serveru a pak otevřete okno příkazového řádku jako správce.
2. Chcete-li změnit adresář do složky bin, spusťte příkaz **cd %ProgramData%\ASR\home\svsystems\bin**
3. Ke generování souboru s heslem, spusťte **genpassphrase.exe - v > MobSvc.passphrase**.
4. Vaše heslo se uloží do souboru v umístění **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Prodloužit platnost certifikátů SSL

Konfigurační server má integrované webový server, která orchestruje činnosti služby Mobility, procesových serverů a hlavní cílové servery, které jsou k němu připojená. Webový server používá certifikát SSL k ověřování klientů. Certifikát vyprší po uplynutí tří let a jde ji obnovit v každém okamžiku.

### <a name="check-expiry"></a>Kontrola vypršení platnosti

Pro nasazení serveru konfigurace před. května 2016 vypršení platnosti certifikátu byla nastavená na jeden rok. Pokud máte certifikát, který vyprší, se stane toto:

- Pokud datum vypršení platnosti je po dobu dvou měsíců nebo méně, spuštění služby odesílání oznámení z portálu a e-mailem (je-li připojila ke Site Recovery oznámení).
- Na stránce prostředků trezoru se zobrazí banner s oznámením. Další informace vyberte informační zprávu.
- Pokud se zobrazí **upgradovat** tlačítko, znamená to, že některé součásti ve vašem prostředí nebyly aktualizovány na verzi 9.4.xxxx.x nebo vyšší verze. Tyto součásti Upgradujte před obnovováním certifikátu. Nejde obnovit ke starším verzím.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V trezoru, otevřete **infrastruktura Site Recovery** > **konfigurační Server**. Vyberte požadované konfigurační server.
2. Datum vypršení platnosti se zobrazí v části **stav konfigurace serveru**.
3. Vyberte **prodloužit platnost certifikátů**.

## <a name="update-windows-licence"></a>Update Windows licence

Licence pomocí šablony OVF k dispozici je zkušební licenci, která je platná po dobu 180 dnů. Pro použití bez přerušení je nutné aktivovat Windows s licencí opatřené.

## <a name="failback-requirements"></a>Požadavky na navrácení služeb po obnovení

Během operace opětovného zapnutí ochrany a navrácení služeb po obnovení musí být místní konfigurační server spuštěn a v připojeném stavu. Pro úspěšné navrácení služeb po obnovení musí existovat virtuální počítač se při navrácení služeb obnoví databázi konfiguračního serveru.

Ujistěte se, že provedete pravidelných naplánovaných záloh konfiguračního serveru. Pokud dojde k havárii a dojde ke ztrátě konfigurační server, musíte obnovit ze záložní kopie konfigurační server a zkontrolujte, zda obnovené konfigurační server má stejnou IP adresu, pomocí kterého se zaregistrují do trezoru. Navrácení služeb po obnovení nebude fungovat, pokud se používá jinou IP adresu pro obnovený konfigurační server.

## <a name="next-steps"></a>Další postup

Přečtěte si podrobné pokyny pro nastavení zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.
