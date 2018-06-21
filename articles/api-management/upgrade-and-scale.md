---
title: Upgrade a škálování instance služby Azure API Management | Microsoft Docs
description: Toto téma popisuje, jak upgradovat a škálovat instance služby Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: ca32c72b1582b2a09f9f1754ad778cf1b682a1c2
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293308"
---
# <a name="upgrade-and-scale-an-api-management-instance"></a>Upgrade a škálování instance služby API Management  

Zákazníci můžete škálovat instance API Management (APIM) tak, že přidání a odebrání jednotek. A **jednotky** se skládá z vyhrazených prostředků Azure a má určité nosné kapacitu, vyjádřené jako počet rozhraní API volání za měsíc. Toto číslo nepředstavuje limit volání, ale spíše hodnotu maximální propustnost umožňující plánování hrubý kapacity. Skutečné propustnosti a latence široce lišit v závislosti na faktorech, například číslo a počet souběžných připojení, typ a počet nakonfigurované zásady, velikost požadavku a odpovědi a back-end latence.

Kapacitu a cenu každé jednotky, závisí na **vrstvy** v které jednotka existuje. Můžete si vybrat mezi čtyři úrovně: **vývojáře**, **základní**, **standardní**, **Premium**. Pokud je potřeba zvýšit kapacitu služby v rámci vrstvu, měli byste přidat jednotku. Pokud vrstvy, který je aktuálně vybraný v instanci APIM neumožňuje přidávat další jednotky, budete muset upgrade na vyšší úrovni vrstvy.

Cena jednotlivých jednotek a dostupných funkcí (například nasazení více oblast) závisí na vrstvu, kterou jste zvolili pro vaše instance APIM. [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) článek, vysvětluje cena za jednotku a funkce můžete získat na jednotlivých úrovních. 

>[!NOTE]
>[Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) článek ukazuje přibližný počet jednotek kapacity v jednotlivých úrovních. Chcete-li získat přesnější čísla, podívejte se na scénáři realistické pro vaše rozhraní API. Najdete v článku [kapacity instance Azure API Management](api-management-capacity.md) článku.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupujte podle kroků v tomto článku, postupujte takto:

+ Máte aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Máte instanci APIM. Další informace najdete v tématu [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

+ Porozumět koncepci [kapacity instance služby Azure API Management] (api-management-capacity.md).

## <a name="upgrade-and-scale"></a>Upgrade a škálování  

Můžete si vybrat mezi čtyři úrovně: **vývojáře**, **základní**, **standardní** a **Premium**. **Vývojáře** vrstvy se má použít k vyhodnocení služby; nesmí být použita pro produkční prostředí. **Vývojáře** úroveň nemá smlouvy o úrovni služeb a nemůže škálovat tuto vrstvu (Přidat nebo odebrat jednotky). 

**Základní**, **standardní** a **Premium** jsou produkční vrstvy, které mají SLA a je možné rozšířit. **Základní** úroveň je nejlevnější vrstvy, který má SLA a škálovat maximálně 2 jednotek, může být **standardní** vrstvu lze škálovat až čtyři jednotkách. Můžete přidat libovolný počet jednotek na **Premium** vrstvy.

**Premium** vrstvy umožňuje distribuovat jedna instance API management napříč jakékoli číslo požadované oblasti Azure. Když vytvoříte původně služby API Management, instance obsahuje pouze jednu jednotku a se nachází v jedné oblasti Azure. Počáteční oblast je určený jako **primární** oblast. Další oblasti lze snadno přidat. Při přidávání oblast, je třeba zadat počet jednotek, které chcete přidělit. Například může mít jednu jednotku v **primární** oblast a pět jednotky v některé jiné oblasti. Počet jednotek pro provoz, který máte v každé oblasti můžete přizpůsobit. Další informace najdete v tématu [nasazení instanci služby Azure API Management na několika oblastmi Azure](api-management-howto-deploy-multi-region.md).

Můžete upgradovat a starší verzi do a z libovolné úrovně. Všimněte si, že upgrade nebo přechod na starší verzi můžete odebrat některé funkce – například virtuální sítě nebo nasazení s více oblast, když přechod na starší verzi standardní nebo základní z úrovně Premium.

>[!NOTE]
>Proces upgradu nebo škálování může trvat od 15 až 45 minut použít. Pokud se provádí zobrazí oznámení.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Použijte portál Azure pro upgrade a škálování

![Škálování APIM na portálu Azure](./media/upgrade-and-scale/portal-scale.png)

1. Přejděte k vaší instanci APIM v [portál Azure](https://portal.azure.com/).
2. Vyberte **škálování a ceny** z nabídky.
3. Vyberte požadovanou úroveň.
4. Zadejte počet **jednotky** chcete přidat. Můžete buď použít posuvník nebo zadat počet jednotek.  
    Pokud se rozhodnete **Premium** úroveň, je nutné nejprve vybrat oblast.
5. Stiskněte klávesu **uložit**

## <a name="next-steps"></a>Další postup

[Postup nasazení instanci služby Azure API Management na několika oblastmi Azure](api-management-howto-deploy-multi-region.md)