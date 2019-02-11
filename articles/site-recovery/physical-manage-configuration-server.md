---
title: Správa konfiguračního serveru pro zotavení po havárii místních fyzických serverů do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak Správa konfiguračního serveru Azure Site Recovery pro zotavení po havárii fyzického serveru do Azure.
services: site-recovery
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: d5ce80e44ee1a3a48443b190ea9259fe2dea0dcb
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983215"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Správa konfiguračního serveru pro zotavení po havárii fyzického serveru

Nastavíte místní konfigurační server, když použijete [Azure Site Recovery](site-recovery-overview.md) služby zotavení po havárii fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místní počítače a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy konfigurační server je po nasazení.

## <a name="prerequisites"></a>Požadavky

Tabulka shrnuje požadavky pro nasazování počítače místní konfigurační server.

| **Komponenta** | **Požadavek** |
| --- |---|
| Procesorová jádra| 8 |
| Paměť RAM | 16 GB|
| Počet disků | 3, včetně operačního systému disku, disk mezipaměti procesového serveru a jednotky pro uchovávání dat pro navrácení služeb po obnovení |
| Volné místo na disku (mezipaměť procesového serveru) | 600 GB
| Volné místo na disku (disk pro uchování) | 600 GB|
| Operační systém  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Národní prostředí operačního systému | English (US)|
| Verze VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Role Windows Serveru | Nepovolí pracovníci v těchto rolích: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V |
| Zásady skupiny| Nepovolí tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku <br> -Zabránit přístupu k nástrojům pro úpravu registru <br> – Logika důvěryhodnosti pro přiložené soubory <br> -Zapnutí provádění skriptů <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Žádné existující výchozí web <br> -Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení  <br> -Žádné existující web/aplikace naslouchá na portu 443<br>|
| Typ NIC | VMXNET3 (Pokud je nasazená jako virtuální počítač VMware) |
| Typ IP adresy | Statická |
| Přístup k internetu | Server potřebuje přístup k těmto adresám URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (není vyžadované pro horizontální navýšení kapacity procesových serverů) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)|

## <a name="download-the-latest-installation-file"></a>Stažení nejnovějšího instalačního souboru

Nejnovější verzi instalačního souboru konfigurace serveru je k dispozici na portálu Site Recovery. Kromě toho můžete stáhnout přímo z [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Přihlaste se k webu Azure portal a přejděte do vašeho trezoru služby Recovery Services.
2. Přejděte do **infrastruktura Site Recovery** > **konfigurační servery** (v části pro VMware a fyzické počítače).
3. Klikněte na tlačítko **+ servery** tlačítko.
4. Na **přidat Server** klikněte na tlačítko pro stažení se stáhnout registrační klíč. Tento klíč budete potřebovat při instalaci konfiguračního serveru k registraci pomocí služby Azure Site Recovery.
5. Klikněte na tlačítko **stáhněte si Microsoft Azure Site Recovery sjednocené instalace** odkaz ke stažení nejnovější verze konfiguračního serveru.

  ![Stránka Stažení](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalace a registrace serveru

1. Spusťte instalační soubor sjednocené instalace.
2. V **před zahájením**vyberte **nainstalovat konfigurační server a procesový server**.

    ![Než začnete](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.
4. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolili požadované adresy URL.

    - Pokud chcete pro připojení s proxy serverem, který je aktuálně nastavený na počítači, vyberte **připojit k Azure Site Recovery pomocí proxy serveru**.
    - Pokud chcete, aby zprostředkovatel připojil přímo, vyberte **připojit přímo k Azure Site Recovery bez proxy serveru**.
    - Pokud stávající proxy server vyžaduje ověření nebo pokud chcete používat vlastní proxy server pro připojení zprostředkovatele vyberte **Connect s vlastním nastavením proxy serveru**a zadejte adresu, port a přihlašovací údaje.
     ![Brána firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Požadavky](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pokud se instalační program zkontroluje, že je nainstalované rozhraní PowerCLI 6.0.
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Umístění instalace](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Na stránce **Výběr sítě** zadejte naslouchací proces (síťový adaptér a port SSL), na kterém konfigurační server odesílá a přijímá data replikace. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Nepoužívejte port 443 pro odesílání nebo příjem provozu replikace.

    ![Výběr sítě](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.


Po dokončení registrace se server zobrazí v okně **Nastavení** > **Servery** v trezoru.


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

Spusťte instalační soubor následujícím způsobem:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Využití vzorků
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Název parametru| Type | Popis| Hodnoty|
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



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Vytvoření vstupní soubor pro MYSQLCredsFilePath

Parametr MySQLCredsFilePath vezme jako vstupní údaje do souboru. Vytvořte soubor v následujícím formátu a předat ji jako vstupní parametr MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Vytvoření vstupní soubor pro ProxySettingsFilePath
Parametr ProxySettingsFilePath vezme jako vstupní údaje do souboru. Vytvořte soubor v následujícím formátu a předat ji jako vstupní parametr ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Upravit nastavení proxy serveru

Nastavení proxy serveru pro počítač serveru konfiguraci můžete upravit následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
2. Spusťte cspsconfigtool.exe na pomocí zkratky vaše.
3. Klikněte na tlačítko **registrace trezoru** kartu.
4. Stáhněte si nový soubor registrace trezoru z portálu a zadejte jako vstup do nástroje.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte nové podrobnosti proxy a klikněte na tlačítko **zaregistrovat** tlačítko.
6. Otevřete okno příkazového řádku Powershellu pro správu.
7. Spusťte následující příkaz:

  ```PowerShell
  $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Pokud máte dalších procesových serverů, které jsou připojené ke konfiguračnímu serveru, budete muset [opravit nastavení proxy serveru na všechny horizontální navýšení kapacity procesových serverů](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) ve vašem nasazení.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Opětovná registrace konfiguračního serveru u stejného trezoru
  1. Přihlaste se ke konfiguračnímu serveru.
  2. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
  3. Klikněte na tlačítko **registrace trezoru** kartu.
  4. Stáhněte si nový registrační soubor z portálu a zadejte jako vstup do nástroje.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Zadejte podrobnosti o Proxy serveru a klikněte na tlačítko **zaregistrovat** tlačítko.  
  6. Otevřete okno příkazového řádku Powershellu pro správu.
  7. Spuštěním následujícího příkazu

      ```PowerShell
      $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Pokud máte více procesový server, budete muset [zaregistrovat znovu](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru pomocí jiného trezoru

> [!WARNING]
> Následující krok zruší přidružení konfiguračního serveru z aktuálního úložiště a zastaví se replikace všech chráněných virtuálních počítačů v rámci konfigurace serveru.

1. Přihlaste se konfigurační server
2. z příkazového řádku správce spusťte příkaz:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
4. Klikněte na tlačítko **registrace trezoru** kartu.
5. Stáhněte si nový registrační soubor z portálu a zadejte jako vstup do nástroje.
6. Zadejte podrobnosti o Proxy serveru a klikněte na tlačítko **zaregistrovat** tlačítko.  
7. Otevřete okno příkazového řádku Powershellu pro správu.
8. Spuštěním následujícího příkazu
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Upgradujte konfigurační server

Spuštění kumulativní aktualizace se aktualizovat konfigurační server. Aktualizace můžete použít pro až N-4 verze. Příklad:

- Pokud používáte 9.7, 9.8, 9.9 nebo 9.10 – můžete upgradovat přímo na 9.11.
- Pokud používáte 9,6 nebo starší, a chcete provést upgrade 9.11, musíte nejprve upgradovat na verzi 9.7. před 9.11.

Odkazy na kumulativní aktualizace pro upgrade pro všechny verze konfiguračního serveru jsou k dispozici v [aktualizace wikistránka](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade serveru následujícím způsobem:

1. Stáhněte si instalační soubor aktualizace ke konfiguračnímu serveru.
2. Dvakrát klikněte na panel spusťte instalační program.
3. Instalační program zjistí aktuální verze, která běží na počítači.
4. Klikněte na tlačítko **OK** potvrďte a spusťte upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

> [!WARNING]
> Zajistěte následující před zahájením vyřazení z provozu konfigurační Server.
> 1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v rámci tohoto konfiguračního serveru.
> 2. [Zrušit přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranit](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všechny zásady replikace z konfiguračního serveru.
> 3. [Odstranit](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všichni hostitelé vCenters servery pro/vSphere, které jsou přidružené ke konfiguračnímu serveru.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Odstranit konfigurační Server z webu Azure portal
1. Na webu Azure portal, přejděte k **infrastruktura Site Recovery** > **konfigurační servery** v nabídce trezoru.
2. Klikněte na konfiguračním serveru, který chcete vyřadit z provozu.
3. Na stránce s podrobnostmi o konfiguračním serveru, klikněte na tlačítko **odstranit** tlačítko.
4. Klikněte na tlačítko **Ano** k potvrzení odstranění serveru.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstalace konfiguračního serveru a jeho závislosti
  > [!TIP]
  Pokud chcete znovu použít konfiguračního serveru pomocí Azure Site Recovery, pak můžete přeskočit ke kroku 4 přímo

1. Přihlaste se k konfiguračního serveru jako správce.
2. Otevřete ovládací panely > aplikace > odinstalovat programy
3. Odinstalujte programy v následujícím pořadí:
  * Agent Microsoft Azure Recovery Services
  * Microsoft Azure Site Recovery Mobility Service/hlavní cílový server
  * Microsoft Azure Site Recovery Provider
  * Microsoft Azure Site Recovery konfigurační Server/Process Server
  * Závislosti na Microsoft Azure Site Recovery konfigurace serveru
  * MySQL Server 5.5
4. Spusťte následující příkaz a příkazového řádku správce.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Odstranění nebo zrušení registrace konfiguračního serveru (PowerShell)

1. [Nainstalujte](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0) modulu Azure PowerShell
2. Přihlásit se ke svému účtu Azure pomocí příkazu
    
    `Connect-AzureRmAccount`
3. Vyberte předplatné, pod kterým je k dispozici v úložišti

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Teď nastavte kontext trezoru
    
    ```PowerShell
    $Vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Získat vyberte konfigurační server

    `$Fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstranění konfiguračního serveru

    `Remove-AzureRmSiteRecoveryFabric -Fabric $Fabric [-Force] `

> [!NOTE]
> **– Platnost** možnost v Remove-AzureRmSiteRecoveryFabric jde použít k vynucení odebrání nebo odstranění konfiguračního serveru.

## <a name="renew-ssl-certificates"></a>Prodloužit platnost certifikátů SSL
Konfigurační server má integrované webový server, která orchestruje činnosti služby Mobility, procesových serverů a hlavní cílové servery, které jsou k němu připojená. Webový server používá certifikát SSL k ověřování klientů. Certifikát vyprší po uplynutí tří let a jde ji obnovit v každém okamžiku.

### <a name="check-expiry"></a>Kontrola vypršení platnosti

Pro nasazení serveru konfigurace před. května 2016 vypršení platnosti certifikátu byla nastavená na jeden rok. Pokud máte certifikát bude vyprší, dojde k následujícímu:

- Pokud datum vypršení platnosti je po dobu dvou měsíců nebo méně, začne služba odesílání oznámení z portálu a e-mailem (je-li připojila ke oznámení Azure Site Recovery).
- Na stránce prostředků trezoru se zobrazí banner s oznámením. Kliknutím na banner pro další podrobnosti.
- Pokud se zobrazí **upgradovat** tlačítko, to znamená, že jsou některé komponenty ve vašem prostředí, kteří se neupgradovali na 9.4.xxxx.x nebo vyšší verze. Upgrade součásti před obnovováním certifikátu. Nejde obnovit ke starším verzím.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V trezoru, otevřete **infrastruktura Site Recovery** > **konfigurační Server**a klikněte na požadované konfigurační server.
2. Datum vypršení platnosti se zobrazí v části **stav konfigurace serveru**
3. Klikněte na tlačítko **prodloužit platnost certifikátů**. 




## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si podrobné pokyny pro nastavení zotavení po havárii [fyzických serverů](tutorial-physical-to-azure.md) do Azure.

