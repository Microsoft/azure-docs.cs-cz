---
title: Horizontální navýšení kapacity Service Fabric typu primárního uzlu Azure
description: Vertikálně Škálujte Service Fabric cluster přidáním nového typu uzlu a odebráním předchozího.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251167"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Vertikální navýšení kapacity primárního typu uzlu clusteru Service Fabric

Tento článek popisuje, jak škálovat typ primárního uzlu clusteru Service Fabric s minimálními prostoji. Obecnou strategií pro upgrade typu uzlu clusteru Service Fabric:

1. Přidejte do clusteru Service Fabric nový typ uzlu, který jste provedli inovovanou (nebo upravenou) SKU a konfigurací sady škálování virtuálního počítače. Tento krok zahrnuje taky nastavení nového nástroje pro vyrovnávání zatížení, podsítě a veřejné IP adresy pro sadu škálování.

1. Po souběžném spuštění původní i upgradované sady škálování zakažte instance původních uzlů po jednom, aby se systémové služby (nebo repliky stavových služeb) migrovali do nové sady škálování.

1. Ověřte, že cluster a nové uzly jsou v pořádku, a pak odeberte původní sadu škálování (a související prostředky) a stav uzlu pro odstraněné uzly.

Následující kroky vás provedou procesem aktualizace velikosti a operačního systému virtuálních počítačů s primárním uzlem pro ukázkový cluster s [odolností stříbrného](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), který je zajištěný pomocí jedné sady škálování s pěti uzly. Bude upgradován typ primárního uzlu:

- Z *Standard_D2_V2* velikosti virtuálního počítače do *úrovně Standard D4_V2* a
- Z operačního systému virtuálního počítače *Windows server 2016 Datacenter s kontejnery* na *Windows Server 2019 Datacenter s kontejnery*.

> [!WARNING]
> Před pokusem o provedení této procedury v produkčním clusteru doporučujeme prostudovat ukázkové šablony a ověřit proces proti testovacímu clusteru. Cluster může být k dispozici také po krátkou dobu.
>
> Pokud stav clusteru není v pořádku, nepokoušejte se nepokusit o typ primárního uzlu postup horizontálního navýšení kapacity, protože tento cluster bude dál jenom destabilizovat.

Tady jsou podrobné šablony nasazení Azure, které použijeme k dokončení tohoto ukázkového scénáře upgradu: https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade

## <a name="set-up-the-test-cluster"></a>Nastavení testovacího clusteru

Pojďme nastavit počáteční testovací Cluster Service Fabric. Nejdřív [Stáhněte](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) ukázkové šablony Azure Resource Manager, které budeme používat k dokončení tohoto scénáře.

Potom se přihlaste ke svému účtu Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Dále otevřete [*parameters.jsv*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) souboru a aktualizujte hodnotu pro `clusterName` na něco jedinečného (v rámci Azure).

Následující příkazy vás provede vygenerováním nového certifikátu podepsaného svým držitelem a nasazením testovacího clusteru. Pokud už máte certifikát, který byste chtěli použít, přejděte k [nasazení clusteru pomocí existujícího certifikátu](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Vygenerování certifikátu podepsaného svým držitelem a nasazení clusteru

Nejdřív přiřaďte proměnné, které budete potřebovat pro nasazení clusteru Service Fabric. Upravte hodnoty pro `resourceGroupName` ,  `certSubjectName` , a `parameterFilePath` `templateFilePath` pro konkrétní účet a prostředí:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> `certOutputFolder`Před spuštěním příkazu pro nasazení nového clusteru Service Fabric zajistěte, aby umístění existovalo na vašem místním počítači.

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

Tato operace vrátí kryptografický otisk certifikátu, který teď můžete použít pro [připojení k novému clusteru](#connect-to-the-new-cluster-and-check-health-status) a kontrolu stavu. (Přeskočte následující část, která je alternativním přístupem k nasazení clusteru.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Použít existující certifikát k nasazení clusteru

Alternativně můžete použít existující Azure Key Vault certifikát k nasazení testovacího clusteru. K tomu je potřeba [získat odkazy na Key Vault](#obtain-your-key-vault-references) a kryptografický otisk certifikátu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Dále určete název skupiny prostředků pro cluster a nastavte `templateFilePath` `parameterFilePath` umístění a:

> [!NOTE]
> Určená skupina prostředků už musí existovat a musí se nacházet ve stejné oblasti jako vaše Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Nakonec spuštěním následujícího příkazu nasaďte počáteční testovací cluster:

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Připojení k novému clusteru a kontrolu stavu

Připojte se ke clusteru a ujistěte se, že jsou všechny pět jeho uzlů v pořádku (nahraďte `clusterName` `thumb` proměnné a vlastními hodnotami):

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

V takovém případě jsme připraveni zahájit postup upgradu.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Nasadit nový typ primárního uzlu s upgradovanou sadou škálování

Aby bylo možné upgradovat (vertikálně škálovat) typ uzlu, nejprve bude nutné nasadit nový typ uzlu, který je zajištěn novou sadou škálování a podpůrnými prostředky. Nová sada škálování bude označená jako primární ( `isPrimary: true` ), stejně jako původní sada škálování (Pokud neprovádíte upgrade typu, který není primární). Prostředky vytvořené v následující části se ve vašem clusteru stanou novým primárním uzlem a původní typ prostředků primárního uzlu se odstraní.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizuje šablonu clusteru s inovovanou sadou škálování.

Tady je seznam úprav původní šablony nasazení clusteru pro přidání nového typu primárního uzlu a podpůrných prostředků.

Požadované změny pro tento krok již byly provedeny v [*Step1-AddPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json) souboru šablony a následující příkaz bude tyto změny podrobně vysvětlit. Pokud dáváte přednost, můžete přeskočit vysvětlení a nadále [získávat odkazy na Key Vault](#obtain-your-key-vault-references) a [nasadit aktualizovanou šablonu](#deploy-the-updated-template) , která do clusteru přidá nový primární typ uzlu.

> [!Note]
> Ujistěte se, že používáte názvy, které jsou jedinečné z původního typu uzlu, sady škálování, nástroje pro vyrovnávání zatížení, veřejné IP adresy a podsítě původního primárního typu uzlu, protože tyto prostředky budou odstraněny později v tomto procesu.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Vytvořit novou podsíť ve stávající virtuální síti

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Vytvoření nové veřejné IP adresy s jedinečným domainNameLabel

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Vytvoření nového nástroje pro vyrovnávání zatížení pro veřejnou IP adresu

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Vytvořit novou sadu škálování virtuálního počítače (s upgradovanými SKU virtuálních počítačů a operačních systémů OS)

Typ uzlu ref

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Skladová položka virtuálního počítače

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

SKU OPERAČNÍHO SYSTÉMU

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

Ujistěte se také, že máte zahrnutá další rozšíření, která jsou pro vaše zatížení nutná.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Přidat do clusteru nový typ primárního uzlu

Teď, když má nový typ uzlu (vmNodeType1Name) vlastní název, podsíť, IP, nástroj pro vyrovnávání zatížení a sadu škálování, může znovu použít všechny ostatní proměnné z původního typu uzlu (například `nt0applicationEndPort` , `nt0applicationStartPort` , a `nt0fabricTcpGatewayPort` ):

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Po implementaci všech změn v šabloně a souborech parametrů přejděte k další části, kde získáte odkazy na Key Vault a nasazení aktualizací do clusteru.

### <a name="obtain-your-key-vault-references"></a>Získat odkazy na Key Vault

K nasazení aktualizované konfigurace budete potřebovat několik odkazů na certifikát clusteru uložený ve vašem Key Vault. Nejjednodušší způsob, jak tyto hodnoty najít, je prostřednictvím Azure Portal. Budete potřebovat:

* **Key Vault URL certifikátu clusteru.** Z Key Vault v Azure Portal vyberte **certifikáty**  >  *požadovaný*  >  **identifikátor tajného** certifikátu.

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Kryptografický otisk certifikátu clusteru** (Už to budete mít, pokud jste [připojení k počátečnímu clusteru](#connect-to-the-new-cluster-and-check-health-status) , abyste zkontrolovali stav jeho stavu.) Z okna se stejným certifikátem (**certifikáty**  >  *požadovaná certifikát*) v Azure Portal zkopírujte **kryptografický otisk X. 509 SHA-1 (v šestnáctkové soustavě)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **ID prostředku Key Vault.** Z Key Vault v Azure Portal vyberte **vlastnosti**  >  **ID prostředku**:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony

`templateFilePath`Podle potřeby upravte podle potřeby a spusťte následující příkaz:

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Po dokončení nasazení znovu zkontrolujte stav clusteru a ujistěte se, že jsou všechny uzly na obou typech uzlů v pořádku.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrace počátečních uzlů do nového typu uzlu

Nyní jsme připraveni aktualizovat původní typ uzlu jako neprimární a začít zakazovat jeho uzly. Při zakázání uzlů se systémové služby a uzly počátečního clusteru migrují do nové sady škálování.

### <a name="unmark-the-original-node-type-as-primary"></a>Zrušit označení původního typu uzlu jako primárního

Nejprve odeberte `isPrimary` označení v šabloně z původního typu uzlu.

```json
{
    "isPrimary": false,
}
```

Pak šablonu nasaďte s aktualizací. Tím se zahájí migrace počátečních uzlů do nové sady škálování.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Dokončení migrace počátečního uzlu do nové sady škálování bude nějakou dobu trvat. Aby se zajistila konzistence dat, může se v jednom okamžiku změnit jenom jeden počáteční uzel. Každá změna uzlu dosazení vyžaduje aktualizaci clusteru. Proto výměna počátečního uzlu vyžaduje dva upgrady clusteru (jeden pro každý pro přidání a odebrání uzlu). Upgrade pěti uzlů počátečních hodnot v tomto ukázkovém scénáři bude mít za následek deset upgradů clusteru.

Pomocí Service Fabric Explorer můžete monitorovat migraci počátečních uzlů do nové sady škálování. Uzly původního typu uzlu (nt0vm) by měly mít všechny *hodnotu false* ve sloupci **je počáteční uzel** , přičemž hodnoty pro nový typ uzlu (nt1vm) budou *pravdivé*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Zakázat uzly v původní sadě škálování typu uzlu

Po migrování všech počátečních uzlů do nové sady škálování můžete zakázat uzly původní sady škálování.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Pomocí Service Fabric Explorer můžete monitorovat průběh uzlů v původní sadě škálování z *zakázání* na stav *zakázáno* .

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer zobrazení stavu zakázaných uzlů":::

U stříbrné a zlaté odolnosti jsou některé uzly v zakázaném stavu, zatímco jiné můžou zůstat ve stavu *vypnutí* . V Service Fabric Explorer na kartě **Podrobnosti** uzlů v části zakázání stavu. Pokud se zobrazuje *Nedokončená bezpečnostní kontrola* druhu *EnsurePartitionQuorem* (zajišťuje kvorum pro oddíly služby infrastruktury), je bezpečné pokračovat.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Pokud se zobrazí nedokončená bezpečnostní kontroly druhu ' EnsurePartitionQuorum ', můžete pokračovat s zastavením dat a odebráním uzlů zablokovaných se stavem zakázáno.":::

Pokud má váš cluster bronzovou trvanlivost, počkejte, až se všechny uzly dostanou na *zakázaný* stav.

### <a name="stop-data-on-the-disabled-nodes"></a>Zastavit data na zakázaných uzlech

Nyní můžete zastavit data na zakázaných uzlech.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Odebrat původní typ uzlu a vyčistit jeho prostředky

Jsme připraveni odebrat původní typ uzlu a jeho přidružené prostředky a uzavřít postup vertikálního škálování.

### <a name="remove-the-original-scale-set"></a>Odebrat původní sadu škálování

Nejdřív odeberte sadu back-Scale v typu uzlu.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Odstranění původních prostředků protokolu IP a nástroje pro vyrovnávání zatížení

Nyní můžete odstranit původní IP adresu a prostředky nástroje pro vyrovnávání zatížení. V tomto kroku si taky aktualizujete název DNS.

> [!Note]
> Tento krok je nepovinný, pokud jste už používali veřejnou IP adresu *standardního* SKU a nástroj pro vyrovnávání zatížení. V takovém případě můžete mít v rámci stejného nástroje pro vyrovnávání zatížení několik typů uzlů nebo uzlů s možností horizontálního rozšíření kapacity.

Spusťte následující příkazy, `$lbname` podle potřeby změňte hodnotu.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Odebrat stav uzlu z původního typu uzlu

V uzlech původní typ uzlu se nyní zobrazí *Chyba* pro svůj **stav**. Odeberte svůj stav uzlu z clusteru.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer by teď měl odrážet jenom pět uzlů nového typu uzlu (nt1vm), všechny s hodnotami stavu *OK*. Stav vašeho clusteru se pořád zobrazuje *Chyba*. Napravuje, že další aktualizace šablony tak, aby odrážela nejnovější změny a znovu nasazuje.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Aktualizace šablony nasazení tak, aby odrážela nově škálovatelný typ primárního uzlu

Požadované změny pro tento krok již byly provedeny v [*Step3-CleanupOriginalPrimaryNodeType.js*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json) souboru šablony a v následujících částech jsou podrobně vysvětleny tyto změny šablony. Pokud dáváte přednost, můžete přeskočit vysvětlení a pokračovat v [nasazení aktualizované šablony](#deploy-the-finalized-template) a dokončit kurz.

#### <a name="update-the-cluster-management-endpoint"></a>Aktualizace koncového bodu správy clusteru

Aktualizujte cluster `managementEndpoint` v šabloně nasazení tak, aby odkazoval na novou IP adresu (aktualizací *VmNodeType0Name* s *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Odebrat odkaz na typ původního uzlu

Odebrat odkaz na typ původního uzlu z prostředku Service Fabric v šabloně nasazení:

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Nakonfigurujte zásady stavu tak, aby ignorovaly existující chyby.

V případě vysoké a vyšší odolnosti clusterů aktualizujte clusterový prostředek v šabloně a nakonfigurujte zásady stavu tak, aby ignorovaly `fabric:/System` stav aplikace, a to přidáním *applicationDeltaHealthPolicies* do vlastností prostředku clusteru, jak je uvedeno níže. Níže uvedené zásady budou ignorovat existující chyby, ale nebudou umožňovat nové chyby v oblasti stavu.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Odebrat podpůrné prostředky pro typ původního uzlu

Odeberte všechny ostatní prostředky související s původním typem uzlu ze šablony ARM a souboru parametrů. Odstraňte následující:

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Nasazení konečné šablony

Nakonec nasaďte upravenou šablonu Azure Resource Manager.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Tento krok může trvat delší dobu, obvykle až dvě hodiny.

Upgrade změní nastavení na *InfrastructureService*; Proto je nutné restartovat uzel. V tomto případě se *forceRestart* ignoruje. Parametr `upgradeReplicaSetCheckTimeout` Určuje maximální dobu, po kterou Service Fabric čeká, až bude oddíl v bezpečném stavu, pokud ještě není v bezpečném stavu. Jakmile kontroly bezpečnosti projde pro všechny oddíly v uzlu, Service Fabric pokračuje s upgradem v tomto uzlu. Hodnota parametru `upgradeTimeout` může být snížena na 6 hodin, ale v případě maximálního zabezpečení 12 hodin by se měla použít.

Po dokončení nasazení ověřte v Azure Portal, že stav prostředku Service Fabric je *připravený*. Ověřte, že máte přístup k novému koncovému bodu Service Fabric Explorer, **stav clusteru** je *OK* a všechny nasazené aplikace fungují správně.

V takovém případě jste vertikálně škálovat typ primárního uzlu clusteru.

## <a name="next-steps"></a>Další kroky

* Naučte se, jak [Přidat typ uzlu do clusteru](virtual-machine-scale-set-scale-node-type-scale-out.md) .
* Přečtěte si o [škálovatelnosti aplikací](service-fabric-concepts-scalability.md).
* Horizontální navýšení nebo navýšení [kapacity clusteru Azure](service-fabric-tutorial-scale-cluster.md)
* [Škálujte cluster Azure pomocí programu](service-fabric-cluster-programmatic-scaling.md) Fluent Azure COMPUTE SDK.
* Horizontální navýšení [nebo zmenšení kapacity samostatného clusteru](service-fabric-cluster-windows-server-add-remove-nodes.md)
