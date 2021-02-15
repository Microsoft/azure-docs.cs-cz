---
title: Azure Private Link pro Azure Data Factory
description: Přečtěte si, jak funguje privátní propojení Azure v Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 9e4d686f582a202dbc543620c7bf73dc4e7adb22
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389174"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link pro Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Pomocí privátního odkazu Azure se můžete připojit k různým nasazením platforem jako služby (PaaS) v Azure prostřednictvím privátního koncového bodu. Privátní koncový bod je privátní IP adresa v konkrétní virtuální síti a podsíti. Seznam nasazení PaaS, která podporují funkce privátního propojení, najdete v tématu [dokumentace k privátním odkazům](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Zabezpečená komunikace mezi zákaznickými sítěmi a Azure Data Factory 
Virtuální síť Azure můžete nastavit jako logickou reprezentaci vaší sítě v cloudu. Díky tomu jsou k dispozici následující výhody:
* Ochranu prostředků Azure můžete zvýšit před útoky ve veřejných sítích.
* Sítě a Data Factory mezi sebou umožní zabezpečenou komunikaci. 

Můžete taky připojit místní síť k virtuální síti tím, že nastavíte připojení k síti VPN (Internet Protocol Security-to-site) s protokolem IPsec (pro připojení typu Site-to-site) nebo Azure ExpressRoute (privátní partnerské vztahy). 

Místní prostředí Integration runtime můžete také nainstalovat do místního počítače nebo virtuálního počítače ve virtuální síti. Díky tomu máte tyto možnosti:
* Spouštějte aktivity kopírování mezi cloudovým úložištěm dat a úložištěm dat v privátní síti.
* Odeslání transformačních aktivit na výpočetní prostředky v místní síti nebo ve službě Azure Virtual Network. 

Mezi Azure Data Factory a zákaznickou virtuální sítí se vyžaduje několik komunikačních kanálů, jak je znázorněno v následující tabulce:

| Doména | Port | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Rovina ovládacího prvku, kterou vyžaduje Data Factory vytváření a monitorování. |
| `*.{region}.datafactory.azure.net` | 443 | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. |
| `*.servicebus.windows.net` | 443 | Vyžadováno místním prostředím Integration runtime pro interaktivní vytváření. |
| `download.microsoft.com` | 443 | Požadováno místním prostředím Integration runtime pro stahování aktualizací. |

Díky podpoře privátního odkazu pro Azure Data Factory můžete:
* Vytvořte ve virtuální síti privátní koncový bod.
* Povolí privátní připojení ke konkrétní instanci objektu pro vytváření dat. 

Komunikace se službou Azure Data Factory přechodem přes privátní odkaz a zajištění zabezpečeného privátního připojení. 

![Diagram privátního odkazu pro architekturu Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Povolení služby privátního propojení pro každý z předchozích komunikačních kanálů nabízí následující funkce:
- **Podporováno**:
   - Datovou továrnu můžete vytvářet a monitorovat ve virtuální síti, i když zablokujete veškerou odchozí komunikaci.
   - Spojení příkazů mezi místním prostředím Integration runtime a službou Azure Data Factory se dá bezpečně provést v prostředí privátní sítě. Přenos dat mezi místním prostředím Integration runtime a službou Azure Data Factory prochází pomocí privátního propojení. 
- **Aktuálně není podporováno**:
   - Interaktivní vytváření, které používá místní prostředí Integration runtime, jako je například test Connection, procházení seznamu složek a seznam tabulek, získání schématu a náhled dat, prochází pomocí privátního odkazu.
   - Novou verzi prostředí Integration runtime v místním prostředí je možné stáhnout automaticky z webu Microsoft Download Center, pokud povolíte automatické aktualizace.

   > [!NOTE]
   > U funkcí, které aktuálně nejsou podporovány, je stále nutné nakonfigurovat dříve uvedenou doménu a port ve virtuální síti nebo v podnikové bráně firewall. 

   > [!NOTE]
   > Připojení k Azure Data Factory prostřednictvím privátního koncového bodu platí pouze pro místní prostředí Integration runtime ve službě Data Factory. V synapse se nepodporuje.

> [!WARNING]
> Když vytvoříte propojenou službu, ujistěte se, že vaše přihlašovací údaje jsou uložené v trezoru klíčů Azure. Jinak přihlašovací údaje nebudou fungovat, když povolíte privátní odkaz v Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů
Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro Data Factory aktualizuje na alias v subdoméně s předponou ' privatelink '. Ve výchozím nastavení vytvoříme také [privátní ZÓNU DNS](../dns/private-dns-overview.md), která odpovídá subdoméně privatelink, a záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu objektu pro vytváření dat mimo virtuální síť s privátním koncovým bodem se přeloží na veřejný koncový bod služby Data Factory. Při překladu z virtuální sítě hostující soukromý koncový bod se adresa URL koncového bodu úložiště přeloží na IP adresu privátního koncového bodu.

Pro znázorněný příklad uvedený výše budou záznamy o prostředcích DNS pro Data Factory DataFactory a v případě, že jsou vyřešeny mimo virtuální síť, která je hostitelem privátního koncového bodu, následující:

| Název | Typ | Hodnota |
| ---------- | -------- | --------------- |
| Objekt DataFactory. {region}. DataFactory. Azure. NET | CNAME   | Objekt DataFactory. {region}. privatelink. DataFactory. Azure. NET |
| Objekt DataFactory. {region}. privatelink. DataFactory. Azure. NET | CNAME   | Veřejný koncový bod služby < Data Factory > |
| Veřejný koncový bod služby < Data Factory >  | A | < veřejné IP adresy služby Data Factory > |

Záznamy o prostředcích DNS pro objekt DataFactory, pokud jsou vyřešené ve virtuální síti hostující soukromý koncový bod, budou:

| Název | Typ | Hodnota |
| ---------- | -------- | --------------- |
| Objekt DataFactory. {region}. DataFactory. Azure. NET | CNAME   | Objekt DataFactory. {region}. privatelink. DataFactory. Azure. NET |
| Objekt DataFactory. {region}. privatelink. DataFactory. Azure. NET   | A | < IP adresu privátního koncového bodu > |

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény pro Data Factory koncový bod na IP adresu privátního koncového bodu. Server DNS byste měli nakonfigurovat tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo aby byly nakonfigurovány záznamy A pro objekt DataFactory. {region}. privatelink. DataFactory. Azure. NET s IP adresou privátního koncového bodu.

Další informace o konfiguraci vlastního serveru DNS pro podporu privátních koncových bodů najdete v následujících článcích:
- [Překlad názvů pro prostředky ve virtuálních sítích Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [Konfigurace DNS pro privátní koncové body](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Nastavit privátní odkaz pro Azure Data Factory
Soukromé koncové body lze vytvořit pomocí [Azure Portal](../private-link/create-private-endpoint-portal.md).

Můžete zvolit, jestli se má připojit k místnímu prostředí Integration runtime k Azure Data Factory prostřednictvím veřejného koncového bodu nebo privátního koncového bodu. 

![Snímek obrazovky blokující veřejný přístup k samoobslužným Integration Runtimeům](./media/data-factory-private-link/disable-public-access-shir.png)


Můžete také přejít na objekt pro vytváření dat Azure v Azure Portal a vytvořit privátní koncový bod, jak je znázorněno zde:

![Snímek obrazovky s podoknem připojení privátního koncového bodu pro vytvoření privátního koncového bodu.](./media/data-factory-private-link/create-private-endpoint.png)

V kroku **prostředku** vyberte **Microsoft. DataFactory/Factory** jako **typ prostředku**. A pokud chcete vytvořit privátní koncový bod pro komunikaci příkazů mezi místním prostředím Integration runtime a službou Azure Data Factory, vyberte jako **cílový dílčí prostředek** možnost **DataFactory** .

![Snímek obrazovky s podoknem připojení privátního koncového bodu pro výběr prostředku](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> Zákaz přístupu k veřejné síti se vztahuje jenom na prostředí Integration runtime v místním prostředí, ne na Azure Integration Runtime a služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime.

Pokud chcete vytvořit privátní koncový bod pro vytváření a monitorování datové továrny ve vaší virtuální síti, vyberte **portál** jako **cílový dílčí prostředek**.

> [!NOTE]
> Po vytvoření privátního koncového bodu pro portál máte stále přístup k portálu Azure Data Factory prostřednictvím veřejné sítě.

## <a name="next-steps"></a>Další kroky

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)
- [Úvod do služby Azure Data Factory](introduction.md)
- [Vytváření vizuálů v Azure Data Factory](author-visually.md)