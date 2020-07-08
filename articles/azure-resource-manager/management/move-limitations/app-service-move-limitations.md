---
title: Přesunout Azure App Service prostředky
description: K přesunutí prostředků App Service do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/13/2019
ms.openlocfilehash: d0ecd117bdcda9238e310a3020dba19a6871a3fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80655774"
---
# <a name="move-guidance-for-app-service-resources"></a>Pokyny pro přesun App Servicech prostředků

Tento článek popisuje postup přesunutí prostředků App Service. Existují konkrétní požadavky na přesun App Servicech prostředků do nového předplatného.

## <a name="move-across-subscriptions"></a>Pohyb mezi předplatnými

Při přesunu webové aplikace v rámci předplatných platí následující pokyny:

- Cílová skupina prostředků nesmí mít žádné existující prostředky App Service. Mezi App Service prostředky patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty TLS/SSL
    - Prostředí App Service
- Všechny prostředky App Service ve skupině prostředků je potřeba přesunout společně. Všimněte si, že App Service prostředí nejde přesunout do nové skupiny prostředků ani do nového předplatného.
- Certifikát vázaný na web můžete přesunout bez odstranění vazeb TLS, pokud se certifikát přesune se všemi ostatními prostředky ve skupině prostředků.
- Prostředky App Service lze přesunout pouze ze skupiny prostředků, ve které byly původně vytvořeny. Pokud prostředek App Service už není v původní skupině prostředků, přesuňte ho zpátky do původní skupiny prostředků. Pak přemístěte prostředek napříč předplatnými.

Pokud si původní skupinu prostředků nepamatujete, můžete ji najít prostřednictvím diagnostiky. Pro webovou aplikaci vyberte možnost **diagnostikovat a vyřešit problémy**. Pak vyberte **Konfigurace a Správa**.

![Vybrat diagnostiku](./media/app-service-move-limitations/select-diagnostics.png)

Vyberte **Možnosti migrace**.

![Výběr možností migrace](./media/app-service-move-limitations/select-migration.png)

Pro přesunutí webové aplikace vyberte možnost Doporučený postup.

![Vyberte doporučené kroky.](./media/app-service-move-limitations/recommended-steps.png)

Zobrazí se doporučené akce, které je třeba provést před přesunutím prostředků. Tyto informace zahrnují původní skupinu prostředků pro webovou aplikaci.

![Doporučení](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Přesun podpory

Chcete-li zjistit, které prostředky App Service lze přesunout, přečtěte si téma Přesunutí stavu podpory pro:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
