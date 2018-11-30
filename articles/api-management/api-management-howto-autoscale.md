---
title: Konfigurace automatického škálování instance Azure API Management | Dokumentace Microsoftu
description: Toto téma popisuje postup nastavení chování automatického škálování pro instance Azure API Management.
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
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444798"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatické škálování instance služby Azure API Management  

Azure API Management, které instanci služby můžete škálovat automaticky na základě sady pravidel. Toto chování lze povolit a nakonfigurovat pomocí Azure monitoru a je podporován pouze v **standardní** a **Premium** úrovně služby Azure API Management.

Tento článek vás provede procesem konfigurace automatického škálování a navrhne optimální konfigurace pravidla automatického škálování.

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v tomto článku, musíte:

+ Máte aktivní předplatné Azure.
+ Žádná instance Azure API Management. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).
+ Vysvětlení konceptu [kapacity instance Azure API Management](api-management-capacity.md).
+ Vysvětlení [ruční škálování procesu instance Azure API Management](upgrade-and-scale.md), včetně nákladů důsledky.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Omezení automatického škálování služby Azure API Management

Určitá omezení a důsledků škálování rozhodnutí třeba zvážit před konfigurací chování automatického škálování.

+ Automatické škálování se dá nastavit jenom pro **standardní** a **Premium** úrovně služby Azure API Management.
+ Cenové úrovně zadejte také maximální počet jednotek pro instance služby.
+ Škálování procesu bude trvat nejméně 20 minut.
+ Pokud služba je uzamčen jiná operace, žádost o škálování se nezdařily a opakovat automaticky.
+ V případě služby u nasazení ve více oblastí, pouze jednotek **primární umístění** je možné škálovat. Nejde škálovat jednotky v jiných umístěních.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Povolení a konfigurace automatického škálování pro službu Azure API Management

Podle následujících pokynů ke konfiguraci automatického škálování pro službu Azure API Management:

1. Přejděte do **monitorování** instance na webu Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Vyberte **automatického škálování** z nabídky na levé straně.

    ![Automatické škálování prostředků Azure monitoru](media/api-management-howto-autoscale/02.png)

3. Vyhledání služby Azure API Management na základě filtrů v rozevíracích nabídek.
4. Vyberte požadovanou instanci služby Azure API Management.
5. V nově otevřeném části, klikněte **povolit automatické škálování** tlačítko.

    ![Povolit automatické škálování Azure monitoru](media/api-management-howto-autoscale/03.png)

6. V **pravidla** klikněte na tlačítko **+ přidat pravidlo**.

    ![Přidání pravidla pro automatické škálování služby Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Definujte nový horizontální navýšení kapacity pravidlo.

   Například s horizontálním navýšením kapacity pravidlo by mohly aktivovat doplněk jednotky Azure API Management, když průměrné kapacity metriky za posledních 30 minut překročí 80 %. Následující tabulka obsahuje konfiguraci pro toto pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální prostředek  | Definujte pravidla na základě aktuální metrik prostředků Azure API Management.                                                                                                                                                                                                     |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Název metriky           | Kapacita          | Kapacitní metriky je metriku Azure API Management odráží využití prostředků, instance Azure API Management.                                                                                                                                                            |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                 |
    | Operátor              | Větší než      |                                                                                                                                                                                                                                                                                 |
    | Prahová hodnota             | 80 %               | Prahové hodnoty pro metriku průměrné kapacity.                                                                                                                                                                                                                                 |
    | Doba trvání (v minutách) | 30                | Časový interval pro výpočet průměru metriku kapacity přes je specifický pro vzorce používání. Čím delší je doba, tím hladší bude reakce – krátkodobých špiček bude mít menší dopad na rozhodnutí horizontální navýšení kapacity. Ale také způsobí zpoždění aktivační událost horizontální navýšení kapacity. |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operace             | Zvýšit počet o |                                                                                                                                                                                                                                                                                 |
    | Počet instancí        | 1                 | Horizontální navýšení kapacity instance Azure API Management 1 jednotku.                                                                                                                                                                                                                          |
    | Přestávka (minuty)   | 60                | Trvá aspoň 20 minut pro službu Azure API Management pro horizontální navýšení kapacity. Ve většině případů nástrojů snížit dobu 60 minut zabraňuje aktivací mnoho škálování činnostem.                                                                                                  |

8. Klikněte na tlačítko **přidat** uložíte pravidlo.

    ![Azure Monitor Horizontální navýšení kapacity pravidlo](media/api-management-howto-autoscale/05.png)

9. Klikněte znovu na **+ přidat pravidlo**.

    Tentokrát škálování v pravidle musí být definován. To zajistí, že prostředky nejsou se ztraceny, pokud snižuje využití rozhraní API.

10. Definujte novou škálovací v pravidle.

    Například škálování v pravidle způsobovat společně s Azure API Management jednotky, po nižší než 35 % průměrná kapacity metriky za posledních 30 minut. Následující tabulka obsahuje konfiguraci pro toto pravidlo.

    | Parametr             | Hodnota             | Poznámky                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zdroj metriky         | Aktuální prostředek  | Definujte pravidla na základě aktuální metrik prostředků Azure API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kritéria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Časová agregace      | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Název metriky           | Kapacita          | Stejné metriky, jako jste použili pro horizontální navýšení kapacity pravidlo.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statistika agregačního intervalu  | Průměr           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operátor              | Méně než         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Prahová hodnota             | 35 %               | Podobně pro horizontální navýšení kapacity pravidla, tato hodnota silně závisí na vzory používání služby Azure API Management. |
    | Doba trvání (v minutách) | 30                | Stejnou hodnotu jako jste použili pro horizontální navýšení kapacity pravidlo.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akce*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operace             | Snížit počet o | Než jaký se použil pro horizontální navýšení kapacity pravidlo.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Počet instancí        | 1                 | Stejnou hodnotu jako jste použili pro horizontální navýšení kapacity pravidlo.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Přestávka (minuty)   | 90                | Horizontální snížení kapacity by mělo být konzervativnější než horizontální navýšení kapacity, aby nástrojů dolů období by měl být delší.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Klikněte na tlačítko **přidat** uložíte pravidlo.

    ![Škálování Azure monitoru v pravidle](media/api-management-howto-autoscale/06.png)

12. Nastavte **maximální** počet jednotek Azure API Management.

    > [!NOTE]
    > Azure API Management má limit jednotek, které instance můžete horizontální navýšení kapacity na. Omezení závisí na vrstvu služby.

    ![Škálování Azure monitoru v pravidle](media/api-management-howto-autoscale/07.png)

13. Klikněte na **Uložit**. Vaše automatické škálování není nakonfigurovaná.

## <a name="next-steps"></a>Další postup

+ [Jak nasadit instanci služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
