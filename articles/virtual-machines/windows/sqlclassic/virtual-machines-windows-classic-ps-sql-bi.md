---
title: SQL Server Business Intelligence | Dokumentace Microsoftu
description: Toto téma používá prostředky vytvořené pomocí modelu nasazení classic a popisuje funkce Business Intelligence (BI), které jsou pro SQL Server běžící na Azure Virtual Machines (VM).
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 2b2f5a441209b76f4c90c1a4682215d388b2d53a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242887"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence v Azure Virtual Machines
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Galerie virtuálních počítačů Microsoft Azure zahrnuje imagí, které obsahují instalace systému SQL Server. Edice SQL serveru v galerii imagí podporovány jsou instalační soubory, které můžete nainstalovat do místních počítačů a virtuálních počítačů. Toto téma shrnuje funkce SQL Server Business Intelligence (BI), které jsou nainstalované na imagích a kroků konfigurace požadovaných po zřízení virtuálního počítače. Toto téma také popisuje podporované topologie nasazení funkce BI a osvědčené postupy.

## <a name="license-considerations"></a>Důležité informace o licenci
Existují dva způsoby, abyste získali licenci pro SQL Server na virtuálních počítačích Microsoft Azure:

1. Využijete mobilitu licencí, které jsou součástí programu Software Assurance. Další informace najdete v tématu [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Platba za hodinová sazba virtuálních počítačů Azure s nainstalovaným SQL serverem. V části "SQL Server" v [ceny Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Další informace o licencování a aktuální míra, naleznete v tématu [Virtual Machines nejčastější dotazy ohledně licencování](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server Imagí dostupných v galerii virtuálních počítačů Azure
Galerie virtuálních počítačů Microsoft Azure obsahuje několik imagí, které obsahují Microsoft SQL Server. Software nainstalovaný v bitové kopie virtuálního počítače se liší v závislosti na verzi operačního systému a verze SQL serveru. Seznam imagí dostupných v galerii virtuálních počítačů Azure se často mění.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Image SQL v galerii virtuálních počítačů Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Následující skript prostředí PowerShell vrátí seznam imagích Azure, které obsahují "SQL-Server" v že ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Další informace o edicích a funkce v systému SQL Server podporovány naleznete v následujících tématech:

* [Edice SQL serveru](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Funkce podporované edicemi SQL serveru 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI součástí nainstalovaných na Image Galerie virtuálních počítačů SQL serveru
Následující tabulka shrnuje funkce Business Intelligence nainstalované v běžných Galerie imagí virtuálního počítače Microsoft Azure pro SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Funkce SQL Server BI | Nainstalovat na image z Galerie | Poznámky |
| --- | --- | --- |
| **Reporting Services – nativní režim** |Ano |Nainstalován, ale vyžaduje konfigurace, včetně adresy URL správce sestav. V části [konfigurace služby Reporting Services](#configure-reporting-services). |
| **Služby Reporting Services v režimu serveru SharePoint.** |Ne |Image z Galerie virtuálním počítači Microsoft Azure neobsahuje SharePoint nebo SharePoint instalační soubory. <sup>1</sup> |
| **Dolování dat a Analysis Services Multidimensional (OLAP)** |Ano |Instalaci a konfiguraci jako výchozí instanci služby Analysis Services |
| **Analysis Services – tabulkové** |Ne |Podporováno v systému SQL Server 2012, 2014 a 2016 bitové kopie, ale to není nainstalovaná ve výchozím nastavení. Nainstalujte jiná instance služby Analysis Services. Naleznete v části instalace jiných služeb SQL Server a funkcí v tomto tématu. |
| **Analysis Services Power Pivot pro službu SharePoint** |Ne |Image z Galerie virtuálním počítači Microsoft Azure neobsahuje SharePoint nebo SharePoint instalační soubory. <sup>1</sup> |

<sup>1</sup> Další informace o SharePoint a Azure virtual machines, najdete v části [architektury Microsoft Azure pro službu SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) a [nasazení služby SharePoint v Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Spusťte následující příkaz prostředí PowerShell k získání seznamu nainstalovaných služeb, které obsahují "SQL" v názvu služby.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Obecná doporučení a osvědčené postupy
* Minimální doporučená velikost virtuálního počítače je **A3** při používání SQL Server Enterprise Edition. **A4** velikost virtuálního počítače se doporučují pro nasazení systému SQL Server BI Analysis Services a Reporting Services.
  
    Informace o aktuální velikosti virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Osvědčeným postupem pro správu disků je k uložení dat, protokol a záložní soubory na jednotky jiné než **C**: a **D**:. Například vytvořit datové disky **E**: a **F**:.
  
  * Ukládání do mezipaměti zásad na výchozí jednotce jednotka **C**: není ideální pro práci s daty.
  * **D**: jednotka je dočasný disk, který se používá především pro stránkovací soubor. **D**: není trvalý disk a není uložen v úložišti objektů blob. Obnovit velikost úlohy správy, jako je například změna k virtuálnímu počítači **D**: jednotky. Doporučuje se **není** použít **D**: jednotka pro soubory databáze, včetně databáze tempdb.
    
    Další informace o vytvoření a připojení disků najdete v tématu [jak připojit datový Disk k virtuálnímu počítači](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Zastavení nebo odinstalaci služby, které nechcete použít. Například pokud virtuální počítač používá jenom pro službu Reporting Services, zastavit nebo odinstalaci služby Analysis Services a SQL Server Integration Services. Na následujícím obrázku je příkladem služby, které jsou spouštěny ve výchozím nastavení.
  
    ![Služby systému SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Databázový stroj SQL Server je vyžadována v Podporované scénáře BI. V jednom serveru topologie virtuálního počítače databázový stroj musí běžet na stejném virtuálním počítači.
  
    Další informace naleznete na následujícím: [odinstalovat Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) a [instanci Analysis Services odinstalovat](https://msdn.microsoft.com/library/ms143687.aspx).
* Zkontrolujte **Windows Update** nových "důležitých aktualizací". Image virtuálního počítače Microsoft Azure se často aktualizují; ale může důležité aktualizace budou dostupné z **Windows Update** po poslední aktualizaci image virtuálního počítače.

## <a name="example-deployment-topologies"></a>Příklad topologií nasazení
Následují příklady nasazení, které používají Microsoft Azure Virtual Machines. Topologie v tyto diagramy jsou pouze některé možné topologie, která vám pomůže s funkcemi SQL Server BI a Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Jeden virtuální počítač
Analysis Services, služby Reporting Services, databázový stroj SQL serveru a zdroje dat na jeden virtuální počítač.

![scénář iaas s BI s 1 virtuální počítač](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dva virtuální počítače
* Analysis Services, služby Reporting Services a databázový stroj SQL serveru na jeden virtuální počítač. Toto nasazení zahrnuje databáze serveru sestav.
* Zdroje dat na druhý virtuální počítač. Druhý virtuální počítač obsahuje databázový stroj SQL serveru jako zdroj dat.

![scénář BI iaas s virtuálními počítači 2](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Smíšené Azure – data v Azure SQL database
* Analysis Services, služby Reporting Services a databázový stroj SQL serveru na jeden virtuální počítač. Toto nasazení zahrnuje databáze serveru sestav.
* Zdroj dat je Azure SQL database.

![virtuální počítač BI iaas scénáře a AzureSQL jako zdroj dat](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybridní – data dostupná místně
* V tomto příkladu nasazení služby Analysis Services Reporting Services a databázový stroj SQL Server běží na jeden virtuální počítač. Virtuální počítač je hostitelem databáze serveru sestav. Virtuální počítač je připojen k místní doméně prostřednictvím virtuální sítě Azure nebo některé jiné VPN, tunelové propojení řešení.
* Zdroj dat je místní.

![BI iaas scénáře virtuálního počítače a místních zdrojů dat](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfigurace služby Reporting Services v nativním režimu
Image Galerie virtuálních počítačů pro SQL Server obsahuje Reporting Services na nativní režim nainstalované, ale není nakonfigurovaný server sestav. Kroky v této části Konfigurace serveru sestav služby Reporting Services. Podrobné informace o konfiguraci vytváření sestav služby nativní režim, naleznete v tématu [instalace sestav Reporting Services nativního režimu serveru (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Podobně jako obsah, který používá skripty prostředí Windows PowerShell ke konfiguraci serveru sestav, naleznete v tématu [použití Powershellu k vytvoření Azure virtuální počítač s nativní serverem sestav v režimu](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Připojení k virtuálnímu počítači a spusťte Správce konfigurace služby Reporting Services
Existují dva běžné pracovní postupy pro připojení k virtuálním počítači Azure:

* Připojení v seznamu, klikněte název virtuálního počítače a potom klikněte na **připojit**. Připojení ke vzdálené ploše se otevře a automaticky se vyplní název počítače.
  
    ![Připojte se k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Připojení k virtuálnímu počítači pomocí připojení ke vzdálené ploše Windows. V uživatelském rozhraní vzdálené plochy:
  
  1. Typ **název cloudové služby** jako název počítače.
  2. Zadejte dvojtečku (:) a číslo veřejného portu, který je nakonfigurován pro koncový bod TCP vzdálené plochy.
     
      Myservice.cloudapp.net:63133
     
      Další informace najdete v tématu [co je Cloudová služba?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Spustit Správce konfigurace služby generování sestav**

V **systému Windows Server 2012/2016**:

1. Z **Start** zadejte **služby Reporting Services** zobrazíte seznam aplikací.
2. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.

V **systému Windows Server 2008 R2**:

1. Klikněte na tlačítko **Start**a potom klikněte na tlačítko **všechny programy**.
2. Klikněte na tlačítko **Microsoft SQL Server 2016**.
3. Klikněte na tlačítko **konfigurační nástroje**.
4. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.

nebo:

1. Klikněte na tlačítko **Start**.
2. V **Prohledat programy a soubory** typ dialogu **služby reporting services**. Pokud je virtuální počítač se systémem Windows Server 2012, zadejte **služby reporting services** na obrazovce Start systému Windows Server 2012.
3. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.
   
    ![Vyhledejte ssrs configuration Manageru](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurace služby Reporting Services
**Účet služby a adresu URL webové služby:**

1. Ověřte, **název serveru** je název místního serveru a klikněte na **připojit**.
2. Všimněte si, prázdné **název databáze serveru sestav**. Databáze se vytvoří při dokončení konfigurace.
3. Ověřte, **stav serveru sestav** je **spuštěno**. Pokud chcete ověřit službu ve Správci serveru systému Windows, je tato služba **SQL Server Reporting Services** služby Windows.
4. Klikněte na tlačítko **účet služby** a podle potřeby změňte účet. Pokud virtuální počítač se používá v prostředí domény připojené k doméně, vestavěné **ReportServer** účtu je dostačující. Další informace o účtu služby, najdete v části [účet služby](https://msdn.microsoft.com/library/ms189964.aspx).
5. Klikněte na tlačítko **adresa URL webové služby** v levém podokně.
6. Klikněte na tlačítko **použít** ke konfiguraci výchozích hodnot.
7. Poznámka: **sestavy adresy URL serveru webové služby**. Mějte na paměti, výchozí port TCP 80 a je součástí adresy URL. V pozdějším kroku vytvořte koncový bod Microsoft Azure virtuálního počítače pro port.
8. V **výsledky** podokně zkontrolujte akce, které byla úspěšně dokončena.

**Databáze:**

1. Klikněte na tlačítko **databáze** v levém podokně.
2. Klikněte na tlačítko **změnit databázi**.
3. Ověřte **vytvořit novou databázi serveru sestav** je vybrána a potom klikněte na tlačítko Další.
4. Ověřte **název serveru** a klikněte na tlačítko **Test připojení**.
5. Pokud je výsledkem **Test připojení byl úspěšný**, klikněte na tlačítko **OK** a potom klikněte na tlačítko **Další**.
6. Poznamenejte si název databáze je **ReportServer** a **režim serveru sestav** je **nativní** klikněte **Další**.
7. Klikněte na tlačítko **Další** na **pověření** stránky.
8. Klikněte na tlačítko **Další** na **Souhrn** stránky.
9. Klikněte na tlačítko **Další** na **průběh a dokončení** stránky.

**Webová adresa URL portálu nebo adresa URL správce sestav pro 2012 a 2014:**

1. Klikněte na tlačítko **adresy URL webového portálu**, nebo **adresa URL správce sestav** 2014 a 2012, v levém podokně.
2. Klikněte na tlačítko **Použít**.
3. V **výsledky** podokně zkontrolujte akce, které byla úspěšně dokončena.
4. Klikněte na tlačítko **ukončovací**.

Informace o oprávnění serveru sestav, naleznete v tématu [udělení oprávnění na nativní režim serveru sestav](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Přejděte na místní správce sestav
Pokud chcete ověřit konfiguraci, přejděte do Správce sestav na virtuálním počítači.

1. Na virtuálním počítači spusťte aplikaci Internet Explorer s oprávněními správce.
2. Přejděte do http://localhost/reports na virtuálním počítači.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Pro připojení k vzdálené webový portál nebo správce sestav pro 2014 a 2012
Pokud chcete pro připojení k webovému portálu nebo správce sestav pro 2014 a 2012, na virtuálním počítači ze vzdáleného počítače, vytvořte nový virtuální počítač koncový bod TCP. Ve výchozím nastavení, server sestav čeká na požadavky HTTP na **port 80**. Při konfiguraci adres URL serveru sestav, chcete-li použít jiný port, musíte zadat číslo tohoto portu v následujících pokynech.

1. Vytvoření koncového bodu pro virtuální počítač z portu TCP 80. Další informace najdete v článku, [koncovým bodům virtuálních počítačů a porty brány Firewall](#virtual-machine-endpoints-and-firewall-ports) části v tomto dokumentu.
2. Otevřete port 80 v bráně firewall virtuálního počítače.
3. Přechod na webový portál nebo sestavy manager pomocí virtuálních počítačů Azure **název DNS** jako název serveru v adrese URL. Příklad:
   
    **Server sestav**: http://uebi.cloudapp.net/reportserver **webový portál**:   http://uebi.cloudapp.net/reports
   
    [Konfigurace brány Firewall pro přístup k serveru sestav](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Vytvoření a publikování sestav na virtuálním počítači Azure
Následující tabulka shrnuje některé z možností dostupných pro existující sestavy z místního počítače na server sestav, které jsou hostované v Azure virtuální počítač Microsoftu pro publikování:

* **Tvůrce sestav**: virtuální počítač obsahuje kliknutím na-jednou verzi Tvůrce sestav Microsoft SQL Server SQL 2014 a 2012. Spuštění sestavy Tvůrce první na virtuálním počítači s SQL 2016:
  
  1. Spusťte prohlížeč s oprávněními správce.
  2. Přechod na webový portál, na virtuálním počítači a vyberte **Stáhnout** ikonu v pravém horním rohu.
  3. Vyberte **Tvůrce sestav**.
     
     Další informace najdete v tématu [spuštění Tvůrce sestav](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: virtuálních počítačů: SQL Server Data Tools je nainstalovaná na virtuálním počítači a slouží k vytvoření **projektů serveru sestav** a sestavy na virtuálním počítači. SQL Server Data Tools můžete publikovat sestavy na server sestav na virtuálním počítači.
* **SQL Server Data Tools: Vzdálený**: V místním počítači, vytvořte projekt služby Reporting Services v SQL Server Data Tools, obsahující sestavy služby Reporting Services. Konfigurace projektu pro připojení k adresu URL webové služby.
  
    ![Vlastnosti projektu rozšíření SSDT pro projekt služby SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Vytvoření. Virtuální pevný disk pevného disku, který obsahuje sestavy a pak nahrajte a připojte jednotku.
  
  1. Vytvoření. Virtuální pevný disk pevného disku v místním počítači, který obsahuje vaše sestavy.
  2. Vytvořte a nainstalujte certifikát pro správu.
  3. Nahrání souboru VHD do Azure pomocí rutiny Add-AzureVHD [vytvoření a nahrání VHD s Windows serverem do Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Připojte disk k virtuálnímu počítači.

## <a name="install-other-sql-server-services-and-features"></a>Nainstalujte další služby SQL Server a funkce
Chcete-li nainstalovat další služby SQL Server, jako je služba Analysis Services v tabulkovém režimu, spusťte Průvodce instalací SQL serveru. Instalační soubory, jsou na místním disku virtuálního počítače.

1. Klikněte na tlačítko **Start** a potom klikněte na tlačítko **všechny programy**.
2. Klikněte na tlačítko **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** nebo **Microsoft SQL Server 2012** a potom klikněte na tlačítko **konfigurační nástroje** .
3. Klikněte na tlačítko **centrum instalace systému SQL Server**.

Nebo spusťte C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe nebo C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Při prvním spuštění instalačního programu systému SQL Server, další instalační soubory stáhnout a vyžadují restartování virtuálního počítače a restartování instalační program systému SQL Server.
> 
> Pokud potřebujete opakovaně přizpůsobení bitové kopie vybrané z Microsoft virtuálního počítače Azure, zvažte možnost vytvořit svoji vlastní image SQL serveru. Analytické funkce služby SysPrep byl povolen systém SQL Server 2012 SP1 kumulativní aktualizaci 2. Další informace najdete v tématu [důležité informace týkající se instalace SQL serveru pomocí nástroje SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) a [podpory nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Chcete-li nainstalovat tabulkovém režimu Analysis Services
Kroky v této části **shrnout** instalace tabulkovém režimu služby Analysis Services. Další informace naleznete v následujících tématech:

* [Instalace služby Analysis Services v tabulkovém režimu](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabulkového modelování (kurz Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Chcete-li nainstalovat tabulkovém režimu Analysis Services:**

1. V Průvodci instalací SQL serveru, klikněte na tlačítko **instalace** v levém podokně a pak klikněte na tlačítko **samostatná instalace nový SQL server nebo přidání funkcí do existující instalace**.
   
   * Pokud se zobrazí **vyhledat složku**vyhledejte c:\SQLServer_13.0_full, c:\SQLServer_12.0_full nebo c:\SQLServer_11.0_full a potom klikněte na tlačítko **Ok**.
2. Klikněte na tlačítko **Další** na stránce aktualizace produktu.
3. Na **typ instalace** stránce **nová instalace systému SQL Server** a klikněte na tlačítko **Další**.
4. Na **Role instalace** klikněte na **instalace funkce SQL Server**.
5. Na **výběr součástí** klikněte na **služby Analysis Services**.
6. Na **konfigurace Instance** stránky, zadejte popisný název, například **tabulkové** do **s názvem Instance** a **Instance Id** textová pole .
7. Na **konfigurace služby Analysis Services** stránce **tabulkovém režimu**. Přidáte aktuálního uživatele do seznamu oprávnění správce.
8. Dokončení a zavřete průvodce instalací SQL serveru.

## <a name="analysis-services-configuration"></a>Konfigurace služby Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Vzdálený přístup k serveru Analysis Services
Server služby Analysis Services podporuje jenom ověřování systému windows. Vzdálený přístup ke službě Analysis Services z klientské aplikace jako SQL Server Management Studio nebo SQL Server Data Tools, virtuální počítač musí být připojené k místní doméně, připojení virtuální sítě Azure. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **výchozí instanci** služby Analysis Services naslouchá na portu TCP **2383**. Otevřete port v bráně firewall virtuálních počítačů. Clusteru s názvem instance služby Analysis Services také naslouchá na portu **2383**.

Pro **pojmenovanou instanci** služby Analysis Services, je potřeba spravovat přístup k portu služby SQL Server Browser. Výchozí konfigurace SQL Server Browser je port **2382**.

V bráně firewall virtuální počítače, otevřete port **2382** a vytvoření statické Analysis Services s názvem instance port.

1. Ověření portů, které jsou již používána na virtuálním počítači a jaký proces používá porty, spusťte s oprávněními správce následující příkaz:
   
        netstat /ao
2. Pomocí SQL Server Management Studio k vytvoření statických služby Analysis Services s názvem instance port aktualizací "Port" hodnota v tabulkovém jako obecné vlastnosti instance. Další informace najdete v tématu "použít pevný port pro výchozí nebo pojmenované instance" v [konfigurace Windows Firewall pro povolení přístupu Analysis Services k](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Restartujte instanci tabulkové služby Analysis Services.

Další informace najdete v článku, **koncovým bodům virtuálních počítačů a porty brány Firewall** části v tomto dokumentu.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Koncovým bodům virtuálních počítačů a porty brány Firewall
Tento oddíl shrnuje koncovým bodům virtuálních počítačů Microsoft Azure k vytvoření a porty, které otevřete v branách firewall virtuálního počítače. Následující tabulka shrnuje **TCP** porty koncových bodů pro vytvoření a porty otevřete v bráně firewall virtuálních počítačů.

* Pokud použijete jeden virtuální počítač a jsou splněny následující dvě položky, není potřeba vytvářet koncovým bodům virtuálních počítačů a není potřeba otevřít porty v bráně firewall na virtuálním počítači.
  
  * Není vzdáleně připojíte k funkcím systému SQL Server na virtuálním počítači. Navázání připojení ke vzdálené ploše na virtuálním počítači a přístup k funkcím systému SQL Server místně na virtuálním počítači není považováno za vzdálené připojení k funkcím systému SQL Server.
  * Virtuální počítač není připojení k doméně služby v místním prostřednictvím virtuální sítě Azure nebo jiného řešení tunelového propojení sítě VPN.
* Pokud virtuální počítač není připojený k doméně, ale chcete vzdáleně připojte k funkcím systému SQL Server na virtuálním počítači:
  
  * Otevřete porty v bráně firewall na virtuálním počítači.
  * Vytvoření koncovým bodům virtuálních počítačů pro uvedené porty (*).
* Pokud virtuální počítač je připojený k doméně pomocí tunelového připojení sítě VPN jako je například virtuální síť Azure, pak koncové body se nevyžadují. Ale otevřete porty v bráně firewall na virtuálním počítači.
  
  | Port | Typ | Popis |
  | --- | --- | --- |
  | **80** |TCP |Sestavy serveru vzdáleného přístupu (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. To je nutné, když virtuální počítač v připojený k doméně. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Výchozí instance SQL Server Analysis Services a Clusterové pojmenované instance. |
  | **definováno uživatelem** |TCP |Vytvoření statické Analysis Services s názvem instance port pro číslo portu, který zvolíte a pak odblokování číslo portu v bráně firewall. |

Další informace o vytváření koncových bodů naleznete v následujících tématech:

* Vytváření koncových bodů:[jak nastavit koncové body k virtuálnímu počítači](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Najdete v části "Kompletní konfigurace kroky pro připojení k virtuálnímu počítači pomocí SQL Server Management Studio" [zřízení virtuálního počítače SQL serveru v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Následující diagram znázorňuje porty otevřete v bráně firewall virtuálního počítače na virtuální počítač povolit vzdálený přístup k funkcím a komponent.

![porty otevřete pro bi aplikací ve virtuálních počítačích Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Zdroje a prostředky
* Projděte si zásady podpory pro serverový software Microsoftu, které používají v prostředí virtuálního počítače Azure. Následující téma shrnuje podporu pro funkce, jako je BitLocker, Clustering převzetí služeb při selhání a vyrovnávání zatížení sítě. [Podpora serverového softwaru Microsoftu pro Azure Virtual Machines](https://support.microsoft.com/kb/2721672).
* [SQL Server na Azure Virtual Machines – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Zřízení virtuálního počítače SQL serveru v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Postup připojení datového disku k virtuálnímu počítači](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrace databáze systému SQL Server na Virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Určete režim serveru z Instance služby Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Multidimenzionální modelování (kurz Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centrum dokumentace Azure](https://azure.microsoft.com/documentation/)
* [Pomocí Power BI v hybridním prostředí](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Odeslat zpětnou vazbu a kontaktní údaje přes připojení ke službě Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Obsah vytvořený komunitou
* [Správa databáze Azure SQL pomocí Powershellu](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

