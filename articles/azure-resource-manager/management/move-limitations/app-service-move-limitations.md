---
title: Přesunout Azure App Service prostředky
description: K přesunutí prostředků App Service do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531368"
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
- Všechny prostředky App Service ve skupině prostředků je potřeba přesunout společně.
- App Service prostředí nejde přesunout do nové skupiny prostředků nebo předplatného. Webovou aplikaci a plán služby App Service však můžete přesunout do nového předplatného bez přesunutí App Service Environment. Po přesunutí už nebude webová aplikace hostována v App Service Environment.
- Certifikát vázaný na web můžete přesunout bez odstranění vazeb TLS, pokud se certifikát přesune se všemi ostatními prostředky ve skupině prostředků.
- Prostředky App Service lze přesunout pouze ze skupiny prostředků, ve které byly původně vytvořeny. Pokud prostředek App Service už není v původní skupině prostředků, přesuňte ho zpátky do původní skupiny prostředků. Pak přemístěte prostředek napříč předplatnými.

Pokud si původní skupinu prostředků nepamatujete, můžete ji najít prostřednictvím diagnostiky. Pro webovou aplikaci vyberte možnost **diagnostikovat a vyřešit problémy**. Pak vyberte **Konfigurace a Správa**.

![Vybrat diagnostiku](./media/app-service-move-limitations/select-diagnostics.png)

Vyberte **Možnosti migrace**.

![Výběr možností migrace](./media/app-service-move-limitations/select-migration.png)

Pro přesunutí webové aplikace vyberte možnost Doporučený postup.

![Vyberte doporučené kroky.](./media/app-service-move-limitations/recommended-steps.png)

Zobrazí se doporučené akce, které je třeba provést před přesunutím prostředků. Tyto informace zahrnují původní skupinu prostředků pro webovou aplikaci.

![Snímek obrazovky ukazuje doporučené kroky pro přesun webových prostředků Microsoft dot.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Přesun podpory

Chcete-li zjistit, které prostředky App Service lze přesunout, přečtěte si téma Přesunutí stavu podpory pro:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../move-resource-group-and-subscription.md).
