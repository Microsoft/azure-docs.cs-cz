---
title: Správa konfiguračního serveru pro fyzické servery v Azure Site Recovery
description: Tento článek popisuje, jak spravovat Azure Site Recovery konfigurační server pro zotavení po havárii fyzického serveru do Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017436"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Správa konfiguračního serveru pro zotavení po havárii fyzického serveru

Místní konfigurační server nastavíte při použití služby [Azure Site Recovery](site-recovery-overview.md) k zotavení po havárii fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místními počítači a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy konfiguračního serveru po jeho nasazení.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Tabulka shrnuje předpoklady pro nasazení místního počítače konfiguračního serveru.

| **Komponenta** | **Požadavek** |
| --- |---|
| Procesorová jádra| 8 |
| Paměť RAM | 16 GB|
| Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesového serveru a jednotky pro uchovávání pro navrácení služeb po obnovení |
| Volné místo na disku (mezipaměť procesového serveru) | 600 GB
| Volné místo na disku (disk pro uchování) | 600 GB|
| Operační systém  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Národní prostředí operačního systému | Angličtina (USA)|
| Verze VMware vSphere PowerCLI | Nevyžadováno|
| Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V |
| Zásady skupiny| Nepovolujte tyto zásady skupiny: <br> -Zakázat přístup k příkazovému řádku <br> – Zakázat přístup k nástrojům pro úpravu registru <br> – Logika vztahu důvěryhodnosti pro přílohy souborů <br> -Zapnout provádění skriptu <br> [Další informace](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | -Žádný předdefinovaný výchozí web <br> -Povolit  [anonymní ověřování](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Povolit nastavení [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br> -Žádný existující web nebo aplikace nenaslouchá na portu 443.<br>|
| Typ síťové karty | VMXNET3 (při nasazení jako virtuální počítač VMware) |
| Typ IP adresy | Static |
| Přístup k internetu | Server potřebuje přístup k těmto adresám URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> – *. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (nevyžaduje se pro procesové servery se škálováním na více instancí) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)|

## <a name="download-the-latest-installation-file"></a>Stažení nejnovějšího instalačního souboru

Nejnovější verzi instalačního souboru konfiguračního serveru najdete na portálu Site Recovery. Navíc ho můžete stáhnout přímo z webu [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Přihlaste se k Azure Portal a přejděte do svého trezoru Recovery Services.
2. Přejděte na **Site Recovery**  >  **konfiguračního serveru** infrastruktury (v části pro & fyzické počítače VMware).
3. Klikněte na tlačítko **+ servery** .
4. Na stránce **Přidat server** klikněte na tlačítko Stáhnout a stáhněte registrační klíč. Tento klíč budete potřebovat při instalaci konfiguračního serveru, abyste ho mohli zaregistrovat ve službě Azure Site Recovery.
5. Kliknutím na odkaz **stáhnout Microsoft Azure Site Recovery Unified Setup** si stáhněte nejnovější verzi konfiguračního serveru.

   ![Stránka pro stažení](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalace a registrace serveru

1. Spusťte instalační soubor sjednocené instalace.
2. V části **než začnete** vyberte **nainstalovat konfigurační server a procesový Server**.

    ![Než začnete](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.
4. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolili požadované adresy URL.

    - Pokud se chcete připojit k proxy serveru, který je aktuálně nastavený na počítači, vyberte **připojit k Azure Site Recovery pomocí proxy server**.
    - Pokud chcete, aby se zprostředkovatel připojil přímo, vyberte **připojit přímo k Azure Site Recovery bez proxy server**.
    - Pokud existující proxy server vyžaduje ověření nebo pokud chcete pro připojení zprostředkovatele používat vlastní proxy server, vyberte **připojit se s vlastním nastavením proxy serveru** a zadejte adresu, port a přihlašovací údaje.
     ![Brána firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Požadavky](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pokud se nacházíte, instalační program zkontroluje, že je nainstalovaná PowerCLI 6,0.
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Umístění instalace](./media/physical-manage-configuration-server/combined-wiz8.png)
10. V části **Výběr sítě** nejdřív Vyberte síťovou kartu, kterou integrovaný procesový Server používá pro zjišťování a nabízenou instalaci služby mobility na zdrojových počítačích, a pak vyberte síťovou kartu, kterou konfigurační server používá pro připojení k Azure. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Pro odesílání a příjem provozu replikace nepoužívejte port 443.

    ![Výběr sítě](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.


Po dokončení registrace se server zobrazí v okně **Nastavení**  >  **servery** v trezoru.


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Úprava nastavení proxy serveru

Nastavení proxy serveru pro počítač konfiguračního serveru můžete upravit následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
2. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na kartu **registrace trezoru** .
4. Stáhněte si nový registrační soubor trezoru z portálu a poskytněte ho jako vstup do nástroje.

   ![Registrace – konfigurace-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte podrobnosti o novém proxy serveru a klikněte na tlačítko **Registrovat** .
6. Otevřete okno příkazového řádku PowerShellu pro správu.
7. Spusťte následující příkaz:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Pokud máte další procesní servery připojené ke konfiguračnímu serveru, musíte [opravit nastavení proxy serveru na všech procesových serverech se škálováním na více](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) instancí v nasazení.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Opětovná registrace konfiguračního serveru se stejným trezorem
1. Přihlaste se ke konfiguračnímu serveru.
2. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na kartu **registrace trezoru** .
4. Stáhněte si nový registrační soubor z portálu a poskytněte ho jako vstup do nástroje.
      ![Registrace – konfigurace-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte podrobnosti proxy serveru a klikněte na tlačítko **Registrovat** .  
6. Otevřete okno příkazového řádku PowerShellu pro správu.
7. Spusťte následující příkaz

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Pokud máte více procesových serverů, je nutné [je znovu zaregistrovat](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru s jiným trezorem

> [!WARNING]
> V následujícím kroku se zruší přidružení konfiguračního serveru od aktuálního trezoru a zastavila se replikace všech chráněných virtuálních počítačů v konfiguračním serveru.

1. Přihlášení na konfigurační server
2. z příkazového řádku správce spusťte příkaz:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
4. Klikněte na kartu **registrace trezoru** .
5. Stáhněte si nový registrační soubor z portálu a poskytněte ho jako vstup do nástroje.
6. Zadejte podrobnosti proxy serveru a klikněte na tlačítko **Registrovat** .  
7. Otevřete okno příkazového řádku PowerShellu pro správu.
8. Spusťte následující příkaz
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Upgrade konfiguračního serveru

Aktualizace konfiguračního serveru spustíte spuštěním kumulativních aktualizací. Aktualizace je možné použít až pro N-4 verze. Například:

- Pokud používáte 9,7, 9,8, 9,9 nebo 9,10, můžete upgradovat přímo na 9,11.
- Pokud používáte 9,6 nebo starší verzi a chcete upgradovat na 9,11, musíte nejdřív upgradovat na verzi 9,7. před 9,11.

Odkazy na kumulativní aktualizace pro upgrade na všechny verze konfiguračního serveru jsou k dispozici na [stránce aktualizace wikiwebu](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Proveďte upgrade serveru následujícím způsobem:

1. Stáhněte instalační soubor aktualizace na konfigurační server.
2. Dvojím kliknutím spusťte instalační program.
3. Instalační program detekuje aktuální verzi spuštěnou v počítači.
4. Kliknutím na **OK** potvrďte a spusťte upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

> [!WARNING]
> Než začnete vyřadit z provozu konfiguračního serveru, zajistěte následující:
> 1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v rámci tohoto konfiguračního serveru.
> 2. [Zrušte přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranění](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všech zásad replikace z konfiguračního serveru.
> 3. [Odstraňte](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všechny servery vCenter servery nebo hostitele vSphere, kteří jsou přidruženi ke konfiguračnímu serveru.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Odstranit konfigurační server z Azure Portal
1. V Azure Portal přejděte na **Site Recovery**  >  **konfigurační servery** infrastruktury z nabídky trezor.
2. Klikněte na konfigurační server, který chcete vyřadit z provozu.
3. Na stránce podrobností konfiguračního serveru klikněte na tlačítko **Odstranit** .
4. Kliknutím na **Ano** potvrďte odstranění serveru.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstalace konfiguračního serveru a jeho závislostí
> [!TIP]
>   Pokud plánujete znovu použít konfigurační server s Azure Site Recovery, můžete přeskočit ke kroku 4 přímo.

1. Přihlaste se ke konfiguračnímu serveru jako správce.
2. Otevřete ovládací panely > programu > odinstalovat programy
3. Odinstalujte programy v následujícím pořadí:
   * Agent Microsoft Azure Recovery Services
   * Služba Microsoft Azure Site Recovery mobility/hlavní cílový server
   * Poskytovatel Microsoft Azure Site Recovery
   * Microsoft Azure Site Recovery konfigurační server/procesový Server
   * Závislosti konfiguračního serveru Microsoft Azure Site Recovery
   * MySQL Server 5,5
4. Spusťte následující příkaz z příkazového řádku a na příkazovém řádku správce.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Odstranění nebo zrušení registrace konfiguračního serveru (PowerShell)

1. [Nainstalovat](/powershell/azure/install-Az-ps) Modul Azure PowerShell
2. Přihlaste se k účtu Azure pomocí příkazu
    
    `Connect-AzAccount`
3. Vyberte předplatné, ve kterém se nachází trezor.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Teď nastavte kontext trezoru.
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Získat výběr konfiguračního serveru

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstranit konfigurační server

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Možnost **-Force** v Remove-AzSiteRecoveryFabric lze použít k vynucení odebrání nebo odstranění konfiguračního serveru.

## <a name="renew-tlsssl-certificates"></a>Obnovení certifikátů TLS/SSL
Konfigurační server má integrovaný webový server, který orchestruje aktivity služby mobility, procesových serverů a hlavních cílových serverů, které jsou k ní připojené. Webový server používá k ověřování klientů certifikát TLS/SSL. Platnost certifikátu vyprší po třech letech a může být kdykoli obnovena.

### <a name="check-expiry"></a>Kontrolovat vypršení platnosti

U nasazení konfiguračního serveru do května 2016 se platnost certifikátu nastavila na jeden rok. Pokud platnost certifikátu vyprší, dojde k následujícímu:

- Pokud je datum vypršení platnosti dva měsíce nebo méně, služba začne odesílat oznámení na portálu a e-mailem (Pokud se přihlásíte k odběru oznámení Azure Site Recovery).
- Na stránce prostředku trezoru se zobrazí banner s oznámením. Další podrobnosti získáte kliknutím na banner.
- Pokud se zobrazí tlačítko **upgradovat** , znamená to, že ve vašem prostředí jsou některé součásti, které nebyly upgradovány na verzi 9.4. xxxx. x nebo vyšší. Upgradujte součásti před obnovením certifikátu. Nemůžete obnovit starší verze.

### <a name="renew-the-certificate"></a>Prodloužit platnost certifikátu

1. V trezoru otevřete **Site Recovery**  >  **konfiguračního serveru** infrastruktury a klikněte na požadovaný konfigurační server.
2. Datum vypršení platnosti se zobrazí v části **stav konfiguračního serveru** .
3. Klikněte na tlačítko **obnovit certifikáty**. 




## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si kurzy k nastavení zotavení po havárii [fyzických serverů](./physical-azure-disaster-recovery.md) do Azure.
