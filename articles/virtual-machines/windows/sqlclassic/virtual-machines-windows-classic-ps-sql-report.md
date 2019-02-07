---
title: Použití Powershellu k vytvoření virtuálního počítače se serverem sestav v nativním režimu | Dokumentace Microsoftu
description: 'Toto téma popisuje a provede nasazení a konfigurace serveru sestav v nativním režimu služby SQL Server Reporting Services ve virtuálním počítači Azure. '
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
ms.openlocfilehash: 8c12190e3c34c3294d2735fdd228aafbf6073f12
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55820109"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Použití PowerShellu k vytvoření virtuálního počítače Azure se serverem sestav v nativním režimu
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Toto téma popisuje a provede nasazení a konfigurace serveru sestav v nativním režimu služby SQL Server Reporting Services ve virtuálním počítači Azure. Kroky v tomto dokumentu pomocí kombinace vyžadováno provedení ručních kroků k vytvoření virtuálního počítače a skript prostředí Windows PowerShell pro konfiguraci služby Reporting Services na virtuálním počítači. Konfigurační skript obsahuje otevírání portu brány firewall pro protokol HTTP nebo HTTPs.

> [!NOTE]
> Pokud nechcete, aby **HTTPS** na serveru sestav **přeskočit krok 2**.
> 
> Po vytvoření virtuálního počítače v kroku 1, přejděte do části použijte skript pro konfiguraci serveru sestav a HTTP. Po spuštění skriptu je připravený k použití na serveru sestav.

## <a name="prerequisites-and-assumptions"></a>Požadavky a předpoklady
* **Předplatné Azure**: Ověřte, počet jader dostupných v předplatném Azure. Pokud vytvoříte doporučená velikost virtuálního počítače **A3**, budete potřebovat **4** dostupných jader. Pokud použijete velikost virtuálního počítače u **A2**, budete potřebovat **2** dostupných jader.
  
  * Ověření základní limitu vašeho předplatného na webu Azure Portal, klikněte na nastavení v levém podokně a pak klikněte na využití v horní nabídce.
  * Chcete-li zvýšit kvótu jader, obraťte se na [podpory Azure](https://azure.microsoft.com/support/options/). Informace o velikosti virtuálních počítačů, naleznete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Skripty Windows Powershellu**: V tématu se předpokládá, že máte praktické znalosti základní prostředí Windows PowerShell. Další informace o použití prostředí Windows PowerShell naleznete v následujících tématech:
  
  * [Spuštění Windows Powershellu v systému Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Začínáme s prostředím Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Krok 1: Zřízení virtuálního počítače Azure
1. Přejděte na web Azure Portal.
2. Klikněte na tlačítko **virtuálních počítačů** v levém podokně.
   
    ![virtuální počítače Microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Klikněte na možnost **Nové**.
   
    ![tlačítko Nová](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Klikněte na tlačítko **z Galerie**.
   
    ![nový virtuální počítač z Galerie](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Klikněte na tlačítko **SQL Server 2014 RTM Standard – Windows Server 2012 R2** a poté klikněte na šipku pokračujte.
   
    ![Další](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Pokud potřebujete data služby Reporting Services řízené funkce předplatných, zvolte **SQL serveru 2014 RTM Enterprise, Windows Server 2012 R2**. Další informace o edicích systému SQL Server a podporovaných funkcích najdete v tématu [funkce, které jsou podporovány edice systému SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Na **konfigurace virtuálního počítače** stránky, upravte následující pole:
   
   * Pokud existuje více než jeden **datum vydání verze**, vyberte nejnovější verzi.
   * **Název virtuálního počítače**: Název počítače se také používá na další stránce konfigurace jako výchozí název cloudové služby DNS. Název DNS musí být jedinečný ve službě Azure. Vezměte v úvahu konfigurace virtuálního počítače s názvem, který popisuje, co se virtuální počítač používat. Například ssrsnativecloud.
   * **Tier**: Standard
   * **Velikost: A3** je doporučená velikost virtuálního počítače pro úlohy SQL serveru. Pokud virtuální počítač slouží pouze jako serveru sestav, je dostatečná velikost virtuálního počítače pro A2, pokud server sestav vyskytne velkých úloh. Informace o cenách virtuálních počítačů, naleznete v tématu [ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nové uživatelské jméno**: název zadáte je vytvořen jako správce na virtuálním počítači.
   * **Nové heslo** a **potvrďte**. Toto heslo se používá pro nový účet správce a je doporučeno používat silné heslo.
   * Klikněte na **Další**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na další stránku upravte následující pole:
   
   * **Cloudová služba**: vyberte **vytvořit novou Cloudovou službu**.
   * **Název DNS služby v cloudu**: Toto je veřejný název DNS cloudové služby, která souvisí s virtuálním Počítačem. Výchozí název je název, který jste zadali pro název virtuálního počítače. Pokud v dalších krocích tohoto tématu vytvořit důvěryhodný certifikát SSL a potom název DNS se používá pro hodnotu vlastnosti "**vydat**" certifikátu.
   * **Oblast nebo skupina vztahů/virtuální sítě**: Vyberte oblast nejbližší vašim koncovým uživatelům.
   * **Účet úložiště**: Použijte účet automaticky generované úložiště.
   * **Skupina dostupnosti**: Žádné.
   * **Koncové body** zachovat **vzdálené plochy** a **Powershellu** koncových bodů a potom přidat buď HTTP nebo HTTPS koncového bodu, v závislosti na vašem prostředí.
     
     * **HTTP**: Výchozí veřejné a soukromé porty jsou **80**. Všimněte si, že používáte privátní port než 80, upravte **$HTTPport = 80** ve skriptu http.
     * **HTTPS**: Výchozí veřejné a soukromé porty jsou **443**. Osvědčeným postupem zabezpečení je změnit privátní port a konfiguraci brány firewall a server sestav použít privátní port. Další informace o koncových bodech najdete v tématu [jak nastavit komunikaci s virtuálním počítačem](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Všimněte si, že pokud používáte jiný port než 443, změňte parametr **$HTTPsport = 443** ve skriptu HTTPS.
   * Klikněte na tlačítko Další. ![Další](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na poslední stránce průvodce, ponechte výchozí **nainstalujte agenta virtuálního počítače** vybrané. Kroky v tomto tématu Neuvedeno agenta virtuálního počítače, ale pokud budete chtít zachovat tento virtuální počítač, agent virtuálního počítače a rozšíření vám umožní zajistit, že CM.  Další informace o agenta virtuálního počítače najdete v tématu [agenta virtuálního počítače a rozšíření – část 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Jednou z výchozí nainstalovaná rozšíření služby ad s je rozšíření "BGINFO", který zobrazuje na ploše virtuálního počítače, systémové informace, například interní IP adresa a volného místa na disku.
9. Klikněte na dokončení. ![OK](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. **Stav** virtuálního počítače se zobrazí jako **spuštění (zřizování)** během procesu zřizování a potom zobrazí jako **systémem** když virtuální počítač je zřízená a připravený k použití.

## <a name="step-2-create-a-server-certificate"></a>Krok 2: Vytvoření certifikátu serveru
> [!NOTE]
> Pokud nechcete, aby HTTPS na serveru sestav, můžete si **přeskočit krok 2** a přejděte do části **použít skript ke konfiguraci serveru sestav a HTTP**. Pomocí skriptu HTTP rychle nakonfigurovat server sestav a server sestav bude připravené k použití.

Pokud chcete používat protokol HTTPS na virtuálním počítači, musíte důvěryhodný certifikát SSL. V závislosti na vašem scénáři můžete použít jednu z těchto dvou metod:

* Platný certifikát SSL vydaný certifikační autoritou (CA) a považuje Microsoft za důvěryhodnou. Certifikáty kořenové certifikační Autority se musí být distribuovány prostřednictvím programu Microsoft Root Certificate Program. Další informace o tomto programu najdete v tématu [Windows a Windows Phone 8 SSL Root Certificate Program (člen certifikační autority)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) a [Úvod do programu Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Certifikát podepsaný svým držitelem. Certifikáty podepsané svým držitelem se nedoporučuje pro produkční prostředí.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Pokud chcete použít certifikát vytvořené pomocí důvěryhodná certifikační autoritu (CA)
1. **Žádost o certifikát serveru pro web od certifikační autority**. 
   
    Průvodce certifikát webového serveru můžete použít ke generování souboru žádosti o certifikát (Certreq.txt), která odesíláte do certifikační autority nebo ke generování žádost pro online certifikační autoritu. Například certifikační služby společnosti Microsoft ve Windows serveru 2012. V závislosti na úroveň záruky identifikace nabízené certifikátu serveru je několik dnů na několik měsíců pro certifikační autoritu a potvrzovat svou žádost odeslat soubor certifikátu. 
   
    Další informace o žádosti o certifikáty serveru naleznete v následujících tématech: 
   
   * Použití [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Nástroje zabezpečení pro správu systému Windows Server 2012.
     
     [Nástroje zabezpečení pro správu systému Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > **Vydat** pole důvěryhodný certifikát SSL by měl být stejný jako **název cloudové služby DNS** jste použili pro nový virtuální počítač.

2. **Instalace certifikátu serveru na webovém serveru**. Webový server je v tomto případě virtuální počítač, který je hostitelem serveru sestav a vytvoří se web v dalších krocích, při konfiguraci služby Reporting Services. Další informace o instalaci certifikátu serveru na webovém serveru pomocí modulu snap-in konzoly MMC certifikátu najdete v tématu [nainstalovat certifikát serveru](https://technet.microsoft.com/library/cc740068).
   
    Pokud chcete použít ke konfiguraci serveru sestav, hodnota certifikáty skript dodaný s tímto tématem **kryptografický otisk** se vyžaduje jako parametr tohoto skriptu. V části Další podrobnosti o tom, jak získat kryptografický otisk certifikátu.
3. Přiřadíte certifikát serveru k serveru sestav. Přiřazení je dokončeno v další části, při konfiguraci serveru sestav.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Chcete-li použít certifikát podepsaný svým držitelem virtuálních počítačů
Certifikát podepsaný svým držitelem byl vytvořen ve virtuálním počítači při zřizování virtuálního počítače. Certifikát má stejný název jako název DNS virtuálního počítače. Pokud se chcete vyhnout chybám certifikátu, je vyžadován, že je certifikát důvěryhodný, v samotných virtuálních počítačů a také všemi uživateli webu.

1. Aby důvěřoval kořenové certifikační Autority certifikátu na místním virtuálním počítači, přidejte certifikát, který **důvěryhodných kořenových certifikačních autorit**. Následuje souhrn kroky. Podrobné pokyny o tom, aby důvěřoval certifikační Autority najdete v tématu [nainstalovat certifikát serveru](https://technet.microsoft.com/library/cc740068).
   
   1. Na webu Azure Portal vyberte virtuální počítač a klikněte na připojit. V závislosti na konfiguraci vašeho prohlížeče můžete být vyzváni k uložení souboru RDP pro připojení k virtuálnímu počítači.
      
       ![Připojte se k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte uživatelské jméno virtuálního počítače, uživatelské jméno a heslo, které jste nakonfigurovali při vytváření virtuálního počítače. 
      
       Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
      
       ![název virtuálního počítače zahrnuje přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Spusťte mmc.exe. Další informace najdete v tématu [jak: Zobrazení certifikátů pomocí modulu Snap-in konzoly MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. V konzolové aplikaci **souboru** nabídky, přidejte **certifikáty** modul snap-in, vyberte **účet počítače** při zobrazení výzvy a potom klikněte na **Další**.
   4. Vyberte **místního počítače** ke správě a potom klikněte na tlačítko **Dokončit**.
   5. Klikněte na tlačítko **Ok** a potom rozbalte **certifikáty – osobní** uzly a pak klikněte na tlačítko **certifikáty**. Certifikát má stejný název jako název DNS virtuálního počítače a končí **cloudapp.net**. Klikněte pravým tlačítkem na název certifikátu a klikněte na tlačítko **kopírování**.
   6. Rozbalte **důvěryhodných kořenových certifikačních autorit** uzel a potom klikněte pravým tlačítkem na **certifikáty** a potom klikněte na tlačítko **vložit**.
   7. Pokud chcete ověřit, dvakrát klikněte na název certifikátu v části **důvěryhodných kořenových certifikačních autorit** a ověřte, zda zde nejsou žádné chyby a se zobrazí váš certifikát. Pokud chcete použít ke konfiguraci serveru sestav, hodnota certifikáty HTTPS skript dodaný s tímto tématem **kryptografický otisk** se vyžaduje jako parametr tohoto skriptu. **Chcete-li získat hodnotu kryptografického otisku**, proveďte následující kroky. Je taky ukázku prostředí PowerShell k načtení kryptografického otisku v části [použít skript ke konfiguraci serveru sestav a HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Dvakrát klikněte na název certifikátu, třeba ssrsnativecloud.cloudapp.net.
      2. Klikněte na kartu **Podrobnosti** .
      3. Klikněte na tlačítko **kryptografický otisk**. Hodnota kryptografického otisku se zobrazí v poli podrobnosti, například a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Zkopírujte kryptografický otisk a uložte hodnoty pro pozdější nebo upravte skript nyní.
      5. (*) Před spuštěním skriptu odeberte mezery mezi dvojice hodnot. Příklad kryptografického otisku jste si poznamenali před by nyní a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Přiřadíte certifikát serveru k serveru sestav. Přiřazení je dokončeno v další části, při konfiguraci serveru sestav.

Pokud používáte certifikát SSL podepsaný svým držitelem, názvem na certifikátu již shoduje s názvem hostitele virtuálního počítače. Proto DNS počítače je už zaregistrovaný jako globálně a je přístupný z jakéhokoli klienta.

## <a name="step-3-configure-the-report-server"></a>Krok 3: Konfigurace serveru sestav
Tato část vás provede konfigurací virtuálního počítače jako server sestav služby Reporting Services v nativním režimu. Jeden z následujících metod slouží ke konfiguraci serveru sestav:

* Konfigurace serveru sestav pomocí skriptu
* Nástroj Configuration Manager použijte ke konfiguraci serveru sestav.

Podrobné kroky, najdete v části [připojení k virtuálnímu počítači a spusťte Správce konfigurace služby Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Poznámka: ověřování:** Ověřování Windows je doporučená metoda ověřování a je výchozí ověřování služby Reporting Services. Jenom uživatelé, kteří jsou nakonfigurované na virtuálním počítači můžete přístup ke službě Reporting Services a přiřadit k rolím služby Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Použijte skript pro konfiguraci serveru sestav a HTTP
Konfigurace serveru sestav pomocí skriptu prostředí Windows PowerShell, proveďte následující kroky. Konfigurace zahrnuje HTTP, nikoli HTTPS:

1. Na webu Azure Portal vyberte virtuální počítač a klikněte na připojit. V závislosti na konfiguraci vašeho prohlížeče můžete být vyzváni k uložení souboru RDP pro připojení k virtuálnímu počítači.
   
    ![Připojte se k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte uživatelské jméno virtuálního počítače, uživatelské jméno a heslo, které jste nakonfigurovali při vytváření virtuálního počítače. 
   
    Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
   
    ![název virtuálního počítače zahrnuje přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na virtuálním počítači, otevřete **Windows PowerShell ISE** s oprávněními správce. Prostředí PowerShell ISE se instaluje standardně na Windows serveru 2012. Doporučuje se, že používáte ISE místo standardní okno Windows Powershellu tak, aby vložte skript do ISE, upravte skript a potom spusťte skript.
3. V prostředí Windows PowerShell ISE, klikněte na tlačítko **zobrazení** nabídky a pak klikněte na tlačítko **zobrazit podokno skriptu**.
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
5. Pokud jste vytvořili virtuální počítač pomocí protokolu HTTP port než 80, upravte parametr $HTTPport = 80.
6. Skript je aktuálně nakonfigurován pro službu Reporting Services. Pokud chcete spustit skript pro službu Reporting Services, upravte část verze cestu k oboru názvů "v11", na příkaz Get-WmiObject.
7. Spusťte skript.

**Ověření**: Ověřte, zda je funkční základní sestavu funkce serveru, najdete v článku [ověřit konfiguraci](#verify-the-configuration) později v tomto tématu.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Použijte skript pro konfiguraci serveru sestav a HTTPS
Konfigurace serveru sestav pomocí Windows Powershellu, proveďte následující kroky. Konfigurace zahrnuje HTTPS, nikoli protokol HTTP.

1. Na webu Azure Portal vyberte virtuální počítač a klikněte na připojit. V závislosti na konfiguraci vašeho prohlížeče můžete být vyzváni k uložení souboru RDP pro připojení k virtuálnímu počítači.
   
    ![Připojte se k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Použijte uživatelské jméno virtuálního počítače, uživatelské jméno a heslo, které jste nakonfigurovali při vytváření virtuálního počítače. 
   
    Například na následujícím obrázku je název virtuálního počítače **ssrsnativecloud** a uživatelské jméno je **testuser**.
   
    ![název virtuálního počítače zahrnuje přihlášení](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na virtuálním počítači, otevřete **Windows PowerShell ISE** s oprávněními správce. Prostředí PowerShell ISE se instaluje standardně na Windows serveru 2012. Doporučuje se, že používáte ISE místo standardní okno Windows Powershellu tak, aby vložte skript do ISE, upravte skript a potom spusťte skript.
3. Pokud chcete povolit spouštění skriptů, spusťte následující příkaz Windows Powershellu:
   
        Set-ExecutionPolicy RemoteSigned
   
    Spusťte následující příkaz pro ověření zásady:
   
        Get-ExecutionPolicy
4. V **Windows PowerShell ISE**, klikněte na tlačítko **zobrazení** nabídky a pak klikněte na tlačítko **zobrazit podokno skriptu**.
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
6. Upravit **$certificatehash** parametr ve skriptu:
   
   * Jedná se **požadované** parametr. Pokud nebyla uložena hodnotu certifikátu z předchozích kroků, pomocí jedné z následujících metod zkopírujte hodnotu hash certifikátu z kryptografického otisku certifikáty.:
     
       Na virtuálním počítači otevřete Windows PowerShell ISE a spusťte následující příkaz:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Výstup bude vypadat nějak takto. V případě, že skript vrací prázdný řádek, virtuální počítač nemá nakonfigurované například certifikát, najdete v části [použít certifikát podepsaný svým držitelem Virtual Machines](#to-use-the-virtual-machines-self-signed-certificate).
     
     NEBO
   * Na virtuálním počítači spusťte mmc.exe a pak přidejte **certifikáty** modul snap-in.
   * V části **důvěryhodných kořenových certifikačních autorit** uzel, klikněte dvakrát na název vašeho certifikátu. Pokud používáte certifikát podepsaný svým držitelem virtuálního počítače, certifikát má stejný název jako název DNS virtuálního počítače a končí **cloudapp.net**.
   * Klikněte na kartu **Podrobnosti** .
   * Klikněte na tlačítko **kryptografický otisk**. Hodnota kryptografického otisku se zobrazí v poli podrobnosti, například af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Před spuštěním skriptu**, odebrat mezery mezi dvojice hodnot. Například af1160b64b288d890a8212ff6ba9c3664f319048
7. Upravit **$httpsport** parametr: 
   
   * Pokud jste použili port 443 pro protokol HTTPS koncového bodu, není potřeba aktualizovat tento parametr ve skriptu. Jinak použijte hodnotu portu, který jste vybrali při konfiguraci privátního koncového bodu HTTPS na virtuálním počítači.
8. Upravit **$DNSName** parametr: 
   
   * Skript je nakonfigurovaný pro zástupný certifikát $DNSName = "+". Pokud chcete nakonfigurovat pro vazbu certifikátu zástupný znak, okomentujte $DNSName ="+"a povolte následující řádek, úplné $DNSNAme odkaz, ## $DNSName="$server.cloudapp.net".
     
       Změňte hodnotu $DNSName, pokud nechcete použít název DNS virtuálního počítače pro službu Reporting Services. Pokud použijete parametr, certifikát, musíte taky použít tento název a zaregistrujte název globálně na serveru DNS.
9. Skript je aktuálně nakonfigurován pro službu Reporting Services. Pokud chcete spustit skript pro službu Reporting Services, upravte část verze cestu k oboru názvů "v11", na příkaz Get-WmiObject.
10. Spusťte skript.

**Ověření**: Ověřte, zda je funkční základní sestavu funkce serveru, naleznete v článku ověřit konfigurační oddíl dále v tomto tématu. Ověřit certifikát vazby otevřete příkazový řádek s oprávněními správce a spusťte následující příkaz:

    netsh http show sslcert

Výsledek bude obsahovat následující:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Konfigurace serveru sestav pomocí nástroje Configuration Manager
Pokud nechcete spustit skript prostředí PowerShell ke konfiguraci serveru sestav, postupujte podle kroků v této části Konfigurace serveru sestav pomocí Správce konfigurace služby Reporting Services v nativním režimu.

1. Na webu Azure Portal vyberte virtuální počítač a klikněte na připojit. Použijte uživatelské jméno a heslo, které jste nakonfigurovali při vytváření virtuálního počítače.
   
    ![Připojte se k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Spusťte službu Windows update a instalaci aktualizací do virtuálního počítače. Pokud se vyžaduje restartování virtuálního počítače, restartujte virtuální počítač a znovu připojit k virtuálnímu počítači z portálu Azure portal.
3. Z nabídky Start ve virtuálním počítači, zadejte **služby Reporting Services** a otevřete **Správce konfigurace služby Reporting Services**.
4. Ponechte výchozí hodnoty pro **název serveru** a **Instance serveru sestav**. Klikněte na **Připojit**.
5. V levém podokně klikněte na tlačítko **adresa URL webové služby**.
6. Ve výchozím nastavení RS je nakonfigurovaný pro protokol HTTP port 80 s IP Adresou "Všechny přiřazené". To add HTTPS:
   
   1. V **certifikát SSL**: Vyberte certifikát, který chcete použít, například [název_virtuálního_počítače]. cloudapp.net. Pokud nejsou uvedeny žádné certifikáty, najdete v části **krok 2: Vytvoření certifikátu serveru** informace o tom, jak nainstalovat a důvěřovat certifikátu na virtuálním počítači.
   2. V části **SSL Port**: Zvolte 443. Pokud jste nakonfigurovali privátního koncového bodu HTTPS do virtuálního počítače s jiným privátním portem, použijte tuto hodnotu tady.
   3. Klikněte na tlačítko **použít** a počkejte na dokončení operace.
7. V levém podokně klikněte na tlačítko **databáze**.
   
   1. Klikněte na tlačítko **změnit databázi**e.
   2. Klikněte na tlačítko **vytvořit novou databázi serveru sestav** a potom klikněte na tlačítko **Další**.
   3. Ponechte výchozí nastavení **název serveru**: jako virtuální počítač zadejte název a ponechejte výchozí **typ ověřování** jako **aktuálního uživatele** – **integrované zabezpečení**. Klikněte na **Další**.
   4. Ponechte výchozí nastavení **název_databáze** jako **ReportServer** a klikněte na tlačítko **Další**.
   5. Ponechte výchozí nastavení **typ ověřování** jako **přihlašovací údaje služby** a klikněte na tlačítko **Další**.
   6. Klikněte na tlačítko **Další** na **Souhrn** stránky.
   7. Po dokončení konfigurace klikněte na tlačítko **Dokončit**.
8. V levém podokně klikněte na tlačítko **adresa URL správce sestav**. Ponechte výchozí nastavení **virtuální adresář** jako **sestavy** a klikněte na tlačítko **použít**.
9. Klikněte na tlačítko **ukončovací** zavřete Správce konfigurace služby Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Krok 4: Otevřít Port brány Firewall na Windows
> [!NOTE]
> Pokud jste použili jedno z skripty ke konfiguraci serveru sestav, můžete tuto část přeskočit. Skript zahrnout krok pro otevření portu brány firewall. Výchozí hodnota je port 80 pro protokol HTTP a port 443 pro protokol HTTPS.
> 
> 

Chcete-li vzdáleně připojit ke správci sestav nebo Server sestav na virtuálním počítači, koncový bod TCP musí být ve virtuální počítač. Je potřeba otevřít stejný port v bráně firewall Virtuálního počítače. Koncový bod se vytvořil při zřizování virtuálního počítače.

Tato část obsahuje základní informace o tom, jak otevřít port brány firewall. Další informace najdete v tématu [konfiguraci brány Firewall pro přístup k serveru sestav](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Pokud jste použili skript ke konfiguraci serveru sestav, můžete tuto část přeskočit. Skript zahrnout krok pro otevření portu brány firewall.
> 
> 

Pokud jste nakonfigurovali privátní port pro protokol HTTPS než 443, následující skript příslušným způsobem upravte. Pro otevření portu **443** v bráně Windows Firewall, proveďte následující kroky:

1. Otevřete okno Windows Powershellu s oprávněními správce.
2. Pokud jste použili jiný port než 443, při konfiguraci koncového bodu HTTPS na virtuálním počítači, aktualizujte port v následujícím příkazu a poté spusťte příkaz:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Po dokončení příkazu **Ok** se zobrazí v okně příkazového řádku.

Pokud chcete ověřit, že je port otevřen, otevřete okno Windows Powershellu a spusťte následující příkaz:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Ověření konfigurace
Pokud chcete ověřit, že funkce serveru základní sestavu teď funguje, otevřete prohlížeč s oprávněními správce a přejděte následující sestavu ad správce sestav serveru adresy URL:

* Na virtuálním počítači přejděte na adresu URL serveru sestav:
  
        http://localhost/reportserver
* Na virtuálním počítači přejděte na adresu URL správce sestav:
  
        http://localhost/Reports
* Ze svého místního počítače, přejděte **vzdálené** sestavy správce ve virtuálním počítači. Aktualizujte název DNS v následujícím příkladu podle potřeby. Po zobrazení výzvy k zadání hesla, použijte přihlašovací údaje správce, který jste vytvořili při zřizování virtuálního počítače. Uživatelské jméno je v [doména]\[uživatelské jméno] formát, kde je název počítače virtuálního počítače, třeba ssrsnativecloud\testuser doména. Pokud nepoužíváte HTTP**S**, odeberte **s** v adrese URL. V části Další informace o vytvoření dalších uživatelů na virtuálním počítači.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Ze svého místního počítače přejděte na adresu URL sestavu vzdáleného serveru. Aktualizujte název DNS v následujícím příkladu podle potřeby. Pokud nepoužíváte protokol HTTPS, odeberte tím s v adrese URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Vytvoření uživatelů a přiřazení rolí
Běžné úlohy správy po konfigurace a ověření na serveru sestav, je vytvořit jeden nebo více uživatelů a přiřazení uživatelů k rolím služby Reporting Services. Další informace naleznete v následujících tématech:

* [Vytvořit místní uživatelský účet](https://technet.microsoft.com/library/cc770642.aspx)
* [Udělte uživateli přístup k serveru sestav (Správce sestav)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Vytvořit a spravovat přiřazení rolí](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Vytvoření a publikování sestav na virtuálním počítači Azure
Následující tabulka shrnuje některé z možností dostupných pro existující sestavy z místního počítače na server sestav, které jsou hostované v Azure virtuální počítač Microsoftu pro publikování:

* **Skript RS.exe**: Pomocí skriptu RS.exe ke kopírování položky sestavy z existujícího serveru sestav do vašeho virtuálního počítače Microsoft Azure. Další informace najdete v části "Nativní režim na nativní režim – virtuálním počítači Microsoft Azure" v [ukázky Reporting Services rs.exe skriptu pro migraci obsahu mezi servery sestav](https://msdn.microsoft.com/library/dn531017.aspx).
* **Tvůrce sestav**: Virtuální počítač obsahuje kliknutím na-jednou verzi Tvůrce sestav Microsoft SQL Server. Spuštění sestavy Tvůrce první na virtuálním počítači:
  
  1. Spusťte prohlížeč s oprávněními správce.
  2. Přejděte do Správce sestav na virtuálním počítači a klikněte na tlačítko **Tvůrce sestav** na pásu karet.
     
     Další informace najdete v tématu [instalace, odinstalace a Tvůrce sestav podporuje](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**:  Pokud jste vytvořili virtuální počítač s SQL serverem 2012, pak SQL Server Data Tools je nainstalovaná na virtuálním počítači a slouží k vytvoření **projektů serveru sestav** a sestavy na virtuálním počítači. SQL Server Data Tools můžete publikovat sestavy na server sestav na virtuálním počítači.
  
    Pokud jste vytvořili virtuální počítač s SQL serverem 2014, můžete nainstalovat SQL Server Data Tools – BI pro sadu visual Studio. Další informace naleznete v následujících tématech:
  
  * [Nástroje Microsoft SQL Server Data Tools – Business Intelligence pro Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Nástroje Microsoft SQL Server Data Tools – Business Intelligence pro sadu Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools a SQL Server Business Intelligence (SSDT BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: Vzdálené**:  V místním počítači vytvořte projekt služby Reporting Services v SQL Server Data Tools, obsahující sestavy služby Reporting Services. Konfigurace projektu pro připojení k adresu URL webové služby.
  
    ![Vlastnosti projektu rozšíření SSDT pro projekt služby SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Použít skript**: Pomocí skriptu pro zkopírování obsahu serveru sestav. Další informace najdete v tématu [ukázky Reporting Services rs.exe skriptu pro migraci obsahu mezi servery sestav](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimalizace nákladů, pokud nepoužíváte virtuální počítač
> [!NOTE]
> Chcete-li minimalizovat náklady pro Azure Virtual Machines při nepoužívá, vypnete virtuální počítač z portálu Azure portal. Je-li vypnout virtuální počítač pomocí možnosti napájení Windows uvnitř virtuálního počítače, se stále účtují stejným způsobem pro virtuální počítač. Pokud chcete snížit náklady, budete muset vypnout virtuální počítač na webu Azure Portal. Pokud už nepotřebujete, virtuálnímu počítači, nezapomeňte odstranit virtuální počítač a souborů VHD přidružené, aby se zabránilo poplatky za úložiště. Další informace najdete v tématu v části Nejčastější dotazy na [podrobnosti o cenách Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Další informace
### <a name="resources"></a>Zdroje a prostředky
* Podobně jako obsah související s nasazení s jedním serverem SQL Server Business Intelligence a SharePoint 2013, najdete v části [pomocí Windows Powershellu pro vytvoření virtuálního počítače Azure s SQL Server BI a SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Obecné informace týkající se nasazení SQL Server Business Intelligence ve službě Azure Virtual Machines najdete v tématu [SQL Server Business Intelligence ve službě Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Další informace o nákladech poplatky za výpočty Azure najdete v tématu na kartě Virtual Machines [cenovou kalkulačku Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Obsah vytvořený komunitou
* Podrobné pokyny o tom, jak vytvořit Reporting Services na nativní režim serveru sestav bez použití skriptu najdete v tématu [hostitelem služby SQL Reporting Services na virtuálním počítači Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Odkazy na další zdroje informací pro SQL Server na virtuálních počítačích Azure
[SQL Server na Azure Virtual Machines – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

