---
title: Privátní odkaz Azure pro Azure Data Factory
description: Přečtěte si, jak funguje privátní propojení Azure v Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 2cd9f01404a4e33303356dd3f452cd7dbc47a747
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328560"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Privátní odkaz Azure pro Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Pomocí privátního odkazu Azure se můžete připojit k různým nasazením platforem jako služby (PaaS) v Azure prostřednictvím privátního koncového bodu. Privátní koncový bod je privátní IP adresa v konkrétní virtuální síti a podsíti. Seznam nasazení PaaS, která podporují funkce privátního propojení, najdete v tématu [dokumentace k privátním odkazům](https://docs.microsoft.com/azure/private-link/). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Zabezpečená komunikace mezi zákaznickými sítěmi a Azure Data Factory 
Virtuální síť Azure můžete nastavit jako logickou reprezentaci vaší sítě v cloudu. Díky tomu jsou k dispozici následující výhody:
* Ochranu prostředků Azure můžete zvýšit před útoky ve veřejných sítích.
* Sítě a Data Factory mezi sebou umožní zabezpečenou komunikaci. 

Můžete taky připojit místní síť k virtuální síti tím, že nastavíte připojení k síti VPN (Internet Protocol Security-to-site) s protokolem IPsec (pro připojení typu Site-to-site) nebo Azure ExpressRoute (privátní partnerské vztahy). 

Místní prostředí Integration runtime můžete také nainstalovat do místního počítače nebo virtuálního počítače ve virtuální síti. Díky tomu máte tyto možnosti:
* Spouštějte aktivity kopírování mezi cloudovým úložištěm dat a úložištěm dat v privátní síti.
* Odeslání transformačních aktivit na výpočetní prostředky v místní síti nebo ve službě Azure Virtual Network. 

Mezi Azure Data Factory a zákaznickou virtuální sítí se vyžaduje několik komunikačních kanálů, jak je znázorněno v následující tabulce:

| Doména | Port | Popis |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Rovina ovládacího prvku, kterou vyžaduje Data Factory vytváření a monitorování. |
| `*.{region}.datafactory.azure.net` | 443 | Požadováno místním prostředím Integration runtime pro připojení ke službě Data Factory. |
| `*.servicebus.windows.net` | 443 | Vyžadováno místním prostředím Integration runtime pro interaktivní vytváření. |
| `download.microsoft.com` | 443 | Požadováno místním prostředím Integration runtime pro stahování aktualizací. |

Díky podpoře privátního odkazu pro Azure Data Factory můžete:
* Vytvořte ve virtuální síti privátní koncový bod.
* Povolí privátní připojení ke konkrétní instanci objektu pro vytváření dat. 

Komunikace se službou Azure Data Factory přechodem přes privátní odkaz a zajištění zabezpečeného privátního připojení. Pro zajištění bezpečnějšího způsobu ochrany prostředků není nutné konfigurovat předchozí doménu a port ve virtuální síti ani v podnikové bráně firewall.  

![Diagram privátního odkazu pro architekturu Azure Data Factory.](./media/data-factory-private-link/private-link-architecture.png)

Povolení služby privátního propojení pro každý z předchozích komunikačních kanálů nabízí následující funkce:
- **Podporováno**:
   - Datovou továrnu můžete vytvářet a monitorovat ve virtuální síti, i když zablokujete veškerou odchozí komunikaci.
   - Spojení příkazů mezi místním prostředím Integration runtime a službou Azure Data Factory se dá bezpečně provést v prostředí privátní sítě. Přenos dat mezi místním prostředím Integration runtime a službou Azure Data Factory prochází pomocí privátního propojení. 
- **Aktuálně není podporováno**:
   - Interaktivní vytváření, které používá místní prostředí Integration runtime, jako je například test Connection, procházení seznamu složek a seznam tabulek, získání schématu a náhled dat, prochází pomocí privátního odkazu.
   - Novou verzi prostředí Integration runtime v místním prostředí je možné stáhnout automaticky z webu Microsoft Download Center, pokud povolíte funkci Automatické aktualizace.

   > [!NOTE]
   > U funkcí, které aktuálně nejsou podporovány, je stále nutné nakonfigurovat dříve uvedenou doménu a port ve virtuální síti nebo v podnikové bráně firewall. 

> [!WARNING]
> Když vytvoříte propojenou službu, ujistěte se, že vaše přihlašovací údaje jsou uložené v trezoru klíčů Azure. Jinak přihlašovací údaje nebudou fungovat, když povolíte privátní odkaz v Azure Data Factory.

## <a name="set-up-private-link-for-azure-data-factory"></a>Nastavit privátní odkaz pro Azure Data Factory
Soukromé koncové body můžete vytvořit pomocí [Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal), PowerShellu nebo rozhraní příkazového řádku Azure CLI.

Můžete také přejít na objekt pro vytváření dat Azure v Azure Portal a vytvořit privátní koncový bod, jak je znázorněno zde:

![Snímek obrazovky s podoknem připojení privátního koncového bodu pro vytvoření privátního koncového bodu.](./media/data-factory-private-link/create-private-endpoint.png)


Pokud chcete zablokovat veřejný přístup k objektu pro vytváření dat Azure a povolit přístup pouze prostřednictvím privátního odkazu, zakažte v Azure Portal přístup k síti Azure Data Factory, jak je znázorněno zde:

![Snímek obrazovky s podoknem "přístup k síti" pro vytvoření privátního koncového bodu.](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Zákaz přístupu k veřejné síti se vztahuje jenom na prostředí Integration runtime v místním prostředí, ne na Azure Integration Runtime a služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime.

> [!NOTE]
> Po zakázání přístupu k veřejné síti máte stále přístup k portálu Azure Data Factory prostřednictvím veřejné sítě.

## <a name="next-steps"></a>Další kroky

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)
- [Úvod do služby Azure Data Factory](introduction.md)
- [Vytváření vizuálů v Azure Data Factory](author-visually.md)

