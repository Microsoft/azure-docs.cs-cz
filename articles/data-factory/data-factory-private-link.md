---
title: Privátní odkaz Azure pro Azure Data Factory
description: Informace o privátním propojení Azure funguje v Azure Data Factory.
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
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596018"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Privátní odkaz Azure pro Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Privátní odkaz vám umožní připojit se k různým službám PaaS v Azure prostřednictvím privátního koncového bodu. Seznam služeb PaaS, které podporují funkce privátního propojení, najdete na [stránce dokumentace k privátním odkazům](https://docs.microsoft.com/azure/private-link/). Privátní koncový bod je privátní IP adresa v konkrétní virtuální síti a podsíti.

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>Zabezpečená komunikace mezi zákaznickou sítí a Azure Data Factory službou
Pokud chcete chránit prostředky Azure před útoky ve veřejné síti nebo umožnit jejich zabezpečenou komunikaci, můžete nastavit Virtual Network Azure jako logickou reprezentaci vaší sítě v cloudu. Můžete taky připojit místní síť k virtuální síti nastavením IPSec VPN (site-to-site) nebo ExpressRoute (privátní partnerské vztahy). Integration Runtime v místním prostředí můžete nainstalovat do místního počítače nebo virtuálního počítače v Virtual Network ke spouštění aktivit kopírování mezi cloudovým úložištěm dat a úložištěm dat v privátní síti nebo při odeslání transformačních aktivit na výpočetní prostředky v místní síti nebo ve službě Azure Virtual Network. 

Mezi Data Factory a zákaznickou virtuální sítí se vyžaduje několik komunikačních kanálů.


| **Doména** | **Port** | **Popis** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Rovina ovládacího prvku Vyžaduje se Data Factory vytváření a monitorování. |
| `*.{region}.datafactory.azure.net` | 443 | Vyžaduje se Integration Runtime pro místní hostování pro připojení ke službě Data Factory. |
| `*.servicebus.windows.net` | 443 | Vyžadované Integration Runtime v místním prostředí pro interaktivní vytváření. |
| `download.microsoft.com` | 443 | Vyžadované Integration Runtime v místním prostředí ke stažení aktualizací. |


Díky podpoře privátního odkazu Azure pro Azure Data Factory můžete ve své virtuální síti vytvořit privátní koncový bod (PE) a povolit privátní připojení ke konkrétnímu Azure Data Factory. Komunikace s Azure Data Factory služby prochází prostřednictvím privátního odkazu Azure, který poskytuje zabezpečené privátní připojení. A nemusíte konfigurovat nad doménou a portem ve virtuální síti nebo v podnikové bráně firewall, která poskytuje bezpečnější způsob ochrany vašich prostředků.  

![Azure Data Factory architektury privátního propojení](./media/data-factory-private-link/private-link-architecture.png)

Tady jsou výhody pro povolení služby privátního propojení pro každý z výše uvedených komunikačních kanálů:
- Doložen Můžete provádět vytváření a monitorování Azure Data Factory ve vaší virtuální síti, i když zablokujete veškerou odchozí komunikaci.
- Doložen Komunikace mezi místně hostovanými Integration Runtime a Azure Data Factory službou se dá bezpečně provést v prostředí privátní sítě. Přenos dat mezi místně hostovanými Integration Runtime a službou Azure Data Factory prochází prostřednictvím privátního odkazu. 
- (Aktuálně se nepodporuje) Interaktivní vytváření pomocí samoobslužných Integration Runtime procházejí prostřednictvím privátního odkazu, jako je například test připojení, procházení seznamu složek a seznam tabulek, získání schématu a zobrazení náhledu dat.
- (Aktuálně se nepodporuje) Novou verzi Integration Runtime v místním prostředí můžete stáhnout automaticky z webu služby Stažení softwaru, pokud povolíte automatické aktualizace.

> [!NOTE]
> U funkcí, které se v současné době nepodporují, je potřeba nakonfigurovat výše uvedenou doménu a port ve virtuální síti nebo v podnikové bráně firewall. 

> [!WARNING]
> Při vytváření propojené služby se ujistěte, že jsou přihlašovací údaje uložené v Azure Key Vault. V opačném případě nefunguje, pokud povolíte službu privátního propojení v Azure Data Factory.

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>Jak nastavit privátní odkaz pro Azure Data Factory
Soukromé koncové body lze vytvořit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure:

[Azure Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


Můžete také přejít k Azure Data Factory v Azure Portal a vytvořit privátní koncový bod (PE):

![Vytvoření privátního koncového bodu](./media/data-factory-private-link/create-private-endpoint.png)


Pokud chcete zablokovat veřejný přístup k tomuto Azure Data Factory a povolit přístup jenom prostřednictvím privátního odkazu, můžete zakázat přístup k síti Azure Data Factory v Azure Portal:

![Vytvoření privátního koncového bodu](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> Zákaz přístupu k veřejné síti se vztahuje jenom na Integration Runtime v místním prostředí, ne na Azure Integration Runtime a SSIS Integration Runtime.

> [!NOTE]
> Uživatelé budou mít stále přístup k portálu Azure Data Factory prostřednictvím veřejné sítě po zakázání přístupu k veřejné síti.

## <a name="next-steps"></a>Další kroky

- [Vytvoření datové továrny pomocí Azure Data Factoryho uživatelského rozhraní](quickstart-create-data-factory-portal.md)

- [Úvod do služby Azure Data Factory](introduction.md)

- [Vytváření vizuálů v Azure Data Factory](author-visually.md)

