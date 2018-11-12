---
title: Konfigurace skupiny dostupnosti Always On na Virtuálním počítači Azure s použitím prostředí PowerShell | Dokumentace Microsoftu
description: Tento kurz používá prostředky, které byly vytvořené pomocí modelu nasazení classic. Použití Powershellu k vytvoření skupiny dostupnosti Always On v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 584fca3df4fee24a4f1c7b93d5371c48be059f7b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257931"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurace skupiny dostupnosti Always On na Virtuálním počítači Azure pomocí Powershellu
> [!div class="op_single_selector"]
> * [Klasické: uživatelského rozhraní](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasické: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Než začnete, vezměte v úvahu, že teď můžete dokončit tuto úlohu v modelu Azure resource manager. Doporučujeme model Azure resource Manageru pro nová nasazení. Zobrazit [SQL serveru skupiny dostupnosti AlwaysOn na virtuálních počítačích Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Doporučujeme, aby většina nových nasazení používala model Resource Manager. Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení.

Virtuální počítače Azure (VM) umožňuje správcům databáze pro snížení nákladů, vysoké dostupnosti systému SQL Server. V tomto kurzu se dozvíte, jak implementovat skupinu dostupnosti s využitím SQL serveru Always On začátku do konce v prostředí Azure. Na konci tohoto kurzu řešení SQL serveru Always On v Azure bude obsahovat následující prvky:

* Virtuální síť, která obsahuje více podsítí, včetně front-endem a back endové podsítě.
* Řadič domény s doménou služby Active Directory.
* Dva virtuální počítače SQL serveru, které se nasazují do back endové podsítě a připojený k doméně služby Active Directory.
* Tři uzly clusteru převzetí služeb při selhání Windows s modelem kvora Většina uzlů.
* Skupina dostupnosti s dvě repliky synchronního potvrzování dostupnosti databáze.

Tento scénář je vhodný pro jeho jednoduchost v Azure, nikoli pro jeho nákladovou efektivitu a dalších faktorů. Například můžete minimalizovat počet virtuálních počítačů pro skupinu dostupnosti dvě repliky a Šetřete na výpočetní hodiny v Azure pomocí řadiče domény jako určující sdílená složka souboru kvora v clusteru převzetí služeb při selhání uzlu. Tato metoda snižuje počet virtuálních počítačů pomocí jedné z výše uvedených konfigurace.

Tento kurz je určen k zobrazení kroky, které jsou potřeba nastavit řešení je popsáno výše, aniž byste museli vypracování podrobností každého kroku. Proto namísto zadávání kroky konfigurace grafického uživatelského rozhraní, používá prostředí PowerShell na vás provedou rychle každého kroku. V tomto kurzu se předpokládá následující:

* Už máte účet Azure s předplatným služby virtuálního počítače.
* Když jste nainstalovali [rutin prostředí Azure PowerShell](/powershell/azure/overview).
* Už máte důkladného porozumění skupinami dostupnosti Always On pro místní řešení. Další informace najdete v tématu [skupiny dostupnosti AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Připojte se ke svému předplatnému Azure a vytvořte virtuální síť
1. V okně Powershellu v místním počítači naimportujte modul Azure stáhnout nastavení publikování do svého počítače a připojení relace prostředí PowerShell ke svému předplatnému Azure pomocí importu stažených nastavení publikování.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get-AzurePublishSettingsFile** příkaz automaticky vygeneruje certifikát pro správu Azure a stáhne do vašeho počítače. Prohlížeč se automaticky otevře a zobrazí výzva k zadání přihlašovacích údajů účtu Microsoft pro vaše předplatné Azure. Na stažený **.publishsettings** soubor obsahuje všechny informace potřebné ke správě vašeho předplatného Azure. Po uložení tohoto souboru do místního adresáře, importujte ho pomocí **Import AzurePublishSettingsFile** příkazu.

   > [!NOTE]
   > Soubor .publishsettings obsahuje vaše přihlašovací údaje (nekódovaný), které se používají ke správě vašich předplatných a služeb Azure. Osvědčené postupy zabezpečení pro tento soubor je dočasně ukládat mimo vašeho adresáře zdrojových souborů (například ve složce Libraries\Documents) a jeho odstranění po dokončení importu. Uživatel se zlými úmysly, který získá přístup k souboru .publishsettings můžete upravit, vytvoření a odstranění služby Azure.

2. Definujte řady proměnných, které použijete k vytvoření cloudové infrastruktury IT.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Věnujte pozornost následujícím zajistit, že vaše příkazy uspějí později:

   * Proměnné **$storageAccountName** a **$dcServiceName** musí být jedinečný, protože slouží k identifikaci cloudové úložiště účtu a cloud serveru, v uvedeném pořadí, v síti Internet.
   * Názvy, které zadáte pro proměnné **$affinityGroupName** a **$virtualNetworkName** jsou nakonfigurované v dokumentu konfigurace virtuální sítě, které budete používat později.
   * **$sqlImageName** určuje aktualizovaný název image virtuálního počítače, který obsahuje SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Pro zjednodušení **Contoso! 000** se o stejné heslo, který se používá v rámci celého kurzu.

3. Vytvořte skupinu vztahů.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Vytvoření virtuální sítě pomocí importu souboru konfigurace.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurační soubor obsahuje následujícího dokumentu XML. Stručně řečeno, určuje virtuální síť s názvem **ContosoNET** ve skupině vztahů, volá **ContosoAG**. Má adresní prostor **10.10.0.0/16** a se dvěma podsítěmi, **10.10.1.0/24** a **10.10.2.0/24**, které jsou v uvedeném pořadí přední podsítě a zpět podsítě. Front-podsíť je, kde můžete umístit klientské aplikace jako Microsoft SharePoint. Zpět podsíť je, kde budete umístit virtuální počítače SQL serveru. Pokud změníte **$affinityGroupName** a **$virtualNetworkName** proměnné dříve, musíte taky změnit odpovídající názvy níže.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Vytvoření účtu úložiště, který je spojen s skupinu vztahů, že jste vytvořili a nastavte ji jako aktuální účet úložiště v rámci vašeho předplatného.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Vytvořte server řadiče domény v nové sadě služby a dostupnost cloudu.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Tyto příkazy potrubím provádět následující akce:

   * **Nový-AzureVMConfig** vytvoří konfiguraci virtuálního počítače.
   * **Přidat AzureProvisioningConfig** poskytuje konfigurační parametry samostatný server Windows.
   * **Přidat AzureDataDisk** přidává datový disk, který budete používat k ukládání dat služby Active Directory, s možností ukládání do mezipaměti na hodnotu None.
   * **New-AzureVM** vytvoří novou cloudovou službu a nový virtuální počítač Azure vytvoří novou cloudovou službu.

7. Počkejte na jejich kompletní zřízení nového virtuálního počítače a stáhnout soubor vzdálené plochy do pracovního adresáře. Vzhledem k tomu, že nový virtuální počítač Azure trvá dlouhou dobu zřizovat, `while` smyčky i nadále posílat nový virtuální počítač, až bude připravená k použití.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

Nyní je úspěšně zřízený server řadiče domény. Dále nakonfigurujete domény služby Active Directory na tomto serveru řadiče domény. Nechte okno Powershellu otevřené v místním počítači. Použijete ho znovu vytvořit dva virtuální počítače s SQL serverem.

## <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
1. Připojte se k serveru řadiče domény spusťte soubor vzdálené plochy. Použít AzureAdmin uživatelské jméno a heslo správce počítače **Contoso! 000**, které jste zadali při vytváření nového virtuálního počítače.
2. Otevřete okno Powershellu v režimu správce.
3. Spusťte následující příkaz **DCPROMO. Soubor EXE** příkaz pro nastavení **corp.contoso.com** domény se datové adresáře na disku M.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    Jakmile se příkaz dokončí, virtuální počítač automaticky restartuje.

4. Připojení k serveru řadiče domény znovu spusťte soubor vzdálené plochy. Tentokrát, přihlaste se jako **CORP\Administrator**.
5. Otevřete okno Powershellu v režimu správce a naimportujte modul Powershellu pro Active Directory pomocí následujícího příkazu:

        Import-Module ActiveDirectory

6. Spuštěním následujících příkazů přidejte tři uživatele do domény.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** slouží ke konfiguraci opravdu všechno spojené s instancí služby SQL Server, clusteru převzetí služeb při selhání a skupiny dostupnosti. **CORP\SQLSvc1** a **CORP\SQLSvc2** se používají jako účty služeb systému SQL Server pro dva virtuální počítače SQL serveru.
7. Následně spusťte následující příkazy, které poskytují **CORP\Install** oprávnění k vytváření počítačových objektů v doméně.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Identifikátor GUID specifikovaný výše je identifikátor GUID pro typ objektu počítače. **CORP\Install** účtu potřebám **číst všechny vlastnosti** a **vytvářet objekty počítačů** oprávnění k vytváření aktivní přímé objektů pro převzetí služeb při selhání clusteru. **Číst všechny vlastnosti** oprávnění je už zadaná k CORP\Install ve výchozím nastavení, takže není nutné explicitně udělit. Další informace o oprávněních, které jsou potřebné k vytvoření clusteru převzetí služeb při selhání najdete v tématu [převzetí služeb při selhání clusteru podrobný průvodce: Konfigurace účtů ve službě Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teď, když jste dokončili konfiguraci služby Active Directory a uživatelských objektů, vytvoříte dva virtuální počítače SQL serveru a připojte je k této doméně.

## <a name="create-the-sql-server-vms"></a>Vytvořit virtuální počítače SQL serveru
1. Dál používat, který je na místním počítači otevřete okno Powershellu. Definujte následující další proměnné:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    IP adresa **10.10.0.4** je obvykle přiřazené na prvním virtuálním počítači, který vytvoříte v **10.10.0.0/16** podsítě vaší virtuální sítí Azure. Měli byste ověřit, že jde o adresu serveru řadiče domény spuštěním **IPCONFIG**.
2. Spusťte následující směrované příkazy k vytvoření prvního virtuálního počítače v clusteru převzetí služeb při selhání, s názvem **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Mějte na paměti následující týkající se výše uvedeného příkazu:

   * **Nový-AzureVMConfig** vytvoří konfiguraci virtuálního počítače se názvu požadované skupiny dostupnosti. Následující virtuální počítače se vytvoří s stejný název sady dostupnosti tak, aby připojí se do stejné skupiny dostupnosti.
   * **Přidat AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač v back podsítě.
   * **New-AzureVM** vytvoří novou cloudovou službu a nový virtuální počítač Azure vytvoří novou cloudovou službu. **Části Networkinterfaceconfigurations** parametr určuje, zda má server DNS pro servery v novou cloudovou službu IP adresu **10.10.0.4**. Toto je IP adresa serveru řadiče domény. Tento parametr je potřebná k povolení nové virtuální počítače v cloudové službě k připojení k doméně služby Active Directory úspěšně. Bez tohoto parametru se musí ručně nastavení IPv4 ve virtuálním počítači použít server řadiče domény jako primární server DNS po zřízení virtuálního počítače a pak virtuální počítač připojit k doméně služby Active Directory.
3. Spusťte následující směrované příkazy k vytvoření virtuálních počítačů serveru SQL s názvem **ContosoSQL1** a **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Mějte na paměti následující týkající se výše uvedené příkazy:

   * **Nový-AzureVMConfig** používá stejný název sady dostupnosti jako server řadiče domény a používá SQL Server 2012 Service Pack 1 Enterprise Edition image v galerii virtuálních počítačů. Také nastaví disk s operačním systémem na čtení ukládání do mezipaměti pouze (bez ukládání do mezipaměti). Doporučujeme migrovat soubory databáze do samostatné datový disk k virtuálnímu počítači připojíte a nakonfigurovat bez pro čtení nebo zápis do mezipaměti. Ale je skoro tak dobré odebrat, ukládání do mezipaměti na disku s operačním systémem, protože nelze odebrat, přečtěte si ukládání do mezipaměti na disku s operačním systémem.
   * **Přidat AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač v back podsítě.
   * **Přidat-AzureEndpoint** přidá koncové body přístupu tak, aby klientské aplikace můžou k tyto instance služby SQL Server na Internetu. Jiné porty disponují ContosoSQL1 a ContosoSQL2.
   * **New-AzureVM** vytvoří nový virtuální počítač SQL serverem v jako ContosoQuorum stejné cloudové službě. Pokud chcete, aby ve stejné skupině dostupnosti, je nutné umístit virtuální počítače ve stejné cloudové službě.
4. Počkejte, než pro každý virtuální počítač k plně zřízený a pro každý virtuální počítač stáhnout jeho soubor vzdálené plochy do pracovního adresáře. `for` Smyčka projde tři nové virtuální počítače a provede příkazy nejvyšší úrovně složených závorkách pro každý z nich.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Virtuální počítače SQL serveru jsou nyní zřízený a spuštěný, ale instalované se systémem SQL Server s výchozími možnostmi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializovat clusteru převzetí služeb při selhání virtuálních počítačů
V této části budete muset upravit tři servery, které budete používat v clusteru převzetí služeb při selhání a instalace systému SQL Server. Zejména:

* Všechny servery: je nutné nainstalovat **Clustering převzetí služeb při selhání** funkce.
* Všechny servery: je třeba přidat **CORP\Install** jako počítač **správce**.
* ContosoSQL1 a pouze ContosoSQL2: je třeba přidat **CORP\Install** jako **sysadmin** role ve výchozí databázi.
* ContosoSQL1 a pouze ContosoSQL2: je třeba přidat **NT AUTHORITY\System** jako u přihlášení s následujícími oprávněními:

  * Příkaz ALTER žádnou skupinu dostupnosti
  * Připojení SQL
  * Zobrazení stavu serveru
* ContosoSQL1 a pouze ContosoSQL2: **TCP** na virtuálním počítači SQL serveru je již povolen protokol. Nicméně stále potřebujete k otevření brány firewall pro vzdálený přístup k systému SQL Server.

Nyní jste připraveni začít. Počínaje **ContosoQuorum**, postupujte podle následujících kroků:

1. Připojte se k **ContosoQuorum** spuštěním soubory vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že počítače byl úspěšně připojen k **corp.contoso.com**.
3. Počkejte na dokončení úlohy automatizované inicializace před pokračováním instalace systému SQL Server.
4. Otevřete okno Powershellu v režimu správce.
5. Nainstalujte funkci Clustering převzetí služeb při selhání s Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidat **CORP\Install** jako místní správce.

        net localgroup administrators "CORP\Install" /Add
7. Odhlaste se z ContosoQuorum. Pak budete hotovi s tímto serverem nyní.

        logoff.exe

V dalším kroku inicializovat **ContosoSQL1** a **ContosoSQL2**. Postupujte podle následujících kroků, které jsou stejné pro oba virtuální počítače s SQL serverem.

1. Připojte se k dva virtuální počítače s SQL serverem pomocí souborů vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že počítače byl úspěšně připojen k **corp.contoso.com**.
3. Počkejte na dokončení úlohy automatizované inicializace před pokračováním instalace systému SQL Server.
4. Otevřete okno Powershellu v režimu správce.
5. Nainstalujte funkci Clustering převzetí služeb při selhání s Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidat **CORP\Install** jako místní správce.

        net localgroup administrators "CORP\Install" /Add
7. Importujte poskytovatele prostředí PowerShell SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Přidat **CORP\Install** jako role sysadmin pro výchozí instanci SQL serveru.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Přidat **NT AUTHORITY\System** jako Přihlaste se pomocí tří oprávnění je popsáno výše.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otevření brány firewall pro vzdálený přístup k systému SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Odhlaste se z obou virtuálních počítačů.

         logoff.exe

A konečně budete připraveni ke konfiguraci skupiny dostupnosti. Použijete PowerShell zprostředkovatele SQL Server provést všechny práce na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurace skupiny dostupnosti
1. Připojte se k **ContosoSQL1** znovu pomocí souborů vzdálené plochy. Místo přihlášení pomocí účtu počítače, přihlaste se pomocí **CORP\Install**.
2. Otevřete okno Powershellu v režimu správce.
3. Definujte následující proměnné:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importujte poskytovatele prostředí PowerShell SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Změňte účet služby SQL Server pro ContosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Změňte účet služby SQL Server pro ContosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Stáhněte si **CreateAzureFailoverCluster.ps1** z [vytvořit Cluster převzetí služeb při selhání pro skupiny dostupnosti Always On na virtuálním počítači Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do místního pracovního adresáře. Tento skript budete používat při vytvoření clusteru funkční převzetí služeb při selhání. Důležité informace o interakci Clustering převzetí služeb při selhání s Windows pomocí sítě Azure najdete v tématu [vysokou dostupnost a zotavení po havárii pro SQL Server ve službě Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Přejděte do pracovního adresáře a vytvořte cluster převzetí služeb při selhání pomocí staženého skriptu.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Povolte skupiny dostupnosti AlwaysOn pro výchozí instance systému SQL Server na **ContosoSQL1** a **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Vytvořit zálohovací adresář a udělení oprávnění pro účty služeb systému SQL Server. Budete používat tento adresář a připraví databázi dostupnosti na sekundární replice.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Vytvoření databáze v **ContosoSQL1** volá **MyDB1**provést úplnou zálohu a zálohu protokolu a obnoví je na **ContosoSQL2** s **WITH NORECOVERY**  možnost.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Vytvořte dostupnost skupiny koncových bodů na virtuální počítače SQL serveru a nastavte správné oprávnění v koncových bodech.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. Vytvoření repliky dostupnosti.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Nakonec vytvořte skupinu dostupnosti a spojení sekundární repliky do skupiny dostupnosti.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>Další postup
Jste teď úspěšně implementovali AlwaysOn SQL serveru tak, že vytvoříte skupinu dostupnosti v Azure. Konfigurace naslouchacího procesu pro tuto skupinu dostupnosti, najdete v článku [konfigurace naslouchacího procesu ILB pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

Další informace o používání SQL serveru v Azure najdete v tématu [systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
