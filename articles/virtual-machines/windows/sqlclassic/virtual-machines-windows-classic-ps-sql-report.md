---
title: Vytvoření virtuálního počítače se serverem sestav v nativním režimu pomocí prostředí PowerShell | Microsoft Docs
description: 'Toto téma popisuje a provede nasazení a konfiguraci serveru sestav v nativním režimu SQL Server Reporting Services ve virtuální počítač Azure. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: edfae3a56bc13e4c41a1676bfc0f4e8cf4cd9d30
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Použití PowerShellu k vytvoření virtuálního počítače Azure se serverem sestav v nativním režimu
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Toto téma popisuje a provede nasazení a konfiguraci serveru sestav v nativním režimu SQL Server Reporting Services ve virtuální počítač Azure. Kroky v tomto dokumentu použít kombinaci ruční kroky k vytvoření virtuálního počítače a skript prostředí Windows PowerShell ke konfiguraci služby Reporting Services na virtuální počítač. Konfigurační skript zahrnuje otevřít port brány firewall pro protokol HTTP nebo HTTPs.

> [!NOTE]
> Pokud nechcete, aby **HTTPS** na serveru sestav **přeskočit krok 2**.
> 
> Po vytvoření virtuálního počítače v kroku 1, přejděte k části pomocí skriptu pro konfiguraci serveru sestav a HTTP. Po spuštění skriptu na serveru sestav je připravený k použití.

## <a name="prerequisites-and-assumptions"></a>Požadavky a předpoklady
* **Předplatné Azure**: ověření počtu jader v rámci vašeho předplatného Azure k dispozici. Pokud vytvoříte doporučená velikost virtuálního počítače u **A3**, je nutné **4** dostupné jader. Pokud používáte velikost virtuálního počítače u **A2**, je nutné **2** dostupné jader.
  
  * Ověření základní limit vašeho předplatného, na portálu Azure klikněte na nastavení v levém podokně a pak klikněte na tlačítko využití v horní nabídce.
  * Obraťte se na základní kvótu zvýšit, [podporu Azure](https://azure.microsoft.com/support/options/). Informace o velikosti virtuálních počítačů, najdete v části [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell skriptování**: tématu se předpokládá, že máte základní znalosti pracovní prostředí Windows PowerShell. Další informace o používání prostředí Windows PowerShell naleznete v následujících tématech:
  
  * [Spuštění prostředí Windows PowerShell v systému Windows Server](https://technet.microsoft.com/library/hh847814.aspx)
  * [Začínáme s prostředím Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Krok 1: Zřídit virtuální počítač Azure
1. Přejděte na portál Azure.
2. Klikněte na tlačítko **virtuální počítače** v levém podokně.
   
    ![virtuální počítače Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Klikněte na možnost **Nové**.
   
    ![tlačítko Nový](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Klikněte na tlačítko **z Galerie**.
   
    ![nový virtuální počítač z Galerie](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Klikněte na tlačítko **SQL Server 2014 RTM Standard – Windows Server 2012 R2** a pak klikněte na šipku pokračujte.
   
    ![další](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Pokud potřebujete data služby Reporting Services řízené funkce předplatných, vyberte **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Další informace o edicích systému SQL Server a podporovaných funkcích najdete v tématu [funkce, které jsou podporovány edice systému SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Na **konfigurace virtuálního počítače** stránky, upravte následující pole:
   
   * Pokud je více než jedna **datum vydání verze**, vyberte nejnovější verzi.
   * **Název virtuálního počítače**: název počítače se používá na další stránce konfigurace jako výchozí název cloudové služby DNS. Název DNS musí být jedinečný v rámci služby Azure. Vezměte v úvahu konfigurace virtuálního počítače s názvem počítače, který popisuje, co se virtuální počítač používá. Například ssrsnativecloud.
   * **Úroveň**: standardní
   * **Velikost: A3** je doporučená velikost virtuálního počítače pro úlohy SQL serveru. Pokud virtuální počítač je použít jenom jako server sestav, je dostatečná velikost virtuálního počítače z A2, pokud server sestav vyskytne velké zatížení. Informace o cenách virtuálních počítačů, najdete v části [ceny služeb Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nové uživatelské jméno**: jméno zadáte se vytvoří jako správce ve virtuálním počítači.
   * **Nové heslo** a **potvrďte**. Toto heslo se používá pro nový účet správce a doporučuje se, že používáte silné heslo.
   * Klikněte na **Další**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na další stránce upravte následující pole:
   
   * **Cloudová služba**: vyberte **vytvořte novou Cloudovou službu**.
   * **Cloudové služby DNS název**: Toto je veřejný název DNS cloudové služby, která souvisí s virtuálním Počítačem. Výchozí název je název, který jste zadali v pro název virtuálního počítače. Pokud v dalších krocích tohoto tématu můžete vytvořit důvěryhodný certifikát SSL a pak se používá název DNS pro hodnotu "**vydané**" certifikátu.
   * **Oblast nebo vztahů skupiny nebo virtuální síť**: Vyberte oblast, která je nejblíž k vašim koncovým uživatelům.
   * **Účet úložiště**: použít účet automaticky generované úložiště.
   * **Skupina dostupnosti**: žádné.
   * **Koncové body** zachovat **vzdálené plochy** a **prostředí PowerShell** koncových bodů a poté přidejte HTTP nebo HTTPS koncový bod, v závislosti na vašem prostředí.
     
     * **HTTP**: výchozí veřejné a privátní porty jsou **80**. Všimněte si, že pokud používáte privátní port než 80, změňte **$HTTPport = 80** ve skriptu http.
     * **HTTPS**: výchozí veřejné a privátní porty jsou **443**. Nejlepším postupem zabezpečení je změnit privátní port a konfiguraci brány firewall a server sestav použít privátní port. Další informace o koncových bodů najdete v tématu [jak nastavit komunikaci s virtuálním počítačem](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si, že pokud používáte jiný port než 443, změňte parametr **$HTTPsport = 443** ve skriptu HTTPS.
   * Klikněte na tlačítko Další. ![další](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na poslední stránce průvodce, ponechte výchozí **nainstalujte agenta virtuálního počítače** vybrané. Kroky v tomto tématu využívají není agent virtuálního počítače, ale pokud budete chtít zachovat tento virtuální počítač, agent virtuálního počítače a rozšíření vám umožní vylepšit mu CM.  Další informace o agenta virtuálního počítače najdete v tématu [agenta virtuálního počítače a rozšíření – část 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Jedním z výchozí nainstalovaná rozšíření služby ad systémem je rozšíření "BGINFO", která zobrazuje na ploše virtuálního počítače, informace o systému, jako je například interní IP adresy a volného místa na disku.
9. Klikněte na dokončení. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. **Stav** virtuálního počítače zobrazí jako **počáteční (zřizování)** během procesu zřizování a potom zobrazí jako **systémem** Pokud virtuální počítač je zřízený a připravené k použití.

## <a name="step-2-create-a-server-certificate"></a>Krok 2: Vytvoření certifikátu serveru
> [!NOTE]
> Pokud nechcete, aby HTTPS na serveru sestav, můžete **přeskočit krok 2** a přejděte k části **použít skript ke konfiguraci serveru sestav a HTTP**. Pomocí tohoto skriptu HTTP rychle nakonfigurovat server sestav a server sestav budou připravené k použití.

Chcete-li používat protokol HTTPS na virtuálním počítači, musíte certifikát SSL a důvěryhodné. V závislosti na scénáři můžete použít jednu z následujících dvou metod:

* Platný certifikát SSL vydaný certifikační autoritou (CA) a Microsoft za důvěryhodnou. Kořenové certifikáty certifikační Autority je potřeba distribuována prostřednictvím programu Microsoft Root Certificate Program. Další informace o tomto programu najdete v tématu [Windows a Windows Phone 8 SSL Root Certificate Program (člen certifikační autority)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) a [Úvod do Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Certifikát podepsaný svým držitelem. Certifikáty podepsané svým držitelem nejsou doporučuje pro provozní prostředí.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Chcete-li použít certifikát důvěryhodné certifikační autority (CA) vytvořený a
1. **Žádosti o certifikát serveru pro web od certifikační autority**. 
   
    Průvodce certifikátem webového serveru můžete použít ke generování soubor žádosti o certifikát (Certreq.txt), který odeslat certifikační autoritě nebo k vytvoření žádosti pro online certifikační autoritu. Například certifikační služby společnosti Microsoft ve Windows serveru 2012. V závislosti na úroveň záruky identifikace poskytované certifikátem serveru je několik dní několik měsíců pro certifikační autoritu schválí vaši žádost a odešle je soubor certifikátu. 
   
    Další informace o vyžádání certifikátů serveru naleznete v následujících tématech: 
   
   * Použití [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Nástroje zabezpečení pro správu systému Windows Server 2012.
     
     [Nástroje zabezpečení pro správu systému Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > **Vydané** pole důvěryhodný certifikát SSL by měl být stejný jako **název cloudové služby DNS** jste použili pro nový virtuální počítač.

2. **Instalace certifikátu serveru na webovém serveru**. Webový server v tomto případě je virtuální počítač, který hostuje server sestav a vytvoří se web v dalších krocích při konfiguraci služby Reporting Services. Další informace o instalaci certifikátu serveru na webovém serveru pomocí modulu snap-in konzoly MMC certifikát najdete v tématu [nainstalovat certifikát serveru](https://technet.microsoft.com/library/cc740068).
   
    Pokud chcete použít skript dodaný spolu s tohoto tématu ke konfiguraci serveru sestav, hodnota certifikáty **kryptografický otisk** se vyžaduje jako parametr skriptu. Najdete v části Další podrobnosti o tom, jak získat kryptografický otisk certifikátu.
3. Přiřadíte certifikát serveru k serveru sestav. Přiřazení byla dokončena v další části, při konfiguraci serveru sestav.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Chcete-li použít certifikát podepsaný svým držitelem virtuální počítače
Certifikát podepsaný svým držitelem byl vytvořen ve virtuálním počítači, při zřizování virtuálního počítače. Certifikát má stejný název jako název DNS virtuálního počítače. Aby se zabránilo chybám certifikát, je vyžadován certifikát, který je důvěryhodný na virtuální počítač a také všichni uživatelé lokality.

1. Tak, aby důvěřoval kořenové certifikační Autority certifikátu na místní počítač, přidejte certifikát, který chcete **důvěryhodné kořenové certifikační autority**. Zde je souhrn kroky. Podrobné pokyny o tom, jak důvěřovat certifikační Autoritě, najdete v tématu [nainstalovat certifikát serveru](https://technet.microsoft.com/library/cc740068).
   
   1. Z portálu Azure vyberte virtuální počítač a klikněte na tlačítko Připojit. V závislosti na konfiguraci vašeho prohlížeče zobrazí se výzva k uložení souboru RDP pro připojení k virtuálnímu počítači.
      
       ![připojení k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte název virtuálního počítače uživatele, uživatelské jméno a heslo, které jste konfigurovali při vytváření virtuálního počítače. 
      
       Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
      
       ![název virtuálního počítače jejíž součástí přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Spusťte mmc.exe. Další informace najdete v tématu [postupy: zobrazení certifikátů pomocí modulu Snap-in konzoly MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. V konzolové aplikaci **soubor** nabídky, přidejte **certifikáty** modul snap-in, vyberte **účet počítače** při zobrazení výzvy a potom klikněte na **Další**.
   4. Vyberte **místního počítače** pro správu a pak klikněte na tlačítko **Dokončit**.
   5. Klikněte na tlačítko **Ok** a potom rozbalte **certifikáty – osobní** uzly a pak klikněte na tlačítko **certifikáty**. Certifikát je pojmenovaný po název DNS virtuálního počítače a končí **cloudapp.net**. Klikněte pravým tlačítkem na název certifikátu a klikněte na tlačítko **kopie**.
   6. Rozbalte **důvěryhodné kořenové certifikační autority** uzel a potom klikněte pravým tlačítkem na **certifikáty** a pak klikněte na **vložení**.
   7. Chcete-li ověřit, dvakrát klikněte na název certifikátu v části **důvěryhodné kořenové certifikační autority** a ověřte, že nejsou žádné chyby a svůj certifikát. Pokud chcete nakonfigurovat server sestav, hodnota certifikáty pomocí skriptu HTTPS Toto téma je součástí **kryptografický otisk** se vyžaduje jako parametr skriptu. **Chcete-li získat hodnotu kryptografického otisku**, proveďte následující kroky. Je také ukázku prostředí PowerShell k načtení kryptografického otisku v části [použít skript ke konfiguraci serveru sestav a HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Dvakrát klikněte na název certifikátu, například ssrsnativecloud.cloudapp.net.
      2. Klikněte **podrobnosti** kartě.
      3. Klikněte na tlačítko **kryptografický otisk**. Hodnota kryptografického otisku je zobrazena v poli podrobnosti, třeba a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Zkopírujte kryptografický otisk a uložit pro pozdější hodnotu nebo upravte skript teď.
      5. (*) Před spuštěním skriptu odeberte mezery mezi dvojice hodnot. Například kryptografický otisk zjištěno před by nyní a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Přiřadíte certifikát serveru k serveru sestav. Přiřazení byla dokončena v další části, při konfiguraci serveru sestav.

Pokud používáte certifikát podepsaný svým držitelem SSL, odpovídá názvu na certifikátu již název hostitele virtuálního počítače. Proto DNS počítače je již zaregistrován globálně a je přístupná z libovolného klienta.

## <a name="step-3-configure-the-report-server"></a>Krok 3: Konfigurace serveru sestav
Tato část vás provede procesem konfigurace virtuálního počítače jako server sestav služby Reporting Services v nativním režimu. Jeden z následujících metod vám pomůže nakonfigurovat server sestav:

* Použít skript, který chcete nakonfigurovat server sestav
* Pomocí nástroje Configuration Manager ke konfiguraci serveru sestav.

Podrobné kroky, najdete v části [připojení k virtuálnímu počítači a spuštění Správce konfigurace služby Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Poznámka: ověřování:** ověřování systému Windows je doporučená metoda ověřování a je výchozí ověřování služby Reporting Services. Pouze uživatelé, kteří jsou nakonfigurované na virtuální počítač přístup k službě Reporting Services a přiřadit do rolí služby Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Konfigurovat server sestav a HTTP pomocí skriptu
Použití skriptu prostředí Windows PowerShell ke konfiguraci serveru sestav, proveďte následující kroky. Konfigurace zahrnuje protokolu HTTP, nikoli HTTPS:

1. Z portálu Azure vyberte virtuální počítač a klikněte na tlačítko Připojit. V závislosti na konfiguraci vašeho prohlížeče zobrazí se výzva k uložení souboru RDP pro připojení k virtuálnímu počítači.
   
    ![připojení k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte název virtuálního počítače uživatele, uživatelské jméno a heslo, které jste konfigurovali při vytváření virtuálního počítače. 
   
    Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
   
    ![název virtuálního počítače jejíž součástí přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na virtuální počítač, otevřete **Windows PowerShell ISE** s oprávněními správce. Prostředí PowerShell ISE je nainstalována ve výchozím nastavení v systému Windows server 2012. Doporučuje se, že používáte ISE místo standardní okno prostředí Windows PowerShell, aby mohli vložit skriptu do ISE, upravte skript a spusťte skript.
3. V systému Windows PowerShell ISE, klikněte **zobrazení** nabídce a pak klikněte na tlačítko **zobrazit podokno skriptu**.
4. Zkopírujte následující skript a vložte skript do podokna skriptu Windows PowerShell ISE.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Pokud vytvoříte virtuální počítač s k portu HTTP než 80, upravte parametr $HTTPport = 80.
6. Skript je aktuálně konfigurována pro službu Reporting Services. Pokud chcete spustit skript pro službu Reporting Services, upravte část verze cesta k oboru názvů verze "11", na příkaz Get-WmiObject.
7. Spusťte skript.

**Ověření**: Pokud chcete ověřit, že funkce základní sestavy serveru funguje, najdete v článku [ověřit konfiguraci](#verify-the-configuration) později v tomto tématu.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Použít skript ke konfiguraci serveru sestav a HTTPS
Pomocí prostředí Windows PowerShell ke konfiguraci serveru sestav, proveďte následující kroky. Konfigurace zahrnuje protokol HTTPS, nikoli protokol HTTP.

1. Z portálu Azure vyberte virtuální počítač a klikněte na tlačítko Připojit. V závislosti na konfiguraci vašeho prohlížeče zobrazí se výzva k uložení souboru RDP pro připojení k virtuálnímu počítači.
   
    ![připojení k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte název virtuálního počítače uživatele, uživatelské jméno a heslo, které jste konfigurovali při vytváření virtuálního počítače. 
   
    Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
   
    ![název virtuálního počítače jejíž součástí přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na virtuální počítač, otevřete **Windows PowerShell ISE** s oprávněními správce. Prostředí PowerShell ISE je nainstalována ve výchozím nastavení v systému Windows server 2012. Doporučuje se, že používáte ISE místo standardní okno prostředí Windows PowerShell, aby mohli vložit skriptu do ISE, upravte skript a spusťte skript.
3. Pokud chcete povolit spouštění skriptů, spusťte následující příkaz prostředí Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Potom můžete spustit následující příkaz pro ověření zásad:
   
        Get-ExecutionPolicy
4. V **Windows PowerShell ISE**, klikněte **zobrazení** nabídce a pak klikněte na tlačítko **zobrazit podokno skriptu**.
5. Zkopírujte následující skript a vložte ho do podokna skriptu Windows PowerShell ISE.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Změnit **$certificatehash** parametr ve skriptu:
   
   * Toto je **požadované** parametr. Pokud hodnotu certifikátu nebyla uložena z předchozích kroků, použijte jednu z následujících metod zkopírujte hodnotu hash certifikátu z certifikátů kryptografický otisk.:
     
       Ve virtuálním počítači otevřete Windows PowerShell ISE a spusťte následující příkaz:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Výstup bude vypadat podobně jako následující. Pokud skript vrátí prázdný řádek, virtuální počítač nemá certifikát třeba nakonfigurovat, najdete v části [použít certifikát podepsaný svým držitelem virtuálních počítačů](#to-use-the-virtual-machines-self-signed-certificate).
     
     NEBO
   * Ve virtuálním počítači spustit mmc.exe a poté přidejte **certifikáty** modul snap-in.
   * V části **důvěryhodné kořenové certifikační úřady** uzlu, dvakrát klikněte na název certifikátu. Pokud používáte certifikát podepsaný svým držitelem virtuálního počítače, certifikát název po název DNS virtuálního počítače a končí **cloudapp.net**.
   * Klikněte **podrobnosti** kartě.
   * Klikněte na tlačítko **kryptografický otisk**. Hodnota kryptografického otisku je zobrazena v poli podrobnosti, třeba af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Před spuštěním skriptu**, odebrat mezery mezi dvojice hodnot. Například af1160b64b288d890a8212ff6ba9c3664f319048
7. Změnit **$httpsport** parametr: 
   
   * Pokud jste použili port 443 pro koncový bod HTTPS, není potřeba aktualizovat tento parametr ve skriptu. Jinak použijte hodnotu portu, který jste vybrali při konfiguraci privátní koncový bod HTTPS na virtuálním počítači.
8. Změnit **$DNSName** parametr: 
   
   * Skript je nakonfigurován pro zástupný certifikát $DNSName = "+". Pokud chcete nakonfigurovat pro vazbu certifikátu zástupný znak, komentář $DNSName ="+"a povolte následující řádek, úplná $DNSNAme odkaz, ## $DNSName="$server.cloudapp.net".
     
       Změňte hodnotu $DNSName, pokud nechcete použít název DNS virtuálního počítače pro službu Reporting Services. Pokud použijete parametr, certifikát, musíte taky použít tento název a zaregistrujte název globálně na serveru DNS.
9. Skript je aktuálně konfigurována pro službu Reporting Services. Pokud chcete spustit skript pro službu Reporting Services, upravte část verze cesta k oboru názvů verze "11", na příkaz Get-WmiObject.
10. Spusťte skript.

**Ověření**: Pokud chcete ověřit, že funkce základní sestavy serveru funguje, najdete v článku [ověřit konfiguraci](#verify-the-connection) později v tomto tématu. Pro ověření certifikátu vazbu otevřete příkazový řádek s oprávněními správce a spusťte následující příkaz:

    netsh http show sslcert

Výsledkem bude zahrnovat následující:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Použijte nástroj Configuration Manager ke konfiguraci serveru sestav
Pokud nechcete spustit skript prostředí PowerShell, který chcete nakonfigurovat server sestav, postupujte podle kroků v této části Konfigurace serveru sestav pomocí nástroje configuration manager služby Reporting Services v nativním režimu.

1. Z portálu Azure vyberte virtuální počítač a klikněte na tlačítko Připojit. Použijte uživatelské jméno a heslo, které jste konfigurovali při vytváření virtuálního počítače.
   
    ![připojení k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Spusťte službu Windows update a instalaci aktualizací do virtuálního počítače. Pokud je vyžadováno restartování virtuálního počítače, restartujte virtuální počítač a znovu připojit k virtuálnímu počítači z portálu Azure.
3. Z nabídky Start na virtuálním počítači, zadejte **služby Reporting Services** a otevřete **Správce konfigurace služby Reporting Services**.
4. Ponechte výchozí hodnoty pro **název serveru** a **instanci serveru sestav**. Klikněte na **Připojit**.
5. V levém podokně klikněte na **adresa URL webové služby**.
6. Ve výchozím nastavení je RS nakonfigurován pro protokol HTTP port 80 s IP Adresou "Všechny přiřazené". Chcete-li přidat HTTPS:
   
   1. V **certifikát SSL**: Vyberte certifikát, který chcete použít, například [název virtuálního počítače]. cloudapp.net. Pokud nejsou uvedeny žádné certifikáty, najdete v části **krok 2: vytvoření certifikátu serveru** informace o tom, jak nainstalovat a důvěřovat certifikátu na virtuálním počítači.
   2. V části **SSL Port**: Zvolte 443. Pokud jste nakonfigurovali privátní koncový bod HTTPS ve virtuálním počítači s jinou privátní port, použijte tuto hodnotu v tomto poli.
   3. Klikněte na tlačítko **použít** a počkejte na dokončení operace.
7. V levém podokně klikněte na **databáze**.
   
   1. Klikněte na tlačítko **změnit Databas**e.
   2. Klikněte na tlačítko **vytvořit novou databázi serveru sestav** a pak klikněte na **Další**.
   3. Ponechte výchozí nastavení **název serveru**: jako virtuální počítač zadejte název a ponechte výchozí nastavení **typ ověřování** jako **aktuální uživatel** – **integrované zabezpečení**. Klikněte na **Další**.
   4. Ponechte výchozí nastavení **název databáze** jako **ReportServer** a klikněte na tlačítko **Další**.
   5. Ponechte výchozí nastavení **typ ověřování** jako **pověření služby** a klikněte na tlačítko **Další**.
   6. Klikněte na tlačítko **Další** na **Souhrn** stránky.
   7. Po dokončení konfigurace klikněte na tlačítko **Dokončit**.
8. V levém podokně klikněte na **adresa URL správce sestav**. Ponechte výchozí nastavení **virtuální adresář** jako **sestavy** a klikněte na tlačítko **použít**.
9. Klikněte na tlačítko **ukončení** zavřete Správce konfigurace služby Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Krok 4: Port brány Firewall systému Windows otevřete
> [!NOTE]
> Pokud jste použili jedno skripty ke konfiguraci serveru sestav, můžete tuto část přeskočit. Skript zahrnuty krok otevřít port brány firewall. Výchozí je port 80 pro protokol HTTP a port 443 pro protokol HTTPS.
> 
> 

Chcete-li vzdáleně připojit ke správci sestav nebo Server sestav na virtuálním počítači, je třeba koncový bod TCP ve virtuálním počítači. Je třeba otevřít stejný port v bráně firewall Virtuálního počítače. Koncový bod byl vytvořen při zřizování virtuálního počítače.

Tato část obsahuje základní informace o tom, jak otevřít port brány firewall. Další informace najdete v tématu [Configure a Firewall for Report Server Access](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Pokud jste použili skript ke konfiguraci serveru sestav, můžete tuto část přeskočit. Skript zahrnuty krok otevřít port brány firewall.
> 
> 

Pokud jste nakonfigurovali privátní port pro protokol HTTPS 443, upravte následující skript správně. Chcete-li otevřít port **443** v bráně Windows Firewall, proveďte následující kroky:

1. Otevřete okno prostředí Windows PowerShell s oprávněními správce.
2. Pokud jste použili jiný port než 443, když jste nakonfigurovali koncový bod HTTPS na virtuálním počítači, aktualizujte port v následujícím příkazu a pak spusťte příkaz:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Po dokončení příkazu **Ok** se zobrazí v příkazovém řádku.

Pokud chcete ověřit, že port je otevřené, otevřete okno prostředí Windows PowerShell a spusťte následující příkaz:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Ověření konfigurace
A ověřte, že funkce serveru základní sestavy je nyní pracuje, otevřete prohlížeč s oprávněními správce a potom vyhledejte následující server ad sestavy správce sestav adresy URL:

* Ve virtuálním počítači přejděte na adresu URL serveru sestav:
  
        http://localhost/reportserver
* Ve virtuálním počítači přejděte na adresu URL správce sestav:
  
        http://localhost/Reports
* Z místního počítače, vyhledejte **vzdáleného** sestavy správce ve virtuálním počítači. Název DNS v následujícím příkladu podle potřeby aktualizujte. Po zobrazení výzvy k zadání hesla, použijte Správce přihlašovacích údajů, které jste vytvořili při zřizování virtuálního počítače. Uživatelské jméno je v [doméně]\[uživatelské jméno] formátu, kde doména je název počítače virtuálních počítačů, například ssrsnativecloud\testuser. Pokud nepoužíváte HTTP**S**, odeberte **s** v adrese URL. Najdete v části Další informace o vytvoření dalších uživatelů na virtuálním počítači.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Z místního počítače přejděte na adresu URL sestavu vzdáleného serveru. Název DNS v následujícím příkladu podle potřeby aktualizujte. Pokud nepoužíváte protokol HTTPS, odeberte s v adrese URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Vytvoření uživatelů a přiřazování rolí
Běžné úlohy správy po konfigurace a ověření na serveru sestav, je vytvoření jednoho nebo více uživatelů a přiřadit uživatele k rolím služby Reporting Services. Další informace naleznete v následujících tématech:

* [Vytvořte místní uživatelský účet](https://technet.microsoft.com/library/cc770642.aspx)
* [Uživatelům udělit přístup k serveru sestav (Správce sestav)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Vytvoření a správa přiřazení rolí](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>K vytvoření a publikování sestav pro virtuální počítač Azure
Následující tabulka shrnuje některé možnosti, které jsou k dispozici pro publikování stávajících sestavách v místním počítači na server sestav, které jsou hostované na virtuální počítač Microsoft Azure:

* **Skript RS.exe**: použití RS.exe skriptu zkopírovat položky sestavy z a existující server sestav pro váš virtuální počítač Microsoft Azure. Další informace najdete v části "Nativního režimu do nativního režimu – virtuálním počítači Microsoft Azure" v [rs.exe Reporting Services ukázkový skript pro migraci obsahu mezi servery sestav](https://msdn.microsoft.com/library/dn531017.aspx).
* **Tvůrce sestav**: obsahuje virtuální počítač a klikněte na-jednou verzi Tvůrce sestav Microsoft SQL Server. Spuštění sestavy Tvůrce první na virtuálním počítači:
  
  1. Spustíte prohlížeč s oprávněními správce.
  2. Přejděte do Správce sestav na virtuálním počítači a klikněte na tlačítko **Tvůrce sestav** na pásu karet.
     
     Další informace najdete v tématu [instalace, odinstalace a podpora Tvůrce sestav](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: Virtuální počítač**: Pokud jste vytvořili virtuální počítač s SQL serverem 2012, pak SQL Server Data Tools je nainstalovaná na virtuálním počítači a slouží k vytvoření **projekty serveru sestav** a sestavy na virtuálním počítači. SQL Server Data Tools můžete publikovat sestavy na server sestav na virtuálním počítači.
  
    Pokud jste vytvořili virtuální počítač se systémem SQL server 2014, můžete nainstalovat SQL Server Data Tools - BI pro sadu visual Studio. Další informace naleznete v následujících tématech:
  
  * [Nástroje Microsoft SQL Server Data Tools – Business Intelligence pro Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Nástroje Microsoft SQL Server Data Tools – Business Intelligence pro sadu Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [Nástroje SQL Server Data Tools a SQL Server Business Intelligence (SSDT-BI)](http://curah.microsoft.com/30004/sql-server-data-tools-ssdt-and-sql-server-business-intelligence)
* **SQL Server Data Tools: Vzdálené**: V místním počítači, vytvoření projektu služby Reporting Services v SQL Server Data Tools, obsahující sestavy služby Reporting Services. Konfigurace projektu pro připojení k adresa URL webové služby.
  
    ![Vlastnosti projektu rozšíření SSDT pro projekt služby SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Použít skript**: kopírování obsahu serveru sestav pomocí skriptu. Další informace najdete v tématu [rs.exe Reporting Services ukázkový skript pro migraci obsahu mezi servery sestav](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimalizovat náklady, pokud nepoužíváte virtuální počítač
> [!NOTE]
> Chcete-li minimalizovat náklady pro virtuální počítače Azure když není používán, vypněte virtuální počítač z portálu Azure. Pokud použijete možnosti napájení Windows uvnitř virtuálního počítače vypnout virtuální počítač, jsou stále účtovat stejnou úroveň pro virtuální počítač. Chcete-li snížit náklady, je potřeba vypnout virtuální počítač na portálu Azure. Pokud již nepotřebujete virtuální počítač, nezapomeňte odstranit virtuální počítač a soubory VHD související náklady na úložiště. Další informace najdete v tématu v části Nejčastější dotazy na [podrobnosti o cenách virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Další informace
### <a name="resources"></a>Zdroje a prostředky
* Podobně jako obsah týkající se nasazení jednoho serveru SQL Server Business Intelligence a SharePoint 2013, najdete v části [použití prostředí Windows PowerShell k vytvoření virtuálního počítače Azure s SQL Server BI a SharePoint 2013](https://msdn.microsoft.com/library/azure/dn385843.aspx).
* Podobně jako obsah týkající se nasazení s více servery SQL Server Business Intelligence a SharePoint 2013, najdete v části [nasazení SQL Server Business Intelligence v Azure Virtual Machines](https://msdn.microsoft.com/library/dn321998.aspx).
* Obecné informace týkající se nasazení systému SQL Server Business Intelligence v Azure Virtual Machines najdete v tématu [SQL Server Business Intelligence v Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Další informace o náklady na poplatky za výpočty v Azure, najdete v části kartu virtuální počítače z [Azure cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Komunitním obsahu
* Podrobné informace o tom, jak vytvořit Reporting Services na nativní režim serveru sestav bez použití skriptu, najdete v části [hostování služby SQL Reporting Services na virtuální počítač Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Odkazy na další zdroje pro systém SQL Server ve virtuálních počítačích Azure
[SQL Server na virtuálních počítačích Azure – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

