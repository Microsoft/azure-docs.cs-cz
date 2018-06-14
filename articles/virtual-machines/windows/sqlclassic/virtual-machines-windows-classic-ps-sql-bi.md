---
title: SQL Server Business Intelligence | Microsoft Docs
description: Toto téma používá prostředky, které jsou vytvořené pomocí modelu nasazení classic a popisuje funkce Business Intelligence (BI), která je k dispozici pro SQL Server běžící na virtuálních počítačích Azure (VM).
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
ms.openlocfilehash: 6f1a95e52def9154253192ab9d43d1e7d621cee7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31425608"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence v Azure Virtual Machines
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.

Virtuální počítač Microsoft Azure gallery zahrnuje bitové kopie, které obsahují instalace systému SQL Server. V edicích systému SQL Server podporován v galerii bitové kopie jsou instalační soubory, které můžete nainstalovat do počítačů místní a virtuální počítače. Toto téma shrnuje funkce SQL Server Business Intelligence (BI), které jsou nainstalované na obrázky a kroků konfigurace požadovaných po zřízení virtuálního počítače. Toto téma také popisuje podporované topologie nasazení funkce BI a osvědčené postupy.

## <a name="license-considerations"></a>Důležité informace o licenci
Existují dva způsoby, jak licenci SQL serveru ve virtuálních počítačích Microsoft Azure:

1. Výhody mobility licencí, které jsou součástí programu Software Assurance. Další informace najdete v tématu [mobilitou licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Platba za hodinu míra Azure virtuálních počítačů s nainstalovaným serverem SQL. Najdete v části "SQL Server" v [ceny služeb Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Další informace o licencování a aktuální míry najdete v tématu [virtuální počítače nejčastější dotazy ohledně licencování](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server bitové kopie k dispozici v galerii virtuálních počítačů Azure
Virtuální počítač Microsoft Azure galerie obsahuje několik imagí, které obsahují Microsoft SQL Server. Software nainstalovaný v Image virtuálních počítačů se liší podle verze operačního systému a verzi systému SQL Server. V seznamu bitových kopií k dispozici v galerii virtuálních počítačů Azure se často mění.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL obrázek v galerii virtuálních počítačů Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Následující skript prostředí PowerShell vrátí seznam Azure bitové kopie, které obsahují "SQL serverem" v ImageName:

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

Další informace o vydání a funkce v systému SQL Server podporovány naleznete v následujících tématech:

* [Edice serveru SQL](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Funkce podporovaných jednotlivými edicemi systému SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI funkce nainstalované Image SQL serveru galerii virtuálních počítačů
Následující tabulka shrnuje nainstalovaných na běžné Galerie obrázků virtuální počítač Microsoft Azure pro systém SQL Server Business Intelligence funkcí:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Funkci SQL Server BI | Nainstalovat na bitovou kopii Galerie | Poznámky |
| --- | --- | --- |
| **Reporting Services – nativní režim** |Ano |Nainstalován, ale vyžaduje konfigurace, včetně adresu URL správce sestav. Najdete v části [konfigurace služby Reporting Services](#configure-reporting-services). |
| **Služby Reporting Services režimu serveru SharePoint** |Ne |Bitovou kopii virtuálního počítače Microsoft Azure Galerie nezahrnuje SharePoint nebo SharePoint instalační soubory. <sup>1</sup> |
| **Analysis Services Multidimensional a Data dolování (OLAP)** |Ano |Nainstalovaný a nakonfigurovaný jako výchozí instanci služby Analysis Services |
| **Tabulkové služby Analysis Services** |Ne |Podporované v systému SQL Server 2012, 2014 a 2016 bitové kopie ale není nainstalována ve výchozím nastavení. Instalovat další instanci služby Analysis Services. Najdete v části instalace jiné služby SQL Server a funkcí v tomto tématu. |
| **Power Pivot. Analysis Services pro službu SharePoint** |Ne |Bitovou kopii virtuálního počítače Microsoft Azure Galerie nezahrnuje SharePoint nebo SharePoint instalační soubory. <sup>1</sup> |

<sup>1</sup> Další informace o virtuálních počítačích Azure a služby SharePoint, naleznete v části [architektury Microsoft Azure pro službu SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) a [nasazení služby SharePoint v Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Spusťte následující příkaz prostředí PowerShell k získání seznamu nainstalovaných služeb, které obsahují "SQL" v názvu služby.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Obecná doporučení a osvědčené postupy
* Minimální doporučená velikost virtuálního počítače je **A3** při použití SQL Server Enterprise Edition. **A4** velikost virtuálního počítače se doporučuje pro nasazení systému SQL Server BI Analysis Services a Reporting Services.
  
    Informace o aktuální velikosti virtuálních počítačů najdete v tématu [velikostí virtuálních počítačů pro Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Osvědčeným postupem pro správu disků je jiné než ukládání dat, log a záložní soubory na jednotkách **C**: a **D**:. Můžete například vytvořit datové disky **E**: a **F**:.
  
  * Jednotka ukládání do mezipaměti zásady pro výchozí jednotce **C**: nevhodné pro práci s daty.
  * **D**: jednotka je dočasné jednotce, která se používá hlavně pro stránkovací soubor. **D**: jednotka není trvalý a neukládají se v úložišti objektů blob. Úlohy správy, například o změně pro virtuální počítač velikost resetování **D**: jednotky. Doporučuje se **není** použít **D**: disku pro soubory databáze, včetně databáze tempdb.
    
    Další informace o vytváření a připojení disků najdete v tématu [jak připojit datový Disk k virtuálnímu počítači](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Zastavení nebo odinstalaci služby, které není plánujete použít. Pro příklad, pokud virtuální počítač se používá pouze pro služby Reporting Services, zastavení nebo odinstalaci služby Analysis Services a SQL Server Integration Services. Na následujícím obrázku je příkladem služby, které jsou spuštěny ve výchozím nastavení.
  
    ![Službu SQL Server.](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > Databázový stroj SQL Server je vyžadována v Podporované scénáře BI. Jeden server topologie virtuálních počítačů je databázový stroj vyžadovat, aby byl spuštěn na stejného virtuálního počítače.
  
    Další informace najdete tady: [odinstalaci služby Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) a [odinstalovat instanci služby Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Zkontrolujte **Windows Update** nové, důležité aktualizace'. Bitové kopie virtuálního počítače Microsoft Azure se často aktualizuje; ale může být důležité aktualizace k dispozici z **Windows Update** po poslední aktualizaci image virtuálního počítače.

## <a name="example-deployment-topologies"></a>Příklad topologie nasazení
Následuje příklad nasazení, které používají virtuální počítače Microsoft Azure. Topologie v těchto diagramy jsou jenom některé z možných topologie, které můžete použít s funkcemi SQL Server BI a virtuální počítače Microsoft Azure.

### <a name="single-virtual-machine"></a>Jeden virtuální počítač
Analysis Services, služby Reporting Services, databázového stroje SQL Server a zdrojů dat na jednom virtuálním počítači.

![scénář BI IAS se 1 virtuálního počítače](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Dva virtuální počítače
* Služba Analysis Services, služby Reporting Services a databázového stroje SQL Server na jeden virtuální počítač. Toto nasazení obsahuje databáze serveru sestav.
* Zdroje dat na druhý virtuální počítač. Druhý virtuální počítač obsahuje databázového stroje SQL Server jako zdroj dat.

![scénář BI iaas s 2 virtuální počítače](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Smíšená Azure – data v databázi Azure SQL
* Služba Analysis Services, služby Reporting Services a databázového stroje SQL Server na jeden virtuální počítač. Toto nasazení obsahuje databáze serveru sestav.
* Zdroj dat je Azure SQL database.

![virtuální počítač scénáře BI iaas a AzureSQL jako zdroj dat](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybridní – data místně
* V tomto příkladu nasazení služby Analysis Services Reporting Services a databázového stroje SQL Server spustit na jednom virtuálním počítači. Virtuální počítač je hostitelem databáze serveru sestav. Virtuální počítač je připojený k doméně místní prostřednictvím virtuálních sítí Azure nebo některých jiných tunelového propojení řešení VPN.
* Zdroj dat je místně.

![BI iaas scénáře virtuálních počítačů a místní zdroje dat](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfigurace služby Reporting Services v nativním režimu
Galerie image virtuálního počítače pro SQL Server obsahuje Reporting Services na nativní režim nainstalován, ale server sestav není nakonfigurován. Postup v této části nakonfigurovat server sestav služby Reporting Services. Podrobné informace o konfiguraci Reporting Services na nativní režim, najdete v části [instalace sestav Reporting Services nativní režim serveru (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Podobně jako obsah, který používá skripty prostředí Windows PowerShell ke konfiguraci serveru sestav, naleznete v části [použít PowerShell k vytvoření virtuálních počítačů s nativní režim sestavy Server Azure](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Připojení k virtuálnímu počítači a spuštění Správce konfigurace služby Reporting Services
Existují dvě běžné pracovní postupy pro připojení k virtuální počítač Azure:

* Kliknutím na tlačítko název virtuálního počítače a potom klikněte na tlačítko **Connect**. Otevře připojení ke vzdálené ploše a název počítače se automaticky vyplní.
  
    ![připojení k virtuálnímu počítači azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Připojte k virtuálnímu počítači s Windows připojení ke vzdálené ploše. V uživatelském rozhraní vzdálené plochy:
  
  1. Typ **název cloudové služby** jako název počítače.
  2. Zadejte dvojtečkou (:) a číslem veřejného portu, který je nakonfigurovaný pro koncový bod TCP vzdálené plochy.
     
      Myservice.cloudapp.net:63133
     
      Další informace najdete v tématu [co je Cloudová služba?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Spuštění, vytváření sestav služby Configuration Manager**

V **systému Windows Server 2012/2016**:

1. Z **spustit** zadejte **služby Reporting Services** zobrazíte seznam aplikací.
2. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.

V **systému Windows Server 2008 R2**:

1. Klikněte na tlačítko **spustit**a potom klikněte na **všechny programy**.
2. Klikněte na tlačítko **Microsoft SQL Server 2016**.
3. Klikněte na tlačítko **konfigurační nástroje**.
4. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.

nebo:

1. Klikněte na tlačítko **spustit**.
2. V **Prohledat programy a soubory** typ dialogu **služby reporting services**. Pokud virtuální počítač se systémem Windows Server 2012, zadejte **služby reporting services** na obrazovce Start systému Windows Server 2012.
3. Klikněte pravým tlačítkem na **Správce konfigurace služby Reporting Services** a klikněte na tlačítko **spustit jako správce**.
   
    ![Vyhledejte Správce konfigurace služby ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurace služby Reporting Services
**Účet služby a adresu URL webové služby:**

1. Ověřte **název serveru** je název místního serveru a klikněte na tlačítko **Connect**.
2. Poznámka: prázdné **název databáze serveru sestav**. Databáze je vytvořen po dokončení konfigurace.
3. Ověřte **stav serveru sestav** je **Začínáme**. Pokud chcete ověřit služby ve Správci serveru systému Windows, je služba **SQL Server Reporting Services** služba systému Windows.
4. Klikněte na tlačítko **účet služby** a podle potřeby změňte účet. Pokud se používá virtuální počítač v doméně připojené k prostředí integrované **ReportServer** účet je dostatečná. Další informace o účtu služby naleznete v tématu [účet služby](https://msdn.microsoft.com/library/ms189964.aspx).
5. Klikněte na tlačítko **adresa URL webové služby** v levém podokně.
6. Klikněte na tlačítko **použít** nakonfigurovat výchozí hodnoty.
7. Poznámka: **sestavy adresy URL serveru webové služby**. Všimněte si, výchozí port TCP 80 a je součástí adresy URL. V pozdější fázi můžete vytvořit koncový bod Microsoft Azure virtuálního počítače pro port.
8. V **výsledky** podokně zkontrolujte akce, které byl úspěšně dokončen.

**Databáze:**

1. Klikněte na tlačítko **databáze** v levém podokně.
2. Klikněte na tlačítko **změnit databázi**.
3. Ověřte **vytvořit novou databázi serveru sestav** je vybrána a pak klikněte na tlačítko Další.
4. Ověřte **název serveru** a klikněte na tlačítko **Test připojení**.
5. Pokud je výsledek **testovací připojení bylo úspěšné**, klikněte na tlačítko **OK** a pak klikněte na **Další**.
6. Poznámka: název databáze je **ReportServer** a **režim serveru sestav** je **nativní** klikněte **Další**.
7. Klikněte na tlačítko **Další** na **pověření** stránky.
8. Klikněte na tlačítko **Další** na **Souhrn** stránky.
9. Klikněte na tlačítko **Další** na **průběhu a dokončit** stránky.

**Webová adresa URL portálu nebo adresa URL správce sestav pro 2012 a 2014:**

1. Klikněte na tlačítko **adresy URL webového portálu**, nebo **adresa URL správce sestav** 2014 a 2012, v levém podokně.
2. Klikněte na tlačítko **Použít**.
3. V **výsledky** podokně zkontrolujte akce, které byl úspěšně dokončen.
4. Klikněte na tlačítko **ukončení**.

Informace o oprávnění serveru sestav, naleznete v části [udělení oprávnění na serveru sestav, nativní režim](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Přejděte na místní správce sestav
Pokud chcete ověřit konfiguraci, přejděte do Správce sestav ve virtuálním počítači.

1. Ve virtuálním počítači spusťte Internet Explorer s oprávněními správce.
2. Přejděte do http://localhost/reports ve virtuálním počítači.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Pro připojení k vzdálené webový portál nebo správce sestav pro 2014 a 2012
Pokud se chcete připojit na webový portál nebo správce sestav pro 2014 a 2012, na virtuálním počítači ze vzdáleného počítače, vytvořte nový virtuální počítač koncový bod TCP. Ve výchozím nastavení, server sestav čeká na požadavky HTTP na **port 80**. Pokud nakonfigurujete adresy URL serveru sestav použít jiný port, zadejte toto číslo portu v následujících pokynech.

1. Vytvoření koncového bodu pro virtuální počítač TCP Port 80. Další informace najdete v tématu, [koncové body virtuálního počítače a porty brány Firewall](#virtual-machine-endpoints-and-firewall-ports) v tomto dokumentu.
2. Otevřete port 80 v bráně firewall virtuálního počítače.
3. Přejděte na webový portál nebo ohlásit manager pomocí virtuální počítač Azure **název DNS** jako název serveru v adrese URL. Příklad:
   
    **Server sestav**: http://uebi.cloudapp.net/reportserver **webový portál**:   http://uebi.cloudapp.net/reports
   
    [Configure a Firewall for Report Server Access](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>K vytvoření a publikování sestav pro virtuální počítač Azure
Následující tabulka shrnuje některé možnosti, které jsou k dispozici pro publikování stávajících sestavách v místním počítači na server sestav, které jsou hostované na virtuální počítač Microsoft Azure:

* **Tvůrce sestav**: obsahuje virtuální počítač a klikněte na-jednou verzi Tvůrce sestav Microsoft SQL serveru pro SQL 2014 a 2012. Spuštění sestavy Tvůrce první na virtuálním počítači s SQL 2016:
  
  1. Spustíte prohlížeč s oprávněními správce.
  2. Přejděte na webový portál na virtuálním počítači a vyberte **Stáhnout** ikonu v pravém horním rohu.
  3. Vyberte **Tvůrce sestav**.
     
     Další informace najdete v tématu [spuštění Tvůrce sestav](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: virtuálních počítačů: SQL Server Data Tools je nainstalovaná na virtuálním počítači a slouží k vytvoření **projekty serveru sestav** a sestavy na virtuálním počítači. SQL Server Data Tools můžete publikovat sestavy na server sestav na virtuálním počítači.
* **SQL Server Data Tools: Vzdálené**: V místním počítači, vytvoření projektu služby Reporting Services v SQL Server Data Tools, obsahující sestavy služby Reporting Services. Konfigurace projektu pro připojení k adresa URL webové služby.
  
    ![Vlastnosti projektu rozšíření SSDT pro projekt služby SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Vytvoření. Pevný disk VHD, který obsahuje sestavy a pak nahrajte a připojte jednotku.
  
  1. Vytvoření. Virtuální pevný disk pevného disku v místním počítači, který obsahuje sestavy.
  2. Vytvoření a nainstalujte certifikát pro správu.
  3. Nahrát soubor virtuálního pevného disku do Azure pomocí rutiny Add-AzureVHD [vytvoření a nahrání virtuálního pevného disku serveru Windows Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Připojte disk k virtuálnímu počítači.

## <a name="install-other-sql-server-services-and-features"></a>Instalace dalších služby SQL Server a funkcí
Chcete-li nainstalovat další služby SQL Server, jako je služba Analysis Services v tabulkovém režimu, spusťte Průvodce instalací serveru SQL. Instalační soubory jsou na místním disku virtuálního počítače.

1. Klikněte na tlačítko **spustit** a pak klikněte na **všechny programy**.
2. Klikněte na tlačítko **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** nebo **Microsoft SQL Server 2012** a pak klikněte na **nástroje pro konfiguraci** .
3. Klikněte na tlačítko **centrum instalace SQL serveru**.

Nebo spusťte C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe nebo C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Při prvním spuštění instalačního programu systému SQL Server, další instalační soubory stáhnout a vyžadují restartování virtuálního počítače a restartování instalace systému SQL Server.
> 
> Pokud potřebujete opakovaně přizpůsobení bitové kopie vybrané z virtuální počítač Microsoft Azure, zvažte vytvoření vlastní image SQL serveru. Funkce Analysis Services SysPrep bylo povoleno s SQL Server 2012 SP1 CU2. Další informace najdete v tématu [důležité informace týkající se instalace SQL serveru pomocí nástroje SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) a [podpora nástroje Sysprep pro role serveru](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Chcete-li nainstalovat tabulkovém režimu Analysis Services
Postup v této části **shrnout** instalaci služby Analysis Services tabulkovém režimu. Další informace naleznete v následujících tématech:

* [Instalace služby Analysis Services v tabulkovém režimu](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabulkové modelování (společnosti Adventure Works kurzu)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Chcete-li nainstalovat tabulkovém režimu Analysis Services:**

1. V Průvodci instalací systému SQL Server, klikněte na tlačítko **instalace** v levém podokně a pak klikněte na tlačítko **samostatná instalace nové SQL serveru nebo přidání funkcí do existující instalace**.
   
   * Pokud se zobrazí **vyhledat složku**, přejděte do c:\SQLServer_13.0_full, c:\SQLServer_12.0_full nebo c:\SQLServer_11.0_full a pak klikněte na tlačítko **Ok**.
2. Klikněte na tlačítko **Další** na stránce aktualizace produktu.
3. Na **typ instalace** vyberte **nová instalace systému SQL Server** a klikněte na tlačítko **Další**.
4. Na **Role instalace** klikněte na tlačítko **instalace funkce SQL Server**.
5. Na **výběr funkce** klikněte na tlačítko **služby Analysis Services**.
6. Na **konfigurace Instance** stránky, zadejte popisný název, například **tabulkového** do **s názvem Instance** a **Instance Id** textová pole .
7. Na **konfigurace služby Analysis Services** vyberte **tabulkovém režimu**. Aktuální uživatel přidáte do seznamu oprávnění správce.
8. Dokončete a zavřete průvodce instalací systému SQL Server.

## <a name="analysis-services-configuration"></a>Konfigurace služby Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Vzdálený přístup k serveru Analysis Services
Server služby Analysis Services podporuje pouze ověřování systému windows. Vzdálený přístup ke službě Analysis Services z klientské aplikace jako SQL Server Management Studio nebo SQL Server Data Tools, virtuální počítač musí být připojené k vaší místní domény, pomocí virtuální sítě Azure. Další informace najdete v tématu [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

A **výchozí instance** služby Analysis Services naslouchá na portu TCP **2383**. Otevřete port v bráně firewall virtuálních počítačů. Clusteru s názvem instance služby Analysis Services také sleduje na portu **2383**.

Pro **pojmenovanou instanci** služby Analysis Services, je potřeba spravovat přístup k portu služby SQL Server Browser. SQL Server Browser výchozí konfigurace je port **. 2382**.

V bráně firewall virtuální počítače, otevřete port **. 2382** a vytvořte statické Analysis Services s názvem instance portu.

1. Pokud chcete ověřit porty, které jsou již v provozu na virtuálního počítače, které proces používá porty, spusťte následující příkaz s oprávněními správce:
   
        netstat /ao
2. Pomocí SQL Server Management Studio k vytvoření statických služby Analysis Services s názvem instance port aktualizací, Port, hodnota v tabulkovém AS obecné vlastnosti instance. Další informace najdete v tématu "použít pevný port pro výchozí nebo pojmenované instance" v [konfigurace brány Windows Firewall a povolit přístup Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Restartujte tabulkové instanci služby Analysis Services.

Další informace najdete v tématu, **koncové body virtuálního počítače a porty brány Firewall** v tomto dokumentu.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Koncové body virtuálního počítače a porty brány Firewall
Tento oddíl shrnuje Microsoft koncové body virtuálního počítače Azure k vytvoření a porty, otevřete v oblasti chráněné branami firewall virtuálního počítače. Následující tabulka shrnuje **TCP** porty koncových bodů pro vytvoření a porty, které chcete otevřít v bráně firewall virtuálních počítačů.

* Pokud používáte jeden virtuální počítač a platí následující dvě položky, není nutné k vytvoření virtuálních počítačů koncových bodů a není potřeba otevřít porty v bráně firewall na virtuálním počítači.
  
  * Se není vzdáleně připojit k dané funkce serveru SQL na virtuálním počítači. Navazování připojení ke vzdálené ploše k virtuálnímu počítači a přístup k systému SQL Server funkce místně na virtuální počítač není považováno za vzdálené připojení k dané funkce serveru SQL.
  * Virtuální počítač zapojit se do místní domény prostřednictvím virtuálních sítí Azure nebo jiné řešení tunelového propojení sítě VPN.
* Pokud virtuální počítač není připojený k doméně, ale chcete vzdáleně připojte k dané funkce serveru SQL na virtuálním počítači:
  
  * Otevřete porty v bráně firewall na virtuálním počítači.
  * Vytvoření virtuálního počítače koncových bodů pro uvedené porty (*).
* Pokud virtuální počítač je připojený k doméně pomocí tunelového připojení sítě VPN, jako je například virtuální sítí Azure, pak koncových bodů nejsou potřeba. Ale otevřete porty v bráně firewall na virtuálním počítači.
  
  | Port | Typ | Popis |
  | --- | --- | --- |
  | **80** |TCP |Server sestav vzdáleného přístupu (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. To je potřeba, když virtuální počítač v připojený k doméně. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Výchozí instance SQL Server Analysis Services a clusteru s názvem instance. |
  | **Uživatelem definované** |TCP |Vytvořte statické Analysis Services s názvem instance, port pro číslo portu, který zvolíte a pak odblokovat číslo portu v bráně firewall. |

Další informace o vytváření koncových bodů naleznete v následujících tématech:

* Vytvoření koncových bodů:[jak nastavit koncové body k virtuálnímu počítači](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Najdete v části "Kompletní konfigurace kroky pro připojení k virtuálnímu počítači pomocí SQL Server Management Studio" [zřizování virtuálního počítače systému SQL Server na platformě Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Následující diagram znázorňuje tyto porty otevřít v bráně firewall virtuálních počítačů pro povolení vzdáleného přístupu k komponenty a funkce na virtuálním počítači.

![porty otevřít pro bi aplikace ve virtuálních počítačích Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Zdroje a prostředky
* Najdete zásadách podpory pro serverového softwaru společnosti Microsoft používá v prostředí virtuálního počítače Azure. Následující téma shrnuje podporu pro funkce, například nástrojem BitLocker, Clustering převzetí služeb při selhání a vyrovnávání zatížení sítě. [Podpora Microsoft serveru software pro virtuální počítače Azure](http://support.microsoft.com/kb/2721672).
* [SQL Server na virtuálních počítačích Azure – přehled](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Zřizování virtuálního počítače systému SQL Server v Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Tom, jak připojit datový Disk k virtuálnímu počítači](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrace databáze do systému SQL Server ve virtuálním počítači Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Určete režim serveru instance Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Multidimenzionální modelování (společnosti Adventure Works kurzu)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centru dokumentace Azure](https://azure.microsoft.com/documentation/)
* [Pomocí Power BI v hybridním prostředí](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Odeslat zpětnou vazbu a kontaktních informací prostřednictvím připojení ke službě Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Komunitním obsahu
* [Správa databáze Azure SQL pomocí prostředí PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

