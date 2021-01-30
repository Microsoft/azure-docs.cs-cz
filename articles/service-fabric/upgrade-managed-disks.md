---
title: Upgrade uzlů clusteru pro používání služby Azure Managed disks
description: Tady je postup, jak upgradovat existující Cluster Service Fabric tak, aby používal Azure Managed disks s malým nebo žádným výpadkem clusteru.
ms.topic: how-to
ms.date: 4/07/2020
ms.openlocfilehash: c374c4536309a13abcf8c882b041a9c5357878e5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090650"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Upgrade uzlů clusteru pro používání služby Azure Managed disks

[Azure Managed disks](../virtual-machines/managed-disks-overview.md) je doporučená nabídka diskového úložiště pro použití s virtuálními počítači Azure k trvalému ukládání dat. Odolnost vašich Service Fabricch úloh můžete zlepšit tím, že upgradujete sady škálování virtuálních počítačů, které budou základem typů uzlů, aby používaly spravované disky. Tady je postup, jak upgradovat existující Cluster Service Fabric tak, aby používal Azure Managed disks s malým nebo žádným výpadkem clusteru.

Všeobecná strategie pro upgrade uzlu clusteru Service Fabric na používání spravovaných disků:

1. Nasaďte jinak duplicitní sadu škálování virtuálního počítače pro daný typ uzlu, ale s objektem [managedDisk](/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) přidaným do `osDisk` části šablony nasazení sady škálování virtuálního počítače. Nová sada škálování by se měla navazovat na stejný nástroj pro vyrovnávání zatížení/IP adresu jako originál, aby vaši zákazníci během migrace nepracovali jako výpadek služeb.

2. Po souběžném spuštění původní i upgradované sady škálování zakažte instance původních uzlů po jednom, aby se systémové služby (nebo repliky stavových služeb) migrovali do nové sady škálování.

3. Ověřte, že cluster a nové uzly jsou v pořádku, a pak odeberte původní sadu škálování a stav uzlu pro odstraněné uzly.

Tento článek vás provede jednotlivými kroky upgradu primárního typu uzlu ukázkového clusteru, který bude používat spravované disky, a zároveň vyloučí jakékoli výpadky clusteru (viz poznámka níže). Počáteční stav ukázkového testovacího clusteru se skládá z jednoho typu uzlu [odolného proti stříbrnému](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), který je zajištěný jednou sadou škálování s pěti uzly.

> [!NOTE]
> Omezení základního nástroje pro vyrovnávání zatížení zabrání přidání dalšího měřítka sady. Místo toho doporučujeme použít nástroj pro vyrovnávání zatížení Standard SKU. Další informace naleznete v [porovnání dvou skladových](../load-balancer/skus.md)položek.

> [!CAUTION]
> Tento postup se projeví pouze v případě, že máte závislosti na DNS clusteru (například při přístupu k [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)). [Osvědčeným postupem pro front-end služby](/azure/architecture/microservices/design/gateway) je mít určitý druh nástroje pro [Vyrovnávání zatížení](/azure/architecture/guide/technology-choices/load-balancing-overview) před typy uzlů, aby bylo možné přeměnit uzel bez výpadku.

Tady jsou [šablony a rutiny](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) pro Azure Resource Manager, které použijeme k dokončení scénáře upgradu. Změny šablon budou vysvětleny v části [nasazení upgradované sady škálování pro typ primárního uzlu](#deploy-an-upgraded-scale-set-for-the-primary-node-type)  níže.

## <a name="set-up-the-test-cluster"></a>Nastavení testovacího clusteru

Pojďme nastavit počáteční testovací Cluster Service Fabric. Nejdřív [Stáhněte](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) ukázkové šablony Azure Resource Manager, které budeme používat k dokončení tohoto scénáře.

Potom se přihlaste ke svému účtu Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Následující příkazy vás provede vygenerováním nového certifikátu podepsaného svým držitelem a nasazením testovacího clusteru. Pokud už máte certifikát, který byste chtěli použít, přejděte k [nasazení clusteru pomocí existujícího certifikátu](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Vygenerování certifikátu podepsaného svým držitelem a nasazení clusteru

Nejdřív přiřaďte proměnné, které budete potřebovat pro nasazení clusteru Service Fabric. Upravte hodnoty pro `resourceGroupName` ,  `certSubjectName` , a `parameterFilePath` `templateFilePath` pro konkrétní účet a prostředí:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> `certOutputFolder`Před spuštěním příkazu pro nasazení nového clusteru Service Fabric zajistěte, aby umístění existovalo na vašem místním počítači.

Dále otevřete [*Initial-1NodeType-UnmanagedDisks.parameters.jsv*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) souboru a upravte hodnoty pro `clusterName` a tak, `dnsName` aby odpovídaly dynamickým hodnotám, které jste nastavili v prostředí PowerShell, a uložte provedené změny.

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

Tato operace vrátí kryptografický otisk certifikátu, který použijete k [připojení k novému clusteru](#connect-to-the-new-cluster-and-check-health-status) a kontrole jeho stavu. (Přeskočte následující část, která je alternativním přístupem k nasazení clusteru.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Použít existující certifikát k nasazení clusteru

K nasazení testovacího clusteru můžete použít také existující Azure Key Vault certifikát. K tomu je potřeba [získat odkazy na Key Vault](#obtain-your-key-vault-references) a kryptografický otisk certifikátu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Otevřete [*Initial-1NodeType-UnmanagedDisks.parameters.jsv*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) souboru a změňte hodnoty pro `clusterName` a `dnsName` na něco jedinečného.

Nakonec určete název skupiny prostředků pro cluster a nastavte `templateFilePath` `parameterFilePath` umístění a umístění *počátečních souborů 1NodeType-UnmanagedDisks* :

> [!NOTE]
> Určená skupina prostředků už musí existovat a musí se nacházet ve stejné oblasti jako vaše Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Nakonec spuštěním následujícího příkazu nasaďte počáteční testovací cluster:

```powershell
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

Připojte se ke clusteru a ujistěte se, že všechny pět jeho uzlů je v pořádku ( `clusterName` nahrazuje `thumb` proměnné a pro váš cluster):

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

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Nasazení upgradované sady škálování pro typ primárního uzlu

Aby bylo možné upgradovat nebo *vertikálně škálovat* typ uzlu, bude nutné nasadit kopii této sady škálování virtuálního počítače typu uzel, která je jinak shodná s původní sadou škálování (včetně odkazu na stejnou `nodeTypeRef` , `subnet` a `loadBalancerBackendAddressPools` ) s tím rozdílem, že obsahuje požadovaný upgrade/změny a vlastní samostatnou podsíť a fond adres příchozího překladu adres (NAT). Vzhledem k tomu, že upgradujete typ primárního uzlu, nová sada škálování bude označena jako primární ( `isPrimary: true` ), stejně jako původní sada škálování. (U neprimárních upgradů typu uzlu jednoduše tuto dobu vynechejte.)

Pro usnadnění práce se pro vás už požadované změny provedly v [šablonách](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *upgrade-1NodeType-2ScaleSets-ManagedDisks* a v souborech [parametrů](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json) .

V následujících částech se podrobně vysvětlují změny v šabloně. Pokud budete chtít, můžete vysvětlení přeskočit a pokračovat k [dalšímu kroku postupu upgradu](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizuje šablonu clusteru s inovovanou sadou škálování.

Tady je přehled úprav původní šablony nasazení clusteru pro přidání upgradované sady škálování pro typ primárního uzlu.

#### <a name="parameters"></a>Parametry

Přidejte parametry pro název instance, počet a velikost nové sady škálování. Všimněte si, že `vmNodeType1Name` je jedinečný pro novou sadu škálování, zatímco hodnoty počtu a velikosti se shodují s původní sadou škálování.

**Soubor šablony**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Soubor parametrů**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Proměnné

V části šablona nasazení `variables` přidejte položku pro fond adres příchozího překladu adres (NAT) nové sady škálování.

**Soubor šablony**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Zdroje informací

V části *prostředky* šablony nasazení přidejte novou sadu škálování virtuálního počítače. Pamatujte na tyto věci:

* Nová sada škálování odkazuje na stejný typ uzlu jako původní:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Nová sada škálování odkazuje na stejnou adresu back-end služby Vyrovnávání zatížení a podsíť (ale používá jiný fond NAT příchozího vyrovnávání zatížení):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Podobně jako původní sada škálování je nová sada škálování označená jako primární typ uzlu. (Při upgradu neprimárních typů uzlů tuto změnu vynechejte.)

    ```json
    "isPrimary": true,
    ```

* Na rozdíl od původní sady škálování je nová sada škálování upgradována, aby používala spravované disky.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Po implementaci všech změn v šabloně a souborech parametrů přejděte k další části, kde získáte odkazy na Key Vault a nasazení aktualizací do clusteru.

### <a name="obtain-your-key-vault-references"></a>Získat odkazy na Key Vault

Pokud chcete nasadit aktualizovanou konfiguraci, můžete nejdřív získat několik odkazů na certifikát clusteru uložený ve vašem Key Vault. Nejjednodušší způsob, jak tyto hodnoty najít, je prostřednictvím Azure Portal. Budete potřebovat:

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

`parameterFilePath` `templateFilePath` Podle potřeby upravte a pak spusťte následující příkaz:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Až se nasazení dokončí, zkontrolujte stav clusteru znovu a zajistěte, aby všechny deset uzlů (pět v originálu a pět v nové sadě škálování) byly v pořádku.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrace počátečních uzlů do nové sady škálování

Nyní jsme připraveni začít s zakázáním uzlů v původní sadě škálování. Vzhledem k tomu, že tyto uzly jsou zakázané, jsou systémové služby a počáteční uzly migrovány na virtuální počítače nové sady škálování, protože jsou také označeny jako primární typ uzlu.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Pomocí Service Fabric Explorer můžete monitorovat migraci počátečních uzlů do nové sady škálování a průběh uzlů v původní sadě škálování z *zakázání* na stav *zakázáno* .

![Service Fabric Explorer zobrazení stavu zakázaných uzlů](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

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

![Service Fabric Explorer zobrazení zakázaných uzlů v chybovém stavu](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Odeberte zastaralé uzly z clusteru Service Fabric a obnovte stav clusteru na *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Odebraná Service Fabric Explorer s vypnutými uzly v chybovém stavu](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se dozvěděli, jak upgradovat sadu škálování virtuálního počítače Service Fabric clusteru na používání spravovaných disků a vyhnout se výpadkům služby během procesu. Další informace o souvisejících tématech najdete v následujících zdrojích informací.

Naučte se:

* [Vertikální navýšení kapacity primárního typu uzlu clusteru Service Fabric](service-fabric-scale-up-primary-node-type.md)

* [Převod šablony sady škálování na používání spravovaných disků](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Odebrání typu Service Fabric uzlu](service-fabric-how-to-remove-node-type.md)

Viz také:

* [Ukázka: upgrade uzlů clusteru pro použití služby Azure Managed disks](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade)

* [Požadavky vertikálního škálování](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)