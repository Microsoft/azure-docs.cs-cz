---
title: Konfigurace automatického škálování instance Azure API Management | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018293"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatické škálování instance služby Azure API Management  

Instance služby Azure API Management se dá automaticky škálovat na základě sady pravidel. Toto chování lze povolit a nakonfigurovat prostřednictvím Azure Monitor a je podporované jenom v úrovních **Standard** a **Premium** služby Azure API Management.

Článek prochází procesem konfigurace automatického škálování a navrhuje optimální konfiguraci pravidel automatického škálování.

> [!NOTE]
> Služba API Management ve vrstvě **spotřeby** se automaticky škáluje na základě provozu – bez nutnosti další konfigurace.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků z tohoto článku, musíte:

+ Mít aktivní předplatné Azure.
+ Mít instanci Azure API Management. Další informace najdete [v tématu Vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Seznamte se s konceptem [kapacity instance Azure API Management](api-management-capacity.md).
+ Principy [procesu ručníškálování instance Azure API Management](upgrade-and-scale.md), včetně nákladů důsledky.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Omezení automatického škálování azure api managementu

Před konfigurací chování automatického škálování je třeba zvážit určitá omezení a důsledky rozhodování o škálování.

+ Automatické škálování lze povolit jenom pro **úrovně Standard** a **Premium** služby Azure API Management.
+ Cenové úrovně také určují maximální počet jednotek pro instanci služby.
+ Proces škálování bude trvat nejméně 20 minut.
+ Pokud je služba uzamčena jinou operací, požadavek škálování se nezdaří a opakujte automaticky.
+ V případě služby s víceregionálními nasazeními lze škálovat pouze jednotky v **primárním umístění.** Jednotky v jiných umístěních nelze škálovat.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Povolení a konfigurace automatického škálování pro službu Azure API Management

Podle následujících kroků nakonfigurujte automatické škálování pro službu Azure API Management:

1. Přejděte na instanci **Monitor** na webu Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Z nabídky vlevo vyberte **Automatické škálování.**

    ![Prostředek automatického škálování Azure Monitoru](media/api-management-howto-autoscale/02.png)

3. Vyhledejte službu Azure API Management na základě filtrů v rozevíracích nabídkách.
4. Vyberte požadovanou instanci služby Azure API Management.
5. V nově otevřené části klikněte na tlačítko **Povolit automatické škálování.**

    ![Povolení automatického škálování Azure Monitoru](media/api-management-howto-autoscale/03.png)

6. V části **Pravidla** klikněte na **+ Přidat pravidlo**.

    ![Pravidlo přidání automatického škálování Azure Monitoru](media/api-management-howto-autoscale/04.png)

7. Definujte nové pravidlo horizontálního navýšení kapacity.

   Pravidlo horizontálního navýšení kapacity může například spustit přidání jednotky správy rozhraní API Azure, když metrika průměrné kapacity za posledních 30 minut překročí 80 %. Níže uvedená tabulka obsahuje konfiguraci pro takové pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální zdroj  | Definujte pravidlo na základě aktuální metriky prostředků správy rozhraní Azure API.                                                                                                                                                                                                     |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Název metriky           | Kapacita          | Metrika kapacity je metrika správy rozhraní Azure API odrážející využití prostředků instance Azure API Management.                                                                                                                                                            |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Operátor              | Větší než      |                                                                                                                                                                                                                                                                                 |
    | Prahová hodnota             | 80 %               | Prahová hodnota pro metriku průměrné kapacity.                                                                                                                                                                                                                                 |
    | Doba trvání (v minutách) | 30                | Časový posun pro průměr metriky kapacity je specifický pro vzorce využití. Čím delší je časové období, tím hladší reakce bude - přerušované hroty budou mít menší vliv na rozhodnutí o horizontálním navýšení kapacity. Však bude také zpoždění horizontálnínavýšení kapacity aktivační události. |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operace             | Zvýšit počet o |                                                                                                                                                                                                                                                                                 |
    | Počet instancí        | 1                 | Škálujte instanci Azure API Management o 1 jednotku.                                                                                                                                                                                                                          |
    | Přestávka (minuty)   | 60                | Služba Azure API Management se může škálovat minimálně 20 minut. Ve většině případů doba ochlazování 60 minut zabraňuje spuštění mnoha horizontálních navýšení kapacity.                                                                                                  |

8. Kliknutím na **Přidat** pravidlo uložte.

    ![Azure Monitor horizontální navýšení kapacity pravidlo](media/api-management-howto-autoscale/05.png)

9. Klikněte znovu na **+ Přidat pravidlo**.

    Tentokrát musí být definována stupnice v pravidle. Zajistí, že prostředky nejsou plýtvání, při snížení využití api.

10. Definujte nové měřítko v pravidle.

    Škálování v pravidle může například vyvolat odebrání jednotky správy rozhraní API Azure, když metrika průměrné kapacity za posledních 30 minut byla nižší než 35 %. Níže uvedená tabulka obsahuje konfiguraci pro takové pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální zdroj  | Definujte pravidlo na základě aktuální metriky prostředků správy rozhraní Azure API.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Název metriky           | Kapacita          | Stejná metrika jako ta, která se používá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operátor              | Menší než         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Prahová hodnota             | 35 %               | Podobně jako pravidlo horizontálního navýšení kapacity tato hodnota do značné míry závisí na vzorcích využití azure api managementu. |
    | Doba trvání (v minutách) | 30                | Stejná hodnota jako hodnota použitá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operace             | Snížit počet o | Na rozdíl od toho, co bylo použito pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Počet instancí        | 1                 | Stejná hodnota jako hodnota použitá pro pravidlo horizontálního navýšení kapacity.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Přestávka (minuty)   | 90                | Škálování by mělo být konzervativnější než horizontální navýšení, takže doba ochlazování by měla být delší.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kliknutím na **Přidat** pravidlo uložte.

    ![Škálování Azure Monitoru v pravidle](media/api-management-howto-autoscale/06.png)

12. Nastavte **maximální** počet jednotek správy rozhraní API Azure.

    > [!NOTE]
    > Azure API Management má limit jednotek, které může instance škálovat. Limit závisí na úrovni služby.

    ![Škálování Azure Monitoru v pravidle](media/api-management-howto-autoscale/07.png)

13. Klikněte na **Uložit**. Automatické škálování bylo nakonfigurováno.

## <a name="next-steps"></a>Další kroky

+ [Postup při nasazení instance služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
