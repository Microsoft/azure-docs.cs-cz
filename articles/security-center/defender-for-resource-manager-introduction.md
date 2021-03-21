---
title: Azure Defender pro Správce prostředků – výhody a funkce
description: Přečtěte si o výhodách a funkcích Azure Defenderu pro Správce prostředků
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 903853f9f37921a68c918d09a94087548b5c562c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100657"
---
# <a name="introduction-to-azure-defender-for-resource-manager"></a>Seznámení s Azure Defenderem pro Správce prostředků

[Azure Resource Manager](../azure-resource-manager/management/overview.md) je služba pro nasazení a správu pro Azure. Poskytuje úroveň správy, která vám umožňuje vytvářet, aktualizovat a odstraňovat prostředky v účtu Azure. Pomocí funkcí správy, jako jsou řízení přístupu, zámky a značky, můžete zabezpečit a organizovat prostředky po nasazení.

Vrstva Cloud Management je zásadní službou připojenou ke všem vašim cloudovým prostředkům. Z tohoto důvodu je to také potenciální cíl pro útočníky. V důsledku toho doporučujeme, aby týmy provozu zabezpečení sledovaly vrstvu správy prostředků pečlivě. 

Azure Defender pro Správce prostředků automaticky monitoruje operace správy prostředků ve vaší organizaci bez ohledu na to, jestli se provádějí prostřednictvím Azure Portal, Azure REST API, Azure CLI nebo jiných programových klientů Azure. Azure Defender spustí pokročilou analýzu zabezpečení a detekuje hrozby a upozorní vás na podezřelou aktivitu.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Stanov|**Azure Defender pro správce prostředků** se účtuje tak, jak je znázorněno na [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/) .|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-resource-manager"></a>Jaké jsou výhody Azure Defenderu pro Správce prostředků?

Azure Defender pro Správce prostředků chrání proti problémům, včetně:

- **Podezřelé operace správy prostředků**, například operace z podezřelých IP adres, zakázání antimalwarových a podezřelých skriptů spuštěných v rozšířeních virtuálních počítačů
- **Využití nástrojů pro využívání,** jako je mikroshlukování nebo PowerZure
- **Boční pohyb** z vrstvy správy Azure na rovinu dat prostředků Azure

:::image type="content" source="media/defender-for-resource-manager-introduction/consistent-management-layer-with-defender.png" alt-text="Diagram přehledu Azure Resource Manager":::

Úplný seznam výstrah poskytovaných programem Azure Defender pro Správce prostředků najdete na [stránce s referenčními údaji o výstrahách](alerts-reference.md#alerts-resourcemanager).


 ## <a name="how-to-investigate-alerts-from-azure-defender-for-resource-manager"></a>Jak prozkoumat výstrahy z Azure Defenderu pro Správce prostředků

Výstrahy zabezpečení z Azure Defenderu pro Správce prostředků jsou založené na hrozbách zjištěných monitorováním Azure Resource Managerch operací. Azure Defender používá interní zdroje protokolů Azure Resource Manager a také protokol aktivit Azure, protokol platformy v Azure, který poskytuje přehled o událostech na úrovni předplatného.

Přečtěte si další informace o [protokolu aktivit Azure](../azure-monitor/essentials/activity-log.md).

Postup při zkoumání výstrah zabezpečení z Azure Defenderu pro Správce prostředků:

1. Otevřete protokol aktivit Azure.

    :::image type="content" source="media/defender-for-resource-manager-introduction/opening-azure-activity-log.png" alt-text="Jak otevřít protokol aktivit Azure":::

1. Filtrovat události na:
    - Předplatné zmíněné v upozornění
    - Časový rámec zjištěné aktivity
    - Související uživatelský účet (Pokud je relevantní)

1. Vyhledejte podezřelé aktivity.

> [!TIP]
> Pro lepší, bohatší prostředí pro šetření můžete streamovat protokoly aktivit Azure do Azure Sentinel, jak je popsáno v tématu [připojení dat z protokolu aktivit Azure](../sentinel/connect-azure-activity.md).



## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o Azure Defenderu pro Správce prostředků. Související materiály najdete v následujícím článku: 

- Výstrahy zabezpečení mohou být generovány Security Center nebo obdrženy Security Center z různých produktů zabezpečení. Pokud chcete všechny tyto výstrahy exportovat do Azure Sentinel, jakýchkoli SIEM třetích stran nebo jakýchkoli jiných externích nástrojů, postupujte podle pokynů v tématu [Export výstrah do Siem](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Povolení Azure Defenderu](enable-azure-defender.md)