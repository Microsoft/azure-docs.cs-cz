---
title: Konfigurace automatického škálování instance služby Azure API Management | Microsoft Docs
description: Toto téma popisuje, jak nastavit chování automatického škálování pro instanci Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: cbdc81789fcd996774090f12523e7404c0aa0111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86205851"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatické škálování instance služby Azure API Management  

Instance služby Azure API Management se může automaticky škálovat na základě sady pravidel. Toto chování se dá povolit a nakonfigurovat prostřednictvím Azure Monitor a podporuje se jenom na úrovních **Standard** a **Premium** služby Azure API Management.

Tento článek vás provede procesem konfigurace automatického škálování a navrhuje optimální konfiguraci pravidel automatického škálování.

> [!NOTE]
> Služba API Management se v úrovni **spotřeby** automaticky škáluje na základě provozu – bez nutnosti jakékoli další konfigurace.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto článku, musíte:

+ Musíte mít aktivní předplatné Azure.
+ Mít instanci Azure API Management. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Pochopení konceptu [kapacity instance služby Azure API Management](api-management-capacity.md).
+ Pochopení [procesu ručního škálování instance služby Azure API Management](upgrade-and-scale.md), včetně ovlivnění nákladů.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Omezení automatického škálování v Azure API Management

Určitá omezení a důsledky rozhodnutí o škálování je třeba zvážit před konfigurací chování automatického škálování.

+ Automatické škálování se dá povolit jenom pro úrovně **Standard** a **Premium** služby Azure API Management.
+ Cenové úrovně také určují maximální počet jednotek pro instanci služby.
+ Škálování procesu bude trvat aspoň 20 minut.
+ Pokud je služba zamčená jinou operací, požadavek na škálování selže a znovu se spustí automaticky.
+ V případě služby s více oblastmi nasazení lze škálovat pouze jednotky v **primárním umístění** . Jednotky v jiných umístěních nelze škálovat.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Povolení a konfigurace automatického škálování pro službu Azure API Management

Při konfiguraci automatického škálování pro službu Azure API Management použijte následující postup:

1. V Azure Portal přejděte na instanci **monitorování** .

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. V nabídce vlevo vyberte **Automatické škálování** .

    ![Azure Monitor prostředek automatického škálování](media/api-management-howto-autoscale/02.png)

3. Vyhledejte službu Azure API Management v závislosti na filtrech v rozevíracích nabídkách.
4. Vyberte požadovanou instanci služby Azure API Management.
5. V nově otevřené části klikněte na tlačítko **Povolit automatické škálování** .

    ![Povolit automatické škálování Azure Monitor](media/api-management-howto-autoscale/03.png)

6. V části **pravidla** klikněte na **+ Přidat pravidlo**.

    ![Azure Monitor pravidlo přidání automatického škálování](media/api-management-howto-autoscale/04.png)

7. Definujte nové pravidlo horizontálního navýšení kapacity.

   Pravidlo horizontálního navýšení kapacity může například aktivovat Přidání jednotky Azure API Management, když průměrná metrika kapacity za posledních 30 minut překračuje 80%. Následující tabulka poskytuje konfiguraci pro toto pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální prostředek  | Definujte pravidlo na základě aktuální metriky prostředků Azure API Management.                                                                                                                                                                                                     |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Název metriky           | Kapacita          | Metrika kapacity je metrika Azure API Management, která odráží využití prostředků instance Azure API Management.                                                                                                                                                            |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Operátor              | Větší než      |                                                                                                                                                                                                                                                                                 |
    | Prahová hodnota             | 80 %               | Prahová hodnota pro průměrnou metriku kapacity.                                                                                                                                                                                                                                 |
    | Doba trvání (v minutách) | 30                | Hodnota TimeSpan pro průměrnou metriku kapacity je specifická pro vzorce použití. Čím delší je časové období, hladší reakce bude mít za následek méně vliv na rozhodnutí o škálování na více instancí. Dojde ale také ke zpoždění triggeru škálování na více instancí. |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operace             | Zvýšit počet o |                                                                                                                                                                                                                                                                                 |
    | Počet instancí        | 1                 | Horizontální navýšení kapacity instance služby Azure API Management o 1 jednotku.                                                                                                                                                                                                                          |
    | Přestávka (minuty)   | 60                | Škálování služby Azure API Management pro horizontální navýšení kapacity trvá déle než 20 minut. Ve většině případů se doba trvání vypíná od 60 minut neaktivuje z aktivace řady škálování.                                                                                                  |

8. Kliknutím na tlačítko **Přidat** uložte pravidlo.

    ![Azure Monitor pravidlo horizontálního navýšení kapacity](media/api-management-howto-autoscale/05.png)

9. Klikněte znovu na **+ Přidat pravidlo**.

    Tentokrát je potřeba definovat měřítko v pravidle. Tím zajistíte, že se prostředky neprojeví, když se využití rozhraní API sníží.

10. Definujte nové měřítko v pravidle.

    Například škála v pravidle může aktivovat odebrání jednotky Azure API Management, když průměrná metrika kapacity za posledních 30 minut byla nižší než 35%. Následující tabulka poskytuje konfiguraci pro toto pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální prostředek  | Definujte pravidlo na základě aktuální metriky prostředků Azure API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Název metriky           | Kapacita          | Stejná metrika jako ta, která se používá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operátor              | Menší než         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Prahová hodnota             | 35 %               | Podobně jako pravidlo horizontálního navýšení kapacity Tato hodnota velmi závisí na vzorech využití API Management Azure. |
    | Doba trvání (v minutách) | 30                | Stejná hodnota jako ta, která se používá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operace             | Snížit počet o | Protiklad k tomu, co bylo použito pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Počet instancí        | 1                 | Stejná hodnota jako ta, která se používá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Přestávka (minuty)   | 90                | Horizontální navýšení kapacity by mělo být více konzervativní než horizontální navýšení kapacity, takže doba trvání musí být delší.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kliknutím na tlačítko **Přidat** uložte pravidlo.

    ![Azure Monitor škálování v pravidle](media/api-management-howto-autoscale/06.png)

12. Nastavte **maximální** počet jednotek Azure API Management.

    > [!NOTE]
    > Azure API Management má limit jednotek, na které může instance navýšit horizontální navýšení kapacity. Limit závisí na úrovni služby.

    ![Azure Monitor škálování v pravidle](media/api-management-howto-autoscale/07.png)

13. Klikněte na **Uložit**. Vaše automatické škálování je nakonfigurované.

## <a name="next-steps"></a>Další kroky

- [Postup při nasazení instance služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
- [Optimalizace a ukládání na cloudové útratě](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)