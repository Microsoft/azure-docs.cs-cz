---
title: Řízení přístupu Azure Cosmos DB podpora brány firewall & IP | Microsoft Docs
description: Naučte se používat zásady řízení přístupu IP pro podporu brány firewall na účty databáze Azure Cosmos DB.
keywords: Řízení přístupu IP, podporu brány firewall
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sngun
ms.openlocfilehash: f0cbbe147386aa5d50e207fdd9c86fd9571ec144
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34611734"
---
# <a name="azure-cosmos-db-firewall-support"></a>Podpora brány firewall Azure Cosmos DB
Zabezpečení dat uložených v databázi účet Azure Cosmos DB, Azure Cosmos DB poskytl podporu pro tajný klíč na základě [modelu autorizace](https://msdn.microsoft.com/library/azure/dn783368.aspx) , využívá ověřovací kód silné Hash-based zprávy (HMAC). Teď kromě tajný autorizace založené na modelu Azure Cosmos DB podporuje zásady řízené řízení přístupu na základě IP pro podporu brány firewall pro příchozí. Tento model je podobná pravidla brány firewall systému tradiční databází a poskytuje další úroveň zabezpečení pro databázový účet Azure Cosmos DB. Pomocí tohoto modelu teď můžete konfigurovat účet Azure Cosmos DB databáze tak, aby byla přístupná jenom z schválené sadu počítačů nebo cloudových služeb. Přístup k prostředkům Azure Cosmos DB z tyto schválené sady počítačů a služeb stále vyžadují volajícího, aby k dispozici platný autorizační token.

> [!NOTE]
> Podpora brány firewall je aktuálně k dispozici pro rozhraní API pro Azure Cosmos databáze SQL a rozhraní API Mongo účty. Možnost konfigurace brány firewall pro ostatní rozhraní API a svrchovaných cloudy, například Azure v Německu nebo Azure Government bude brzy k dispozici. Pokud máte v úmyslu konfigurovat koncový bod služby seznamu ACL pro vaši databázi Cosmos Azure účet, který má existující brány firewall IP nakonfigurovaný, Upozorňujeme konfiguraci brány firewall, odeberte IP brány firewall a pak nakonfigurujte koncový bod služby seznamu ACL. Po dokončení konfigurace koncového bodu služby, můžete znovu povolit IP brány firewall v případě potřeby.

## <a name="ip-access-control-overview"></a>Přehled řízení přístupu IP
Ve výchozím nastavení je účet databáze Azure Cosmos DB přístupný z veřejného internetu za předpokladu, že k žádosti je přiložený platný autorizační token. Při konfiguraci řízení přístupu na základě zásad IP musí uživatel poskytnout sadu IP adres nebo rozsahy IP adres ve formátu CIDR. Zahrnou se jako seznam povolených klientských IP adres pro příslušný účet databázový účet. Jakmile se tato konfigurace použije, server blokuje všechny požadavky z počítačů mimo tento seznam povolených.  Následující diagram popisuje připojení zpracování toku řízení přístupu na základě IP:

![Diagram zobrazující proces připojení u řízení přístupu na základě IP](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurace zásad řízení přístupu IP
Zásady řízení přístupu IP lze nastavit na portálu Azure nebo prostřednictvím kódu programu prostřednictvím [rozhraní příkazového řádku Azure](cli-samples.md), [prostředí Azure Powershell](powershell-samples.md), nebo [REST API](/rest/api/cosmos-db/) aktualizací **ipRangeFilter** vlastnost. 

Pokud chcete nastavit zásady řízení přístupu IP na portálu Azure, přejděte na stránku účtu Azure Cosmos DB, klikněte na **brány Firewall** v navigační nabídce, pak změňte **povolit přístup ze** hodnotu  **Vybrané sítě**a potom klikněte na **Uložit**. 

![Snímek obrazovky ukazující, jak chcete otevřít stránku brány Firewall na portálu Azure](./media/firewall-support/azure-portal-firewall.png)

Po IP řízení přístupu na portálu poskytuje možnost určit IP adresy a rozsahy, jakož i přepínače a umožňuje přístup k jiným službám Azure a portálu Azure. Další informace o těchto přepínače najdete v následujících částech.

> [!NOTE]
> Povolením IP zásad řízení přístupu pro váš účet Azure Cosmos DB databáze, veškerý přístup ke svému účtu Azure Cosmos DB databáze z počítače mimo nastavenou povoleny, seznam rozsahů IP adres jsou zablokované. Na základě tento model procházení operace roviny dat z portálu také se zablokuje k zajištění integrity řízení přístupu.

## <a name="connections-from-the-azure-portal"></a>Připojení z portálu Azure 

Když povolíte zásad řízení přístupu IP prostřednictvím kódu programu, je nutné přidat IP adresu pro portál Azure **ipRangeFilter** vlastnost k získání přístupu. Portál IP adresy jsou:

|Oblast|IP adresa|
|------|----------|
|Všechny oblasti s výjimkou těchto zadané níže|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Německo|51.4.229.218|
|Čína|139.217.8.252|
|Vláda USA|52.244.48.71|

Ve výchozím nastavení je povolen přístup na portál Azure, když změníte nastavení brány Firewall pro **vybrané sítě** na portálu Azure. 

![Snímek obrazovky ukazující, jak povolit Azure přístup k portálu](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-other-azure-paas-services"></a>Připojení z jiných služeb Azure PaaS 
V Azure služby PaaS, jako je Azure Stream analytics, funkce Azure a Azure App Service se používají ve spojení s Azure Cosmos DB. Účet databáze z těchto služeb, jejichž IP adresy nejsou snadno dostupné pro povolení přístupu k databázi Azure Cosmos přidat IP adresu 0.0.0.0 do seznamu povolených IP adres, které jsou spojené s vaším účtem Azure Cosmos DB databáze prostřednictvím kódu programu. 

Ve výchozím nastavení je povolen přístup k jiným službám Azure, když změníte nastavení brány Firewall pro **vybrané sítě** na portálu Azure. 

![Snímek obrazovky ukazující, jak chcete otevřít stránku brány Firewall na portálu Azure](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Připojení z vaší aktuální IP adresy

Pro zjednodušení vývoje, portálu Azure vám pomůže identifikovat a přidejte do seznamu povolených IP klientského počítače, aby aplikace běžící váš počítač přístup k účtu Azure Cosmos DB. IP adresa klienta tady je zjištěna, jak je vidět na portálu. Může být IP adresa klienta počítače, ale mohou být také IP adresu brány vaší sítě. Nezapomeňte odebrat před přechodem do produkčního prostředí.

Chcete-li aktuální IP, vyberte **přidat Moje aktuální IP**, který přidá aktuální IP do seznamu IP adres a potom klikněte na **Uložit**.

![Snímek obrazovky ukazující, jak nakonfigurovat nastavení brány firewall pro aktuální IP adresu](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Připojení z cloudové služby
Cloudové služby v Azure, jsou běžné způsob, jak hostování logikou střední úrovně služby pomocí Azure Cosmos DB. Pokud chcete umožnit přístup k účtu Azure Cosmos DB databáze z cloudové služby, veřejnou IP adresu cloudové služby musíte přidat do seznamu povolených IP adres, které jsou spojené s vaším účtem Azure Cosmos DB databáze pomocí [konfigurace zásad řízení přístupu IP](#configure-ip-policy). To zajistí, že všechny instance rolí, služeb cloud mít přístup ke svému účtu databáze Azure Cosmos DB. IP adresy můžete načíst pro vaše cloudové služby na portálu Azure, jak je znázorněno na následujícím snímku obrazovky:

![Snímek obrazovky s veřejnou IP adresu pro cloudové služby zobrazí na portálu Azure](./media/firewall-support/public-ip-addresses.png)

Při škálování cloudové služby tak, že přidáte další role instance tyto nové instance automaticky mít přístup k účtu Azure Cosmos DB databáze, protože jsou součástí stejné cloudové služby.

## <a name="connections-from-virtual-machines"></a>Připojení z virtuálních počítačů
[Virtuální počítače](https://azure.microsoft.com/services/virtual-machines/) nebo [sady škálování virtuálního počítače](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) lze také použít k hostování střední vrstvy služeb pomocí Azure Cosmos DB.  Jak nakonfigurovat účet Azure Cosmos DB databáze pro povolení přístupu z virtuálních počítačů, veřejné IP adresy virtuálního počítače nebo škálovací sadu virtuálních počítačů musí být nakonfigurován jako jednu z povolených IP adres pro váš účet Azure Cosmos DB databáze pomocí [konfigurace zásad řízení přístupu IP](#configure-ip-policy). IP adresy pro virtuální počítače na portálu Azure můžete načíst, jak je znázorněno na následujícím snímku obrazovky.

![Snímek obrazovky s veřejnou IP adresu pro virtuální počítač, který se zobrazí na portálu Azure](./media/firewall-support/public-ip-addresses-dns.png)

Když přidáte instance dalších virtuálních počítačů do skupiny, jsou automaticky k dispozici přístup ke svému účtu databáze Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Připojení z Internetu
Při přístupu k účtu Azure Cosmos DB databáze z počítače na Internetu, musí být IP adresa klienta nebo rozsah IP adres počítače přidány do seznamu povolených IP adresy pro účet Azure Cosmos DB databáze. 

## <a name="troubleshooting-the-ip-access-control-policy"></a>Řešení potíží s zásad řízení přístupu IP
### <a name="portal-operations"></a>Operace portálu
Povolením IP zásad řízení přístupu pro váš účet Azure Cosmos DB databáze, veškerý přístup ke svému účtu Azure Cosmos DB databáze z počítače mimo nastavenou povoleny, seznam rozsahů IP adres jsou zablokované. Proto pokud chcete povolit portálu datové roviny operací, jako je procházení kolekcí a dotazů dokumentů, musíte výslovně povolit pomocí Azure přístup k portálu **brány Firewall** na portálu. 

![Snímek obrazovky ukazující, jak chcete umožnit přístup k portálu Azure](./media/firewall-support/azure-portal-firewall.png)

### <a name="sdk--rest-api"></a>Sadu SDK a Rest API
Pro z bezpečnostních důvodů, přístup prostřednictvím sady SDK nebo REST API z počítače není na seznamu povolených vrátí obecné 404 nebyl nalezen odpověď se žádné další podrobnosti. Ověřte IP nakonfigurovaných pro váš účet Azure Cosmos DB databáze seznamu povolených zajistit že správné zásady konfigurace se použije ke svému účtu databáze Azure Cosmos DB.

## <a name="next-steps"></a>Další postup
Informace o tipy pro zvýšení výkonu související se sítí najdete v tématu [tipy pro zvýšení výkonu](performance-tips.md).

