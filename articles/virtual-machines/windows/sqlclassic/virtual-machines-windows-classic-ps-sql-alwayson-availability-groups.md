---
title: Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure pomocí PowerShellu | Microsoft Docs
description: V tomto kurzu se používají prostředky, které byly vytvořeny pomocí modelu nasazení Classic. Pomocí PowerShellu vytvoříte skupinu dostupnosti Always On v Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978142"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurace skupiny dostupnosti Always On na virtuálním počítači Azure pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Klasický: uživatelské rozhraní](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasický: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Než začnete, zvažte, že teď můžete tuto úlohu dokončit v modelu Azure Resource Manager. Pro nová nasazení doporučujeme model Azure Resource Manager. Viz [SQL Server skupiny dostupnosti Always On na virtuálních počítačích Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Doporučujeme, aby většina nových nasazení používala model Správce prostředků. Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../../azure-resource-manager/management/deployment-models.md). Tento článek se věnuje použití klasického modelu nasazení.

Virtuální počítače Azure můžou správcům databází pomáhat snížit náklady na SQL Server systém s vysokou dostupností. V tomto kurzu se dozvíte, jak implementovat skupinu dostupnosti pomocí SQL Server v rámci prostředí Azure vždy na konci. Na konci tohoto kurzu se SQL Server řešení Always On v Azure skládá z následujících prvků:

* Virtuální síť, která obsahuje více podsítí, včetně front-endu a back-endové podsítě.
* Řadič domény s doménou služby Active Directory.
* Dva SQL Server virtuální počítače, které jsou nasazeny do back-endové podsítě a připojeny k doméně služby Active Directory.
* Cluster s podporou převzetí služeb při selhání s Windows se třemi uzly s modelem kvora s většinou uzlů.
* Skupina dostupnosti se dvěma replikami synchronního potvrzování databáze dostupnosti.

Tento scénář je vhodný pro zjednodušení v Azure, ne pro své nákladové efektivity nebo jiné faktory. Můžete například minimalizovat počet virtuálních počítačů pro skupinu dostupnosti se dvěma replikami, které se uloží na výpočetní dobu v Azure, a to pomocí řadiče domény, jako je určující sdílená složka kvora v clusteru s podporou převzetí služeb při selhání se dvěma uzly. Tato metoda snižuje počet virtuálních počítačů o jednu z výše uvedených konfigurací.

V tomto kurzu se dozvíte, jaké kroky potřebujete k nastavení výše uvedeného řešení, aniž byste museli vykazovat podrobnosti o jednotlivých krocích. Proto namísto poskytování kroků konfigurace grafického uživatelského rozhraní pomocí skriptování PowerShellu rychle provedete jednotlivé kroky. V tomto kurzu se předpokládá následující:

* Už máte účet Azure s předplatným virtuálního počítače.
* Nainstalovali jste [rutiny Azure PowerShell](/powershell/azure/overview).
* Už máte plnou znalostí skupin dostupnosti Always On pro místní řešení. Další informace najdete v tématu [skupiny dostupnosti Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Připojte se k předplatnému Azure a vytvořte virtuální síť.
1. V okně PowerShellu v místním počítači Importujte modul Azure, Stáhněte si soubor nastavení publikování na váš počítač a připojte relaci PowerShellu k vašemu předplatnému Azure importem staženého nastavení publikování.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Příkaz **Get-AzurePublishSettingsFile** automaticky vygeneruje certifikát pro správu pomocí Azure a stáhne ho do vašeho počítače. Automaticky se otevře prohlížeč a zobrazí se výzva k zadání přihlašovacích údajů účet Microsoft pro vaše předplatné Azure. Stažený soubor **. publishsettings** obsahuje všechny informace, které potřebujete ke správě předplatného Azure. Po uložení tohoto souboru do místního adresáře ho naimportujte pomocí příkazu **Import-AzurePublishSettingsFile** .

   > [!NOTE]
   > Soubor. publishsettings obsahuje vaše přihlašovací údaje (nekódované), které se používají ke správě předplatných a služeb Azure. Osvědčeným postupem zabezpečení pro tento soubor je uložit ho dočasně mimo vaše zdrojové adresáře (například ve složce Libraries\Documents) a po dokončení importu ho odstranit. Uživatel se zlými úmysly, který získá přístup k souboru. publishsettings, může upravit, vytvořit a odstranit vaše služby Azure.

2. Definujte řadu proměnných, které použijete k vytvoření vaší infrastruktury IT v cloudu.

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

    Věnujte pozornost následujícím akcím, abyste zajistili, že příkazy budou později úspěšné:

   * Proměnné **$storageAccountName** a **$dcServiceName** musí být jedinečné, protože se používají k identifikaci účtu cloudového úložiště a cloudového serveru v Internetu.
   * Názvy, které zadáte pro proměnné **$affinityGroupName** a **$virtualNetworkName** , se konfigurují v konfiguračním dokumentu virtuální sítě, který použijete později.
   * **$sqlImageName** určuje aktualizovaný název bitové kopie virtuálního počítače, která obsahuje SQL Server 2012 Service Pack 1 Enterprise Edition.
   * Pro jednoduchost je **Contoso! 000** stejné heslo, které se používá v celém kurzu.

3. Vytvořte skupinu vztahů.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Vytvořte virtuální síť importováním konfiguračního souboru.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurační soubor obsahuje následující dokument XML. V krátkém případě určuje virtuální síť s názvem **ContosoNET** ve skupině vztahů s názvem **ContosoAG**. Má adresní prostor **10.10.0.0/16** a má dvě podsítě, **10.10.1.0/24** a **10.10.2.0/24**, což jsou front-Subnet a back-Subnet v uvedeném pořadí. Front-Subnet je místo, kde můžete umístit klientské aplikace, jako je Microsoft SharePoint. Zpětná podsíť je místo, kam umístíte SQL Server virtuální počítače. Změníte-li **$affinityGroupName** a **$virtualNetworkName** proměnných dříve, je nutné také změnit odpovídající názvy níže.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

5. Vytvořte účet úložiště, který je přidružený ke skupině vztahů, kterou jste vytvořili, a nastavte ho jako aktuální účet úložiště v předplatném.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Vytvořte Server řadiče domény v nové cloudové službě a skupině dostupnosti.

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

    Tyto příkazy v kanálu dělají následující věci:

   * **New-AzureVMConfig** vytvoří konfiguraci virtuálního počítače.
   * **Add-AzureProvisioningConfig** poskytuje konfigurační parametry samostatného Windows serveru.
   * **Add-AzureDataDisk** přidá datový disk, který budete používat k ukládání dat služby Active Directory, s možností ukládání do mezipaměti nastavenou na žádné.
   * **New-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v nové cloudové službě.

7. Počkejte na úplné zřízení nového virtuálního počítače a Stáhněte soubor vzdálené plochy do pracovního adresáře. Vzhledem k tomu, že nový virtuální počítač Azure trvá zřízení delší dobu, `while` cyklus bude pokračovat v dotazování nového virtuálního počítače, dokud nebude připravený k použití.

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

Server řadiče domény se teď úspěšně zřídil. Dále nakonfigurujete doménu služby Active Directory na tomto serveru řadiče domény. Nechte okno PowerShellu otevřené na místním počítači. Později ji budete používat k vytvoření těchto dvou SQL Server virtuálních počítačů.

## <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
1. Připojte se k serveru řadiče domény spuštěním souboru vzdálené plochy. Použijte uživatelské jméno pro správce počítače AzureAdmin a heslo **Contoso! 000**, které jste zadali při vytváření nového virtuálního počítače.
2. Otevřete okno Powershellu v režimu správce.
3. Spusťte následující příkaz **dcpromo. Příkaz EXE** pro nastavení domény **Corp.contoso.com** s datovými adresáři na jednotce M.

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

    Po dokončení příkazu se virtuální počítač automaticky restartuje.

4. Znovu se připojte k serveru řadiče domény spuštěním souboru vzdálené plochy. Tentokrát se přihlaste jako **CORP\Administrator**.
5. Otevřete okno PowerShellu v režimu správce a importujte modul PowerShell služby Active Directory pomocí následujícího příkazu:

        Import-Module ActiveDirectory

6. Spuštěním následujících příkazů přidejte do domény tři uživatele.

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

    **CORP\Install** se používá ke konfiguraci všeho souvisejícího s instancemi služby SQL Server, clusterem s podporou převzetí služeb při selhání a skupinou dostupnosti. **CORP\SQLSvc1** a **CORP\SQLSvc2** se používají jako účty služby SQL Server pro dva virtuální počítače SQL Server.
7. Dále spusťte následující příkazy, které **CORP\Install** oprávnění k vytváření počítačových objektů v doméně.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Výše uvedený identifikátor GUID je identifikátor GUID typu objektu počítače. Účet **CORP\Install** potřebuje oprávnění **číst všechny vlastnosti** a **vytvořit objekty počítače** k vytvoření aktivních přímých objektů pro cluster s podporou převzetí služeb při selhání. Oprávnění **číst všechny vlastnosti** již ve výchozím nastavení CORP\Install, takže je nemusíte explicitně udělovat. Další informace o oprávněních potřebných k vytvoření clusteru s podporou převzetí služeb při selhání najdete v tématu [podrobný průvodce clusterem s podporou převzetí služeb při selhání: Konfigurace účtů ve službě Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teď, když jste dokončili konfiguraci služby Active Directory a uživatelských objektů, vytvoříte dva SQL Server virtuální počítače a připojíte je k této doméně.

## <a name="create-the-sql-server-vms"></a>Vytvoření virtuálních počítačů s SQL Server
1. Pokračujte v používání okna PowerShellu otevřeného na místním počítači. Definujte následující další proměnné:

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

    IP adresa **10.10.0.4** se obvykle přiřazuje PRVNÍmu virtuálnímu počítači, který vytvoříte v podsíti **10.10.0.0/16** vaší virtuální sítě Azure. Ověřte, zda se jedná o adresu serveru řadiče domény spuštěním **příkazu ipconfig**.
2. Spusťte následující příkazy v kanálu k vytvoření prvního virtuálního počítače v clusteru s podporou převzetí služeb při selhání s názvem **ContosoQuorum**:

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

    Všimněte si následujícího příkazu, který se týká výše uvedeného příkazu:

   * **New-AzureVMConfig** vytvoří konfiguraci virtuálního počítače s požadovaným názvem skupiny dostupnosti. Následující virtuální počítače se vytvoří se stejným názvem sady dostupnosti, aby byly připojené ke stejné skupině dostupnosti.
   * **Add-AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač do back-Subnet.
   * **New-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v nové cloudové službě. Parametr **DnsSettings** určuje, že server DNS pro servery v nové cloudové službě má IP adresu **10.10.0.4**. Toto je IP adresa serveru řadiče domény. Tento parametr je potřeba k tomu, aby se nové virtuální počítače v cloudové službě daly úspěšně připojit k doméně služby Active Directory. Bez tohoto parametru musíte ručně nastavit nastavení IPv4 ve vašem VIRTUÁLNÍm počítači tak, aby po zřízení virtuálního počítače používalo Server řadiče domény jako primární server DNS, a pak připojte virtuální počítač k doméně služby Active Directory.
3. Spusťte následující příkazy v kanálu k vytvoření SQL Server virtuálních počítačů s názvem **ContosoSQL1** a **ContosoSQL2**.

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

    Všimněte si následujících příkazů, které se týkají výše uvedených příkazů:

   * **Příkaz New-AzureVMConfig** používá stejný název sady dostupnosti jako server řadiče domény a používá image SQL Server 2012 Service Pack 1 Enterprise Edition v galerii virtuálních počítačů. Také nastaví disk s operačním systémem jenom na ukládání do mezipaměti pro zápis (bez ukládání do mezipaměti). Doporučujeme migrovat soubory databáze na samostatný datový disk, který připojíte k virtuálnímu počítači, a nakonfigurovat jej bez ukládání do mezipaměti pro čtení nebo zápis. Další nejlepší věc je ale odebrat ukládání do mezipaměti pro zápis na disk s operačním systémem, protože na disku s operačním systémem nemůžete odebrat ukládání do mezipaměti pro čtení.
   * **Add-AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač do back-Subnet.
   * **Add-AzureEndpoint** přidá koncové body přístupu, aby klientské aplikace mohly přistupovat k těmto instancím služby SQL Server Services na internetu. K ContosoSQL1 a ContosoSQL2 jsou přidány různé porty.
   * **New-AzureVM** vytvoří nový virtuální počítač SQL Server ve stejné cloudové službě jako ContosoQuorum. Virtuální počítače musíte umístit do stejné cloudové služby, pokud chcete, aby byly ve stejné skupině dostupnosti.
4. Počkejte, až se všechny virtuální počítače úplně zřídí, a pro každý z nich Stáhněte svůj soubor vzdálené plochy do pracovního adresáře. Cyklická smyčka `for` projde třemi novými virtuálními počítači a spustí příkazy uvnitř složených závorek nejvyšší úrovně pro každé z nich.

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

    Virtuální počítače s SQL Server jsou nyní zřízené a spuštěné, ale jsou nainstalovány s SQL Server s výchozími možnostmi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializace virtuálních počítačů clusteru s podporou převzetí služeb při selhání
V této části je potřeba upravit tři servery, které budete používat v clusteru s podporou převzetí služeb při selhání a instalaci SQL Server. Zejména:

* Všechny servery: je potřeba nainstalovat funkci **clusteringu s podporou převzetí služeb při selhání** .
* Všechny servery: musíte přidat **CORP\Install** jako **správce**počítače.
* Pouze ContosoSQL1 a ContosoSQL2: je třeba přidat **CORP\Install** jako roli **sysadmin** do výchozí databáze.
* Jenom ContosoSQL1 a ContosoSQL2: musíte přidat **NT AUTHORITY\SYSTEM** jako přihlašování s následujícími oprávněními:

  * Změna jakékoli skupiny dostupnosti
  * Připojit SQL
  * Zobrazit stav serveru
* Pouze ContosoSQL1 a ContosoSQL2: protokol **TCP** je již na SQL SERVERm virtuálním počítači povolen. Pořád ale musíte otevřít bránu firewall pro vzdálený přístup k SQL Server.

Teď jste připraveni začít. Od **ContosoQuorum**použijte následující postup:

1. Připojte se k **ContosoQuorum** spuštěním souborů vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že se počítače úspěšně připojily k **Corp.contoso.com**.
3. Než budete pokračovat, počkejte na dokončení instalace SQL Server, aby se spustily úlohy automatizované inicializace.
4. Otevřete okno Powershellu v režimu správce.
5. Nainstalujte funkci Clustering s podporou převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidejte **CORP\Install** jako místního správce.

        net localgroup administrators "CORP\Install" /Add
7. Odhlaste se z ContosoQuorum. Nyní jste hotovi s tímto serverem.

        logoff.exe

Dále inicializujte **ContosoSQL1** a **ContosoSQL2**. Postupujte podle následujících kroků, které jsou u obou SQL Server virtuálních počítačů stejné.

1. Připojte se k těmto dvěma virtuálním počítačům SQL Server spuštěním souborů vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso! 000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, že se počítače úspěšně připojily k **Corp.contoso.com**.
3. Než budete pokračovat, počkejte na dokončení instalace SQL Server, aby se spustily úlohy automatizované inicializace.
4. Otevřete okno Powershellu v režimu správce.
5. Nainstalujte funkci Clustering s podporou převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidejte **CORP\Install** jako místního správce.

        net localgroup administrators "CORP\Install" /Add
7. Importujte zprostředkovatele SQL Server PowerShellu.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Přidejte **CORP\Install** jako roli sysadmin pro výchozí instanci SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Přidejte **NT AUTHORITY\SYSTEM** jako přihlašování se třemi výše uvedenými oprávněními.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otevřete bránu firewall pro vzdálený přístup k SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Odhlaste se z obou virtuálních počítačů.

         logoff.exe

Nakonec budete připraveni ke konfiguraci skupiny dostupnosti. Pomocí zprostředkovatele SQL Server PowerShellu provedete veškerou práci na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurace skupiny dostupnosti
1. Znovu se připojte k **ContosoSQL1** spuštěním souborů vzdálené plochy. Místo přihlášení pomocí účtu počítače se přihlaste pomocí **CORP\Install**.
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
4. Importujte zprostředkovatele SQL Server PowerShellu.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Změna účtu služby SQL Server pro ContosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Změna účtu služby SQL Server pro ContosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Stáhněte si **CreateAzureFailoverCluster. ps1** z [vytváření clusteru s podporou převzetí služeb při selhání pro skupiny dostupnosti Always On na virtuálním počítači Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do místního pracovního adresáře. Pomocí tohoto skriptu vám pomůžete vytvořit funkční cluster s podporou převzetí služeb při selhání. Důležité informace o tom, jak Windows Clustering s podporou převzetí služeb při selhání spolupracuje se sítí Azure, najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Přejděte do pracovního adresáře a vytvořte cluster s podporou převzetí služeb při selhání se staženým skriptem.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Povolte skupiny dostupnosti Always On pro výchozí instance SQL Server v **ContosoSQL1** a **ContosoSQL2**.

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
10. Vytvořte záložní adresář a udělte oprávnění účtům služby SQL Server. Tento adresář použijete k přípravě databáze dostupnosti v sekundární replice.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Vytvořte databázi v **ContosoSQL1** s názvem **MyDB1**, proveďte úplnou zálohu i zálohu protokolu a obnovte je v **ContosoSQL2** s možností **WITH NORECOVERY** .

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Vytvořte koncové body skupiny dostupnosti na virtuálních počítačích SQL Server a nastavte správná oprávnění pro koncové body.

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
13. Vytvořte repliky dostupnosti.

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
14. Nakonec vytvořte skupinu dostupnosti a připojte se k ní sekundární replika do skupiny dostupnosti.

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

## <a name="next-steps"></a>Další kroky
Nyní jste úspěšně nasadili SQL Server vždy k vytvoření skupiny dostupnosti v Azure. Pokud chcete pro tuto skupinu dostupnosti nakonfigurovat naslouchací proces, přečtěte si téma [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti Always On v Azure](../classic/ps-sql-int-listener.md).

Další informace o používání SQL Server v Azure najdete v článku [SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
