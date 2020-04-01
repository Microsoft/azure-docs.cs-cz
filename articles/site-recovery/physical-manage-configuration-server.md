---
title: Správa konfiguračního serveru pro fyzické servery v azure site recovery
description: Tento článek popisuje, jak spravovat konfigurační server Azure Site Recovery pro fyzické zotavení po havárii serveru do Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: eb7e891c031be5ac01295905d5c3304dc6818737
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478962"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Správa konfiguračního serveru pro zotavení po havárii fyzického serveru

Místní konfigurační server nastavíte, když používáte službu [Azure Site Recovery](site-recovery-overview.md) pro zotavení po havárii fyzických serverů do Azure. Konfigurační server koordinuje komunikaci mezi místními počítači a Azure a spravuje replikaci dat. Tento článek shrnuje běžné úlohy pro správu konfiguračního serveru po jeho nasazení.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Tabulka shrnuje předpoklady pro nasazení místního počítače konfiguračního serveru.

| **Komponenta** | **Požadavek** |
| --- |---|
| Procesorová jádra| 8 |
| Paměť RAM | 16 GB|
| Počet disků | 3, včetně disku operačního systému, disku mezipaměti procesu a retenční jednotky pro navrácení služeb po selhání |
| Volné místo na disku (mezipaměť procesového serveru) | 600 GB
| Volné místo na disku (disk pro uchování) | 600 GB|
| Operační systém  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Národní prostředí operačního systému | Angličtina (USA)|
| Verze VMware vSphere PowerCLI | Není požadováno|
| Role Windows Serveru | Nepovolujte tyto role: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V |
| Zásady skupiny| Nepovolujte tyto zásady skupiny: <br> - Zabránit přístupu k příkazovému řádku <br> - Zabránit přístupu k nástrojům pro úpravu registru <br> - Logika důvěryhodnosti pro přílohy souborů <br> - Zapnutí spuštění skriptu <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Žádné pre-existující výchozí webové stránky <br> - Povolit [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Povolit nastavení [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Žádné pre-existující webové stránky / aplikace poslech na portu 443<br>|
| Typ nic | VMXNET3 (při nasazení jako virtuální virtuální měna VMware) |
| Typ IP adresy | Statická |
| Přístup k internetu | Server potřebuje přístup k těmto adresám URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(není vyžadováno pro horizontální navýšení kapacity procesních serverů) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (orchestrace řídicího kanálu)<br>9443 (přenos dat)|

## <a name="download-the-latest-installation-file"></a>Stažení nejnovějšího instalačního souboru

Nejnovější verze instalačního souboru konfiguračního serveru je k dispozici na portálu site recovery. Kromě toho jej lze stáhnout přímo ze služby [Stažení softwaru](https://aka.ms/unifiedsetup).

1. Přihlaste se k portálu Azure a vyhledejte trezor služby Recovery Services.
2. Přejděte na > **servery konfigurace infrastruktury** **pro obnovení lokality**(v části Pro vmware & fyzické počítače).
3. Klikněte na tlačítko **+Servery.**
4. Na stránce **Přidat server** klikněte na tlačítko Stáhnout a stáhněte si registrační klíč. Tento klíč potřebujete během instalace konfiguračního serveru k jeho registraci pomocí služby Azure Site Recovery.
5. Kliknutím na odkaz **Stáhnout sjednocené nastavení obnovení webu Microsoft Azure** stáhněte nejnovější verzi konfiguračního serveru.

   ![Stránka ke stažení](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalace a registrace serveru

1. Spusťte instalační soubor sjednocené instalace.
2. V **části Before You Begin**vyberte Install the configuration server and process **server**.

    ![Než začnete](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Na stránce **Licence k softwaru jiného výrobce** vyberte **Souhlasím** pro stažení a instalaci MySQL.
4. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet. Ujistěte se, že jste povolili požadované adresy URL.

    - Pokud se chcete připojit k proxy serveru, který je aktuálně nastavený v počítači, vyberte **Připojit k Azure Site Recovery pomocí proxy serveru**.
    - Pokud chcete, aby se poskytovatel připojil přímo, vyberte **Připojit přímo k Azure Site Recovery bez proxy serveru**.
    - Pokud existující proxy server vyžaduje ověření nebo pokud chcete použít vlastní proxy server pro připojení zprostředkovatele, vyberte **Připojit s vlastním nastavením proxy**serveru a zadejte adresu, port a pověření.
     ![Brána firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

    ![Požadavky](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Na stránce **Konfigurace MySQL** vytvořte přihlašovací údaje pro přihlašování k nainstalované instanci serveru MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pokud ano, instalační program zkontroluje, zda je nainstalován powercli 6.0.
9. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.

    ![Umístění instalace](./media/physical-manage-configuration-server/combined-wiz8.png)
10. V **části Výběr sítě**nejprve vyberte síťovou síťovou koutek, kterou integrovaný procesní server používá pro zjišťování a nabízenou instalaci služby mobility ve zdrojových počítačích, a pak vyberte síťovou síťovou konfigurační server, kterou konfigurační server používá pro připojení k Azure. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Nepoužívejte port 443 pro odesílání nebo příjem replikačních přenosů.

    ![Výběr sítě](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.


Po dokončení registrace se server zobrazí v okně **Nastavení** > **serverů** v úschovně.


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Změna nastavení proxy serveru

Nastavení proxy serveru pro konfigurační server můžete upravit následujícím způsobem:

1. Přihlaste se ke konfiguračnímu serveru.
2. Spusťte soubor cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na kartu **Registrace trezoru.**
4. Stáhněte si nový registrační soubor úschovny z portálu a poskytněte jej jako vstup do nástroje.

   ![registr-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte nové podrobnosti proxy serveru a klepněte na tlačítko **Registrovat.**
6. Otevřete příkazové okno Prostředí PowerShell pro správu.
7. Spusťte následující příkaz:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Pokud máte k konfiguračnímu serveru připojeny další procesní servery, je třeba [opravit nastavení serveru proxy na všech horizontálních procesních serverech](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) ve vašem nasazení.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Opětovná registrace konfiguračního serveru se stejným trezorem
1. Přihlaste se k konfiguračnímu serveru.
2. Spusťte soubor cspsconfigtool.exe pomocí zástupce na ploše.
3. Klikněte na kartu **Registrace trezoru.**
4. Stáhněte si nový registrační soubor z portálu a poskytněte jej jako vstup do nástroje.
      ![registr-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Zadejte podrobnosti o serveru proxy a klepněte na tlačítko **Registrovat.**  
6. Otevřete příkazové okno Prostředí PowerShell pro správu.
7. Spusťte následující příkaz

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Pokud máte více procesních serverů, je třeba [je znovu zaregistrovat](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrace konfiguračního serveru s jiným trezorem

> [!WARNING]
> Následující krok odpojí konfigurační server od aktuálního úložiště a replikace všech chráněných virtuálních počítačů pod konfiguračním serverem je zastavena.

1. Přihlášení ke konfiguračnímu serveru
2. z příkazového řádku správce spusťte příkaz:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Spusťte soubor cspsconfigtool.exe pomocí zástupce na ploše.
4. Klikněte na kartu **Registrace trezoru.**
5. Stáhněte si nový registrační soubor z portálu a poskytněte jej jako vstup do nástroje.
6. Zadejte podrobnosti o serveru proxy a klepněte na tlačítko **Registrovat.**  
7. Otevřete příkazové okno Prostředí PowerShell pro správu.
8. Spusťte následující příkaz
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Upgrade konfiguračního serveru

Spuštěním kumulativní aktualizace můžete aktualizovat konfigurační server. Aktualizace lze použít až pro verze N-4. Například:

- Pokud používáte 9.7, 9.8, 9.9 nebo 9.10 - můžete upgradovat přímo na 9.11.
- Pokud používáte verzi 9.6 nebo starší a chcete upgradovat na verzi 9.11, musíte nejprve upgradovat na verzi 9.7. před 9.11.

Odkazy na kumulativní aktualizaci pro upgrade na všechny verze konfiguračního serveru jsou k dispozici na [stránce aktualizace wikiwebu](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgradujte server takto:

1. Stáhněte soubor instalačního programu aktualizace na konfigurační server.
2. Poklepáním spusťte instalační program.
3. Instalační program zjistí aktuální verzi spuštěnou v počítači.
4. Potvrďte to klepnutím na **tlačítko OK** a spusťte upgrade. 


## <a name="delete-or-unregister-a-configuration-server"></a>Odstranění nebo zrušení registrace konfiguračního serveru

> [!WARNING]
> Než začnete vyřazovat konfigurační server z provozu, ujistěte se, že jste ho vyřazovali z provozu.
> 1. [Zakažte ochranu](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) všech virtuálních počítačů v rámci tohoto konfiguračního serveru.
> 2. [Zrušte přidružení](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) a [odstranění](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) všech zásad replikace z konfiguračního serveru.
> 3. [Odstraňte](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) všechny servery vCenters/hostitele vSphere, kteří jsou přidruženi ke konfiguračnímu serveru.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Odstranění konfiguračního serveru z webu Azure Portal
1. Na webu Azure Portal přejděte na**konfigurační servery infrastruktury** **obnovení** > webu z nabídky Úložiště.
2. Klikněte na konfigurační server, který chcete vyřadit z provozu.
3. Na stránce podrobností konfiguračního serveru klikněte na tlačítko **Odstranit.**
4. Klepnutím na tlačítko **Ano** potvrďte odstranění serveru.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstalace konfiguračního serveru a jeho závislostí
> [!TIP]
>   Pokud máte v plánu znovu použít konfigurační server s Azure Site Recovery znovu, pak můžete přeskočit ke kroku 4 přímo

1. Přihlaste se ke konfiguračnímu serveru jako správce.
2. Otevření Ovládacího panelu > Program > Programy odinstalace programů
3. Odinstalujte programy v následujícím pořadí:
   * Microsoft Azure Recovery Services Agent
   * Služba mobility webu Microsoft Azure/hlavní cílový server
   * Zprostředkovatel obnovení webu Microsoft Azure
   * Konfigurační server/procesní server pro obnovení webu Microsoft Azure
   * Závislosti konfiguračního serveru obnovení webu Microsoft Azure
   * MySQL Server 5,5
4. Spusťte následující příkaz z příkazového řádku správce.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Odstranění nebo zrušení registrace konfiguračního serveru (PowerShell)

1. [Instalace](https://docs.microsoft.com/powershell/azure/install-Az-ps) Modul Azure PowerShell
2. Přihlášení k účtu Azure pomocí příkazu
    
    `Connect-AzAccount`
3. Vyberte předplatné, ve kterém je trezor k dispozici.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Nyní nastavte kontext trezoru
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Získejte výběr konfiguračního serveru

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Odstranění konfiguračního serveru

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Možnost **-Force** v remove-AzSiteRecoveryFabric lze vynutit odebrání nebo odstranění konfiguračního serveru.

## <a name="renew-tlsssl-certificates"></a>Obnovení certifikátů TLS/SSL
Konfigurační server má vestavěný webový server, který orchestruje aktivity služby Mobility, procesních serverů a hlavních cílových serverů, které jsou k němu připojeny. Webový server používá k ověřování klientů certifikát TLS/SSL. Platnost certifikátu vyprší po třech letech a lze jej kdykoli obnovit.

### <a name="check-expiry"></a>Zkontrolovat vypršení platnosti

U nasazení konfiguračního serveru před květnem 2016 byla expirace certifikátu nastavena na jeden rok. Pokud máte certifikát bude vyprší, dojde k následující:

- Pokud je datum vypršení platnosti dva měsíce nebo méně, služba začne odesílat oznámení na portálu a e-mailem (pokud jste se přihlásili k odběru oznámení Azure Site Recovery).
- Na stránce prostředků úschovny se zobrazí nápis s oznámením. Klikněte na banner pro více informací.
- Pokud se zobrazí tlačítko **Upgradovat,** znamená to, že ve vašem prostředí jsou některé součásti, které nebyly upgradovány na verze 9.4.xxxx.x nebo vyšší. Před obnovením certifikátu inovujte součásti. Ve starších verzích nelze obnovit.

### <a name="renew-the-certificate"></a>Obnovení certifikátu

1. V úschovně otevřete > **konfigurační server** **infrastruktury obnovení lokality**a klepněte na požadovaný konfigurační server.
2. Datum vypršení platnosti se zobrazí v části **Stav konfiguračního serveru**
3. Klepněte na **tlačítko Obnovit certifikáty**. 




## <a name="common-issues"></a>Běžné problémy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Další kroky

Projděte si kurzy pro nastavení zotavení po havárii [fyzických serverů](tutorial-physical-to-azure.md) do Azure.

