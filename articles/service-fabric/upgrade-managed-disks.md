---
title: Upgrade uzlů clusteru na spravované disky Azure
description: Tady je postup, jak upgradovat existující cluster Service Fabric tak, aby používal spravované disky Azure s malými nebo žádnými prostoji vašeho clusteru.
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758050"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Upgrade uzlů clusteru na spravované disky Azure

[Spravované disky Azure](../virtual-machines/windows/managed-disks-overview.md) jsou doporučené nabídky diskového úložiště pro použití s virtuálními počítači Azure pro trvalé ukládání dat. Odolnost úloh Service Fabric můžete zlepšit upgradem škálovacích sad virtuálních počítačů, které jsou základem typů uzlů, aby používaly spravované disky. Tady je postup, jak upgradovat existující cluster Service Fabric tak, aby používal spravované disky Azure s malými nebo žádnými prostoji vašeho clusteru.

Obecnou strategií pro inovaci uzlu clusteru Service Fabric pro použití spravovaných disků je:

1. Nasaďte jinak duplicitní škálovací sadu virtuálních počítačů tohoto typu uzlu, ale s objektem [managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) přidaným `osDisk` do části šablony nasazení škálovací sady virtuálních počítačů. Nová škálovací sada by se měla vázat na stejný nástroj pro vyrovnávání zatížení / IP jako původní, aby vaši zákazníci během migrace nezaznamenali výpadek služby.

2. Jakmile jsou původní i upgradované škálovací sady spuštěny vedle sebe, zakažte původní instance uzlů jeden po druhém, aby systémové služby (nebo repliky stavových služeb) migrovaly do nové škálovací sady.

3. Ověřte, zda jsou cluster a nové uzly v pořádku, a potom odeberte původní škálovací sadu a stav uzlu odstraněných uzlů.

Tento článek vás provede kroky upgradu typu primárního uzlu ukázkového clusteru tak, aby používal spravované disky, aniž byste museli prostoji clusteru (viz poznámka níže). Počáteční stav ukázkového testovacího clusteru se skládá z jednoho typu uzlu [odolnosti silver](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster), podporovaného jednou škálovací sadou s pěti uzly.

> [!CAUTION]
> K výpadku tohoto postupu dojde pouze v případě, že máte závislosti na dns clusteru (například při přístupu k [aplikaci Service Fabric Explorer).](service-fabric-visualizing-your-cluster.md) [Architektonická best practice pro front-end ové služby](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) je mít nějaký druh nástrojů pro [vyrovnávání zatížení](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) před typy uzlů, aby bylo možné vyměnit uzel bez výpadku.

Tady jsou [šablony a rutiny](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) pro Azure Resource Manager, které použijeme k dokončení scénáře upgradu. Změny šablony budou vysvětleny v [části Nasazení upgradované škálovací sady pro typ primárního uzlu](#deploy-an-upgraded-scale-set-for-the-primary-node-type) níže.

## <a name="set-up-the-test-cluster"></a>Nastavení testovacího clusteru

Nastavíme počáteční testovací cluster Service Fabric. Nejprve [si stáhněte](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) ukázkové šablony Správce prostředků Azure, které použijeme k dokončení tohoto scénáře.

Dále se přihlaste ke svému účtu Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Následující příkazy vás provedou generováním nového certifikátu podepsaného svým držitelem a nasazením testovacího clusteru. Pokud již máte certifikát, který chcete použít, přeskočte na [Použít existující certifikát k nasazení clusteru](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Generovat certifikát podepsaný svým držitelem a nasadit cluster

Nejprve přiřaďte proměnné, které budete potřebovat pro nasazení clusteru Service Fabric. Upravte hodnoty `resourceGroupName` `certSubjectName`pro `parameterFilePath`, `templateFilePath` , a pro váš konkrétní účet a prostředí:

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
> Před spuštěním `certOutputFolder` příkazu pro nasazení nového clusteru Service Fabric se ujistěte, že v místním počítači existuje umístění.

Dále otevřete soubor [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) a `clusterName` `dnsName` upravte hodnoty a odpovídající dynamickým hodnotám nastaveným v prostředí PowerShell a uložte změny.

Potom nasaďte testovací cluster Service Fabric:

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

Po dokončení nasazení vyhledejte soubor *.pfx* (`$certPfx`) v místním počítači a importujte jej do úložiště certifikátů:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

Operace vrátí kryptografický otisk certifikátu, který použijete k [připojení k novému clusteru](#connect-to-the-new-cluster-and-check-health-status) a ke kontrole jeho stavu. (Přeskočte následující část, která je alternativním přístupem k nasazení clusteru.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Nasazení clusteru pomocí existujícího certifikátu

K nasazení testovacího clusteru můžete také použít existující certifikát Azure Key Vault. Chcete-li to provést, budete muset [získat odkazy na trezor klíčů](#obtain-your-key-vault-references) a kryptografický otisk certifikátu.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Otevřete soubor [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) a `clusterName` změňte hodnoty pro a `dnsName` na něco jedinečného.

Nakonec určete název skupiny prostředků pro `templateFilePath` `parameterFilePath` cluster a nastavte umístění a souborů *Initial-1NodeType-UnmanagedDisks:*

> [!NOTE]
> Určená skupina prostředků musí již existovat a musí být umístěna ve stejné oblasti jako trezor klíčů.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Nakonec spusťte následující příkaz k nasazení počátečnítestovací cluster:

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

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Připojení k novému clusteru a kontrola stavu

Připojte se ke clusteru a ujistěte se, že `clusterName` všech `thumb` pět jeho uzlů je v pořádku (nahrazení proměnných a pro váš cluster):

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

S tím jsme připraveni zahájit upgrade procedury.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Nasazení upgradované škálovací sady pro primární typ uzlu

Chcete-li upgradovat nebo *vertikálně škálovat*typ uzlu, budeme muset nasadit kopii škálovací sady virtuálního počítače tohoto typu uzlu, která `nodeTypeRef` `subnet`je `loadBalancerBackendAddressPools`jinak shodná s původní škálovací sadou (včetně odkazu na stejnou , a ) s tím rozdílem, že obsahuje požadovanou upgrade/změny a vlastní samostatnou podsíť a vstupní fond adres NAT. Vzhledem k tomu, že upgradujeme typ primárního uzlu, bude nová škálovací sada označena jako primární (`isPrimary: true`), stejně jako původní škálovací sada. (Pro upgrady typu primárního uzlu jednoduše vynechte.)

Pro větší pohodlí již byly provedeny požadované změny v [šabloně](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) *Upgrade-1NodeType-2ScaleSets-ManagedDisks* a [v souborech parametrů.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

V následujících částech budou podrobně vysvětleny změny šablony. Pokud chcete, můžete přeskočit vysvětlení a pokračovat k [dalšímu kroku postupu upgradu](#obtain-your-key-vault-references).

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Aktualizace šablony clusteru pomocí upgradované škálovací sady

Zde jsou změny po oddílech původní šablony nasazení clusteru pro přidání upgradované škálovací sady pro primární typ uzlu.

#### <a name="parameters"></a>Parametry

Přidejte parametry pro název instance, počet a velikost nové škálovací sady. Všimněte `vmNodeType1Name` si, že je jedinečný pro novou škálovací sadu, zatímco hodnoty počtu a velikosti jsou shodné s původní škálovací sadou.

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

V části `variables` šablony nasazení přidejte položku pro příchozí fond adres NAT nové škálovací sady.

**Soubor šablony**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>Zdroje a prostředky

V části *prostředky šablony* nasazení přidejte novou škálovací sadu virtuálních strojů a mějte na paměti následující:

* Nová škálovací sada odkazuje na stejný typ uzlu jako originál:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Nová škálovací sada odkazuje na stejnou back-endovou adresu a podsíť vykladače vyrovnávání zatížení (ale používá jiný příchozí fond NAT pro vyrovnávání zatížení):

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

* Stejně jako původní škálovací sada je nová škálovací sada označena jako primární typ uzlu. (Při inovaci typů neprimárních uzlů tuto změnu vynechte.)

    ```json
    "isPrimary": true,
    ```

* Na rozdíl od původní škálovací sady je nová škálovací sada upgradována tak, aby používala spravované disky.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Po implementaci všech změn v souborech šablon a parametrů přejděte k další části, kde získáte odkazy na trezor klíčů a nasadíte aktualizace do clusteru.

### <a name="obtain-your-key-vault-references"></a>Získání referencí trezoru klíčů

Chcete-li nasadit aktualizovanou konfiguraci, nejprve získáte několik odkazů na certifikát clusteru uložených v trezoru klíčů. Nejjednodušší způsob, jak najít tyto hodnoty je prostřednictvím portálu Azure. Budete potřebovat:

* **Adresa URL trezoru klíčů vašeho certifikátu clusteru.** Z trezoru klíčů na webu Azure Portal vyberte **certifikáty** > Požadovaný tajný identifikátor*certifikátu* > **Secret Identifier**:

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **Kryptografický otisk certifikátu clusteru.** (Pravděpodobně již máte, pokud jste [připojeni k počáteční clusteru](#connect-to-the-new-cluster-and-check-health-status) ke kontrole jeho stavu stavu.) Ze stejného listu certifikátu **(certifikáty** > *požadovaný certifikát)* na webu Azure Portal zkopírujte **x.509 SHA-1 thumbprint (v hex)**:

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **ID prostředku trezoru klíčů.** Z trezoru klíčů na webu Azure portal vyberte**ID prostředku** **vlastnosti** > :

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Nasazení aktualizované šablony

Upravte `parameterFilePath` `templateFilePath` a podle potřeby a spusťte následující příkaz:

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

Po dokončení nasazení zkontrolujte stav clusteru znovu a ujistěte se, že všech deset uzlů (pět na původní a pět na nové škálovací sady) jsou v pořádku.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Migrace semenných uzlů do nové škálovací sady

Nyní jsme připraveni začít zakázat uzly původní škálovací sady. Jakmile se tyto uzly stanou zakázanými, systémové služby a osiva uzly migrují do virtuálních terminálů nové škálovací sady, protože jsou také označeny jako primární typ uzlu.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Pomocí průzkumníka service fabric můžete sledovat migraci osivových uzlů do nové škálovací sady a postup uzlů v původní škálovací sadě ze *stavu Zakázání* na *Zakázané.*

![Průzkumník prostředků infrastruktury služby zobrazující stav zakázaných uzlů](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Může trvat nějakou dobu k dokončení operace zakázání přes všechny uzly původní škálovací sady. Chcete-li zaručit konzistenci dat, může se měnit pouze jeden osivový uzel. Každá změna osiva uzlu vyžaduje aktualizaci clusteru; proto nahrazení uzlu osiva vyžaduje dva upgrady clusteru (jeden pro přidání uzlu a odebrání). Inovace pěti uzly osiva v tomto ukázkovém scénáři bude mít za následek deset upgradů clusteru.

## <a name="remove-the-original-scale-set"></a>Odebrání původní škálovací sady

Po dokončení operace zakázání odeberte škálovací sadu.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

V Průzkumníku prostředků service fabric se odebrané uzly (a tedy *stav clusteru*) nyní zobrazí ve stavu *Chybová.*

![Průzkumník prostředků infrastruktury služby zobrazující zakázané uzly v chybovém stavu](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Odeberte zastaralé uzly z clusteru Service Fabric a obnovte stav clusteru na *OK*.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Průzkumník prostředků Service Fabric s odebranými uzly v chybovém stavu](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se dozvěděli, jak upgradovat škálovací sady virtuálních počítačů clusteru Service Fabric tak, aby používaly spravované disky, aniž byste během procesu zabraňovali výpadkům služeb. Další informace o souvisejících tématech najdete v následujících zdrojích.

Naučte se:

* [Vertikální navýšení kapacity primárního typu uzlu clusteru Service Fabric](service-fabric-scale-up-node-type.md)

* [Převedení šablony škálovací sady na spravované disky](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Odebrání typu uzlu Service Fabric](service-fabric-how-to-remove-node-type.md)

Viz také:

* [Ukázka: Upgrade uzlů clusteru na spravované disky Azure](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [Důležité informace o vertikálním měřítku](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)