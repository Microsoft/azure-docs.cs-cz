---
title: Konfigurace brány firewall IP pro váš účet Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat zásady řízení přístupu IP pro podporu brány firewall na účtech Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162940"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurace brány firewall IP v Azure Cosmos DB

Data uložená v účtu Azure Cosmos DB můžete zabezpečit pomocí ip firewallů. Azure Cosmos DB podporuje ovládací prvky přístupu založené na PROTOKOLU IP pro příchozí podporu brány firewall. Bránu firewall IP na účtu Azure Cosmos DB můžete nastavit jedním z následujících způsobů:

* Pomocí webu Azure Portal
* Deklarativně pomocí šablony Azure Resource Manageru
* Programově prostřednictvím rozhraní NASTAVENÍ Azure nebo prostředí Azure PowerShell aktualizací vlastnosti **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Konfigurace brány firewall IP pomocí portálu Azure

Pokud chcete nastavit zásady řízení přístupu k IP adresám na webu Azure Portal, přejděte na stránku účtu Azure Cosmos DB a v navigační nabídce vyberte **bránu firewall a virtuální sítě.** Změňte hodnotu **Povolit přístup z** **vybraných sítí**a pak vyberte **Uložit**.

![Snímek obrazovky znázorňující, jak otevřít stránku Firewall na webu Azure Portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Když je zapnutá kontrola přístupu k IP adresám, portál Azure poskytuje možnost zadat IP adresy, rozsahy IP adres a přepínače. Přepínače umožňují přístup k dalším službám Azure a portálu Azure. V následujících částech jsou uvedeny podrobnosti o těchto přepínačích.

> [!NOTE]
> Po povolení zásad řízení přístupu k IP pro váš účet Azure Cosmos DB jsou odmítnuty všechny požadavky na váš účet Azure Cosmos DB z počítačů mimo povolený seznam rozsahů IP adres. Procházení prostředků Azure Cosmos DB z portálu je také blokován, aby byla zajištěna integrita řízení přístupu.

### <a name="allow-requests-from-the-azure-portal"></a>Povolení požadavků z webu Azure Portal

Když programově povolíte zásadu řízení přístupu k IP adresám, musíte přidat IP adresu pro portál Azure do vlastnosti **ipRangeFilter,** abyste mohli udržovat přístup. IP adresy portálu jsou:

|Region (Oblast)|IP adresa|
|------|----------|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|US Gov|52.244.48.71|
|Všechny ostatní regiony|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Požadavky na přístup k portálu Azure můžete povolit tak, že vyberete možnost **Povolit přístup z portálu Azure,** jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky znázorňující, jak povolit přístup k Portálu Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Povolení požadavků z globálních datacenter Azure nebo jiných zdrojů v rámci Azure

Pokud k účtu Azure Cosmos DB přistupujete ze služeb, které neposkytují statickou IP adresu (například Azure Stream Analytics a Azure Functions), můžete k omezení přístupu použít ip bránu firewall. Přístup z jiných zdrojů v rámci Azure můžete povolit výběrem **možnosti Přijmout připojení z datových center Azure,** jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky znázorňující, jak otevřít stránku Firewall na webu Azure Portal](./media/how-to-configure-firewall/enable-azure-services.png)

Pokud tuto možnost povolíte, `0.0.0.0` bude adresa IP přidána do seznamu povolených adres IP. IP `0.0.0.0` adresa omezuje požadavky na váš účet Azure Cosmos DB z rozsahu IP adres datového centra Azure. Toto nastavení neumožňuje přístup k účtu služby Azure Cosmos DB z žádného jiného rozsahu IP adres.

> [!NOTE]
> Tato možnost nakonfiguruje bránu firewall tak, aby umožňovala všechny požadavky z Azure, včetně požadavků z předplatných jiných zákazníků nasazených v Azure. Seznam ip služeb povolených touto možností je široký, takže omezuje účinnost zásad brány firewall. Tuto možnost použijte pouze v případě, že vaše požadavky nepocházejí ze statických IP adres nebo podsítí ve virtuálních sítích. Výběr této možnosti automaticky umožňuje přístup z webu Azure Portal, protože portál Azure se nasadí v Azure.

### <a name="requests-from-your-current-ip"></a>Požadavky z aktuální IP adresy

Pro zjednodušení vývoje vám portál Azure pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených. Aplikace se spuštěnou vaší počítače pak přístup k účtu Azure Cosmos DB.

Portál automaticky detekuje IP adresu klienta. Může se jedná o IP adresu klienta vašeho počítače nebo IP adresu síťové brány. Než přenesete své úlohy do produkčního prostředí, nezapomeňte tuto adresu IP odebrat.

Chcete-li přidat aktuální IP adresu do seznamu IP adres, vyberte **Přidat aktuální IP adresu**. Potom vyberte **Uložit**.

![Snímek obrazovky s konfigurací nastavení brány firewall pro aktuální IP adresu](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Požadavky od cloudových služeb

V Azure jsou cloudové služby běžným způsobem hostování logiky služeb střední vrstvy pomocí Azure Cosmos DB. Chcete-li povolit přístup k účtu Azure Cosmos DB z cloudové služby, musíte přidat veřejnou IP adresu cloudové služby do seznamu povolených IP adres přidružených k vašemu účtu Azure Cosmos DB [konfigurací zásad řízení přístupu k IP.](#configure-ip-policy) Tím zajistíte, že všechny instance rolí cloudových služeb mají přístup k vašemu účtu Azure Cosmos DB.

IP adresy pro cloudové služby můžete načíst na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresou cloudové služby zobrazenou na webu Azure Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Když navertíte cloudovou službu přidáním instancí rolí, tyto nové instance budou mít automaticky přístup k účtu Azure Cosmos DB, protože jsou součástí stejné cloudové služby.

### <a name="requests-from-virtual-machines"></a>Požadavky z virtuálních počítačů

[Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) můžete také použít k hostování služeb střední vrstvy pomocí Azure Cosmos DB. Chcete-li nakonfigurovat účet Cosmos DB tak, aby umožňoval přístup z virtuálních počítačů, musíte nakonfigurovat veřejnou IP adresu virtuálního počítače a/nebo škálovací sadu virtuálních počítačů jako jednu z povolených IP adres pro váš účet Azure Cosmos DB [konfigurací zásad řízení přístupu k IP.](#configure-ip-policy)

Ip adresy pro virtuální počítače můžete načíst na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresou virtuálního počítače zobrazenou na webu Azure Portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Když do skupiny přidáte instance virtuálních strojů, automaticky získají přístup k vašemu účtu Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Žádosti z internetu

Při přístupu k účtu Azure Cosmos DB z počítače na internetu musí být ip adresa klienta nebo rozsah IP adres počítače přidándo seznamu povolených IP adres pro váš účet.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurace brány firewall IP pomocí šablony Správce prostředků

Chcete-li nakonfigurovat řízení přístupu k účtu Azure Cosmos DB, ujistěte se, že šablona Správce prostředků určuje atribut **ipRangeFilter** se seznamem povolených rozsahů IP adres. Pokud konfigurujete ip firewall na již `locations` nasazený účet Cosmos, ujistěte se, že pole odpovídá aktuálně nasazeným hodnotám. Pole a další `locations` vlastnosti nelze současně změnit. Další informace a ukázky šablon Azure Resource Manageru pro Azure Cosmos DB najdete v [tématu Šablony Azure Resource Manageru pro Azure Cosmos DB](resource-manager-samples.md)

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

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurace zásad řízení přístupu k IP pomocí rozhraní příkazového příkazu Azure

Následující příkaz ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu k IP adresám:

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

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurace zásad řízení přístupu k PROTOKOLU IP pomocí prostředí PowerShell

Následující skript ukazuje, jak vytvořit účet Azure Cosmos DB s řízením přístupu k IP adresám:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Řešení problémů se zásadami řízení přístupu k IP protokolu IP

Problémy se zásadami řízení přístupu k ip můžete vyřešit pomocí následujících možností:

### <a name="azure-portal"></a>portál Azure

Povolením zásad řízení přístupu k IP pro váš účet Azure Cosmos DB zablokujete všechny požadavky na váš účet z počítačů mimo povolený seznam rozsahů IP adres. Chcete-li povolit operace datové roviny portálu, jako je procházení kontejnerů a dotazování na dokumenty, musíte explicitně povolit přístup k portálu Azure portal pomocí podokna **brány firewall** na portálu.

### <a name="sdks"></a>Sady SDK

Při přístupu k prostředkům Azure Cosmos DB pomocí sad SDK z počítačů, které nejsou v seznamu povolených, obecné **403 Zakázané** odpovědi je vrácena bez dalších podrobností. Ověřte povolený seznam IP adres pro váš účet a ujistěte se, že je na váš účet Azure Cosmos DB použita správná konfigurace zásad.

### <a name="source-ips-in-blocked-requests"></a>Zdrojové IP adresy v blokovaných požadavcích

Povolte diagnostické protokolování na vašem účtu Azure Cosmos DB. Tyto protokoly zobrazit každý požadavek a odpověď. Zprávy související s bránou firewall jsou protokolovány s návratovým kódem 403. Filtrováním těchto zpráv můžete zobrazit zdrojové IP adresy pro blokované požadavky. Viz [Protokolování diagnostiky Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Požadavky z podsítě s povoleným koncovým bodem služby pro Azure Cosmos DB

Požadavky z podsítě ve virtuální síti, která má koncový bod služby pro Azure Cosmos DB povoleno odešle virtuální síť a identitu podsítě na účty Azure Cosmos DB. Tyto požadavky nemají veřejnou IP adresu zdroje, takže je filtry IP odmítnou. Chcete-li povolit přístup z konkrétních podsítí ve virtuálních sítích, přidejte seznam řízení přístupu, jak je uvedeno v [poli Konfigurace virtuální sítě a přístupu na bázi podsítě pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Může trvat až 15 minut, než se pravidla brány firewall použijí.

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat koncový bod virtuální síťové služby pro váš účet Azure Cosmos DB, přečtěte si následující články:

* [Řízení přístupu virtuální sítě a podsítě pro váš účet Azure Cosmos DB](vnet-service-endpoint.md)
* [Konfigurace přístupu k virtuální síti a podsíti pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
