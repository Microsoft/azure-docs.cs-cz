---
title: Horizontální navýšení kapacity typu uzlu Azure Service Fabric
description: Naučte se škálovat Cluster Service Fabric přidáním sady škálování virtuálního počítače.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: a8a8a432d4a11427f2c4f545a0907540af3112bc
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056478"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type-by-adding-a-virtual-machine-scale-set"></a>Navýšení kapacity Service Fabric typ primárního uzlu clusteru přidáním sady škálování virtuálního počítače
Tento článek popisuje, jak škálovat typ primárního uzlu clusteru Service Fabric pomocí zvýšení prostředků virtuálního počítače. Cluster Service Fabric je sada virtuálních nebo fyzických počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Počítač nebo virtuální počítač, který je součástí clusteru, se nazývá uzel. Sady škálování virtuálních počítačů jsou výpočetním prostředkem Azure, který můžete použít k nasazení a správě kolekce virtuálních počítačů jako sady. Každý typ uzlu, který je definovaný v clusteru Azure, je [nastavený jako samostatná sada škálování](service-fabric-cluster-nodetypes.md). Každý typ uzlu se pak dá spravovat samostatně. Po vytvoření clusteru Service Fabric můžete škálovat typ uzlu clusteru vertikálně (změnit prostředky uzlů) nebo upgradovat operační systém typu virtuálních počítačů typu uzel.  Cluster můžete škálovat kdykoli, a to i v případě, že úlohy běží v clusteru.  I když se cluster škáluje, vaše aplikace se automaticky škálují.

> [!WARNING]
> Pokud stav clusteru není v pořádku, nepokoušejte se nepokusit o typ primárního uzlu postup horizontálního navýšení kapacity, protože tento cluster bude dál jenom destabilizovat.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Postup upgradu velikosti a operačního systému primárního typu virtuálního uzlu
Tady je postup aktualizace velikosti virtuálního počítače a operačního systému virtuálních počítačů typu primární uzel.  Po upgradu jsou virtuální počítače typu primární uzel standardní D4_V2 a běží na Windows serveru 2016 Datacenter s kontejnery.

> [!WARNING]
> Před pokusem o provedení této procedury v produkčním clusteru doporučujeme prostudovat ukázkové šablony a ověřit proces proti testovacímu clusteru. Cluster je také nedostupný po dobu. Nemůžete provádět změny ve více VMSS deklarovaných jako stejný typ NodeType paralelně; Chcete-li aplikovat změny na jednotlivé VMSS NodeType, budete muset provést operace odděleného nasazení.

1. Pomocí těchto ukázkových souborů [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) a [parametrů](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) nasaďte počáteční cluster se dvěma typy uzlů a dvěma sadami škálování (jedna sada škálování na typ uzlu).  Sady škálování mají standardní D2_V2 velikosti a běží na Windows Serveru 2012 R2 Datacenter.  Počkejte, než cluster dokončí upgrade standardních hodnot.   
2. Volitelné – nasaďte stavový vzorek do clusteru.
3. Po rozhodnutí o upgradu virtuálních počítačů typu primární uzel přidejte novou sadu škálování na typ primárního uzlu pomocí těchto ukázkových [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a souborů [parametrů](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) , takže typ primárního uzlu má teď dvě sady škálování.  Systémové služby a uživatelské aplikace jsou schopné migrovat mezi virtuálními počítači ve dvou různých sadách škálování.  Nové virtuální počítače sady škálování mají velikost Standard D4_V2 a používají Windows Server 2016 Datacenter s kontejnery.  K nové sadě škálování se přidávají taky nové nástroje pro vyrovnávání zatížení a veřejná IP adresa.  
    Pokud chcete v šabloně najít novou sadu škálování, vyhledejte prostředek Microsoft. COMPUTE/virtualMachineScaleSets s názvem a parametrem *vmNodeType2Name* .  Nová sada škálování se přidá k primárnímu typu uzlu pomocí vlastnosti->virtualMachineProfile->extensionProfile->rozšíření – >Properties->nastavení->nodeTypeRef.
4. Zkontrolujte stav clusteru a ověřte, zda jsou všechny uzly v dobrém stavu.
5. Zakažte uzly v staré sadě škálování typu primárního uzlu s záměrem odebrat uzel. Můžete zakázat všechny najednou a operace se zařadí do fronty. Počkejte, dokud nebudou všechny uzly zakázané, což může nějakou dobu trvat.  Vzhledem k zakázání starších uzlů v typu uzlu se systémové služby a uzly počáteční verze migrují na virtuální počítače nové sady škálování v primárním uzlu.
6. Odeberte starší sadu škálování z primárního typu uzlu. (Po zakázání uzlů jako v kroku 5 v okně Sada škálování virtuálního počítače v Azure Portal zrušte přidělení uzlů ze starého typu uzlu jeden po druhém.)
7. Odeberte Nástroj pro vyrovnávání zatížení přidružený k staré sadě škálování. Cluster není k dispozici, ale nová veřejná IP adresa a nástroj pro vyrovnávání zatížení jsou nakonfigurovány pro novou sadu škálování.  
8. Uložte nastavení DNS veřejné IP adresy přidružené k starému škálování typu primárního uzlu nastavenému v proměnné a odeberte tuto veřejnou IP adresu.
9. Nahraďte nastavení DNS veřejné IP adresy přidružené k novému primárnímu typu škálování sady s nastavením DNS odstraněné veřejné IP adresy.  Cluster je nyní dosažitelný.
10. Odeberte stav uzlu uzlů z clusteru.  Pokud byla úroveň odolnosti staré sady škálování stříbrná nebo zlatá, tento krok se provádí automaticky systémem.
11. Pokud jste v předchozím kroku nasadili stavovou aplikaci, ověřte, že je aplikace funkční.

## <a name="set-up-the-test-cluster"></a>Nastavení testovacího clusteru

Začněte stažením dvou sad souborů, které budeme potřebovat pro tento kurz, [šablonu]() a [parametry]() a za [šablonu]() a [parametry]().

Potom se přihlaste ke svému účtu Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Tento kurz vás provede scénářem vytvoření certifikátu podepsaného svým držitelem. Pokud chcete použít existující certifikát z Azure Key Vault, přeskočte následující krok a místo toho zrcadlte kroky v části [použití existujícího certifikátu k nasazení clusteru](./upgrade-managed-disks.md#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Vygenerování certifikátu podepsaného svým držitelem a nasazení clusteru

Nejdřív přiřaďte proměnné, které budete potřebovat pro nasazení clusteru Service Fabric. Upravte hodnoty pro `resourceGroupName` , `certSubjectName` , a `parameterFilePath` `templateFilePath` pro konkrétní účet a prostředí:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> `certOutputFolder`Před spuštěním příkazu pro nasazení nového clusteru Service Fabric zajistěte, aby umístění existovalo na vašem místním počítači.

Dále otevřete *Deploy-2NodeTypes-2ScaleSets.parameters.jsv* souboru a upravte hodnoty pro `clusterName` a tak, `dnsName` aby odpovídaly dynamickým hodnotám, které jste nastavili v prostředí PowerShell, a uložte provedené změny.

Pak nasaďte testovací Cluster Service Fabric:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Po dokončení nasazení Najděte na svém místním počítači soubor *. pfx* ( `$certPfx` ) a naimportujte ho do úložiště certifikátů:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Tato operace vrátí kryptografický otisk certifikátu, který použijete k připojení k novému clusteru a kontrole jeho stavu.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Připojení k novému clusteru a kontrolu stavu

Připojte se ke clusteru a ujistěte se, že jsou všechny jeho uzly v pořádku ( `clusterName` nahradí `thumb` proměnné a pro váš cluster):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Jsme připraveni zahájit postup upgradu.

## <a name="upgrade-the-primary-node-type-vms"></a>Upgrade typu virtuálních počítačů s primárním uzlem

Až se rozhodnete upgradovat virtuální počítače typu primární uzel, přidejte novou škálu na typ primárního uzlu tak, aby typ primárního uzlu měl teď dvě sady škálování. K dispozici jsou ukázkové soubory [šablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) a [parametrů](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) pro zobrazení nezbytných změn. Nové virtuální počítače sady škálování mají velikost Standard D4_V2 a používají Windows Server 2016 Datacenter s kontejnery. K nové sadě škálování se přidávají taky nové nástroje pro vyrovnávání zatížení a veřejná IP adresa. 

Pokud chcete v šabloně najít novou sadu škálování, vyhledejte prostředek Microsoft. COMPUTE/virtualMachineScaleSets s názvem a parametrem vmNodeType2Name. Nová sada škálování se přidá k primárnímu typu uzlu pomocí vlastnosti->virtualMachineProfile->extensionProfile->rozšíření – >Properties->nastavení->nodeTypeRef.

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony

`parameterFilePath` `templateFilePath` Podle potřeby upravte a pak spusťte následující příkaz:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Až se nasazení dokončí, zkontrolujte stav clusteru znovu a zajistěte, aby byly všechny uzly (v originálu a na nové sadě škálování) v dobrém stavu.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrace uzlů do nové sady škálování

Nyní jsme připraveni začít s zakázáním uzlů v původní sadě škálování. Vzhledem k tomu, že tyto uzly jsou zakázané, jsou systémové služby a počáteční uzly migrovány na virtuální počítače nové sady škálování, protože jsou také označeny jako primární typ uzlu.

V případě škálování neprimárních typů uzlů byste v tomto kroku upravili omezení umístění služby tak, aby zahrnovalo novou sadu virtuálních počítačů nebo typ uzlu, a pak omezila starý počet instancí sady škálování virtuálního počítače na hodnotu nula, jeden uzel v čase (aby se zajistilo, že odebrání uzlu nebude mít vliv na spolehlivost clusteru).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Pomocí Service Fabric Explorer můžete monitorovat migraci počátečních uzlů do nové sady škálování a průběh uzlů v původní sadě škálování z *zakázání* na stav *zakázáno* .

> [!NOTE]
> Dokončení operace zakázání ve všech uzlech původní sady škálování může nějakou dobu trvat. Aby se zajistila konzistence dat, může se v jednom okamžiku změnit jenom jeden počáteční uzel. Každá změna uzlu dosazení vyžaduje aktualizaci clusteru. Proto výměna počátečního uzlu vyžaduje dva upgrady clusteru (jeden pro každý pro přidání a odebrání uzlu). Upgrade pěti uzlů počátečních hodnot v tomto ukázkovém scénáři bude mít za následek deset upgradů clusteru.

## <a name="remove-the-original-scale-set"></a>Odebrat původní sadu škálování

Po dokončení operace vypnutí odeberte sadu škálování.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

V Service Fabric Explorer se teď odebrané uzly (a tedy *stav clusteru*) zobrazí v *chybovém* stavu.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Odebrání starého nástroje pro vyrovnávání zatížení a aktualizace nastavení DNS

Nyní můžeme odebrat prostředky, které se vztahují k starému primárnímu typu uzlu, počínaje nástrojem pro vyrovnávání zatížení a starou veřejnou IP adresou. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

V dalším kroku aktualizujeme nastavení DNS nové veřejné IP adresy tak, aby se zrcadlí nastavení z veřejné IP adresy typu původní primární uzel.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Až budete mít víc, podívejte se na stav clusteru.

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Nakonec odeberte stav uzlu pro každý související uzel. Pokud je úroveň odolnosti staré sady škálování stříbrná nebo zlatá, dojde k tomu automaticky.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Typ primárního uzlu clusteru byl nyní upgradován. Ověřte, že všechny nasazené aplikace fungují správně a že stav clusteru je OK.

## <a name="next-steps"></a>Další kroky
* Naučte se, jak [Přidat typ uzlu do clusteru](virtual-machine-scale-set-scale-node-type-scale-out.md) .
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)
