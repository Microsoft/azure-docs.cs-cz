---
title: Postup konfigurace brány firewall protokolu IP pro váš účet Azure Cosmos
description: Zjistěte, jak nakonfigurovat zásady řízení přístupu IP pro podporu brány firewall u účtů databáze Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632573"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Postup konfigurace brány firewall protokolu IP pro váš účet Azure Cosmos

Můžete zabezpečit data uložená ve vašem účtu Azure Cosmos pomocí brány firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí. Brány firewall protokolu IP v účtu Azure Cosmos můžete nastavit pomocí jedné z následujících způsobů:

1. Na webu Azure Portal
2. Deklarativně pomocí šablony Azure Resource Manageru
3. Programově pomocí rozhraní příkazového řádku Azure nebo Azure Powershellu prostřednictvím aktualizace **ipRangeFilter** vlastnost.

## <a id="configure-ip-policy"></a> Konfigurace brány firewall protokolu IP pomocí webu Azure portal

Pokud chcete nastavit zásady řízení přístupu IP na webu Azure Portal, přejděte na stránku účtu služby Azure Cosmos DB, klikněte na **brány Firewall a virtuální sítě** v navigační nabídce, změňte **povolit přístup z** hodnota k **vybrané sítě**a potom klikněte na tlačítko **Uložit**. 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/how-to-configure-firewall/azure-portal-firewall.png)

Jakmile je zapnuté řízení přístupu IP, webu Azure portal poskytuje možnost zadejte IP adresy, rozsahy IP adres, jakož i přepne na povolení přístupu k jiným službám Azure a webu Azure portal. Podrobnosti o těchto přepínačích jsou k dispozici v následujících částech.

> [!NOTE]
> Když povolíte zásady řízení přístupu IP pro váš účet Azure Cosmos, všechny požadavky ke svému účtu Azure Cosmos z počítačů mimo seznamu povolených rozsahů IP adres jsou odmítnuty. Procházení prostředků Azure Cosmos DB z portálu jsou taky možné zablokovat k zajištění integrity řízení přístupu.

### <a name="allow-requests-from-the-azure-portal"></a>Povolení požadavků na webu Azure Portal 

Pokud povolíte zásadu řízení přístupu IP prostřednictvím kódu programu, je potřeba přidat IP adresu na webu Azure portal **ipRangeFilter** vlastnost chcete zachovat přístup. Portálu IP adresy jsou:

|Oblast|IP adresa|
|------|----------|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|Vláda USA|52.244.48.71|
|Všech oblastech kromě výše uvedených tří|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Můžete povolit přístup k webu Azure portal kliknutím **povolit přístup z portálu Azure portal** možnosti, jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak povolit přístup na portál Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Povolit požadavky od globálních Datacenter Azure nebo jiných zdrojů v rámci Azure

Pokud přístup k účtu Azure Cosmos ze služeb, které neposkytují statickou IP adresu, třeba Azure Stream analytics, Azure Functions atd. můžete ji pomocí brány firewall protokolu IP k omezení přístupu k. Pokud chcete povolit přístup k účtu Azure Cosmos z těchto služeb, musíte povolit nastavení brány firewall. Pokud chcete povolit toto nastavení brány firewall, přidejte do seznamu povolených IP adres IP adresa-0.0.0.0. 0.0.0.0 omezuje požadavky ke svému účtu Azure Cosmos z rozsahu IP adres datacentra Azure. Toto nastavení neumožňuje přístup pro jakékoli jiné rozsahy IP adres ke svému účtu Azure Cosmos.

> [!NOTE]
> Tato možnost nakonfiguruje bránu firewall povolit všechny žádosti od Azure včetně požadavků z předplatných ostatních zákazníků nasazené v Azure. Seznam IP adres povolená tato možnost je široké, takže omezuje efektivitu zásady brány firewall. Tato možnost by měla sloužit pouze v případě, že vaše žádosti není pocházejí z statických IP adres nebo podsítí ve virtuálních sítích. Pokud vyberete tuto možnost automaticky umožňuje přístup z portálu Azure portal, protože na webu Azure portal je nasazené v Azure.

Můžete povolit přístup k webu Azure portal kliknutím **přijímat připojení z veřejných datových center Azure** možnosti, jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Požadavky z aktuální IP adresa

Pro zjednodušení vývoje na webu Azure portal vám pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací, tak, aby aplikace spuštění vaše počítače můžou přistupovat k účtu Azure Cosmos. IP adresa klienta automaticky zjistí na portálu. Může být IP adresa klienta vašeho počítače nebo IP adresu brány sítě. Ujistěte se, že chcete odebrat tuto IP adresu před přepnutím do produkčního prostředí svoje úlohy. 

Chcete-li povolit vaši aktuální IP adresu, vyberte **přidat moji aktuální IP adresu**, která přidá vaši aktuální IP adresa na seznam IP adres a potom klikněte na **Uložit**.

![Snímek obrazovky s informacemi postupu konfigurace nastavení brány firewall pro aktuální IP adresa](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Požadavky ze služby cloud services

V Azure cloudové služby jsou běžným způsobem pro hostování logikou střední úrovně služby využívající službu Azure Cosmos DB. Pokud chcete povolit přístup ke svému účtu Azure Cosmos z cloudové služby, veřejnou IP adresu z cloudové služby musí přidat do seznamu povolených IP adres, které jsou spojené s vaším účtem Azure Cosmos pomocí [konfigurace zásad řízení přístupu IP](#configure-ip-policy). Tím se zajistí, že všechny instance rolí cloudové služby mají přístup ke svému účtu Azure Cosmos. IP adresy můžete načíst pro cloudové služby na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro cloudovou službu, zobrazí na webu Azure Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Při horizontálním navýšením kapacity cloudové služby tak, že přidáte další role instance tyto nové instance automaticky mít přístup k účtu Azure Cosmos, protože jsou součástí stejné cloudové službě.

### <a name="requests-from-virtual-machines"></a>Požadavky z virtuálních počítačů

[Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) lze také použít k hostování střední vrstvy služby pomocí služby Azure Cosmos DB. Ke konfiguraci účtu Cosmos pro povolení přístupu z virtuálních počítačů, veřejné IP adresy virtuálního počítače nebo škálovací sady virtuálních počítačů musí být nakonfigurovaný jako jednu z povolených IP adres pro váš účet Azure Cosmos pomocí [konfigurace IP zásada řízení přístupu](#configure-ip-policy). Můžete získat IP adresy pro virtuální počítače na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro virtuální počítač zobrazený na portálu Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Když přidáte instance dalších virtuálních počítačů do skupiny, se automaticky poskytnou přístup k vašemu účtu Azure Cosmos.

### <a name="requests-from-the-internet"></a>Požadavky z Internetu

Při přístupu k účtu Azure Cosmos z počítače na Internetu, klient IP adresu nebo rozsah IP adres počítače musí přidat do seznamu povolených IP adres pro váš účet.

## <a id="configure-ip-firewall-arm"></a>Konfigurace brány firewall protokolu IP pomocí šablony Resource Manageru

Ke konfiguraci řízení přístupu ke svému účtu Azure Cosmos, by měl určit šablony Resource Manageru **ipRangeFilter** atribut se seznamem rozsahů IP adres, které by měly být uvedeny v seznamu povolených. Například přidejte následující kód JSON do šablony:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
     "databaseAccountOfferType": "Standard",
     "name": "[parameters('databaseAccountName')]",
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Konfigurace zásad řízení přístupu IP pomocí Azure CLI

Následující příkaz ukazuje, jak vytvořit účet Azure Cosmos pomocí řízení přístupu IP: 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

Pokud chcete aktualizovat nastavení brány firewall pro existující účet, spusťte následující příkaz:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Řešení potíží s zásady řízení přístupu IP

Problémů se zásadami řízení přístupu IP můžete řešit pomocí následujících možností: 

### <a name="azure-portal"></a>portál Azure 
Když zapnete zásady řízení přístupu IP pro váš účet Azure Cosmos, jsou blokovány všechny požadavky ke svému účtu z počítače mimo seznamu povolených rozsahů IP adres. Proto pokud chcete povolit operace roviny dat portálu třeba k procházení kontejnery a dotazování dokumentů, je potřeba explicitně povolit přístup na portál Azure pomocí **brány Firewall** podokně na portálu.

### <a name="sdks"></a>Sady SDK 
Při přístupu k prostředkům Azure Cosmos pomocí sady SDK z počítače, které nejsou na seznamu povolených **je vrácena obecná 404 odpověď nebyla nalezena se žádné další podrobnosti**. Ověření seznamu povolených IP pro svůj účet a ujistěte se, že je konfigurace správná zásad zaúčtované k vašemu účtu Cosmos. 

### <a name="check-source-ips-in-blocked-requests"></a>Zkontrolujte zdrojové IP adresy v Blokované požadavky
Povolit protokolování diagnostiky ve vašem účtu Azure Cosmos, tyto protokoly by ukazují jednotlivých požadavků a odpovědí. **Zprávy týkající se brány firewall se protokolují interně 403 návratový kód**. Pomocí filtrování tyto zprávy, uvidíte zdrojové IP adresy pro blokované požadavky. Zobrazit [protokolování diagnostiky služby Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Požadavky z podsítě se koncový bod služby pro databázi Azure Cosmos povoleno
Požadavky z podsítě ve virtuální síti, která má koncový bod služby pro službu Azure Cosmos DB povolené odešle virtuální síť a podsíť identity k účtům Azure Cosmos. Tyto požadavky nemají veřejné IP adresy zdroje, tak odmítnuté filtrů IP adres. Chcete-li povolit přístup z konkrétních podsítí ve virtuálních sítích, přidejte seznam řízení přístupu virtuální síť uvedené v [konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md). Může trvat až 15 minut pro pravidla brány firewall, chcete-li použít.


## <a name="next-steps"></a>Další postup

Pokud chcete nakonfigurovat koncový bod služby virtuální sítě pro váš účet Azure Cosmos DB, naleznete v následujících článcích:

* [Virtuální síť a podsíť řízení přístupu pro váš účet Azure Cosmos](vnet-service-endpoint.md)
* [Konfigurace virtuální sítě a podsítě na základě přístupu pro váš účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md)


