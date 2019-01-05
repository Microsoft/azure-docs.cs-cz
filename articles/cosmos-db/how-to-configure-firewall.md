---
title: Konfigurovat bránu firewall protokolu IP pro váš účet Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat zásady řízení přístupu IP pro podporu brány firewall u účtů databáze Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 7d451f7eae16426c85ed5540b35993cd9b218b83
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033141"
---
# <a name="configure-an-ip-firewall-for-your-azure-cosmos-db-account"></a>Konfigurovat bránu firewall protokolu IP pro váš účet Azure Cosmos DB

Můžete zabezpečit data uložená v účtu služby Azure Cosmos DB s použitím branách firewall protokolu IP. Azure Cosmos DB podporuje řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí. Brány firewall protokolu IP můžete nastavit na účet služby Azure Cosmos DB pomocí jedné z následujících způsobů:

* Na webu Azure Portal
* Deklarativně pomocí šablony Azure Resource Manageru
* Programově pomocí rozhraní příkazového řádku Azure nebo Azure Powershellu prostřednictvím aktualizace **ipRangeFilter** vlastnost

## <a id="configure-ip-policy"></a> Konfigurovat bránu firewall protokolu IP pomocí webu Azure portal

Pokud chcete nastavit zásady řízení přístupu IP na webu Azure Portal, přejděte na stránku účtu služby Azure Cosmos DB a vyberte **brány Firewall a virtuální sítě** v navigační nabídce. Změnit **povolit přístup z** hodnota, která se **vybrané sítě**a pak vyberte **Uložit**. 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/how-to-configure-firewall/azure-portal-firewall.png)

Pokud je zapnuté řízení přístupu IP, na webu Azure portal umožňuje zadat IP adres, rozsahy IP adres a přepínače. Přepínače povolení přístupu k jiným službám Azure a webu Azure portal. Následující části obsahují podrobné informace o těchto přepínačích.

> [!NOTE]
> Když povolíte zásadu řízení přístupu IP pro váš účet Azure Cosmos DB, všechny požadavky na účtu služby Azure Cosmos DB z počítačů mimo seznamu povolených rozsahů IP adres jsou odmítnuty. Procházení prostředků Azure Cosmos DB z portálu se taky zablokuje k zajištění integrity řízení přístupu.

### <a name="allow-requests-from-the-azure-portal"></a>Povolení požadavků na webu Azure Portal 

Pokud povolíte zásadu řízení přístupu IP prostřednictvím kódu programu, je potřeba přidat IP adresu na webu Azure portal **ipRangeFilter** vlastnost chcete zachovat přístup. Portálu IP adresy jsou:

|Oblast|IP adresa|
|------|----------|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|Vláda USA|52.244.48.71|
|Všechny ostatní oblasti|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Můžete povolit přístup k webu Azure portal tak, že vyberete **povolit přístup z portálu Azure portal** možnosti, jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak povolit přístup na portál Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Povolit požadavky od globálních Datacenter Azure nebo jiných zdrojů v rámci Azure

Pokud přístup k účtu služby Azure Cosmos DB ze služeb, které neposkytují statickou IP adresu (například Azure Stream Analytics a Azure Functions), můžete stále použít brány firewall protokolu IP k omezení přístupu. Pokud chcete povolit přístup k účtu Azure Cosmos DB z těchto služeb, přidejte do seznamu povolených IP adres IP adresu 0.0.0.0. Hodnotu 0.0.0.0 adresu omezuje požadavky na účtu služby Azure Cosmos DB z rozsahu IP adres datacentra Azure. Toto nastavení neumožňuje přístup pro jakékoli jiné rozsahy IP adres ke svému účtu Azure Cosmos DB.

> [!NOTE]
> Tato možnost nakonfiguruje bránu firewall tak, aby všechny požadavky ze Azure, včetně požadavků z předplatných ostatních zákazníků nasazené v Azure. Seznam IP adres povolená tato možnost je široké, omezuje efektivitu zásady brány firewall. Tuto možnost použijte jenom v případě, že vaše žádosti není pocházejí z statických IP adres nebo podsítí ve virtuálních sítích. Pokud vyberete tuto možnost automaticky umožňuje přístup z portálu Azure portal, protože na webu Azure portal je nasazené v Azure.

Můžete povolit přístup k webu Azure portal tak, že vyberete **přijímají připojení z v rámci veřejných datových centrech Azure** možnosti, jak je znázorněno na následujícím snímku obrazovky: 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Požadavky z aktuální IP adresa

Pro zjednodušení vývoje na webu Azure portal vám pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací. Aplikace, které běží vaše počítače pak můžou účtu služby Azure Cosmos DB. 

Na portálu automaticky detekuje IP adresu klienta. Může být IP adresa klienta vašeho počítače nebo IP adresu brány sítě. Ujistěte se, že chcete odebrat tuto IP adresu, než se rozhodnete svoje úlohy do produkčního prostředí. 

Chcete-li přidat vaši aktuální IP adresa na seznam IP adres, **přidat moji aktuální IP adresu**. Potom vyberte **Uložit**.

![Snímek obrazovky s informacemi postupu konfigurace nastavení brány firewall pro aktuální IP adresa](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Požadavky ze služby cloud services

V Azure cloudové služby jsou běžným způsobem pro hostování střední vrstvy služby logiky pomocí služby Azure Cosmos DB. Pokud chcete povolit přístup ke svému účtu Azure Cosmos DB z cloudové služby, musíte přidat veřejnou IP adresu z cloudové služby do seznamu povolených IP adres, které jsou spojené s vaším účtem Azure Cosmos DB pomocí [konfigurace zásad řízení přístupu IP](#configure-ip-policy). Tím se zajistí, že všechny instance rolí cloudové služby mají přístup ke svému účtu Azure Cosmos DB. 

IP adresy můžete načíst pro cloudové služby na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro cloudovou službu, zobrazí na webu Azure Portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Při horizontálním navýšením kapacity cloudové služby tak, že přidáte instance rolí tyto nové instance automaticky mít přístup k účtu Azure Cosmos DB, protože jsou součástí stejné cloudové službě.

### <a name="requests-from-virtual-machines"></a>Požadavky z virtuálních počítačů

Můžete také použít [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/) nebo [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) pro hostování střední vrstvy služeb s využitím služby Azure Cosmos DB. Ke konfiguraci účtu služby Cosmos DB, pokud chcete povolit přístup z virtuálních počítačů, musíte nakonfigurovat veřejnou IP adresu virtuálního počítače nebo škálovací sady jako jeden z povolených IP adres pro váš účet Azure Cosmos DB pomocí virtuálních počítačů [ Konfigurace zásad řízení přístupu IP](#configure-ip-policy). 

Můžete získat IP adresy pro virtuální počítače na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro virtuální počítač zobrazený na portálu Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Když přidáte instancí virtuálních počítačů do skupiny, automaticky získají přístup k vašemu účtu Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Požadavky z Internetu

Při přístupu k účtu služby Azure Cosmos DB z počítače na Internetu, klient IP adresu nebo rozsah IP adres počítače musí přidat do seznamu povolených IP adres pro váš účet.

## <a id="configure-ip-firewall-arm"></a>Konfigurace brány firewall protokolu IP s použitím šablony Resource Manageru

Ke konfiguraci řízení přístupu k účtu služby Azure Cosmos DB, ujistěte se, aby určovala šablony Resource Manageru **ipRangeFilter** atribut se seznamem povolených rozsahů IP adres. Například přidejte následující kód JSON do šablony:

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

## <a id="configure-ip-firewall-cli"></a>Konfigurace zásad řízení přístupu IP pomocí rozhraní příkazového řádku Azure

Následující příkaz ukazuje, jak vytvořit účet služby Azure Cosmos DB pomocí řízení přístupu IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
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

## <a id="troubleshoot-ip-firewall"></a>Řešení potíží s zásadu řízení přístupu IP

Řešit potíže s zásadu řízení přístupu IP s použitím následujících možností: 

### <a name="azure-portal"></a>portál Azure 
Když zapnete zásady řízení přístupu IP pro váš účet Azure Cosmos DB, zablokujete všechny požadavky ke svému účtu z počítače mimo seznamu povolených rozsahů IP adres. Povolit portálu operace roviny dat, jako je procházení kontejnery a dotazování dokumentů, je potřeba explicitně povolit přístup na portál Azure s použitím **brány Firewall** podokně na portálu.

### <a name="sdks"></a>Sady SDK 
Když zobrazujete prostředky Azure Cosmos DB s použitím sady SDK z počítače, které nejsou na seznamu povolených, obecný **404 Nenalezeno** vrátí odpověď se žádné další podrobnosti. Ověření seznamu povolených IP pro váš účet a ujistěte se, že správné zásady konfigurace se použije ke svému účtu Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>Zdrojové IP adresy v Blokované požadavky
Povolte protokolování diagnostiky ve vašem účtu Azure Cosmos DB. Tyto protokoly ukazují jednotlivých požadavků a odpovědí. Zprávy týkající se brány firewall jsou protokolovány interně 403 návratový kód. Pomocí filtrování tyto zprávy, uvidíte zdrojové IP adresy pro blokované požadavky. Zobrazit [protokolování diagnostiky služby Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Povolené žádosti z podsítě s koncovým bodem služby pro službu Azure Cosmos DB
Požadavky z podsítě ve virtuální síti, která má koncový bod služby pro službu Azure Cosmos DB povolené odešle virtuální síť a podsíť identity k účtům Azure Cosmos DB. Tyto požadavky nemají veřejné IP adresy zdroje, proto je zamítnout filtrů IP adres. Pokud chcete povolit přístup z konkrétních podsítí ve virtuálních sítích, přidejte seznam řízení přístupu, jak je uvedeno v [konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Může trvat až 15 minut pro pravidla brány firewall, chcete-li použít.


## <a name="next-steps"></a>Další postup

Pokud chcete nakonfigurovat koncový bod služby virtuální sítě pro váš účet Azure Cosmos DB, naleznete v následujících článcích:

* [Virtuální síť a podsíť, řízení přístupu pro váš účet Azure Cosmos DB](vnet-service-endpoint.md)
* [Konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)


