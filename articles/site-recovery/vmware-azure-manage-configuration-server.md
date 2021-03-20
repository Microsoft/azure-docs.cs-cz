---
title: Správa konfiguračního serveru pro zotavení po havárii pomocí Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 2f1edc14efdeaf70bf4c2acc0e31e1517753ed3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546343"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Správa konfiguračního serveru pro zotavení po havárii fyzických serverů nebo virtuálních počítačů VMware

Místní konfigurační server nastavíte při použití [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii virtuálních počítačů VMware a fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místními VMware a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy konfiguračního serveru po jeho nasazení.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Aktualizovat licenci Windows

Licence, která je součástí šablony OVF, je zkušební licence platná po dobu 180 dnů. V případě nepřerušovaného využití musíte aktivovat Windows pomocí předem získané licence. Aktualizace licencí se dá provést buď pomocí samostatného klíče, nebo standardního klíče služby správy klíčů. Doprovodné materiály k dispozici na [příkazovém řádku Windows DISM pro běžící operační](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options)systém. Pokud chcete získat klíče, přečtěte si téma [nastavení klienta služby správy klíčů](/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Přístup ke konfiguračnímu serveru

Ke konfiguračnímu serveru se dostanete takto:

* Přihlaste se k virtuálnímu počítači, na kterém je nasazený, a spusťte **Azure Site Recovery Configuration Manager** z zástupce na ploše.
* Případně můžete ke konfiguračnímu serveru přistupovat vzdáleně z https://*ConfigurationServerName*/: 44315/. Přihlaste se pomocí přihlašovacích údajů správce.

## <a name="modify-vmware-server-settings"></a>Úprava nastavení serveru VMware

1. Pokud chcete po [přihlášení](#access-configuration-server)přidružit jiný server VMware ke konfiguračnímu serveru, vyberte **Přidat vCenter Server/vSphere ESXi server**.
2. Zadejte podrobnosti a pak vyberte **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Upravit přihlašovací údaje pro automatické zjišťování

1. Pokud chcete aktualizovat přihlašovací údaje používané pro připojení k serveru VMware pro automatické zjišťování virtuálních počítačů VMware, po [přihlášení](#access-configuration-server)vyberte účet a klikněte na **Upravit**.
2. Zadejte nové přihlašovací údaje a pak vyberte **OK**.

    ![Úprava VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Přihlašovací údaje můžete také upravit prostřednictvím CSPSConfigtool.exe.

1. Přihlaste se ke konfiguračnímu serveru a spusťte CSPSConfigtool.exe
2. Vyberte účet, který chcete upravit, a klikněte na **Upravit**.
3. Zadejte upravené přihlašovací údaje a klikněte na **OK** .

## <a name="modify-credentials-for-mobility-service-installation"></a>Úprava přihlašovacích údajů pro instalaci služby mobility

Upravte přihlašovací údaje používané k automatické instalaci služby mobility na virtuální počítače VMware, které povolíte pro replikaci.

1. Po [přihlášení](#access-configuration-server)vyberte **spravovat přihlašovací údaje virtuálního počítače** .
2. Vyberte účet, který chcete upravit, a klikněte na **Upravit** .
3. Zadejte nové přihlašovací údaje a pak vyberte **OK**.

    ![Úprava přihlašovacích údajů služby mobility](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Přihlašovací údaje můžete také měnit prostřednictvím CSPSConfigtool.exe.

1. Přihlaste se ke konfiguračnímu serveru a spusťte CSPSConfigtool.exe
2. Vyberte účet, který chcete upravit, a klikněte na **Upravit** .
3. Zadejte nové přihlašovací údaje a klikněte na **OK**.

## <a name="add-credentials-for-mobility-service-installation"></a>Přidání přihlašovacích údajů pro instalaci služby mobility

Pokud jste při OVF nasazení konfiguračního serveru nenechali přidávat přihlašovací údaje,

1. Po [přihlášení](#access-configuration-server)vyberte **spravovat přihlašovací údaje virtuálního počítače**.
2. Klikněte na **Přidat přihlašovací údaje virtuálního počítače**.
    ![Snímek obrazovky se zobrazí v podokně spravovat přihlašovací údaje virtuálního počítače pomocí odkazu přidat přihlašovací údaje virtuálního počítače.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Zadejte nové přihlašovací údaje a klikněte na **Přidat**.

Přihlašovací údaje můžete také přidat prostřednictvím CSPSConfigtool.exe.

1. Přihlaste se ke konfiguračnímu serveru a spusťte CSPSConfigtool.exe
2. Klikněte na **Přidat**, zadejte nové přihlašovací údaje a klikněte na **OK**.

## <a name="modify-proxy-settings"></a>Úprava nastavení proxy serveru

Upravte nastavení proxy serveru používaného počítačem konfiguračního serveru pro přístup k Internetu do Azure. Pokud máte počítač procesového serveru kromě výchozího procesového serveru spuštěného na počítači konfiguračního serveru, upravte nastavení na obou počítačích.

1. Po [přihlášení](#access-configuration-server) ke konfiguračnímu serveru vyberte **Spravovat připojení**.
2. Aktualizujte hodnoty proxy serveru. Pak vyberte **Uložit** a aktualizujte nastavení.

## <a name="add-a-network-adapter"></a>Přidat síťový adaptér

Šablona Open Virtualization Format (OVF) nasadí virtuální počítač konfiguračního serveru s jedním síťovým adaptérem.

- [K virtuálnímu počítači můžete přidat další adaptér](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale musíte ho přidat před registrací konfiguračního serveru v trezoru.
- Pokud chcete po registraci konfiguračního serveru v trezoru přidat adaptér, přidejte ho do vlastností virtuálního počítače. Pak je potřeba [znovu zaregistrovat](#reregister-a-configuration-server-in-the-same-vault) Server v trezoru.

## <a name="how-to-renew-ssl-certificates"></a>Postup obnovení certifikátů SSL

Konfigurační server má integrovaný webový server, který orchestruje aktivity agentů mobility na všech chráněných počítačích, procesových serverech, které jsou na více instancích, a na hlavních cílových serverech, které jsou k němu připojené. Webový server používá k ověřování klientů certifikát SSL. Platnost certifikátu vyprší po třech letech a může být kdykoli obnovena.

### <a name="check-expiry"></a>Kontrolovat vypršení platnosti

Datum vypršení platnosti se zobrazí v části **stav konfiguračního serveru**. U nasazení konfiguračního serveru do května 2016 se platnost certifikátu nastavila na jeden rok. Pokud máte certifikát, jehož platnost vyprší, dojde k následujícímu:

- Pokud je datum vypršení platnosti dva měsíce nebo méně, služba začne odesílat oznámení na portálu a e-mailem (Pokud se přihlásíte k odběru oznámení Site Recovery).
- Na stránce prostředku trezoru se zobrazí banner s oznámením. Pokud chcete získat další informace, vyberte banner.
- Pokud se zobrazí tlačítko **upgradovat** , znamená to, že některé součásti ve vašem prostředí nebyly upgradovány na 4. xxxx. x nebo vyšší verze. Před obnovením certifikátu upgradujte součásti. Nemůžete obnovit starší verze.

### <a name="if-certificates-are-yet-to-expire"></a>Pokud certifikáty ještě vyprší

1. Obnovení prodlužujete tak, že v trezoru otevřete **Site Recovery**  >  **konfigurační server** infrastruktury. Vyberte požadovaný konfigurační server.
2. Zajistěte, aby všechny součásti procesové servery se škálováním na více systémů, hlavní cílové servery a agenti mobility na všech chráněných počítačích používaly nejnovější verze a jsou v připojeném stavu
3. Nyní vyberte možnost **obnovit certifikáty**.
4. Postupujte pečlivě podle pokynů na této stránce a kliknutím na tlačítko OK obnovte certifikáty na vybraném konfiguračním serveru a jeho přidružených součástech.

### <a name="if-certificates-have-already-expired"></a>Pokud již vypršela platnost certifikátů

1. Po vypršení platnosti příspěvku **nejde obnovit certifikáty z Azure Portal**. Než budete pokračovat, ujistěte se, že všechny součásti procesové servery se škálováním na více systémů, hlavní cílové servery a agenti mobility na všech chráněných počítačích mají nejnovější verze a jsou v připojeném stavu.
2. **Tento postup použijte pouze v případě, že již vypršela platnost certifikátů.** Přihlaste se ke konfiguračnímu serveru, přejděte na jednotky C > program data > Site Recovery > Home > svsystems > bin a spusťte nástroj "RenewCerts" prováděcí nástroj jako správce.
3. Okno spuštění PowerShellu se zobrazí a spustí obnovení certifikátů. Tento proces může trvat až 15 minut. Nezavírejte okno až do dokončení obnovení.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Opětovná registrace konfiguračního serveru ve stejném trezoru

V případě potřeby můžete konfigurační server ve stejném trezoru znovu zaregistrovat. Pokud máte další počítač procesového serveru, Kromě výchozího procesového serveru, který běží na počítači konfiguračního serveru, znovu proveďte registraci obou počítačů.


1. V trezoru otevřete **Spravovat**  >    >  **servery konfigurace** infrastruktury Site Recovery.
2. V části **servery** vyberte **Stáhnout registrační klíč** a Stáhněte si soubor s přihlašovacími údaji trezoru.
3. Přihlaste se k počítači konfiguračního serveru.
4. V **%ProgramData%\ASR\home\svsystems\bin** otevřete **cspsconfigtool.exe**.
5. Na kartě **registrace trezoru** vyberte **Procházet** a vyhledejte soubor s přihlašovacími údaji trezoru, který jste stáhli.
6. V případě potřeby zadejte proxy server podrobnosti. Pak vyberte **Register** (Registrovat).
7. Otevřete okno příkazového řádku PowerShellu pro správu a spusťte následující příkaz:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Aby bylo možné **načíst nejnovější certifikáty** z konfiguračního serveru do procesového serveru se škálováním na více instancí, spusťte příkaz *" \<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> "--registermt*

8. Nakonec restartujte obengine spuštěním následujícího příkazu.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru s jiným trezorem

> [!WARNING]
> V následujícím kroku se zruší přidružení konfiguračního serveru od aktuálního trezoru a zastavila se replikace všech chráněných virtuálních počítačů v konfiguračním serveru.

1. Přihlaste se ke konfiguračnímu serveru.
2. Otevřete okno příkazového řádku PowerShellu pro správu a spusťte následující příkaz:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Spusťte portál pro prohlížeč zařízení konfiguračního serveru pomocí zástupce na ploše.
4. Proveďte registrační postup podobný nové [registraci](vmware-azure-tutorial.md#register-the-configuration-server)konfiguračního serveru.

## <a name="upgrade-the-configuration-server"></a>Upgrade konfiguračního serveru

Aktualizace konfiguračního serveru spustíte spuštěním kumulativních aktualizací. Aktualizace je možné použít až pro N-4 verze. Například:

- Pokud spouštíte 9,7, 9,8, 9,9 nebo 9,10, můžete upgradovat přímo na 9,11.
- Pokud spustíte 9,6 nebo starší a chcete upgradovat na 9,11, musíte nejdřív upgradovat na verzi 9,7. před 9,11.

Podrobné pokyny k příkazu podpory Azure Site Recovery Components najdete [tady](./service-updates-how-to.md#support-statement-for-azure-site-recovery).
Odkazy na kumulativní aktualizace pro upgrade na všechny verze konfiguračního serveru jsou k dispozici [zde](./service-updates-how-to.md#links-to-currently-supported-update-rollups).

> [!IMPORTANT]
> U všech nových verzí N Azure Site Recovery součástí, které jsou vydány, jsou všechny verze nižší než N-4 považovány za nepodporovanou. Je vždy vhodné upgradovat na nejnovější dostupné verze.</br>
> Podrobné pokyny k příkazu podpory Azure Site Recovery Components najdete [tady](./service-updates-how-to.md#support-statement-for-azure-site-recovery).

Proveďte upgrade serveru následujícím způsobem:

1. V trezoru přejdete na **Správa**  >  **Site Recovery**  >  **Konfigurace serverů** infrastruktury.
2. Pokud je k dispozici aktualizace, zobrazí se odkaz ve sloupci **verze agenta** >.
    ![Aktualizace](./media/vmware-azure-manage-configuration-server/update2.png)
3. Stáhněte instalační soubor aktualizace na konfigurační server.

    ![Snímek obrazovky, který ukazuje, kde se má kliknout ke stažení instalačního souboru aktualizace.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dvojím kliknutím spusťte instalační program.
5. Instalační program detekuje aktuální verzi spuštěnou v počítači. Kliknutím na **Ano** zahájíte upgrade.
6. Po dokončení upgradu se konfigurace serveru ověří.

    ![Snímek obrazovky, který ukazuje dokončenou konfiguraci ověření serveru.](./media/vmware-azure-manage-configuration-server/update3.png)

7. Kliknutím na tlačítko **Dokončit** ukončíte instalační program.
8. Chcete-li upgradovat zbývající součásti Site Recovery, přečtěte si naše [pokyny k upgradu](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Upgrade konfiguračního serveru/procesového serveru z příkazového řádku

Spusťte instalační soubor následujícím způsobem:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Ukázkové použití
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Název parametru| Typ | Description| Hodnoty|
|-|-|-|-|
| /ServerMode|Vyžadováno|Určuje, jestli se má nainstalovat konfigurační i procesový server, nebo jenom procesový server.|CS<br>PS|
|/InstallLocation|Vyžadováno|Složka, ve které jsou nainstalované komponenty| Libovolná složka v počítači|
|/MySQLCredsFilePath|Vyžadováno|Cesta k souboru, ve kterém jsou uložené přihlašovací údaje serveru MySQL|Soubor by měl být v níže uvedeném formátu.|
|/VaultCredsFilePath|Vyžadováno|Cesta k souboru s přihlašovacími údaji trezoru|Platná cesta k souboru|
|/EnvType|Vyžadováno|Typ prostředí, které chcete chránit |VMware<br>NonVMware|
|/PSIP|Vyžadováno|IP adresa NIC, která se použije pro přenos dat replikace| Libovolná platná IP adresa|
|/CSIP|Vyžadováno|IP adresa NIC, na které konfigurační server naslouchá| Libovolná platná IP adresa|
|/PassphraseFilePath|Vyžadováno|Úplná cesta k umístění souboru s heslem|Platná cesta k souboru|
|/BypassProxy|Volitelné|Určuje, že se konfigurační server připojí k Azure bez proxy serveru.|Tuto hodnotu získejte z Venu.|
|/ProxySettingsFilePath|Volitelné|Nastavení proxy serveru (výchozí proxy server vyžaduje ověření, nebo vlastní proxy server)|Soubor by měl být v níže uvedeném formátu.|
|DataTransferSecurePort|Volitelné|Číslo portu na PSIP, které se má použít pro data replikace| Platné číslo portu (výchozí hodnota je 9433)|
|/SkipSpaceCheck|Volitelné|Přeskočí kontrolu místa na disku mezipaměti.| |
|/AcceptThirdpartyEULA|Vyžadováno|Příznak značí přijetí smlouvy EULA třetích stran| |
|/ShowThirdpartyEULA|Volitelné|Zobrazí smlouvy EULA třetích stran. Pokud je zadán jako vstup, všechny ostatní parametry budou ignorovány| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Vytvoření vstupu souboru pro MYSQLCredsFilePath

Parametr MySQLCredsFilePath jako vstup přebírá soubor. Vytvořte soubor pomocí následujícího formátu a předejte ho jako vstupní parametr MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Vytvoření vstupu souboru pro ProxySettingsFilePath
Parametr ProxySettingsFilePath má jako vstup soubor. Vytvořte soubor pomocí následujícího formátu a předejte ho jako vstupní parametr ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v konfiguračním serveru.
2. [Zrušte přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranění](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všech zásad replikace z konfiguračního serveru.
3. [Odstraňte](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všechny servery vCenter nebo hostitele vSphere, kteří jsou přidruženi ke konfiguračnímu serveru.
4. V trezoru otevřete **Site Recovery**  >  **konfigurační servery** infrastruktury.
5. Vyberte konfigurační server, který chcete odebrat. Pak na stránce **Podrobnosti** vyberte **Odstranit**.

    ![Odstranit konfigurační server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Odstranění pomocí PowerShellu

Volitelně můžete konfigurační server odstranit pomocí prostředí PowerShell.

1. [Nainstalujte](/powershell/azure/install-Az-ps) modul Azure PowerShell.
2. Přihlaste se ke svému účtu Azure pomocí tohoto příkazu:

    `Connect-AzAccount`
3. Vyberte předplatné trezoru.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Nastavte kontext trezoru.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Načtěte konfigurační server.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstraňte konfigurační server.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Můžete použít možnost **-Force** v Remove-AzSiteRecoveryFabric pro vynucené odstranění konfiguračního serveru.

## <a name="generate-configuration-server-passphrase"></a>Generovat heslo konfiguračního serveru

1. Přihlaste se ke konfiguračnímu serveru a otevřete okno příkazového řádku jako správce.
2. Chcete-li změnit adresář na složku bin, spusťte příkaz **CD%ProgramData%\ASR\home\svsystems\bin**
3. Chcete-li vygenerovat soubor s heslem, spusťte **genpassphrase.exe-v > MobSvc. přístupový klíč**.
4. Vaše přístupové heslo bude uloženo v souboru na adrese **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="refresh-configuration-server"></a>Aktualizovat konfigurační server

1. V Azure Portal přejděte do **trezoru Recovery Services**  >  **Správa**  >  **Site Recovery infrastruktury**  >  **pro konfigurační servery VMware & fyzické počítače**  >   .
2. Klikněte na konfigurační server, který chcete aktualizovat.
3. V okně s podrobnostmi o zvoleném konfiguračním **serveru klikněte na** tlačítko  >  **aktualizovat server**.
4. Sledujte průběh úlohy v části monitorování **trezoru služby Recovery Services**  >    >  **Site Recovery úlohy**.

## <a name="failback-requirements"></a>Požadavky na navrácení služeb po obnovení

Při opětovném zapnutí ochrany a navrácení služeb po obnovení musí místní konfigurační server běžet a v připojeném stavu. Pro úspěšné navrácení služeb po obnovení musí být virtuální počítač, který se nezdařil, v databázi konfiguračního serveru.

Ujistěte se, že provádíte pravidelné naplánování záložních záloh konfiguračního serveru. Pokud dojde k havárii a dojde ke ztrátě konfiguračního serveru, musíte nejdřív obnovit konfigurační server ze záložní kopie a zajistit, aby obnovený konfigurační server měl stejnou IP adresu, se kterou se zaregistroval v trezoru. Navrácení služeb po obnovení nebude fungovat, pokud se pro obnovený konfigurační server používá jiná IP adresa.

## <a name="next-steps"></a>Další kroky

Přečtěte si kurzy k nastavení zotavení po havárii [virtuálních počítačů VMware](vmware-azure-tutorial.md) do Azure.