---
title: " Spravovat konfigurační server pro obnovení po havárii fyzického serveru s Azure Site Recovery | Microsoft Docs"
description: Tento článek popisuje, jak spravovat existující konfigurační server pro zotavení po havárii fyzického serveru do Azure, se službou Azure Site Recovery.
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 04/11/2018
ms.author: anoopkv
ms.openlocfilehash: 580d32a51f6b38916ddccd46784b80b1179c29c4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Správa konfigurace serveru pro zotavení po havárii fyzického serveru

Můžete nastavení konfigurace serveru místní, při použití [Azure Site Recovery](site-recovery-overview.md) služby pro zotavení po havárii fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místní počítače a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy správy konfigurační server po je nasazený.

## <a name="prerequisites"></a>Požadavky

Tabulka shrnuje předpoklady pro nasazení na místním počítači konfigurace serveru.

| **Komponenta** | **Požadavek** |
| --- |---|
| Procesorová jádra| 8 |
| Paměť RAM | 16 GB|
| Počet disků | 3, včetně disku operačního systému, proces disku mezipaměti serveru a jednotka pro uchování pro navrácení služeb po obnovení |
| Volné místo na disku (mezipaměť procesového serveru) | 600 GB
| Volné místo na disku (disk pro uchování) | 600 GB|
| Operační systém  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Národní prostředí operačního systému | English (US)|
| Verze VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Role Windows Serveru | Nepovolíte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V |
| Zásady skupiny| Nemáte povolit tyto zásady skupiny: <br> -Zabránit přístupu do příkazového řádku <br> -Zabránit přístupu do nástroje pro úpravy registru <br> -Důvěřovat logiku pro přílohy souborů <br> -Zapnout provádění skriptu <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Žádné existující výchozí web <br> -Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení  <br> -Žádná existující web nebo aplikace naslouchá na portu 443<br>|
| Typ síťový adaptér | VMXNET3 (Pokud je nasazený jako virtuální počítač VMware) |
| Typ IP adresy | Statická |
| Přístup k internetu | Server potřebuje přístup k tyto adresy URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - dc.services.visualstudio.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (není požadováno pro servery se Škálováním na více systémů procesu) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)|

## <a name="download-the-latest-installation-file"></a>Stažení nejnovějšího instalačního souboru

Nejnovější verzi instalačního souboru konfigurace serveru je k dispozici na portálu Site Recovery. Kromě toho se můžete stáhnout přímo z [Microsoft Download Center](http://aka.ms/unifiedsetup).

1. Přihlaste se k portálu Azure a přejděte do trezoru služeb zotavení.
2. Přejděte do **infrastruktury obnovení lokality** > **konfigurační servery** (v části pro VMware a fyzické počítače).
3. Klikněte **+ servery** tlačítko.
4. Na **přidat Server** klikněte na tlačítko Stáhnout na stáhnout registrační klíč. Je nutné tento klíč během instalace konfigurační Server a zaregistrujte ho pomocí služby Azure Site Recovery.
5. Klikněte **stáhnout instalaci služby Microsoft Azure Site Recovery Unified** odkaz ke stažení nejnovější verze konfigurace serveru.

  ![Stažení stránky](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalace a registrace serveru

1. Spusťte instalační soubor sjednocené instalace.
2. V **než začnete**, vyberte **nainstalujte konfigurační server a procesový server**.

    ![Než začnete](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.
4. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolené požadované adresy URL.

    - Pokud se chcete připojit s proxy serverem, který je aktuálně nastavený na počítač, vyberte **připojit k Azure Site Recovery pomocí proxy serveru**.
    - Pokud chcete, aby zprostředkovatel připojil přímo, vyberte **připojit se přímo k Azure Site Recovery bez serveru proxy**.
    - Pokud stávající proxy server vyžaduje ověřování, nebo pokud chcete používat vlastní proxy server pro připojení poskytovatele, vyberte **Connect s vlastním nastavením proxy**a zadejte adresu, port a přihlašovací údaje.
     ![Brána firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Požadavky](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pokud jste, potom instalační program kontroluje, že je nainstalována PowerCLI 6.0.
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Umístění instalace](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Na stránce **Výběr sítě** zadejte naslouchací proces (síťový adaptér a port SSL), na kterém konfigurační server odesílá a přijímá data replikace. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Nepoužívejte port 443 pro odesílání nebo přijímání provoz replikace.

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
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
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



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Vytvoření vstupní soubor pro MYSQLCredsFilePath

Parametr MySQLCredsFilePath vezme jako vstupní soubor. Vytvořte soubor v následujícím formátu a předejte jej jako vstupní parametr MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Vytvoření vstupní soubor pro ProxySettingsFilePath
Parametr ProxySettingsFilePath vezme jako vstupní soubor. Vytvořte soubor v následujícím formátu a předejte jej jako vstupní parametr ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Upravte nastavení proxy serveru

Nastavení proxy pro počítače konfigurace serveru můžete upravit takto:

1. Přihlaste se na konfiguračním serveru.
2. Spusťte cspsconfigtool.exe pomocí zástupce na vaše.
3. Klikněte **registrace trezoru** kartě.
4. Nový soubor registrace trezoru stáhnout z portálu a zadejte jako vstup pro tento nástroj.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte nové podrobnosti o proxy serveru a klikněte na **zaregistrovat** tlačítko.
6. Otevřete okno příkazového prostředí PowerShell pro správu.
7. Spusťte následující příkaz:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Pokud máte další proces servery připojené k konfigurační server, budete muset [opravte nastavení proxy serveru na všech serverech Škálováním na více systémů proces](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) ve vašem nasazení.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Znovu zaregistrujte konfigurační server s ke stejnému trezoru
  1. Přihlaste se k konfigurační Server.
  2. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
  3. Klikněte **registrace trezoru** kartě.
  4. Stáhněte si nový soubor registrace z portálu a zadejte jako vstup pro nástroj.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Zadejte podrobnosti o Proxy serveru a klikněte na **zaregistrovat** tlačítko.  
  6. Otevřete okno příkazového prostředí PowerShell pro správu.
  7. Spusťte následující příkaz

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Pokud máte více procesový server, budete muset [zaregistrovat znovu](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Zaregistrujte konfigurační server s jiným trezorem

> [!WARNING]
> Následující krok zrušíte konfigurační server z aktuální trezoru a zastaví se všechny chráněné virtuální počítače v rámci konfigurace serveru replikace.

1. Přihlaste se na konfiguračním serveru
2. z příkazového řádku správce spusťte příkaz:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Spusťte cspsconfigtool.exe pomocí zástupce na ploše.
4. Klikněte **registrace trezoru** kartě.
5. Stáhněte si nový soubor registrace z portálu a zadejte jako vstup pro nástroj.
6. Zadejte podrobnosti o Proxy serveru a klikněte na **zaregistrovat** tlačítko.  
7. Otevřete okno příkazového prostředí PowerShell pro správu.
8. Spusťte následující příkaz
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Upgrade serveru konfigurace

Spuštění aktualizace kumulativní aktualizace konfigurace serveru. Aktualizace můžete použít pro až N-4 verze. Příklad:

- Pokud používáte 9.7 9.8, 9.9 nebo 9.10 – můžete upgradovat přímo do 9.11.
- Pokud používáte 9,6 nebo starším systémem, a chcete upgradovat na 9.11, je nutné nejprve upgradovat na verzi 9.7. před 9.11.

Odkazy na kumulativní aktualizace pro upgrade na všechny verze serveru konfigurace jsou k dispozici v [wiki aktualizace stránky](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade serveru následujícím způsobem:

1. Stáhněte si soubor instalačního programu aktualizace na konfiguračním serveru.
2. Dvakrát klikněte na panel spusťte instalační program.
3. Instalační program zjistí aktuální verze na počítači spuštěna.
4. Klikněte na tlačítko **OK** potvrďte a spustíte upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Odstranit nebo zrušit registraci konfigurační server

> [!WARNING]
> Zkontrolujte následující před zahájením vyřazení z provozu konfigurační Server.
> 1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) pro všechny virtuální počítače v rámci této konfigurace serveru.
> 2. [Zrušit přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranit](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všechny zásady replikace z konfiguračního serveru.
> 3. [Odstranit](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všichni hostitelé Vcenter vSphere servery, které jsou přidružené k konfigurační Server.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Odstraňte konfigurační Server z portálu Azure
1. Na portálu Azure, přejděte do **infrastruktura Site Recovery** > **konfigurační servery** nabídce trezoru.
2. Klikněte na konfigurační server, který chcete vyřadit z provozu.
3. Na stránce s podrobnostmi o konfigurační Server, klikněte na **odstranit** tlačítko.
4. Klikněte na tlačítko **Ano** potvrďte odstranění serveru.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstalujte konfiguračního serveru a jeho závislosti
  > [!TIP]
  Pokud budete chtít použít konfigurační Server s Azure Site Recovery znovu, potom můžete přeskočit ke kroku 4 přímo

1. Přihlaste se k serveru Configuration jako správce.
2. Otevřete ovládací panely > Program > odinstalovat programy
3. Odinstalujte programy v tomto pořadí:
  * Agent Microsoft Azure Recovery Services
  * Microsoft Azure Site Recovery Mobility služby nebo hlavní cílový server
  * Zprostředkovatele služby Microsoft Azure Site Recovery
  * Microsoft Azure Site obnovení konfigurace serveru nebo procesový Server
  * Microsoft Azure Site Recovery konfigurace serveru závislosti
  * MySQL Server 5.5
4. Spusťte následující příkaz a správu příkazového řádku.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Odstranit nebo zrušit registraci konfigurační server (PowerShell)

1. [Nainstalujte](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) modul Azure PowerShell
2. Přihlášení do ke svému účtu Azure pomocí příkazu
    
    `Connect-AzureRmAccount`
3. Vyberte předplatné, ve kterém se nachází v úložišti

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Nyní nastavit váš trezor kontext
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Získat vyberte konfigurační server

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstraňte konfigurační Server

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> **-Force** možnost v Remove-AzureRmSiteRecoveryFabric je možné vynutit odebrání nebo odstranění konfigurační server.

## <a name="renew-ssl-certificates"></a>Obnovení certifikátů SSL
Konfigurační server má integrované webový server, která orchestruje činnosti služby Mobility, proces servery a hlavních cílových serverů, které jsou k němu připojená. Webový server používá certifikát SSL k ověřování klientů. Platnost certifikátu vyprší po tři roky a můžete obnovit kdykoli.

### <a name="check-expiry"></a>Kontrola vypršení platnosti

Pro nasazení serveru konfigurace před může 2016 vypršení platnosti certifikátu byla nastavena na jeden rok. Pokud máte certifikát se blíží uplynutí platnosti, dojde k následujícímu:

- Když datum vypršení platnosti je dvou měsíců nebo méně, spuštění služby odesílání oznámení na portálu a e-mailem (Pokud se přihlášení k odběru oznámení Azure Site Recovery).
- Na stránce prostředků úložiště se zobrazí nápis informující o oznámení. Klikněte na tlačítko Banner informující o další podrobnosti.
- Pokud se zobrazí **upgradovat nyní** tlačítko, to znamená, že se některé součásti ve vašem prostředí, které nebyly aktualizovány na verzi 9.4.xxxx.x nebo vyšší verze. Upgrade součástí než obnovení certifikátu. Nelze obnovit na starší verze.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V úložišti, otevřete **infrastruktura Site Recovery** > **konfigurační Server**a klikněte na požadované konfigurační server.
2. Datum vypršení platnosti se zobrazí pod **stav konfigurace serveru**
3. Klikněte na tlačítko **obnovení certifikátů**. 




## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Další postup

Přečtěte si podrobné pokyny pro nastavení zotavení po havárii [fyzických serverů](tutorial-physical-to-azure.md) do Azure.

