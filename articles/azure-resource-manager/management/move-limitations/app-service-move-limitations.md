---
title: Přesunutí prostředků služby Azure App Service
description: Pomocí Správce prostředků Azure přesuňte prostředky služby App Service do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655774"
---
# <a name="move-guidance-for-app-service-resources"></a>Přesunutí pokynů pro prostředky služby App Service

Tento článek popisuje kroky k přesunutí prostředků služby App Service. Existují specifické požadavky na přesunutí prostředků služby App Service do nového předplatného.

## <a name="move-across-subscriptions"></a>Přechod mezi předplatnými

Při přesouvání webové aplikace mezi předplatnými platí následující pokyny:

- Cílová skupina prostředků nesmí mít žádné existující prostředky služby App Service. Mezi prostředky služby App Service patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty TLS/SSL
    - Prostředí App Service
- Všechny prostředky služby App Service ve skupině prostředků musí být přesunuty společně. Všimněte si, že prostředí služby App Service nelze přesunout do nové skupiny prostředků ani do nového předplatného.
- Certifikát vázaný na web můžete přesunout bez odstranění vazeb TLS, pokud je certifikát přesunut se všemi ostatními prostředky ve skupině prostředků.
- Prostředky služby App Service lze přesunout pouze ze skupiny prostředků, ve kterém byly původně vytvořeny. Pokud prostředek služby App Service již není v původní skupině prostředků, přesuňte jej zpět do původní skupiny prostředků. Potom přesuňte prostředek mezi předplatnými.

Pokud si nepamatujete původní skupinu prostředků, můžete ji najít prostřednictvím diagnostiky. Pro webovou aplikaci vyberte **Diagnostikovat a řešit problémy**. Potom vyberte **Možnost Konfigurace a správa**.

![Vybrat diagnostiku](./media/app-service-move-limitations/select-diagnostics.png)

Vyberte **možnosti migrace**.

![Vybrat možnosti migrace](./media/app-service-move-limitations/select-migration.png)

Vyberte možnost doporučených kroků pro přesunutí webové aplikace.

![Výběr doporučených kroků](./media/app-service-move-limitations/recommended-steps.png)

Zobrazí se doporučené akce, které je třeba provést před přesunutím prostředků. Informace zahrnují původní skupinu prostředků pro webovou aplikaci.

![Doporučení](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Přesun podpory

Pokud chcete zjistit, které prostředky služby App Service se dá přesunout, přečtěte si, proč chcete přesunout stav podpory:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Další kroky

Příkazy k přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
