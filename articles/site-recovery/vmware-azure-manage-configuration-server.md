---
title: Správa konfiguračního serveru pro zotavení po havárii pomocí Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 93b10d56ae34ebdfe78dd20705634dea58721274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257274"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Správa konfiguračního serveru pro zotavení po havárii virtuálního počítače VMware nebo fyzického serveru

Místní konfigurační server nastavíte, když do Azure azure [site recovery](site-recovery-overview.md) použijete pro zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místním vmware a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy pro správu konfiguračního serveru po jeho nasazení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Aktualizovat licenci systému Windows

Licence dodávaná se šablonou OVF je zkušební licence platná po dobu 180 dnů. Pro nepřetržité používání je nutné aktivovat systém Windows s obstaránou licencí. Aktualizaci licence lze provést prostřednictvím samostatného klíče nebo standardního klíče SLUŽBY KMS. Pokyny jsou k dispozici na [příkazovém řádku systému DISM pro spuštění operačního systému](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Chcete-li získat klíče, naleznete [v nastavení klienta SLUŽBY SPRÁVY KLÍČŮ](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Přístup konfigurační server

Konfigurační server můžete přistupovat následujícím způsobem:

* Přihlaste se k virtuálnímu počítači, na kterém se nasazuje, a spusťte **Azure Site Recovery Configuration Manager** z zástupce plochy.
* Případně můžete přistupovat k konfiguračnímu serveru vzdáleně z https://*ConfigurationServerName*/:44315/. Přihlaste se pomocí přihlašovacích údajů správce.

## <a name="modify-vmware-server-settings"></a>Změna nastavení serveru VMware

1. Chcete-li přidružit jiný server VMware ke konfiguračnímu serveru, vyberte po [přihlášení](#access-configuration-server)možnost **Přidat server vCenter Server/server vSphere ESXi**.
2. Zadejte podrobnosti a pak vyberte **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Úprava přihlašovacích údajů pro automatické zjišťování

1. Chcete-li aktualizovat pověření použitá pro připojení k serveru VMware pro automatické zjišťování virtuálních měn VMware, zvolte po [přihlášení](#access-configuration-server)účet a klepněte na tlačítko **Upravit**.
2. Zadejte nová pověření a pak vyberte **OK**.

    ![Úprava vmware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Pověření můžete také upravit prostřednictvím souboru CSPSConfigtool.exe.

1. Přihlášení ke konfiguračnímu serveru a spuštění souboru CSPSConfigtool.exe
2. Vyberte účet, který chcete upravit, a klepněte na tlačítko **Upravit**.
3. Zadejte upravená pověření a klepněte na **tlačítko Ok.**

## <a name="modify-credentials-for-mobility-service-installation"></a>Úprava přihlašovacích údajů pro instalaci služby Mobility Service

Upravte pověření používaná k automatické instalaci služby Mobility service na virtuální chod v zařízení VMware, které povolíte pro replikaci.

1. Po [přihlášení](#access-configuration-server)vyberte **Spravovat přihlašovací údaje virtuálního počítače.**
2. Vyberte účet, který chcete upravit, a klepněte na **tlačítko Upravit.**
3. Zadejte nová pověření a pak vyberte **OK**.

    ![Úprava přihlašovacích údajů služby Mobility Service](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Pověření můžete také upravit prostřednictvím souboru CSPSConfigtool.exe.

1. Přihlaste se ke konfiguračnímu serveru a spusťte soubor CSPSConfigtool.exe
2. Vyberte účet, který chcete upravit, a klepněte na **tlačítko Upravit.**
3. Zadejte nová pověření a klepněte na tlačítko **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Přidání přihlašovacích údajů pro instalaci služby Mobility

Pokud jste při nasazení konfiguračního serveru OVF nepřidali přihlašovací údaje,

1. Po [přihlášení](#access-configuration-server)vyberte **Spravovat přihlašovací údaje virtuálního počítače**.
2. Klikněte na **Přidat přihlašovací údaje virtuálního počítače**.
    ![přihlašovací údaje k mobilitě doplňků](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Zadejte nová pověření a klikněte na **Přidat**.

Můžete také přidat pověření prostřednictvím souboru CSPSConfigtool.exe.

1. Přihlaste se ke konfiguračnímu serveru a spusťte soubor CSPSConfigtool.exe
2. Klepněte na tlačítko **Přidat**, zadejte nová pověření a klepněte na tlačítko **Ok**.

## <a name="modify-proxy-settings"></a>Změna nastavení proxy serveru

Upravte nastavení proxy serveru používané počítačem konfiguračního serveru pro přístup k Internetu do Azure. Pokud máte kromě výchozího procesního serveru spuštěný v konfiguračním serveru počítač i procesní server, upravte nastavení v obou počítačích.

1. Po [přihlášení](#access-configuration-server) ke konfiguračnímu serveru vyberte **Spravovat připojení**.
2. Aktualizujte hodnoty proxy serveru. Pak vyberte **Uložit,** chcete-li aktualizovat nastavení.

## <a name="add-a-network-adapter"></a>Přidání síťového adaptéru

Šablona Open Virtualization Format (OVF) nasazuje virtuální počítač konfiguračního serveru s jedním síťovým adaptérem.

- Do [virtuálního počítače](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)můžete přidat další adaptér , ale musíte ho přidat před registrací konfiguračního serveru v úložišti.
- Chcete-li přidat adaptér po registraci konfiguračního serveru v úložišti, přidejte adaptér ve vlastnostech virtuálního počítače. Poté je třeba [znovu zaregistrovat](#reregister-a-configuration-server-in-the-same-vault) server v úschovně.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Opětovná registrace konfiguračního serveru ve stejném úložišti

Konfigurační server můžete znovu zaregistrovat ve stejném úložišti, pokud potřebujete. Pokud máte další procesový server ový počítač, kromě výchozího procesního serveru spuštěného v počítači konfiguračního serveru znovu zaregistrujte oba počítače.


1. V úschovně otevřete **položku Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. V **části Servery**vyberte **Stáhnout registrační klíč** a stáhněte soubor přihlašovacích údajů úschovny.
3. Přihlaste se k počítači konfiguračního serveru.
4. V **%ProgramData%\ASR\home\svsystems\bin**otevřete **soubor cspsconfigtool.exe**.
5. Na kartě **Registrace úložiště** vyberte **Procházet**a vyhledejte stažený soubor přihlašovacích údajů trezoru.
6. V případě potřeby zadejte podrobnosti o serveru proxy. Potom vyberte **Zaregistrovat**.
7. Otevřete okno příkazů PowerShell u správce a spusťte následující příkaz:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Chcete-li **získat nejnovější certifikáty** z konfiguračního serveru na horizontální navýšení kapacity procesního serveru, spusťte příkaz *Instalační\<jednotka\Obnovení webu Microsoft Azure\agent\cdpcli.exe>" --registermt*

8. Nakonec restartujte modul spuštěním následujícího příkazu.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru s jiným trezorem

> [!WARNING]
> Následující krok odpojí konfigurační server od aktuálního úložiště a replikace všech chráněných virtuálních počítačů pod konfiguračním serverem je zastavena.

1. Přihlaste se k konfiguračnímu serveru.
2. Otevřete okno příkazů PowerShell u správce a spusťte následující příkaz:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Spusťte portál prohlížeče konfiguračního serveru pomocí zástupce na ploše.
4. Proveďte kroky registrace podobné nové [registraci](vmware-azure-tutorial.md#register-the-configuration-server)konfiguračního serveru .

## <a name="upgrade-the-configuration-server"></a>Upgrade konfiguračního serveru

Spuštěním kumulativní aktualizace můžete aktualizovat konfigurační server. Aktualizace lze použít až pro verze N-4. Například:

- Pokud spustíte 9.7, 9.8, 9.9 nebo 9.10, můžete upgradovat přímo na 9.11.
- Pokud spustíte verzi 9.6 nebo starší a chcete upgradovat na verzi 9.11, musíte nejprve upgradovat na verzi 9.7. před 9.11.

Podrobné pokyny k prohlášení o podpoře součástí Azure Site Recovery naleznete [zde](https://aka.ms/asr_support_statement).
Odkazy na kumulativní aktualizaci pro upgrade na všechny verze konfiguračního serveru jsou k dispozici [zde](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> S každou novou verzí "N" součásti Azure Site Recovery, která je vydána, všechny verze pod "N-4" se považuje za mimo podporu. Vždy je vhodné upgradovat na nejnovější dostupné verze.</br>
> Podrobné pokyny k prohlášení o podpoře součástí Azure Site Recovery naleznete [zde](https://aka.ms/asr_support_statement).

Upgradujte server takto:

1. V úschovně přejděte na **spravovat** >  > **konfigurační servery infrastruktury****obnovení lokality**.
2. Pokud je k dispozici aktualizace, zobrazí se odkaz ve sloupci **> verze agenta.**
    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update2.png)
3. Stáhněte soubor instalačního programu aktualizace na konfigurační server.

    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update1.png)

4. Poklepáním spusťte instalační program.
5. Instalační program zjistí aktuální verzi spuštěnou v počítači. Klepnutím na tlačítko **Ano** spusťte upgrade.
6. Po dokončení upgradu se ověří konfigurace serveru.

    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update3.png)

7. Klepnutím na **tlačítko Dokončit** zavřete instalační program.
8. Chcete-li upgradovat zbytek součástí site recovery, naleznete v našich [pokynech k upgradu](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Upgrade konfiguračního serveru/procesního serveru z příkazového řádku

Instalační soubor spusťte následujícím způsobem:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Použití vzorku
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Název parametru| Typ | Popis| Hodnoty|
|-|-|-|-|
| /ServerMode|Požaduje se|Určuje, jestli se má nainstalovat konfigurační i procesový server, nebo jenom procesový server.|CS<br>PS|
|/InstallLocation|Požaduje se|Složka, ve které jsou nainstalované komponenty| Libovolná složka v počítači|
|/MySQLCredsFilePath|Požaduje se|Cesta k souboru, ve kterém jsou uložené přihlašovací údaje serveru MySQL|Soubor by měl být v níže uvedeném formátu.|
|/VaultCredsFilePath|Požaduje se|Cesta k souboru s přihlašovacími údaji trezoru|Platná cesta k souboru|
|/EnvType|Požaduje se|Typ prostředí, které chcete chránit |VMware<br>NonVMware|
|/PSIP|Požaduje se|IP adresa NIC, která se použije pro přenos dat replikace| Libovolná platná IP adresa|
|/CSIP|Požaduje se|IP adresa NIC, na které konfigurační server naslouchá| Libovolná platná IP adresa|
|/PassphraseFilePath|Požaduje se|Úplná cesta k umístění souboru s heslem|Platná cesta k souboru|
|/BypassProxy|Nepovinné|Určuje, že se konfigurační server připojí k Azure bez proxy serveru.|Tuto hodnotu získejte z Venu.|
|/ProxySettingsFilePath|Nepovinné|Nastavení proxy serveru (výchozí proxy server vyžaduje ověření, nebo vlastní proxy server)|Soubor by měl být v níže uvedeném formátu.|
|DataTransferSecurePort|Nepovinné|Číslo portu na PSIP, které se má použít pro data replikace| Platné číslo portu (výchozí hodnota je 9433)|
|/SkipSpaceCheck|Nepovinné|Přeskočí kontrolu místa na disku mezipaměti.| |
|/AcceptThirdpartyEULA|Požaduje se|Příznak značí přijetí smlouvy EULA třetích stran| |
|/ShowThirdpartyEULA|Nepovinné|Zobrazí smlouvy EULA třetích stran. Pokud je zadán jako vstup, všechny ostatní parametry budou ignorovány| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Vytvořit vstup souboru pro MYSQLCredsFilePath

Parametr MySQLCredsFilePath přebírá jako vstup soubor. Vytvořte soubor pomocí následujícího formátu a předajte jej jako vstupní parametr MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Vytvořit vstup souboru pro proxySettingsFilePath
Parametr ProxySettingsFilePath přebírá jako vstup soubor. Vytvořte soubor pomocí následujícího formátu a předajte jej jako vstupní parametr ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) všech virtuálních počítačů pod konfiguračním serverem.
2. [Zrušte přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstraňte](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všechny zásady replikace z konfiguračního serveru.
3. [Odstraňte](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všechny servery vCenter/hostitele vSphere, kteří jsou přidruženi k konfiguračnímu serveru.
4. V úschovně otevřete > **konfigurační servery infrastruktury** **obnovení lokality**.
5. Vyberte konfigurační server, který chcete odebrat. Potom na stránce **Podrobnosti** vyberte **Odstranit**.

    ![Odstranit konfigurační server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Odstranění pomocí PowerShellu

Konfigurační server můžete volitelně odstranit pomocí prostředí PowerShell.

1. [Nainstalujte](https://docs.microsoft.com/powershell/azure/install-Az-ps) modul Azure PowerShell.
2. Přihlaste se ke svému účtu Azure pomocí tohoto příkazu:

    `Connect-AzAccount`
3. Vyberte předplatné trezoru.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Nastavte kontext úschovny.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Načíst konfigurační server.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstraňte konfigurační server.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Můžete použít **možnost -Force** v Remove-AzSiteRecoveryFabric pro vynucené odstranění konfiguračního serveru.

## <a name="generate-configuration-server-passphrase"></a>Generovat přístupové heslo konfiguračního serveru

1. Přihlaste se ke konfiguračnímu serveru a otevřete okno příkazového řádku jako správce.
2. Chcete-li změnit adresář ve složce přihrádky, spusťte příkaz **CD %ProgramData%\ASR\home\svsystems\bin**
3. Chcete-li vygenerovat soubor heslem, spusťte **soubor genpassphrase.exe -v > přístupové heslo MobSvc..**
4. Heslo bude uloženo v souboru umístěném na adrese **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Prodloužení platnosti certifikátů SSL

Konfigurační server má vestavěný webový server, který organizuje aktivity služby mobility, procesních serverů a hlavních cílových serverů, které jsou k němu připojeny. Webový server používá k ověřování klientů certifikát SSL. Platnost certifikátu vyprší po třech letech a může být kdykoli obnovena.

### <a name="check-expiry"></a>Zkontrolovat vypršení platnosti

U nasazení konfiguračního serveru před květnem 2016 byla expirace certifikátu nastavena na jeden rok. Pokud máte certifikát, jehož platnost vyprší, dojde k následujícímu:

- Pokud je datum vypršení platnosti dva měsíce nebo méně, služba začne odesílat oznámení na portálu a e-mailem (pokud jste se přihlásili k odběru oznámení site recovery).
- Na stránce prostředků úschovny se zobrazí nápis s oznámením. Další informace získáte výběrem nápisu.
- Pokud se zobrazí tlačítko **Upgradovat,** znamená to, že některé součásti ve vašem prostředí nebyly upgradovány na verze 9.4.xxxx.x nebo vyšší. Před obnovením certifikátu inovujte součásti. Ve starších verzích nelze obnovit.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V úschovně otevřete server **Site Recovery Infrastructure** > **Configuration Server**. Vyberte požadovaný konfigurační server.
2. Datum vypršení platnosti se zobrazí v části **Stav konfiguračního serveru**.
3. Vyberte **možnost Obnovit certifikáty**.

## <a name="refresh-configuration-server"></a>Aktualizovat konfigurační server

1. Na webu Azure Portal přejděte na **úložiště služby Recovery** > Services Recovery Infrastructure > **pro** > obnovení**lokality****v systému VMware &** > **konfigurační servery pro fyzické počítače.**
2. Klikněte na konfigurační server, který chcete aktualizovat.
3. V okně s podrobnostmi o zvoleném konfiguračním serveru klepněte na tlačítko **Další** > **aktualizovat server**.
4. Sledujte průběh úlohy v části**Úlohy**obnovení **služby Vault** > **Obnovení** > webu .

## <a name="failback-requirements"></a>Požadavky na navrácení služeb po obnovení

Během opětovného ochrany a navrácení služeb po obnovení musí být místní konfigurační server spuštěn a v připojeném stavu. Pro úspěšné navrácení služeb po obnovení musí v databázi konfiguračního serveru existovat selhání virtuálního počítače.

Ujistěte se, že budete pravidelně plánovat zálohování konfiguračního serveru. Pokud dojde k havárii a dojde ke ztrátě konfiguračního serveru, musíte nejprve obnovit konfigurační server ze záložní kopie a zajistit, aby obnovený konfigurační server měl stejnou adresu IP, se kterou byl zaregistrován do úložiště. Obnovení po obnovení nebude fungovat, pokud je pro obnovený konfigurační server použita jiná adresa IP.

## <a name="next-steps"></a>Další kroky

Projděte si kurzy pro nastavení zotavení po havárii [virtuálních počítačích VMware](vmware-azure-tutorial.md) do Azure.
