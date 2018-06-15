---
title: Konfigurace skupiny dostupnosti Always On na virtuální počítač Azure pomocí prostředí PowerShell | Microsoft Docs
description: Tento kurz používá prostředky, které byly vytvořeny s modelem nasazení classic. Použít PowerShell k vytvoření skupiny dostupnosti Always On v Azure.
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
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29401278"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurace skupiny dostupnosti Always On na virtuální počítač Azure pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Klasické: uživatelského rozhraní](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasické: prostředí PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Než začnete, vezměte v úvahu, že je nyní možné dokončit tuto úlohu v modelu Azure resource manager. Doporučujeme, abyste model nástroje Správce prostředků Azure pro nová nasazení. V tématu [SQL serveru Always On skupiny dostupnosti na virtuálních počítačích Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Doporučujeme vám, že většina nových nasazení používala model Resource Manager. Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje použití klasického modelu nasazení.

Virtuální počítače Azure (VM) může pomoct správci databází za účelem snížení nákladů systému SQL Server vysokou dostupností. V tomto kurzu se dozvíte, jak implementovat skupinu dostupnosti pomocí SQL serveru Always On klient server v prostředí Azure. Na konci tohoto kurzu vaše řešení SQL serveru Always On v Azure budou tvořeny následující prvky:

* Virtuální síť, která obsahuje více podsítí, včetně front-end a back-end podsítě.
* Řadič domény s doménou služby Active Directory.
* Dva SQL serveru virtuálních počítačů, které jsou nasazeny do podsítě back-end a připojený k doméně služby Active Directory.
* Tři uzly clusteru převzetí služeb při selhání systému Windows s modelem kvora Většina uzlů.
* Skupina dostupnosti databáze dostupnosti s dvěma replik se synchronním potvrzováním.

Tento scénář je vhodný pro jeho jednoduchost v Azure, ne pro jeho nákladová efektivnost nebo dalších faktorů. Například můžete minimalizovat počet virtuálních počítačů pro skupinu dostupnosti dvě repliky uložit na výpočetní hodiny v Azure pomocí řadiče domény jako sdílenou složku kvora v clusteru s podporou převzetí služeb při selhání dvěma uzly. Tato metoda snižuje počet virtuálních počítačů pomocí jedné z výše uvedených konfigurace.

Tento kurz je určený tak, aby zobrazovalo kroky, které je potřeba nastavit řešení popsané výše, bez vypracování na podrobnosti o jednotlivých kroků. Proto místo kroky konfigurace grafickým uživatelským rozhraním, používá prostředí PowerShell na vás provede rychle každý krok. Tento kurz předpokládá následující:

* Už máte účet Azure s předplatným virtuálního počítače.
* Jste nainstalovali [rutin prostředí Azure PowerShell](/powershell/azure/overview).
* Už máte plnou Principy Always On skupin dostupnosti pro místní řešení. Další informace najdete v tématu [Always On skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Připojení k předplatnému Azure a vytvoření virtuální sítě
1. V okně prostředí PowerShell v místním počítači naimportujte modul Azure, stáhnout nastavení publikování do vašeho počítače a připojení relace prostředí PowerShell k předplatnému Azure importováním stažené nastavení publikování.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    **Get-AzurePublishSettingsFile** příkaz automaticky vygeneruje certifikát pro správu s Azure a stáhne do počítače. V prohlížeči se otevře automaticky a se zobrazí výzva k zadání přihlašovacích údajů účtu Microsoft pro vaše předplatné Azure. Stažené **.publishsettings** soubor obsahuje všechny informace, které potřebujete ke správě vašeho předplatného Azure. Po uložení tohoto souboru do místního adresáře, ho importovat pomocí **Import AzurePublishSettingsFile** příkaz.

   > [!NOTE]
   > Soubor .publishsettings obsahuje vaše pověření (nekódovaných), které se používají ke správě vašich předplatných Azure a služby. Nejlepším způsobem zabezpečení pro tento soubor je dočasně uložit mimo adresáře zdroje (například ve složce Libraries\Documents) a poté jej odstraňte po dokončení importu. Uživatel se zlými úmysly, který získá přístup k souboru .publishsettings můžete upravit, vytvoření a odstranění služeb Azure.

2. Definujte řady proměnných, které budete používat k vytvoření vaší cloudové infrastruktury IT.

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

    Věnujte pozornost následujícím zajistit, že vaše příkazy bude úspěšné později:

   * Proměnné **$storageAccountName** a **$dcServiceName** musí být jedinečný, protože se používá k identifikaci cloudu účet a cloudu server úložiště, v uvedeném pořadí, v síti Internet.
   * Názvy, které zadáte pro proměnné **$affinityGroupName** a **$virtualNetworkName** jsou nakonfigurované v dokumentu konfigurace virtuální sítě, které budete používat později.
   * **$sqlImageName** určuje aktualizované název image virtuálního počítače, který obsahuje SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Pro jednoduchost **Contoso! 000** je stejné heslo, které se používá napříč celou kurzu.

3. Vytvořte skupinu vztahů.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Vytvoření virtuální sítě pomocí importu konfiguračního souboru.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurační soubor obsahuje následující dokument XML. Stručně řečeno, určuje virtuální sítě s názvem **ContosoNET** ve skupině vztahů názvem **ContosoAG**. Má adresní prostor **10.10.0.0/16** a má dvě podsítě, **10.10.1.0/24** a **10.10.2.0/24**, které jsou v uvedeném pořadí front podsíť a zpět podsítě. Front podsíť je, kde můžete umístit klientské aplikace, třeba Microsoft SharePoint. Back podsíť je, kde budete umístit virtuální počítače serveru SQL. Pokud změníte **$affinityGroupName** a **$virtualNetworkName** proměnné starší, musíte změnit taky odpovídající názvy níže.

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

5. Vytvořte účet úložiště, který je spojen s skupinu vztahů, že jste vytvořili a nastavte ji jako aktuální účet úložiště v rámci vašeho předplatného.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Vytvořte server řadiče domény v nové cloudové služby a dostupnost sady.

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

    Tyto příkazy vytvoření kanálu provádět následující akce:

   * **Nové AzureVMConfig** vytvoří konfigurace virtuálního počítače.
   * **Přidat AzureProvisioningConfig** obsahuje parametry konfigurace samostatný server systému Windows.
   * **Přidat AzureDataDisk** přidá datový disk, který použijete pro ukládání dat služby Active Directory, s možností ukládání do mezipaměti, nastavena na hodnotu None.
   * **Nový-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v rámci nové cloudové služby.

7. Počkejte kompletní zřízení nového virtuálního počítače a stáhněte si soubor vzdálené plochy do pracovní adresář. Vzhledem k tomu, že nový virtuální počítač Azure trvá příliš dlouho a zajišťují, `while` smyčky nadále dotazování nový virtuální počítač, dokud je připravený k použití.

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

Nyní je úspěšně zřízený server řadiče domény. Dále nakonfigurujete domény služby Active Directory na tomto serveru řadiče domény. Ponechte okno prostředí PowerShell otevřené v místním počítači. Budete ho později používali k vytváření dva virtuální počítače SQL serveru.

## <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
1. Připojení k serveru řadiče domény spuštěním souboru vzdálené plochy. Uživatelské jméno AzureAdmin a heslo správce počítače **Contoso! 000**, který jste zadali při vytvoření nového virtuálního počítače.
2. Otevřete okno prostředí PowerShell v režimu správce.
3. Spusťte následující **DCPROMO. EXE** příkaz nastavit **corp.contoso.com** domény s datové adresáře na disku M.

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

    Po dokončení příkazu virtuální počítač se automaticky restartuje.

4. Znova se připojte k serveru řadiče domény spuštěním souboru vzdálené plochy. Tentokrát, přihlaste se jako **CORP\Administrator**.
5. Otevřete okno prostředí PowerShell v režimu správce a importujte modul Active Directory PowerShell pomocí následujícího příkazu:

        Import-Module ActiveDirectory

6. Spusťte následující příkazy pro přidání tři uživatelů k doméně.

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

    **CORP\Install** slouží ke konfiguraci všechno související s instancí služby SQL Server, převzetí služeb při selhání clusteru a skupinu dostupnosti. **CORP\SQLSvc1** a **CORP\SQLSvc2** jsou použity jako účty služby SQL Server pro dva virtuální počítače SQL serveru.
7. Potom spusťte následující příkazy umožnit **CORP\Install** oprávnění k vytváření počítačových objektů v doméně.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Identifikátor GUID uveden výše je identifikátor GUID pro objekt typu počítače. **CORP\Install** musí účet **čtení všech vlastností** a **vytvářet objekty počítačů** oprávnění k vytvoření Active přímé objekty pro převzetí služeb při selhání clusteru. **Čtení všech vlastností** oprávnění je již CORP\Install ve výchozím nastavení přiděleno, takže není nutné ji explicitně udělit. Další informace o oprávnění, které jsou potřebné k vytvoření clusteru převzetí služeb při selhání najdete v tématu [převzetí služeb při selhání clusteru podrobný průvodce: Konfigurace účty ve službě Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teď, když jste dokončili konfigurace služby Active Directory a uživatelských objektů, vytvoříte dva virtuální počítače serveru SQL a připojovat je k této doméně.

## <a name="create-the-sql-server-vms"></a>Vytvoření virtuálních počítačů serveru SQL
1. Nadále používat okno prostředí PowerShell, který je otevřený v místním počítači. Definujte následující další proměnné:

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

    IP adresa **10.10.0.4** je obvykle přiřazena první virtuální počítač, který vytvoříte v **10.10.0.0/16** podsíť virtuální sítě Azure. Ověřte, že jde o adresu serveru řadiče domény tak, že spustíte **IPCONFIG**.
2. Spusťte následující přesměruje příkazů pro vytvoření první virtuální počítač v clusteru převzetí služeb při selhání, s názvem **ContosoQuorum**:

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

    Vezměte na vědomí následující týkající se výše uvedeného příkazu:

   * **Nové AzureVMConfig** vytvoří konfigurace virtuálního počítače s názvem sadu požadovanou dostupnosti. Se stejným názvem sady dostupnosti bude vytvořen dalších virtuálních počítačů, tak, aby se připojit do stejné skupiny dostupnosti.
   * **Přidat AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač v back podsíti.
   * **Nový-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v rámci nové cloudové služby. **DnsSettings** parametr určuje, zda má server DNS pro servery v cloudové službě novou IP adresu **10.10.0.4**. Toto je IP adresa serveru řadiče domény. Tento parametr je potřebná k povolení nové virtuální počítače v rámci cloudové služby pro připojení k doméně služby Active Directory úspěšně. Tento parametr musíte ručně nastavit nastavením IPv4 v virtuálního počítače používat server řadiče domény jako primární server DNS po zřízení virtuálního počítače a potom připojí virtuální počítač k doméně služby Active Directory.
3. Spusťte následující přesměruje příkazů pro vytvoření virtuálních počítačů serveru SQL s názvem **ContosoSQL1** a **ContosoSQL2**.

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

    Vezměte na vědomí následující týkající se výše uvedené příkazy:

   * **Nové AzureVMConfig** používá stejný název sady dostupnosti jako server řadiče domény a bitovou kopii systému SQL Server 2012 Service Pack 1 Enterprise Edition v galerii virtuálních počítačů. Také nastaví disku operačního systému na čtení ukládání do mezipaměti pouze (bez ukládání do mezipaměti). Doporučujeme migraci soubory databáze do samostatné datový disk, který připojíte k virtuálnímu počítači a konfigurace bez pro čtení a ukládání do mezipaměti. Skoro je však k odebrání ukládání do mezipaměti na disku operačního systému, protože nelze odebrat, přečtěte si ukládání do mezipaměti na disku operačního systému.
   * **Přidat AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač v back podsíti.
   * **Přidat AzureEndpoint** přidá přístup koncových bodů, aby klientské aplikace přístup tyto instance služby SQL Server na Internetu. K ContosoSQL1 a ContosoSQL2 mají jiné porty.
   * **Nový-AzureVM** vytvoří nový virtuální počítač SQL Server v rámci stejné cloudové služby jako ContosoQuorum. Virtuální počítače musí být ve stejné cloudové služby, pokud chcete, aby se ve stejné sadě dostupnosti.
4. Počkejte, než pro každý virtuální počítač plně zřídit a pro každý virtuální počítač ke stažení souboru jeho vzdálené plochy do pracovního adresáře. `for` Smyčky procházení tři nové virtuální počítače a spouští příkazy uvnitř nejvyšší úrovně složené závorky pro každý z nich.

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

    Virtuální počítače serveru SQL je nyní opatřen a spuštěná, ale instalované se systémem SQL Server s výchozími možnostmi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializovat převzetí služeb při selhání clusteru virtuálních počítačů
V této části musíte upravit tři servery, které budete používat v clusteru převzetí služeb při selhání a instalace systému SQL Server. Zejména:

* Všechny servery: je potřeba nainstalovat **Clustering převzetí služeb při selhání** funkce.
* Všechny servery: je nutné přidat **CORP\Install** jako počítač **správce**.
* ContosoSQL1 a ContosoSQL2 pouze: je nutné přidat **CORP\Install** jako **sysadmin** role v výchozí databáze.
* ContosoSQL1 a ContosoSQL2 pouze: je nutné přidat **NT AUTHORITY\System** jako u přihlášení s následujícími oprávněními:

  * Příkaz ALTER žádnou skupinu dostupnosti
  * Připojení SQL
  * Zobrazení stavu serveru
* ContosoSQL1 a ContosoSQL2 pouze: **TCP** na virtuální počítač SQL Server je již povolen protokol. Však stále musíte otevřít bránu firewall pro vzdálený přístup systému SQL Server.

Teď jste připravení začít. Počínaje **ContosoQuorum**, postupujte podle následujících kroků:

1. Připojení k **ContosoQuorum** spuštěním soubory vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, který jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že počítače byl úspěšně připojen k **corp.contoso.com**.
3. Počkejte na dokončení spuštěných úloh automatizované inicializace před pokračováním instalace systému SQL Server.
4. Otevřete okno prostředí PowerShell v režimu správce.
5. Instalace funkce Clustering převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidat **CORP\Install** jako místní správce.

        net localgroup administrators "CORP\Install" /Add
7. Odhlásit z ContosoQuorum. Jste hotovi s tímto serverem teď.

        logoff.exe

V dalším kroku inicializovat **ContosoSQL1** a **ContosoSQL2**. Postupujte podle následujících kroků, které jsou stejné pro oba virtuální počítače serveru SQL.

1. Připojte k dva virtuální počítače SQL serveru spuštěním soubory vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, který jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že počítače byl úspěšně připojen k **corp.contoso.com**.
3. Počkejte na dokončení spuštěných úloh automatizované inicializace před pokračováním instalace systému SQL Server.
4. Otevřete okno prostředí PowerShell v režimu správce.
5. Instalace funkce Clustering převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidat **CORP\Install** jako místní správce.

        net localgroup administrators "CORP\Install" /Add
7. Importujte poskytovatele prostředí PowerShell serveru SQL.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Přidat **CORP\Install** jako roli správce systému pro výchozí instanci SQL serveru.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Přidat **NT AUTHORITY\System** jako Přihlaste se pomocí tří oprávnění popsané výše.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otevření brány firewall pro vzdálený přístup systému SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Odhlásit z oba virtuální počítače.

         logoff.exe

Nakonec budete připraveni ke konfiguraci skupiny dostupnosti. Zprostředkovatel SQL Server prostředí PowerShell budete používat k provádění všech činností, které na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Nakonfigurujte skupinu dostupnosti
1. Připojení k **ContosoSQL1** znovu spuštěním soubory vzdálené plochy. Místo přihlášení pomocí účtu počítače, přihlaste se pomocí **CORP\Install**.
2. Otevřete okno prostředí PowerShell v režimu správce.
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
4. Importujte poskytovatele prostředí PowerShell serveru SQL.

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
7. Stáhněte si **CreateAzureFailoverCluster.ps1** z [vytvořit Cluster převzetí služeb při selhání pro skupiny dostupnosti Always On ve virtuálním počítači Azure](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do místní pracovní adresář. Tento skript budete používat k vytváření clusteru s podporou převzetí služeb při selhání funkční. Důležité informace o clusteringu Windows převzetí služeb při selhání jak komunikuje s síť Azure, najdete v části [vysoké dostupnosti a zotavení po havárii pro SQL Server v Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Změňte pracovní adresář a vytvoření clusteru převzetí služeb při selhání pomocí staženého skriptu.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Povolte Always On skupiny dostupnosti pro výchozí instance systému SQL Server na **ContosoSQL1** a **ContosoSQL2**.

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
10. Vytvořte adresář zálohy a udělit oprávnění účtům služby SQL Server. Příprava databáze dostupnosti na sekundární repliku použijete tento adresář.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Vytvořit databázi na **ContosoSQL1** názvem **MyDB1**, provést úplnou zálohu a zálohu protokolu a obnoví je na **ContosoSQL2** s **WITH NORECOVERY**  možnost.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Vytvořte dostupnost skupiny koncových bodů na virtuálních počítačích SQL Server a nastavit oprávnění u koncových bodů.

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
14. Nakonec vytvořte skupinu dostupnosti a připojení sekundární repliky ke skupině dostupnosti.

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
Jste teď úspěšně implementovali SQL serveru Always On tak, že vytvoříte skupinu dostupnosti v Azure. Ke konfiguraci naslouchacího procesu pro tuto skupinu dostupnosti, najdete v části [nakonfigurovat modul pro naslouchání ILB pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

Další informace o používání systému SQL Server v Azure najdete v tématu [systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
