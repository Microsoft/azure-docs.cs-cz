---
title: Konfigurace skupiny dostupnosti vždy zapnuté na virtuálním počítači Azure pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Tento kurz používá prostředky, které byly vytvořeny s klasickým modelem nasazení. Pomocí PowerShellu můžete vytvořit skupinu dostupnosti always on v Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978142"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Konfigurace skupiny dostupnosti vždy zapnuté na virtuálním počítači Azure pomocí PowerShellu
> [!div class="op_single_selector"]
> * [Klasika: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasika: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Než začnete, zvažte, že teď můžete dokončit tento úkol v modelu Správce prostředků Azure. Doporučujeme model Azure správce prostředků pro nová nasazení. Viz [SQL Server Always On skupiny dostupnosti na virtuálních počítačích Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Doporučujeme, aby většina nových nasazení používala model Správce prostředků. Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasické](../../../azure-resource-manager/management/deployment-models.md). Tento článek se věnuje použití klasického modelu nasazení.

Virtuální počítače Azure (VM) můžou správcům databází pomoci snížit náklady na vysoce dostupnost systému SQL Serveru. Tento kurz ukazuje, jak implementovat skupinu dostupnosti pomocí SQL Server always On end v prostředí Azure. Na konci kurzu se vaše řešení SQL Server Always On v Azure bude skládat z následujících prvků:

* Virtuální síť, která obsahuje více podsítí, včetně front-endu a back-endové podsítě.
* Řadič domény s doménou služby Active Directory.
* Dva virtuální servery SQL Server, které jsou nasazeny do podsítě back-end a připojeny k doméně služby Active Directory.
* Cluster s podporou převzetí služeb při selhání systému Windows se třemi uznami s kvorem uvětšiny uzlu.
* Skupina dostupnosti se dvěma replikami synchronního potvrzení databáze dostupnosti.

Tento scénář je dobrou volbou pro jeho jednoduchost v Azure, nikoli pro jeho efektivitu nákladů nebo jiné faktory. Můžete například minimalizovat počet virtuálních počítačů pro skupinu dostupnosti se dvěma replikami, abyste ušetřili výpočetní hodiny v Azure, a to pomocí řadiče domény jako důkazního svědka sdílené složky kvora v clusteru s podporou převzetí služeb při selhání se dvěma uzny. Tato metoda snižuje počet virtuálních počítačů o jeden z výše uvedené konfigurace.

Tento kurz je určen k zobrazení kroků, které jsou nutné k nastavení popsaného řešení výše, aniž byste podrobně popsali podrobnosti každého kroku. Proto místo poskytování kroků konfigurace grafického uživatelského rozhraní používá skriptování prostředí PowerShell, které vás rychle provede každým krokem. Tento kurz předpokládá následující:

* Už máte účet Azure s předplatným virtuálního počítače.
* Nainstalovali jste [rutiny Prostředí Azure PowerShell](/powershell/azure/overview).
* Už máte solidní znalosti o vždy na dostupnost skupiny pro místní řešení. Další informace naleznete [v tématu Vždy na skupiny dostupnosti (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Připojení k předplatnému Azure a vytvoření virtuální sítě
1. V okně PowerShellu v místním počítači importujte modul Azure, stáhněte soubor nastavení publikování do počítače a připojte relaci PowerShellu k předplatnému Azure importováním stažených nastavení publikování.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    Příkaz **Get-AzurePublishSettingsFile** automaticky vygeneruje certifikát správy s Azure a stáhne ho do vašeho počítače. Automaticky se otevře prohlížeč a budete vyzváni k zadání přihlašovacích údajů účtu Microsoft pro vaše předplatné Azure. Stažený soubor **.publishsettings** obsahuje všechny informace, které potřebujete ke správě předplatného Azure. Po uložení tohoto souboru do místního adresáře jej importujte pomocí příkazu **Import-AzurePublishSettingsFile.**

   > [!NOTE]
   > Soubor .publishsettings obsahuje vaše přihlašovací údaje (nekódované), které se používají ke správě předplatných a služeb Azure. Osvědčeným postupem zabezpečení pro tento soubor je dočasně jej uložit mimo zdrojové adresáře (například ve složce Knihovny\Dokumenty) a po dokončení importu jej odstranit. Uživatel se zlými úmysly, který získá přístup k souboru .publishsettings, může upravovat, vytvářet a odstraňovat vaše služby Azure.

2. Definujte řadu proměnných, které použijete k vytvoření cloudové IT infrastruktury.

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

    Věnujte pozornost následujícím uům, abyste zajistili, že vaše příkazy budou později úspěšné:

   * Proměnné **$storageAccountName** a **$dcServiceName** musí být jedinečné, protože se používají k identifikaci vašeho účtu cloudového úložiště a cloudového serveru na Internetu.
   * Názvy, které zadáte pro proměnné **$affinityGroupName** a **$virtualNetworkName,** jsou nakonfigurovány v konfiguračním dokumentu virtuální sítě, který budete později používat.
   * **$sqlImageName** určuje aktualizovaný název bitové kopie virtuálního počítače, která obsahuje sql server 2012 Service Pack 1 Enterprise Edition.
   * Pro jednoduchost **contoso!000** je stejné heslo, které se používá v celém kurzu.

3. Vytvořte skupinu spřažení.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Importem konfiguračního souboru vytvořte virtuální síť.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    Konfigurační soubor obsahuje následující dokument XML. Stručně řečeno, určuje virtuální síť s názvem **ContosoNET** ve skupině spřažení s názvem **ContosoAG**. Má adresní prostor **10.10.0.0/16** a má dvě podsítě, **10.10.1.0/24** a **10.10.2.0/24**, což jsou přední podsíť a zadní podsíť. Přední podsíť je místo, kam můžete umístit klientské aplikace, jako je například Microsoft SharePoint. Zadní podsíť je místo, kam umístíte virtuální servery SQL Server. Pokud změníte **$affinityGroupName** a **$virtualNetworkName** proměnné dříve, musíte také změnit odpovídající názvy níže.

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

5. Vytvořte účet úložiště, který je přidružený ke skupině spřažení, kterou jste vytvořili, a nastavte ho jako aktuální účet úložiště ve vašem předplatném.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Vytvořte server řadiče domény v nové cloudové službě a sadě dostupnosti.

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

    Tyto příkazy s potrubím dělají následující věci:

   * **New-AzureVMConfig** vytvoří konfiguraci virtuálního počítače.
   * **Funkce Add-AzureProvisioningConfig** poskytuje parametry konfigurace samostatného serveru Windows.
   * **Add-AzureDataDisk** přidá datový disk, který budete používat pro ukládání dat služby Active Directory, s možností ukládání do mezipaměti nastavenou na hodnotu Žádný.
   * **New-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v nové cloudové službě.

7. Počkejte, až se nový virtuální počítač plně zřídí, a stáhněte si soubor vzdálené plochy do pracovního adresáře. Vzhledem k tomu, že nové virtuální `while` počítač Azure trvá dlouhou dobu zřízení, smyčky pokračuje dotazování nového virtuálního počítače, dokud je připraven k použití.

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

Server řadiče domény je nyní úspěšně zřízen. Dále nakonfigurujete doménu služby Active Directory na tomto serveru řadiče domény. Ponechte okno Prostředí PowerShell otevřené v místním počítači. Budete ji znovu použít později k vytvoření dvou virtuálních připojení SQL Server.

## <a name="configure-the-domain-controller"></a>Konfigurace řadiče domény
1. Připojte se k serveru řadiče domény spuštěním souboru vzdálené plochy. Použijte uživatelské jméno správce počítače AzureAdmin a heslo **Contoso!000**, které jste zadali při vytváření nového virtuálního počítače.
2. Otevřete okno PowerShellu v režimu správce.
3. Spusťte následující **dcpromo. EXE** příkaz pro nastavení **domény corp.contoso.com** s datovými adresáři na jednotce M.

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
5. Otevřete okno Prostředí PowerShell v režimu správce a importujte modul Prostředí Active Directory PowerShell pomocí následujícího příkazu:

        Import-Module ActiveDirectory

6. Spusťte následující příkazy a přidejte do domény tři uživatele.

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

    **Corp\Install** slouží ke konfiguraci všeho, co souvisí s instancemi služby SQL Server, clusterem s podporou převzetí služeb při selhání a skupinou dostupnosti. **CORP\SQLSvc1** a **CORP\SQLSvc2** se používají jako účty služeb serveru SQL Server pro dva virtuální servery SQL Server.
7. Dále spusťte následující příkazy a **udělte corp\Install** oprávnění k vytváření objektů počítače v doméně.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    Výše uvedený identifikátor GUID je identifikátor GUID pro typ objektu počítače. **Corp\Install** účet potřebuje **číst všechny vlastnosti** a **vytvořit objekty počítače** oprávnění k vytvoření active direct objekty pro cluster s podporou převzetí služeb při selhání. Oprávnění **Číst všechny vlastnosti** je již uděleno corp\install ve výchozím nastavení, takže není nutné udělit explicitně. Další informace o oprávněních potřebných k vytvoření clusteru s podporou převzetí služeb při selhání naleznete v tématu Průvodce krok [za krokem převzetí mše s podporou převzetí služeb při selhání: Konfigurace účtů ve službě Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Teď, když jste dokončili konfiguraci služby Active Directory a uživatelských objektů, vytvoříte dva virtuální servery SQL Server a připojíte je k této doméně.

## <a name="create-the-sql-server-vms"></a>Vytvoření virtuálních měn SQL Server
1. Pokračujte v používání okna Prostředí PowerShell, které je otevřené v místním počítači. Definujte následující další proměnné:

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

    IP adresa **10.10.0.4** se obvykle přiřazuje k prvnímu virtuálnímu počítači, který vytvoříte v podsíti **10.10.0.0/16** virtuální sítě Azure. Měli byste ověřit, zda se jedná o adresu serveru řadiče domény, spuštěním protokolu **IPCONFIG**.
2. Spusťte následující příkazy s potrubím a vytvořte první virtuální virtuální ms v clusteru s podporou převzetí služeb při selhání s názvem **ContosoQuorum**:

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

    Všimněte si následujícího příkazu výše:

   * **New-AzureVMConfig** vytvoří konfiguraci virtuálního počítače s požadovaným názvem sady dostupnosti. Následné virtuální chody se vytvoří se stejným názvem skupiny dostupnosti tak, aby byly připojeny ke stejné sadě dostupnosti.
   * **Add-AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač do zadní podsítě.
   * **New-AzureVM** vytvoří novou cloudovou službu a vytvoří nový virtuální počítač Azure v nové cloudové službě. Parametr **DnsSettings** určuje, že server DNS pro servery v nové cloudové službě má adresu IP **10.10.0.4**. Jedná se o adresu IP serveru řadiče domény. Tento parametr je potřeba k povolení nové virtuální ch odpyka v cloudové službě úspěšně připojit k doméně služby Active Directory. Bez tohoto parametru je nutné ručně nastavit nastavení IPv4 ve vašem virtuálním počítači, abyste po zřízení virtuálního počítače používali server řadiče domény jako primární server DNS, a pak připojit virtuální počítač k doméně služby Active Directory.
3. Spusťte následující příkazy s potrubím a vytvořte virtuální servery SQL Server s názvem **ContosoSQL1** a **ContosoSQL2**.

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

    Všimněte si následující, pokud jde o výše uvedené příkazy:

   * **New-AzureVMConfig** používá stejný název sady dostupnosti jako server řadiče domény a používá bitovou kopii SQL Server 2012 Service Pack 1 Enterprise Edition v galerii virtuálních počítačů. Nastaví také disk operačního systému pouze na ukládání do mezipaměti (bez ukládání do mezipaměti zápisu). Doporučujeme migrovat databázové soubory na samostatný datový disk, který připojíte k virtuálnímu počítače, a nakonfigurovat ho bez ukládání do mezipaměti pro čtení nebo zápis. Další nejlepší věcí je však odstranit ukládání zápisu do mezipaměti na disku operačního systému, protože nelze odebrat ukládání do mezipaměti čtení na disku operačního systému.
   * **Add-AzureProvisioningConfig** připojí virtuální počítač k doméně služby Active Directory, kterou jste vytvořili.
   * **Set-AzureSubnet** umístí virtuální počítač do zadní podsítě.
   * **Add-AzureEndpoint** přidá koncové body přístupu, aby klientské aplikace měly přístup k těmto instancím služeb SQL Server na Internetu. Různé porty jsou dány ContosoSQL1 a ContosoSQL2.
   * **New-AzureVM** vytvoří nový virtuální počítač SQL Server ve stejné cloudové službě jako ContosoQuorum. Virtuální aplikace je nutné umístit do stejné cloudové služby, pokud chcete, aby byly ve stejné sadě dostupnosti.
4. Počkejte, až se každý virtuální počítač plně zřídí, a pro každý virtuální počítač stáhne soubor vzdálené plochy do vašeho pracovního adresáře. Smyčka `for` cyklicky prochází tři nové virtuální hry a provede příkazy uvnitř horní úrovně kudrnaté závorky pro každý z nich.

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

    Virtuální servery SQL Server jsou teď zřízené a spuštěné, ale jsou nainstalované s SQL Serverem s výchozími možnostmi.

## <a name="initialize-the-failover-cluster-vms"></a>Inicializovat virtuální hodclusterového clusteru s podporou převzetí služeb při selhání
V této části je třeba upravit tři servery, které budete používat v clusteru s podporou převzetí služeb při selhání a instalaci serveru SQL Server. Konkrétně:

* Všechny servery: Je třeba nainstalovat funkci **Clustering s podporou převzetí služeb při selhání.**
* Všechny servery: Je třeba přidat **corp\install** jako **správce**počítače .
* Pouze ContosoSQL1 a ContosoSQL2: Ve výchozí databázi je třeba přidat **roli CORP\Install** jako roli **sysadmin.**
* Pouze contosoSQL1 a ContosoSQL2: Je třeba přidat **NT AUTHORITY\System** jako přihlášení s následujícími oprávněními:

  * Změnit libovolnou skupinu dostupnosti
  * Připojení SQL
  * Zobrazit stav serveru
* Pouze ContosoSQL1 a ContosoSQL2: Protokol **TCP** je již povolen na virtuálním počítači SQL Server. Stále však musíte otevřít bránu firewall pro vzdálený přístup k serveru SQL Server.

Nyní jste připraveni začít. Počínaje **contosoquorum**, postupujte podle následujících kroků:

1. Připojte se ke **službě ContosoQuorum** spuštěním souborů vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso!000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, zda byly počítače úspěšně připojeny k **corp.contoso.com**.
3. Před pokračováním počkejte na dokončení instalace serveru SQL Server spuštěním úloh automatické inicializace.
4. Otevřete okno PowerShellu v režimu správce.
5. Nainstalujte funkci Clustering s podporou převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidejte **corp\nainstalovat** jako místního správce.

        net localgroup administrators "CORP\Install" /Add
7. Odhlaste se z ContosoQuorum. S tímto serverem jste nyní skončili.

        logoff.exe

Dále inicializovat **ContosoSQL1** a **ContosoSQL2**. Postupujte podle následujících kroků, které jsou identické pro oba virtuální servery SQL Server.

1. Připojte se ke dvěma virtuálním počítačům SQL Server spuštěním souborů vzdálené plochy. Použijte uživatelské jméno správce počítače **AzureAdmin** a heslo **Contoso!000**, které jste zadali při vytváření virtuálních počítačů.
2. Ověřte, zda byly počítače úspěšně připojeny k **corp.contoso.com**.
3. Před pokračováním počkejte na dokončení instalace serveru SQL Server spuštěním úloh automatické inicializace.
4. Otevřete okno PowerShellu v režimu správce.
5. Nainstalujte funkci Clustering s podporou převzetí služeb při selhání systému Windows.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Přidejte **corp\nainstalovat** jako místního správce.

        net localgroup administrators "CORP\Install" /Add
7. Importujte zprostředkovatele prostředí POWERShell serveru SQL Server.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Přidejte **corp\nainstalovat** jako roli sysadmin pro výchozí instanci serveru SQL Server.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Přidejte **NT AUTHORITY\System** jako přihlášení se třemi výše popsanými oprávněními.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Otevřete bránu firewall pro vzdálený přístup k serveru SQL Server.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Odhlásit se z obou virtuálních discích.

         logoff.exe

Nakonec jste připraveni nakonfigurovat skupinu dostupnosti. Budete používat SQL Server PowerShell Provider k provedení všech prací na **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>Konfigurace skupiny dostupnosti
1. Připojte se ke **službě ContosoSQL1** opětovným spuštěním souborů vzdálené plochy. Místo přihlášení pomocí účtu počítače se přihlaste pomocí **služby CORP\Install**.
2. Otevřete okno PowerShellu v režimu správce.
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
4. Importujte zprostředkovatele prostředí POWERShell serveru SQL Server.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Změňte účet služby SERVERU SQL Server pro ContosoSQL1 na CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Změňte účet služby SERVERU SQL Server pro ContosoSQL2 na CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Stáhnout **CreateAzureFailoverCluster.ps1** z [vytvořit cluster s podporou převzetí služeb při selhání pro vždy na dostupnost skupiny v virtuálním počítači Azure](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) do místního pracovního adresáře. Tento skript vám pomůže vytvořit funkční cluster s podporou převzetí služeb při selhání. Důležité informace o interakci clusterů s podporou převzetí služeb při selhání windows se sítí Azure najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server ve virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Přecházejte do pracovního adresáře a vytvořte cluster s podporou převzetí služeb při selhání se staženým skriptem.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Povolit skupiny dostupnosti Always On pro výchozí instance serveru SQL Server na **contosoSQL1** a **ContosoSQL2**.

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
10. Vytvořte záložní adresář a udělte oprávnění pro účty služby SQL Server. Tento adresář použijete k přípravě databáze dostupnosti na sekundární replice.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Vytvořte databázi na **ContosoSQL1** s názvem **MyDB1**, vezměte úplnou zálohu i zálohu protokolu a obnovte je na **ContosoSQL2** pomocí **možnosti WITH NORECOVERY.**

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
14. Nakonec vytvořte skupinu dostupnosti a připojte sekundární repliku ke skupině dostupnosti.

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
Nyní jste úspěšně implementovali SQL Server always on vytvořením skupiny dostupnosti v Azure. Pokud chcete nakonfigurovat naslouchací proces pro tuto skupinu dostupnosti, přečtěte si téma [Konfigurace naslouchací proces ILB pro skupiny dostupnosti always on v Azure](../classic/ps-sql-int-listener.md).

Další informace o používání SQL Serveru v Azure najdete v tématu [SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
