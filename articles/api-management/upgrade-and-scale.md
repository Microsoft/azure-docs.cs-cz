---
title: Upgrade a škálování instance Azure API Management | Dokumenty společnosti Microsoft
description: Toto téma popisuje, jak upgradovat a škálovat instanci Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/18/2018
ms.author: apimpm
ms.openlocfilehash: 64649c86dbd3c3469247308bfc4dd0ed12e06949
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018240"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Upgrade a škálování instance Azure API Management  

Zákazníci můžou škálovat instanci Azure API Management (APIM) přidáním a odebráním jednotek. **Jednotka** se skládá z vyhrazených prostředků Azure a má určitou nosnost vyjádřenou jako počet volání rozhraní API za měsíc. Toto číslo nepředstavuje omezení volání, ale spíše maximální propustnost hodnotu umožňující plánování hrubé kapacity. Skutečná propustnost a latence se značně liší v závislosti na faktorech, jako je počet a rychlost souběžných připojení, druh a počet nakonfigurovaných zásad, velikosti požadavků a odpovědí a latence back-endu.

Kapacita a cena každé jednotky závisí na **úrovni,** ve které jednotka existuje. Můžete si vybrat mezi čtyřmi úrovněmi: **Vývojář**, **Basic**, **Standard**, **Premium**. Pokud potřebujete zvýšit kapacitu pro službu v rámci úrovně, měli byste přidat jednotku. Pokud úroveň, která je aktuálně vybraná v instanci APIM neumožňuje přidání dalších jednotek, je třeba upgradovat na vyšší úroveň.

Cena každé jednotky a dostupné funkce (například nasazení ve více oblastech) závisí na úrovni, kterou jste zvolili pro instanci APIM. Článek [s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) vysvětluje cenu za jednotku a funkce, které získáte v každé úrovni. 

>[!NOTE]
>V článku [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se zobrazí přibližný počet jednotkové kapacity v každé vrstvě. Chcete-li získat přesnější čísla, musíte se podívat na realistický scénář pro vaše api. Podívejte se na [článek o kapacitě instance azure api managementu.](api-management-capacity.md)

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků z tohoto článku, musíte:

+ Mít aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Mají instanci APIM. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).

+ Seznamte se s konceptem [kapacity instance Azure API Management](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Upgrade a škálování  

Můžete si vybrat mezi čtyřmi úrovněmi: **Vývojář**, **Basic**, **Standard** a **Premium**. Úroveň **Developer** by měla být použita k vyhodnocení služby; neměl by být používán pro výrobu. Úroveň **Developer** nemá sla a tuto úroveň nelze škálovat (přidat nebo odebrat jednotky). 

**Základní**, **Standardní** a **Premium** jsou výrobní úrovně, které mají sla a lze je škálovat. Úroveň **Basic** je nejlevnější úroveň, která má sla a může být škálována až na 2 jednotky, **úroveň Standard** lze škálovat až na čtyři jednotky. Do úrovně **Premium** můžete přidat libovolný počet jednotek.

Úroveň **Premium** umožňuje distribuovat jednu instanci Azure API Management na libovolný počet požadovaných oblastí Azure. Když zpočátku vytvoříte službu Azure API Management, instance obsahuje jenom jednu jednotku a je umístěna v jedné oblasti Azure. Počáteční oblast je označena jako **primární** oblast. Další oblasti lze snadno přidat. Při přidávání oblasti zadáte počet jednotek, které chcete přidělit. Můžete mít například jednu jednotku v **primární** oblasti a pět jednotek v jiné oblasti. Počet jednotek můžete přizpůsobit provozu, který máte v každé oblasti. Další informace najdete v [tématu Jak nasadit instanci služby Azure API Management do více oblastí Azure](api-management-howto-deploy-multi-region.md).

Můžete upgradovat a downgrade do a z libovolné úrovně. Všimněte si, že upgrade nebo downgrading můžete odebrat některé funkce – například VNETs nebo více oblastí nasazení, při přechodu na standardní nebo základní z úrovně Premium.

> [!NOTE]
> Použití upgradu nebo škálování může trvat 15 až 45 minut. Budete upozorněni, když je hotovo.

> [!NOTE]
> Služba API Management ve vrstvě **spotřeby** se automaticky škáluje na základě provozu.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Upgrade a škálování pomocí portálu Azure

![Škálování APIM na webu Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. Přejděte na instanci APIM na [webu Azure Portal](https://portal.azure.com/).
2. V nabídce vyberte **Měřítko a ceny.**
3. Vyberte požadovanou úroveň.
4. Zadejte počet **jednotek, které** chcete přidat. Můžete použít posuvník nebo zadat počet jednotek.  
    Pokud zvolíte úroveň **Premium,** musíte nejprve vybrat oblast.
5. Stiskněte **Uložit**.

## <a name="downtime-during-scaling-up-and-down"></a>Prostoje při rozšiřování nahoru a dolů
Pokud jste škálování z nebo na úroveň Developer, bude prostoje. V opačném případě nedochází k prostojům. 


## <a name="next-steps"></a>Další kroky

- [Postup při nasazení instance služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
- [Jak automaticky škálovat instanci služby Azure API Management](api-management-howto-autoscale.md)
