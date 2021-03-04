---
title: Upgrade na Azure Defender – Azure Security Center
description: V tomto rychlém startu se dozvíte, jak upgradovat na Azure Defender pro další zabezpečení Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 2f37a59d5db3883754b602b2b2525e07b57206b7
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099348"
---
# <a name="quickstart-set-up-azure-security-center"></a>Rychlý Start: nastavení Azure Security Center

Azure Security Center poskytuje jednotnou správu zabezpečení a ochranu před hrozbami napříč hybridními a víceúčelovými úlohami. I když bezplatné funkce nabízí jenom omezené zabezpečení pro vaše prostředky Azure, takže Azure Defender tyto možnosti rozšiřuje na místní a jiné cloudy. Azure Defender vám pomůže najít a opravit chyby zabezpečení, použít řízení přístupu a aplikací k blokování škodlivých aktivit, zjišťovat hrozby pomocí analýz a inteligentních funkcí a rychle reagovat při útoku. Azure Defender si můžete vyzkoušet bez jakýchkoli nákladů. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

V této části se seznámíte se všemi doporučenými kroky pro povolení Azure Security Center a Azure Defenderu. Po dokončení všech kroků rychlého startu budete mít následující:

> [!div class="checklist"]
> * Security Center povolených ve vašich předplatných Azure
> * Azure Defender povolený ve vašich předplatných Azure
> * Nastavení automatického shromažďování dat
> * Nastavení e-mailových oznámení pro výstrahy zabezpečení
> * Vaše hybridní a multi-cloudové počítače připojené k Azure

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Pokud nemáte předplatné, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

Pokud chcete povolit Azure Defender v rámci předplatného, musíte mít přiřazenou roli Vlastník předplatného, přispěvatele předplatného nebo správce zabezpečení.

## <a name="enable-security-center-on-your-azure-subscription"></a>Povolení Security Center v předplatném Azure

> [!TIP]
> Pokud chcete povolit Security Center u všech předplatných v rámci skupiny pro správu, přečtěte si téma [povolení Security Center ve více předplatných Azure](onboard-management-group.md).

1. Přihlaste se k webu [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

1. V nabídce portálu vyberte **Security Center**. 

    Otevře se stránka s přehledem Security Center.

    :::image type="content" source="./media/security-center-get-started/overview.png" alt-text="Řídicí panel s přehledem Security Center" lightbox="./media/security-center-get-started/overview.png":::

    Stránka **Security Center – Přehled** poskytuje jednotný přehled o stavu hybridních cloudových úloh a umožňuje tak zkoumat a posuzovat zabezpečení úloh a identifikaci a omezení rizika. Security Center automaticky, a to bez nákladů, povolí žádné z vašich předplatných Azure, které jste předtím nepřipojili vy nebo jiný uživatel předplatného.

Seznam předplatných můžete zobrazit a filtrovat tak, že vyberete položku nabídky **předplatná** . Security Center upraví displej tak, aby odrážel stav zabezpečení vybraných předplatných. 

Během několika minut od prvního spuštění Security Center se může zobrazit:

- **Doporučení** , jak zvýšit zabezpečení připojených prostředků.
- Inventarizace vašich prostředků, které se teď Security Center společně s stav zabezpečení každého z nich.

Pokud chcete plně využít výhod Security Center, pokračujte dalšími kroky v části rychlý Start.



## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste povolili Azure Security Center. Dalším krokem je povolit Azure Defender pro jednotnou správu zabezpečení a ochranu před hrozbami napříč vašimi hybridními cloudy.

> [!div class="nextstepaction"]
> [Rychlý Start: povolení Azure Defenderu](enable-azure-defender.md)