---
title: Upgrade a škálování instance služby Azure API Management | Dokumentace Microsoftu
description: Toto téma popisuje, jak upgradovat a škálovat instance Azure API Management.
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
ms.openlocfilehash: 2aef9b6f32d562776be3e3da25b017d5ff88c4c4
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024312"
---
# <a name="upgrade-and-scale-an-azure-api-management-instance"></a>Upgrade a škálování instance služby Azure API Management  

Zákazníci můžou škálovat instance Azure API Management (APIM) přidáváním a odebíráním jednotek. A **jednotky** se skládá z vyhrazené prostředky Azure a má na určité nosné kapacitu, vyjádřené jako počtu volání API za měsíc. Toto číslo nepředstavuje limit volání, ale spíše hodnotu maximální propustnost umožňující plánování hrubý kapacity. Skutečná propustnost a latence široce lišit v závislosti na faktorech, jako je počet a frekvence souběžných připojení, typ a počet konfigurovaných zásad, velikosti požadavků a odpovědí a latence back-endu.

Kapacitu a cena za každou jednotku závisí **úroveň** v které existence jednotky. Můžete si vybrat mezi 4 úrovních: **Developer**, **základní**, **standardní**, **Premium**. Pokud potřebujete ke zvýšení kapacity pro službu v rámci úrovně, měli byste přidat jednotku. Pokud na úrovni, která je aktuálně vybrána ve vaší instanci APIM neumožňuje přidání víc jednotek, budete muset upgradovat na vrstvu vyšší úrovně.

Cena každé jednotky a dostupné funkce (například nasazení v různých oblastech) závisí na úroveň, kterou jste zvolili pro vaši instanci služby APIM. [Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) článek, vysvětluje cena za jednotku a funkcích získáte v každé úrovni. 

>[!NOTE]
>[Podrobnosti o cenách](https://azure.microsoft.com/pricing/details/api-management/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) článek ukazuje přibližný počet jednotku kapacity v každé úrovni. Chcete-li získat přesnější čísla, podívejte se na reálné scénáře pro vaše rozhraní API. Zobrazit [kapacity instance Azure API Management](api-management-capacity.md) článku.

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v tomto článku, musíte:

+ Máte aktivní předplatné Azure.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Máte instanci služby APIM. Další informace najdete v tématu [vytvoření instance Azure API Management](get-started-create-service-instance.md).

+ Vysvětlení konceptu [kapacity instance Azure API Management](api-management-capacity.md).

## <a name="upgrade-and-scale"></a>Upgrade a škálování  

Můžete si vybrat mezi 4 úrovních: **Developer**, **základní**, **standardní** a **Premium**. **Developer** úroveň by měla sloužit pro vyhodnocení služby; by neměla být sloužící k produkčním účelům. **Developer** úroveň nemá smlouvu SLA a už nebude možné škálovat tuto úroveň (Přidání nebo odebrání jednotky). 

**Základní**, **standardní** a **Premium** jsou produkční úrovně, smlouvy SLA, která je možné škálovat. **Základní** úroveň nejlevnější úroveň, která se neuzavírá smlouva SLA, může být škálován až 2 jednotky **standardní** úroveň můžete škálovat až čtyři jednotky. Můžete přidat libovolný počet jednotek, aby se **Premium** vrstvy.

**Premium** úroveň umožňuje distribuovat jediné instance Azure API Management napříč libovolným počtem požadované oblasti Azure. Při počátečním vytvoření služby Azure API Management, instance obsahuje pouze jednu jednotku a je umístěn v jedné oblasti Azure. Počáteční oblast je vyhrazená jako **primární** oblasti. Je možné snadno přidat další oblasti. Při přidávání oblast, zadejte počet jednotek, které chcete přidělit. Například můžete mít jednu jednotku **primární** oblasti a pěti jednotek v některé oblasti. Můžete přizpůsobit počet jednotek, aby se provoz, který máte v jednotlivých oblastech. Další informace najdete v tématu [jak nasadit instanci služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md).

Může upgrade a downgrade do a ze všech úrovních. Všimněte si, že upgradu nebo downgradu můžete odebrat některé funkce – například virtuálních sítí nebo nasazení ve více oblastech, když Downgrade z úrovně Premium na Standard nebo Basic.

>[!NOTE]
>Upgrade nebo určený počet číslic proces může trvat 15 až 45 minut použít. Po dokončení, dostanete oznámení.

## <a name="use-the-azure-portal-to-upgrade-and-scale"></a>Upgrade a škálování pomocí webu Azure portal

![Škálování služby APIM na webu Azure portal](./media/upgrade-and-scale/portal-scale.png)

1. Přejděte k vaší instanci APIM v [webu Azure portal](https://portal.azure.com/).
2. Vyberte **škálování a ceny** z nabídky.
3. Vyberte požadovanou úroveň.
4. Zadejte počet **jednotky** chcete přidat. Můžete buď pomocí posuvníku nebo zadat počet jednotek.  
    Pokud se rozhodnete **Premium** vrstvy, musíte nejprve vybrat oblast.
5. Stiskněte **Uložit**.

## <a name="next-steps"></a>Další postup

- [Jak nasadit instanci služby Azure API Management do několika oblastí Azure](api-management-howto-deploy-multi-region.md)
- [Jak automaticky horizontálně snižovat instanci služby Azure API Management](api-management-howto-autoscale.md)
