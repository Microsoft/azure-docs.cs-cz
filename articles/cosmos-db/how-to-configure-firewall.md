---
title: Konfigurace brány firewall protokolu IP pro účet Azure Cosmos DB
description: Naučte se konfigurovat zásady řízení přístupu IP pro podporu brány firewall na účtech Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 69c39d2478ed7d488c1209c2c7e16c241c59bcef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88814174"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurace brány firewall protokolu IP v Azure Cosmos DB

Data uložená ve vašem účtu Azure Cosmos DB můžete zabezpečit pomocí bran firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adres pro podporu příchozích bran firewall. Bránu firewall protokolu IP můžete na účtu Azure Cosmos DB nastavit pomocí některého z následujících způsobů:

* Pomocí webu Azure Portal
* Deklarativně pomocí šablony Azure Resource Manageru
* Prostřednictvím kódu programu pomocí Azure CLI nebo Azure PowerShell aktualizací vlastnosti **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurace brány firewall protokolu IP pomocí Azure Portal

Pokud chcete nastavit zásadu řízení přístupu k IP adresám v Azure Portal, přejděte na stránku Azure Cosmos DB účet a v navigační nabídce vyberte **firewall a virtuální sítě** . Změňte hodnotu **Povolení přístupu z** hodnoty na **vybrané sítě**a potom vyberte **Uložit**.

:::image type="content" source="./media/how-to-configure-firewall/azure-portal-firewall.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

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

Žádosti o přístup k Azure Portal můžete povolit tak, že vyberete možnost **Povolit přístup z Azure Portal** , jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-portal.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Povolení požadavků z globálních datacenter Azure nebo jiných zdrojů v rámci Azure

Pokud k účtu Azure Cosmos DB přistupujete ze služeb, které neposkytují statickou IP adresu (například Azure Stream Analytics a Azure Functions), můžete k omezení přístupu používat i bránu firewall protokolu IP. Přístup z jiných zdrojů v rámci Azure můžete povolit tak, že v **datacentrech Azure vyberete možnost přijmout připojení** , jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/how-to-configure-firewall/enable-azure-services.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

Pokud povolíte tuto možnost, IP adresa `0.0.0.0` se přidá do seznamu povolených IP adres. `0.0.0.0`IP adresa omezuje požadavky na účet Azure Cosmos DB z rozsahu IP adres datacentra Azure. Toto nastavení neumožňuje přístup k účtu služby Azure Cosmos DB z žádného jiného rozsahu IP adres.

> [!NOTE]
> Tato možnost nakonfiguruje bránu firewall tak, aby povolovala všechny požadavky z Azure, včetně žádostí od předplatných ostatních zákazníků nasazených v Azure. Seznam IP adres povolených touto možností je roztažitelné, takže omezuje efektivitu zásad brány firewall. Tuto možnost použijte jenom v případě, že vaše požadavky nepocházejí ze statických IP adres nebo podsítí ve virtuálních sítích. Volba této možnosti automaticky povolí přístup z Azure Portal, protože Azure Portal je nasazená v Azure.

### <a name="requests-from-your-current-ip"></a>Požadavky z vaší aktuální IP adresy

Z důvodu zjednodušení vývoje vám Azure Portal pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací. Aplikace, na kterých běží váš počítač, můžou mít přístup k vašemu Azure Cosmos DB účtu.

Portál automaticky rozpozná IP adresu klienta. Může to být IP adresa klienta vašeho počítače nebo IP adresa vaší síťové brány. Nezapomeňte odebrat tuto IP adresu, aby bylo možné převzít své úlohy do produkčního prostředí.

Pokud chcete do seznamu IP adres přidat aktuální IP adresu, vyberte **Přidat moji aktuální IP adresu**. Pak vyberte **Uložit**.

:::image type="content" source="./media/how-to-configure-firewall/enable-current-ip.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

### <a name="requests-from-cloud-services"></a>Žádosti z cloudových služeb

V Azure jsou Cloud Services běžným způsobem hostování logiky služby střední vrstvy pomocí Azure Cosmos DB. Pokud chcete povolit přístup k vašemu Azure Cosmos DB účtu z cloudové služby, musíte do seznamu povolených IP adres přidružených k vašemu účtu Azure Cosmos DB přidat veřejnou IP adresu cloudové služby, a to tak, že [nakonfigurujete zásady řízení přístupu IP](#configure-ip-policy). Tím se zajistí, že všechny instance rolí Cloud Services mají přístup k vašemu účtu Azure Cosmos DB.

IP adresy pro cloudové služby můžete načíst v Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

Při horizontálním navýšení kapacity cloudové služby přidáním instancí rolí budou mít tyto nové instance automaticky přístup k Azure Cosmos DBmu účtu, protože jsou součástí stejné cloudové služby.

### <a name="requests-from-virtual-machines"></a>Žádosti z virtuálních počítačů

Můžete také použít [virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo službu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) k hostování služeb střední vrstvy pomocí Azure Cosmos DB. Pokud chcete nakonfigurovat účet Cosmos DB tak, aby umožňoval přístup z virtuálních počítačů, musíte nakonfigurovat veřejnou IP adresu virtuálního počítače nebo sady škálování virtuálního počítače jako jednu z povolených IP adres pro účet Azure Cosmos DB [konfigurací zásad řízení přístupu IP](#configure-ip-policy).

Můžete načíst IP adresy pro virtuální počítače v Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/how-to-configure-firewall/public-ip-addresses-dns.png" alt-text="Snímek obrazovky ukazující, jak otevřít stránku brány firewall v Azure Portal":::

Když do skupiny přidáte instance virtuálních počítačů, automaticky získají přístup k vašemu Azure Cosmos DB účtu.

### <a name="requests-from-the-internet"></a>Žádosti z Internetu

Když k účtu Azure Cosmos DB přistupujete z počítače na internetu, musí se do seznamu povolených IP adres pro váš účet přidat IP adresa klienta nebo rozsah IP adres.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurace brány firewall protokolu IP pomocí šablony Správce prostředků

Pokud chcete nakonfigurovat řízení přístupu k účtu Azure Cosmos DB, ujistěte se, že šablona Správce prostředků určuje vlastnost **ipRules** s polem s povolenými rozsahy IP adres. Pokud konfigurujete bránu firewall protokolu IP pro již nasazený účet Cosmos, ujistěte se, že `locations` pole odpovídá aktuálně nasazenému účtu. Nemůžete současně upravovat `locations` pole a další vlastnosti. Další informace a ukázky šablon Azure Resource Manager pro Azure Cosmos DB najdete v [šablonách Azure Resource Manager pro Azure Cosmos DB](resource-manager-samples.md)

> [!IMPORTANT]
> Vlastnost **ipRules** byla představena s rozhraním API verze 2020-04-01. Předchozí verze vystavily namísto toho vlastnost **ipRangeFilter** , což je seznam IP adres oddělených čárkami.

Následující příklad ukazuje, jak je vlastnost **ipRules** vystavena v rozhraní API verze 2020-04-01 nebo novější:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2020-04-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRules": [
      {
        "ipAddressOrRange": "40.76.54.131"
      },
      {
        "ipAddressOrRange": "52.176.6.30"
      },
      {
        "ipAddressOrRange": "52.169.50.45"
      },
      {
        "ipAddressOrRange": "52.187.184.26"
      }
    ]
  }
}
```

Zde je stejný příklad pro libovolnou verzi rozhraní API před 2020-04-01:

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurace zásad řízení přístupu IP pomocí Azure CLI

Následující příkaz ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu IP:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurace zásad řízení přístupu IP pomocí prostředí PowerShell

Následující skript ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu IP:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRules = @("192.168.221.17","183.240.196.255","40.76.54.131")

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRules"=$ipRules
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2020-04-01" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Řešení potíží se zásadami řízení přístupu k IP adresám

Problémy se zásadami řízení přístupu k IP adresám můžete řešit pomocí následujících možností:

### <a name="azure-portal"></a>portál Azure

Když zapnete zásadu řízení přístupu IP pro účet Azure Cosmos DB, zablokují se všechny požadavky na váš účet z počítačů mimo povolený Seznam rozsahů IP adres. Pokud chcete povolit operace s datovou rovinou portálu, jako jsou kontejnery procházení a dotazování na dokumenty, musíte explicitně povolit přístup k Azure Portal pomocí podokna **firewall** na portálu.

### <a name="sdks"></a>Sady SDK

Při přístupu k prostředkům Azure Cosmos DB pomocí sad SDK z počítačů, které nejsou v seznamu povolených, je vrácena obecná odpověď **403** , která neobsahuje žádné další podrobnosti. Ověřte seznam povolených IP adres pro váš účet a ujistěte se, že se pro váš Azure Cosmos DB účet používá správná konfigurace zásad.

### <a name="source-ips-in-blocked-requests"></a>Zdrojové IP adresy v blokovaných žádostech

Povolte protokolování diagnostiky v účtu Azure Cosmos DB. Tyto protokoly zobrazují každý požadavek a odpověď. Zprávy týkající se brány firewall jsou protokolovány pomocí návratového kódu 403. Filtrováním těchto zpráv můžete zobrazit zdrojové IP adresy blokovaných požadavků. Viz [protokolování diagnostiky Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Žádosti z podsítě s koncovým bodem služby pro Azure Cosmos DB povoleny

Žádosti z podsítě ve virtuální síti s povoleným koncovým bodem služby pro Azure Cosmos DB odesílají virtuální síť a identitu podsítě pro Azure Cosmos DB účty. Tyto požadavky nemají veřejnou IP adresu zdroje, takže filtry IP je odmítnou. Chcete-li v rámci virtuálních sítí dovolit přístup z určitých podsítí, přidejte seznam řízení přístupu, jak je uvedeno v tématu [jak nakonfigurovat virtuální síť a přístup založený na podsíti pro účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Použití pravidel brány firewall může trvat až 15 minut.

### <a name="private-ip-addresses-in-list-of-allowed-addresses"></a>Privátní IP adresy v seznamu povolených adres

Vytvoření nebo aktualizace účtu Azure Cosmos se seznamem povolených adres, které obsahují soukromé IP adresy, se nezdaří. Ujistěte se, že se v seznamu nezadala žádná privátní IP adresa.

## <a name="next-steps"></a>Další kroky

Chcete-li nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos DB, přečtěte si následující články:

* [Řízení přístupu k virtuální síti a podsíti pro váš Azure Cosmos DB účet](vnet-service-endpoint.md)
* [Konfigurace přístupu na základě virtuální sítě a podsítě pro účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
