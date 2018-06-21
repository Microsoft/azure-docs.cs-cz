---
title: Používání databáze MySQL na Azure zásobníku | Microsoft Docs
description: Zjistěte, jak můžete nasadit databází MySQL jako služba na Azure zásobníku a rychlé kroky k nasazení adaptér zprostředkovatele prostředků MySQL serveru.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295753"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Používání databází MySQL v zásobníku Microsoft Azure
Pomocí zprostředkovatele prostředků Azure zásobníku MySQL Server vystavit databází MySQL jako služba Azure zásobníku. Službu MySQL resource provider spustí na poskytovateli prostředků MySQL virtuální počítač, který je virtuální počítač jádra systému Windows Server.

## <a name="deploy-the-resource-provider"></a>Nasazení poskytovatele prostředků 
1. Pokud jste tak již neučinili, zaregistrujte vaší development kit a stáhnout bitovou kopii jádro systému Windows Server 2016 Datacenter ke stažení přes správu Marketplace. Je nutné použít bitovou kopii jádro systému Windows Server 2016. Skript můžete také použít k vytvoření [bitové kopie systému Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Nezapomeňte vybrat možnost jádra.) 

2. Přihlaste se k hostiteli, který přístup privilegované koncový bod virtuálního počítače:
    - Instalace Azure SDK Přihlaste se na fyzickém hostiteli.  
    - Na integrované systémy hostitele musí být systém, kterým může přistupovat privilegované koncový bod. 
    
    >[!NOTE] 
    > Systém, na kterém je spuštěn skript *musí* být systém Windows 10 nebo Windows Server 2016 na nejnovější verzi modulu runtime rozhraní .NET nainstalované. Instalace selže. Hostitel ASDK zásobník Azure splňuje tato kritéria. 
    
3. Stáhněte poskytovatele prostředků MySQL binární. Pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. 
    >[!NOTE]  
    > Poskytovatel prostředků má minimální odpovídající Azure zásobníku sestavení. Ujistěte se, že jste stáhnout správné binární verze zásobník Azure, který běží.

    | Verze Azure zásobníku | Verze databáze MySQL RP| 
    | --- | --- | 
    | Verze 1804 (1.0.180513.1)|[MySQL RP verze 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Verze 1802 (1.0.180302.1) | [MySQL RP verze 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Verze 1712 (1.0.180102.3 nebo 1.0.180106.1 (integrované systémy)) | [MySQL RP verze 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Pro ASDK se vytvoří certifikát podepsaný svým držitelem v rámci tohoto procesu. Pro integrované systémy je nutné zadat příslušný certifikát. Pokud potřebujete přidat vlastní certifikát, umístit soubor .pfx v **DependencyFilesLocalPath** , která splňuje následující kritéria: 
    - Certifikát zástupných znaků pro \*.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\> nebo certifikát jedné lokalitě s běžný název mysqladapter.dbadapter.\< oblast\>.\< externí plně kvalifikovaný název domény\>. 
    - Tento certifikát musí být důvěryhodný. To znamená řetěz certifikátů, musí existovat bez nutnosti zprostředkující certifikáty. 
    - V DependencyFilesLocalPath existuje pouze jeden certifikát souboru. 
    - Název souboru nesmí obsahovat žádné speciální znaky ani mezery. 

5. Otevřete **nové** konzolu prostředí PowerShell se zvýšenými oprávněními (správce). Pak přejděte do adresáře, které jste extrahovali soubory. Použijte nové okno se pokud chcete vyhnout potížím, které by mohly nastat z nesprávné modulů prostředí PowerShell, které jsou již načtena v systému. 

6. Spustit **DeployMySqlProvider.ps1** skriptu. Skript provede tyto kroky: 
    - Stáhne MySQL konektor binárního souboru (to se dá zajistit offline). 
    - Ukládání certifikáty a další artefakty na účet úložiště v Azure zásobníku. 
    - Publikuje Galerie balíčky, abyste mohli nasadit databází SQL v galerii. 
    - Publikuje balíček Galerie pro nasazení hostitelskými servery. 
    - Nasazení virtuálního počítače pomocí bitové kopie zásobníku systému Windows Server 2016 Azure marketplace a nainstaluje poskytovatele prostředků. 
    - Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálních počítačů. 
    - Zaregistruje poskytovatel prostředků s místní Azure Resource Manager (klient a správce). 

    Můžete zadejte požadované parametry na příkazovém řádku nebo spusťte skript bez parametrů a pak je po zobrazení výzvy zadejte. 

    Tady je příklad, který můžete spustit z příkazového řádku prostředí PowerShell. Nezapomeňte změnit informace o účtu a hesla podle potřeby: 

    ```powershell 
    # Install the AzureRM.Bootstrapper module and set the profile. 
    Install-Module -Name AzureRm.BootStrapper -Force 
    Use-AzureRmProfile -Profile 2017-03-09-profile 

    # Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
    $domain = "AzureStack"  

    # For integrated systems, use the IP address of one of the ERCS virtual machines 
    $privilegedEndpoint = "AzS-ERCS01" 

    # Point to the directory where the resource provider installation files were extracted. 
    $tempDir = 'C:\TEMP\MYSQLRP' 

    # The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
    $serviceAdmin = "admin@mydomain.onmicrosoft.com" 
    $AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 

    # Set the credentials for the new resource provider VM local administrator account 
    $vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 

    # And the cloudadmin credential required for privileged endpoint access. 
    $CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

    # Change the following as appropriate. 
    $PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

    # Run the installation script from the folder where you extracted the installation files. 
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>Parametry DeployMySqlProvider.ps1 
Tyto parametry můžete zadat na příkazovém řádku. Pokud ho použít nechcete, nebo pokud žádné parametr ověření selže, zobrazí se výzva k zadání požadované parametry. 

| Název parametru | Popis | Komentář nebo výchozí hodnotu | 
| --- | --- | --- | 
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegované koncový bod. | _Požadované_ | 
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure zásobníku. Pomocí stejných přihlašovacích údajů, které jste použili pro nasazení Azure zásobníku. | _Požadované_ | 
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce MySQL zprostředkovatele prostředků virtuálních počítačů. | _Požadované_ | 
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegované koncového bodu. |  _Požadované_ | 
| **DependencyFilesLocalPath** | Cesta k místní sdílené složky, která obsahuje [mysql konektor net 6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Pokud zadáte jednu z těchto cest, soubor certifikátu musí být umístěny v tomto adresáři také. | _Volitelné_ (_povinné_ pro více uzly) | 
| **DefaultSSLCertificatePassword** | Heslo pro certifikát .pfx. | _Požadované_ | 
| **MaxRetryCount** | Počet přístupů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 | 
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy, v sekundách. | 120 | 
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky k). | Ne | 
| **Režim DebugMode** | Brání automatické čištění při selhání. | Ne | 
| **AcceptLicense** | Přeskočí řádku tak, aby přijímal GPL licence.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKU může trvat až jednu hodinu, mají být zobrazeny v portálu. Databázi nelze vytvořit, dokud nebude vytvořen verze SKU. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure zásobníku 
> [!NOTE] 
>  Po dokončení skriptu instalace musíte aktualizovat na portálu najdete v okně správce. 

1. Přihlaste se k portálu pro správu jako správce služeb. 
2. Ověřte, zda nasazení proběhla úspěšně. Přejděte na **skupiny prostředků**a pak vyberte **systému.\< umístění\>.mysqladapter** skupinu prostředků. Ověřte, zda všechny čtyři nasazení proběhla úspěšně:

      ![Ověření nasazení MySQL RP](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Další postup
[Přidat hostitelskými servery](azure-stack-mysql-resource-provider-hosting-servers.md)
