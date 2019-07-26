---
title: Konfigurace brány firewall protokolu IP pro účet Azure Cosmos DB
description: Naučte se konfigurovat zásady řízení přístupu IP pro podporu brány firewall na Azure Cosmos DB databázových účtech.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/25/2019
ms.author: mjbrown
ms.openlocfilehash: 0b8ad6c5addbff293e9f7e9b8af6ed34d4dd274b
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494880"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurace brány firewall protokolu IP v Azure Cosmos DB

Data uložená ve vašem účtu Azure Cosmos DB můžete zabezpečit pomocí bran firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Bránu firewall protokolu IP můžete na účtu Azure Cosmos DB nastavit pomocí některého z následujících způsobů:

* Z Azure Portal
* Deklarativně pomocí šablony Azure Resource Manager
* Prostřednictvím kódu programu pomocí Azure CLI nebo Azure PowerShell aktualizací vlastnosti **ipRangeFilter**

## <a id="configure-ip-policy"></a>Konfigurace brány firewall protokolu IP pomocí Azure Portal

Pokud chcete nastavit zásadu řízení přístupu k IP adresám v Azure Portal, přejděte na stránku Azure Cosmos DB účet a v navigační nabídce vyberte **firewall a virtuální sítě** . Změňte hodnotu **Povolení přístupu z** hodnoty na **vybrané sítě**a potom vyberte **Uložit**. 

![Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Když je řízení přístupu IP zapnuté, Azure Portal poskytuje možnost zadat IP adresy, rozsahy IP adres a přepínače. Přepínače povolují přístup k dalším službám Azure a Azure Portal. Následující části obsahují podrobné informace o těchto přepínačích.

> [!NOTE]
> Po povolení zásad řízení přístupu IP pro účet Azure Cosmos DB se odmítnou všechny požadavky na účet Azure Cosmos DB z počítačů mimo povolený Seznam rozsahů IP adres. Procházení prostředků Azure Cosmos DB z portálu je taky blokované, aby se zajistila integrita řízení přístupu.

### <a name="allow-requests-from-the-azure-portal"></a>Povolení žádostí z Azure Portal

Pokud zásadu řízení přístupu k IP adrese povolíte programově, je nutné přidat IP adresu pro Azure Portal do vlastnosti **ipRangeFilter** pro zachování přístupu. IP adresy portálu:

|Oblast|IP adresa|
|------|----------|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|US Gov|52.244.48.71|
|Všechny ostatní oblasti|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Přístup k Azure Portal můžete povolit tak, že vyberete možnost **Povolit přístup z Azure Portal** , jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak povolit přístup k Azure Portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Povoluje žádosti z globálních datových center Azure nebo jiných zdrojů v Azure.

Pokud k účtu Azure Cosmos DB přistupujete ze služeb, které neposkytují statickou IP adresu (například Azure Stream Analytics a Azure Functions), můžete k omezení přístupu používat i bránu firewall protokolu IP. Pokud chcete povolit přístup k účtu Azure Cosmos DB z těchto služeb, přidejte IP adresu 0.0.0.0 do seznamu povolených IP adres. Adresa 0.0.0.0 omezuje požadavky na účet Azure Cosmos DB z rozsahu IP adres datacentra Azure. Toto nastavení nepovoluje přístup k vašemu Azure Cosmos DB účtu pro žádné jiné rozsahy IP adres.

> [!NOTE]
> Tato možnost nakonfiguruje bránu firewall tak, aby povolovala všechny požadavky z Azure, včetně žádostí od předplatných ostatních zákazníků nasazených v Azure. Seznam IP adres povolených touto možností je roztažitelné, takže omezuje efektivitu zásad brány firewall. Tuto možnost použijte jenom v případě, že vaše požadavky nepocházejí ze statických IP adres nebo podsítí ve virtuálních sítích. Volba této možnosti automaticky povolí přístup z Azure Portal, protože Azure Portal je nasazená v Azure.

Přístup k Azure Portal můžete povolit výběrem možnosti **přijmout připojení z veřejné datacentra Azure** , jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Požadavky z vaší aktuální IP adresy

Z důvodu zjednodušení vývoje vám Azure Portal pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací. Aplikace, na kterých běží váš počítač, můžou mít přístup k vašemu Azure Cosmos DB účtu. 

Portál automaticky rozpozná IP adresu klienta. Může to být IP adresa klienta vašeho počítače nebo IP adresa vaší síťové brány. Nezapomeňte odebrat tuto IP adresu, aby bylo možné převzít své úlohy do produkčního prostředí. 

Pokud chcete do seznamu IP adres přidat aktuální IP adresu, vyberte **Přidat moji aktuální IP adresu**. Potom vyberte **Uložit**.

![Snímek obrazovky, který ukazuje, jak nakonfigurovat nastavení brány firewall pro aktuální IP adresu](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Žádosti z cloudových služeb

V Azure jsou Cloud Services běžným způsobem hostování logiky služby střední vrstvy pomocí Azure Cosmos DB. Pokud chcete povolit přístup k vašemu Azure Cosmos DB účtu z cloudové služby, musíte do seznamu povolených IP adres přidružených k vašemu účtu Azure Cosmos DB přidat veřejnou IP adresu cloudové služby, a to tak, že nakonfigurujete [zásady řízení přístupu IP](#configure-ip-policy). Tím se zajistí, že všechny instance rolí Cloud Services mají přístup k vašemu účtu Azure Cosmos DB. 

IP adresy pro cloudové služby můžete načíst v Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresou pro cloudovou službu zobrazenou v Azure Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Při horizontálním navýšení kapacity cloudové služby přidáním instancí rolí budou mít tyto nové instance automaticky přístup k Azure Cosmos DBmu účtu, protože jsou součástí stejné cloudové služby.

### <a name="requests-from-virtual-machines"></a>Žádosti z virtuálních počítačů

Můžete také použít [virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) k hostování služeb střední vrstvy pomocí Azure Cosmos DB. Pokud chcete nakonfigurovat účet Cosmos DB tak, aby umožňoval přístup z virtuálních počítačů, musíte nakonfigurovat veřejnou IP adresu virtuálního počítače nebo sady škálování virtuálního počítače jako jednu z povolených IP adres pro váš účet Azure Cosmos DB [konfigurací. zásada řízení přístupu IP](#configure-ip-policy). 

Můžete načíst IP adresy pro virtuální počítače v Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresou pro virtuální počítač zobrazený v Azure Portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Když do skupiny přidáte instance virtuálních počítačů, automaticky získají přístup k vašemu Azure Cosmos DB účtu.

### <a name="requests-from-the-internet"></a>Žádosti z Internetu

Když k účtu Azure Cosmos DB přistupujete z počítače na internetu, musí se do seznamu povolených IP adres pro váš účet přidat IP adresa klienta nebo rozsah IP adres.

## <a id="configure-ip-firewall-arm"></a>Konfigurace brány firewall protokolu IP pomocí šablony Správce prostředků

Pokud chcete nakonfigurovat řízení přístupu ke svému účtu Azure Cosmos DB, ujistěte se, že šablona Správce prostředků určuje atribut **ipRangeFilter** seznamem povolených ROZSAHŮ IP adres. Pokud konfigurujete bránu firewall protokolu IP pro již nasazený účet `locations` Cosmos, ujistěte se, že pole odpovídá aktuálně nasazenému účtu. Nemůžete současně upravovat `locations` pole a další vlastnosti. Další informace a ukázky šablon ARM pro Azure Cosmos DB najdete v [Azure Resource Manager šablony pro Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2016-03-31",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "enableMultipleWriteLocations": "[parameters('multipleWriteLocations')]",
    "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a id="configure-ip-firewall-cli"></a>Konfigurace zásad řízení přístupu IP pomocí Azure CLI

Následující příkaz ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Chcete-li aktualizovat nastavení brány firewall pro existující účet, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="configure-ip-firewall-ps"></a>Konfigurace zásad řízení přístupu IP pomocí prostředí PowerShell

Následující skript ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu IP:

```azurepowershell-interactive

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

# Add local machine's IP address to firewall, InterfaceAlias is your Network Adapter's name
$ipRangeFilter = Get-NetIPConfiguration | Where-Object InterfaceAlias -eq "Ethernet 2" | Select-Object IPv4Address

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "ipRangeFilter"=$ipRangeFilter
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="troubleshoot-ip-firewall"></a>Řešení potíží se zásadami řízení přístupu k IP adresám

Problémy se zásadami řízení přístupu k IP adresám můžete řešit pomocí následujících možností: 

### <a name="azure-portal"></a>portál Azure 
Když zapnete zásadu řízení přístupu IP pro účet Azure Cosmos DB, zablokují se všechny požadavky na váš účet z počítačů mimo povolený Seznam rozsahů IP adres. Pokud chcete povolit operace s datovou rovinou portálu, jako jsou kontejnery procházení a dotazování na dokumenty, musíte explicitně povolit přístup k Azure Portal pomocí podokna **firewall** na portálu.

### <a name="sdks"></a>Sady SDK 
Při přístupu k prostředkům Azure Cosmos DB pomocí sad SDK z počítačů, které nejsou v seznamu povolených, je vrácena obecná odpověď **403** , která neobsahuje žádné další podrobnosti. Ověřte seznam povolených IP adres pro váš účet a ujistěte se, že se pro váš Azure Cosmos DB účet používá správná konfigurace zásad. 

### <a name="source-ips-in-blocked-requests"></a>Zdrojové IP adresy v blokovaných žádostech
Povolte protokolování diagnostiky v účtu Azure Cosmos DB. Tyto protokoly zobrazují každý požadavek a odpověď. Zprávy týkající se brány firewall jsou protokolovány pomocí návratového kódu 403. Filtrováním těchto zpráv můžete zobrazit zdrojové IP adresy blokovaných požadavků. Viz [protokolování diagnostiky Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Žádosti z podsítě s koncovým bodem služby pro Azure Cosmos DB povoleny
Žádosti z podsítě ve virtuální síti s povoleným koncovým bodem služby pro Azure Cosmos DB odesílají virtuální síť a identitu podsítě pro Azure Cosmos DB účty. Tyto požadavky nemají veřejnou IP adresu zdroje, takže filtry IP je odmítnou. Chcete-li v rámci virtuálních sítí dovolit přístup z určitých podsítí, přidejte seznam řízení přístupu, jak je uvedeno v tématu [jak nakonfigurovat virtuální síť a přístup založený na podsíti pro účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Použití pravidel brány firewall může trvat až 15 minut.


## <a name="next-steps"></a>Další kroky

Chcete-li nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos DB, přečtěte si následující články:

* [Řízení přístupu k virtuální síti a podsíti pro váš Azure Cosmos DB účet](vnet-service-endpoint.md)
* [Konfigurace přístupu na základě virtuální sítě a podsítě pro účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)

