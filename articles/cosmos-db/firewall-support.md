---
title: Azure Cosmos DB, podporu brány firewall & IP řízení přístupu | Dokumentace Microsoftu
description: Zjistěte, jak používat zásady řízení přístupu IP pro podporu brány firewall u účtů databáze Azure Cosmos DB.
keywords: Řízení přístupu IP, podporu brány firewall
services: cosmos-db
author: kanshiG
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: govindk
ms.openlocfilehash: 6d1daededcf8f0efdc6a3a5649aa830110192fef
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43381836"
---
# <a name="azure-cosmos-db-firewall-support"></a>Podpora brány firewall služby Azure Cosmos DB
Zabezpečení dat uložených v účtu databáze Azure Cosmos DB, Azure Cosmos DB poskytuje podporu pro tajného kódu na základě [modelu autorizace](https://msdn.microsoft.com/library/azure/dn783368.aspx) , který využívá ověřovací kód zprávy silné Hash-based (metoda HMAC). Nyní kromě tajného kódu autorizace na základě modelu služby Azure Cosmos DB podporuje zásady řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí řízené. Tento model je podobný pravidla brány firewall tradiční databázový systém a poskytuje další úroveň zabezpečení, které účet databáze Azure Cosmos DB. V tomto modelu teď můžete nakonfigurovat k účtu databáze Azure Cosmos DB k vyla přístupná jen v schválenou sadu počítačů nebo cloudových služeb. Přístup k prostředkům služby Azure Cosmos DB z těchto schválených sad počítače a služby se stále vyžadují volající předložit platný autorizační token.

> [!NOTE]
> Podpora brány firewall je aktuálně dostupná pro účty SQL API služby Azure Cosmos DB a rozhraní API Mongodb. Brzy bude k dispozici možnost ke konfiguraci brány firewall pro ostatní rozhraní API a suverénních cloudech, jako je Azure Germany a Azure Government. Pokud máte v úmyslu nakonfigurovat koncový bod služby seznamu ACL pro účet služby Azure Cosmos DB, který má existující brány firewall protokolu IP nakonfigurovaný, mějte prosím na paměti konfigurace brány firewall, odeberte brány firewall protokolu IP a potom nakonfigurovat seznam ACL koncového bodu služby. Po dokončení konfigurace koncového bodu služby, můžete znovu povolit brány firewall protokolu IP v případě potřeby.

## <a name="ip-access-control-overview"></a>Přehled řízení přístupu IP
Ve výchozím nastavení je účet databáze Azure Cosmos DB přístupný z veřejného internetu za předpokladu, že k žádosti je přiložený platný autorizační token. Při konfiguraci řízení přístupu na základě zásad IP musí uživatel poskytnout sadu IP adres nebo rozsahy IP adres ve formátu CIDR. Zahrnou se jako seznam povolených klientských IP adres pro příslušný účet databázový účet. Jakmile se tato konfigurace použije, server blokuje všechny požadavky z počítačů mimo tento seznam povolených.  Připojení zpracování toku pro řízení přístupu na základě IP adresy je popsaný v následujícím diagramu:

![Diagram znázorňující postup připojení k řízení přístupu na základě IP adresy](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurace zásad řízení přístupu IP
Zásady řízení přístupu IP lze nastavit na webu Azure Portal nebo prostřednictvím kódu programu přes [rozhraní příkazového řádku Azure](cli-samples.md), [prostředí Azure Powershell](powershell-samples.md), nebo [rozhraní REST API](/rest/api/cosmos-db/) aktualizací **ipRangeFilter** vlastnost. 

Pokud chcete nastavit zásady řízení přístupu IP na webu Azure Portal, přejděte na stránku účtu služby Azure Cosmos DB, klikněte na **brány Firewall a virtuální sítě** v navigační nabídce, změňte **povolit přístup z** hodnota k **vybrané sítě**a potom klikněte na tlačítko **Uložit**. 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/firewall-support/azure-portal-firewall.png)

Po řízení přístupu IP na portálu umožňuje zadat IP adresy a rozsahy, jakož i přepne na povolení přístupu k jiným službám Azure a webu Azure portal. Další informace o těchto přepínačích najdete v následujících částech.

> [!NOTE]
> Tím, že zásady řízení přístupu IP pro váš účet databáze Azure Cosmos DB, veškerý přístup k účtu databáze Azure Cosmos DB z počítačů mimo nakonfigurované povolený seznam rozsahů IP adres jsou zablokované. Tím, že tento model procházení operace roviny dat z portálu se taky zablokuje k zajištění integrity řízení přístupu.

## <a name="connections-from-the-azure-portal"></a>Připojení z webu Azure portal 

Pokud povolíte zásadu řízení přístupu IP prostřednictvím kódu programu, je potřeba přidat IP adresu na webu Azure portal **ipRangeFilter** vlastnost chcete zachovat přístup. Portálu IP adresy jsou:

|Oblast|IP adresa|
|------|----------|
|Všech oblastech s výjimkou určeným níže|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|Vláda USA|52.244.48.71|

Přístup k webu Azure portal je ve výchozím nastavení povolena, když změníte nastavení brány Firewall na **vybrané sítě** na webu Azure Portal. 

![Snímek obrazovky ukazující, jak povolit přístup na portál Azure](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>Připojení z datových center Global Azure nebo služby Azure PaaS

"Služby azure PaaS, jako je Azure Stream analytics, Azure atd. funkce se používají ve spojení s Azure Cosmos DB. Pokud chcete umožnit aplikacím z jiných služeb Azure PaaS pro připojení k vašim prostředkům služby Azure Cosmos DB, musíte povolit nastavení brány firewall. Pokud chcete povolit toto nastavení brány firewall, přidejte do seznamu povolených IP adres IP adresa-0.0.0.0. Ip adresa-0.0.0.0 znamená, že připojení ze všech rozsahu IP adres datacentra Azure jsou povolené pro připojení k vašim prostředkům služby Azure Cosmos DB."

> [!IMPORTANT]
> Touto možností se brána firewall nakonfiguruje tak, aby povolovala všechna připojení z Azure, včetně připojení z předplatných ostatních zákazníků. Když vyberete tuto možnost, ujistěte se, že vaše přihlašovací a uživatelská oprávnění omezují přístup pouze na autorizované uživatele.
> 

Přístup k připojení z v rámci globální síti datových center Azure je ve výchozím nastavení povolena, když změníte nastavení brány Firewall na **vybrané sítě** na webu Azure Portal. 

![Snímek obrazovky ukazující, jak na webu Azure Portal otevřete stránku brány Firewall](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Připojení z aktuální IP adresa

Pro zjednodušení vývoje na webu Azure portal vám pomůže identifikovat a přidat IP adresu klientského počítače do seznamu povolených aplikací, tak, aby aplikace spuštění vaše počítače můžou přistupovat k účtu Azure Cosmos DB. IP adresa klienta tady se zjistí, jak je vidět portálu. Může být IP adresa klienta vašeho počítače, ale mohou být také IP adresu brány sítě. Nezapomeňte odebrat před přechodem do produkčního prostředí.

Chcete-li povolit vaši aktuální IP adresu, vyberte **přidat moji aktuální IP adresu**, která přidá vaši aktuální IP adresa na seznam IP adres a potom klikněte na **Uložit**.

![Snímek obrazovky s informacemi postupu konfigurace nastavení brány firewall pro aktuální IP adresa](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Připojení ze služby cloud services
V Azure cloudové služby jsou běžným způsobem pro hostování logikou střední úrovně služby využívající službu Azure Cosmos DB. Pokud chcete povolit přístup k účtu databáze Azure Cosmos DB z cloudové služby, musí veřejnou IP adresu z cloudové služby přidat do seznamu povolených IP adres, které jsou přidružené k účtu databáze Azure Cosmos DB pomocí [konfigurací IP přístupu řízení zásad](#configure-ip-policy). Tím se zajistí, že všechny instance rolí cloudové služby mají přístup k vašemu účtu databáze Azure Cosmos DB. IP adresy můžete načíst pro cloudové služby na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro cloudovou službu, zobrazí na webu Azure Portal](./media/firewall-support/public-ip-addresses.png)

Při horizontálním navýšením kapacity cloudové služby tak, že přidáte další role instance tyto nové instance automaticky mít přístup k účtu databáze Azure Cosmos DB, protože jsou součástí stejné cloudové službě.

## <a name="connections-from-virtual-machines"></a>Připojení z virtuálních počítačů
[Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo [škálovací sady virtuálních počítačů](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) lze také použít k hostování střední vrstvy služby pomocí služby Azure Cosmos DB.  Ke konfiguraci účtu databáze Azure Cosmos DB pro povolení přístupu z virtuálních počítačů, veřejné IP adresy virtuálního počítače nebo virtuálního počítače škálovací sady je nutné nakonfigurovat jako jednu z povolených IP adres pro váš účet databáze Azure Cosmos DB [konfigurace zásad řízení přístupu IP](#configure-ip-policy). Můžete získat IP adresy pro virtuální počítače na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky s veřejnou IP adresu pro virtuální počítač zobrazený na portálu Azure portal](./media/firewall-support/public-ip-addresses-dns.png)

Když přidáte instance dalších virtuálních počítačů do skupiny, se automaticky poskytnou přístup k vašemu účtu databáze Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Připojení z Internetu
Když přístup k účtu databáze Azure Cosmos DB z počítače na Internetu, klient IP adresu nebo rozsah IP adres na počítači musí být přidaný do seznamu povolených IP adres pro účet databáze Azure Cosmos DB. 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Nastavení řízení přístupu IP pomocí šablony Azure Resource Manageru

Přidejte následující kód JSON do šablony nastavit řízení přístupu IP. Šablona Resource Manageru pro účet bude mít ipRangeFilter atribut, který je seznam rozsahů IP adres, které by měly být uvedeny v seznamu povolených.

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
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>Řešení potíží s zásady řízení přístupu IP
### <a name="portal-operations"></a>Operace portálu
Tím, že zásady řízení přístupu IP pro váš účet databáze Azure Cosmos DB, veškerý přístup k účtu databáze Azure Cosmos DB z počítačů mimo nakonfigurované povolený seznam rozsahů IP adres jsou zablokované. Proto pokud chcete povolit operace roviny dat portálu třeba k procházení kontejnery a dotazování dokumentů, je potřeba explicitně povolit přístup na portál Azure pomocí **brány Firewall** stránky na portálu. 

### <a name="sdk--rest-api"></a>Sadu SDK a Rest API
Pro z bezpečnostních důvodů přístup prostřednictvím sady SDK nebo rozhraní REST API z počítače není na seznamu povolených aplikací vrátí obecný 404 Nenalezeno odpovědí s žádné další podrobnosti. Zkontrolujte IP adresy nakonfigurované pro váš účet databáze Azure Cosmos DB seznamu povolených aplikací k Ujistěte se, že se použije správné zásady konfigurace pro váš účet databáze Azure Cosmos DB.

## <a name="next-steps"></a>Další postup
Informace o tipy ke zvýšení výkonu související se sítí najdete v tématu [tipy ke zvýšení výkonu](performance-tips.md).

