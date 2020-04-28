---
title: Upgrade a škálování instance služby Azure API Management | Microsoft Docs
description: Toto téma popisuje, jak upgradovat a škálovat instanci služby Azure API Management.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "70018240"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Upgrade a škálování instance služby Azure API Management  

Zákazníci můžou instanci Azure API Management (APIM) škálovat přidáním a odebráním jednotek. **Jednotka** se skládá z vyhrazených prostředků Azure a má určitou kapacitu s kapacitou určenou pro zatížení vyjádřenou jako počet volání rozhraní API za měsíc. Toto číslo nepředstavuje limit volání, ale spíše maximální hodnotu propustnosti, která umožňuje hrubou plánování kapacity. Skutečná propustnost a latence se v podstatě liší v závislosti na faktorech, jako je počet a frekvence souběžných připojení, druh a počet konfigurovaných zásad, velikosti požadavků a odpovědí a latence back-endu.

Kapacita a cena každé jednotky závisí na **úrovni** , ve které jednotka existuje. Můžete si vybrat ze čtyř vrstev: **Developer**, **Basic**, **Standard**, **Premium**. Pokud potřebujete zvýšit kapacitu služby v rámci vrstvy, měli byste přidat jednotku. Pokud vrstva, která je aktuálně vybraná ve vaší instanci APIM, nepovoluje přidávání dalších jednotek, musíte upgradovat na úroveň vyšší úrovně.

Cena každé jednotky a dostupné funkce (například nasazení ve více oblastech) závisí na vrstvě, kterou jste zvolili pro instanci APIM. V článku [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) se vysvětluje cena za jednotku a funkce, které získáte v jednotlivých úrovních. 

>[!NOTE]
>Článek s [podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) ukazuje přibližný počet kapacit jednotek v každé úrovni. Pokud chcete získat přesnější čísla, musíte se podívat na reálný scénář pro vaše rozhraní API. Podívejte se na [kapacitu článku instance služby Azure API Management](api-management-capacity.md) .

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto článku, musíte:

+ Máte aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Mít instanci APIM. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

+ Pochopení konceptu [kapacity instance služby Azure API Management](api-management-capacity.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upgrade-and-scale"></a>Upgrade a škálování  

Můžete si vybrat ze čtyř vrstev: **Developer**, **Basic**, **Standard** a **Premium**. K vyhodnocení služby by se měla použít vrstva pro **vývojáře** . neměl by se používat pro produkční prostředí. Úroveň pro **vývojáře** nemá smlouvu SLA a nelze škálovat tuto úroveň (přidat/odebrat jednotky). 

**Basic**, **Standard** a **Premium** jsou produkční úrovně, které mají smlouvu SLA a dají se škálovat. Úroveň **Basic** je úroveň nejlevnější, která má smlouvu SLA a je možné ji škálovat až na 2 jednotky. úroveň **Standard** se dá škálovat až na čtyři jednotky. Do úrovně **Premium** můžete přidat libovolný počet jednotek.

Úroveň **Premium** vám umožňuje distribuovat jednu instanci Azure API Management napříč libovolným počtem požadovaných oblastí Azure. Při počátečním vytvoření služby Azure API Management instance obsahuje jenom jednu jednotku a je umístěná v jedné oblasti Azure. Počáteční oblast je určena jako **primární** oblast. Další oblasti lze snadno přidat. Při přidávání oblasti určíte počet jednotek, které chcete přidělit. Například můžete mít jednu jednotku v **primární** oblasti a pět jednotek v některé jiné oblasti. Počet jednotek můžete přizpůsobit pro provoz, který máte v každé oblasti. Další informace najdete v tématu [nasazení instance služby Azure API Management Service do několika oblastí Azure](api-management-howto-deploy-multi-region.md).

Můžete upgradovat a downgradovat z libovolné úrovně a z ní. Upozorňujeme, že upgrade nebo downgrading může odebrat některé funkce (například virtuální sítě nebo nasazení ve více oblastech), když se downgrade na úroveň Standard nebo Basic z úrovně Premium.

> [!NOTE]
> Použití procesu upgradu nebo škálování může trvat 15 až 45 minut. Po dokončení se zobrazí oznámení.

> [!NOTE]
> Služba API Management se v úrovni **spotřeby** automaticky škáluje podle provozu.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Použití Azure Portal k upgradu a škálování

![Škálování APIM v Azure Portal](./media/upgrade-and-scale/portal-scale.png)

1. V [Azure Portal](https://portal.azure.com/)přejděte na svou instanci APIM.
2. V nabídce vyberte možnost **škálování a ceny** .
3. Vyberte požadovanou úroveň.
4. Zadejte počet **jednotek** , které chcete přidat. Můžete buď použít posuvník, nebo zadat počet jednotek.  
    Pokud zvolíte úroveň **Premium** , musíte nejdřív vybrat oblast.
5. Stiskněte **Uložit**.

## <a name="downtime-during-scaling-up-and-down"></a>Výpadek při vertikálním navýšení a zmenšení
Pokud provádíte škálování z nebo do úrovně Developer, dojde k výpadkům. V opačném případě nedochází k výpadkům. 


## <a name="next-steps"></a>Další kroky

- [Postup při nasazení instance služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
- [Automatické škálování instance služby Azure API Management](api-management-howto-autoscale.md)
