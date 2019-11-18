---
title: Přesunout Azure App Service prostředky
description: K přesunutí prostředků App Service do nové skupiny prostředků nebo předplatného použijte Azure Resource Manager.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: dca9b3a8f328192683cfde586f0ccdb01e84dc16
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150893"
---
# <a name="move-guidance-for-app-service-resources"></a>Pokyny pro přesun App Servicech prostředků

Postup přesunutí prostředků App Service se liší v závislosti na tom, jestli přesouváte prostředky v rámci předplatného nebo na nové předplatné.

## <a name="move-in-same-subscription"></a>Přesunout do stejného předplatného

Při přesunu webové aplikace _v rámci stejného předplatného_nemůžete PŘESOUVAT certifikáty SSL třetích stran. Webovou aplikaci ale můžete přesunout do nové skupiny prostředků bez přesunu jejího certifikátu třetí strany a funkce SSL vaší aplikace pořád funguje.

Pokud chcete přesunout certifikát SSL s webovou aplikací, postupujte podle těchto kroků:

1. Odstraňte certifikát třetí strany z webové aplikace, ale uchovejte kopii certifikátu.
2. Přesun webové aplikace.
3. Nahrajte do přesunuté webové aplikace certifikát třetí strany.

## <a name="move-across-subscriptions"></a>Pohyb mezi předplatnými

Při přesunu webovou aplikaci _napříč předplatnými_, platí následující omezení:

- Cílová skupina prostředků nesmí mít nějaké stávající prostředky App Service. Prostředky App Service patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty SSL
    - Prostředí App Service
- Všechny prostředky App Service ve skupině prostředků daly přesunout najednou.
- Prostředky App Service lze pouze přesunout ze skupiny prostředků, ve kterém byly původně vytvořeny. Pokud prostředek App Service už není v původní skupině prostředků, přesuňte ho zpátky do původní skupiny prostředků. Pak přemístěte prostředek napříč předplatnými.

Pokud si původní skupinu prostředků nepamatujete, můžete ji najít prostřednictvím diagnostiky. Pro webovou aplikaci vyberte možnost **diagnostikovat a vyřešit problémy**. Pak vyberte **Konfigurace a Správa**.

![Vybrat diagnostiku](./media/app-service-move-limitations/select-diagnostics.png)

Vyberte **Možnosti migrace**.

![Výběr možností migrace](./media/app-service-move-limitations/select-migration.png)

Pro přesunutí webové aplikace vyberte možnost Doporučený postup.

![Vyberte doporučené kroky.](./media/app-service-move-limitations/recommended-steps.png)

Zobrazí se doporučené akce, které je třeba provést před přesunutím prostředků. Tyto informace zahrnují původní skupinu prostředků pro webovou aplikaci.

![Doporučení](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Přesunout App Service Certificate

App Service Certificate můžete přesunout do nové skupiny prostředků nebo předplatného. Pokud je váš App Service Certificate vázaný na webovou aplikaci, musíte před přesunutím prostředků do nového předplatného provést některé kroky. Před přesunutím prostředků Odstraňte vazbu SSL a privátní certifikát z webové aplikace. App Service Certificate není nutné odstraňovat, stačí jenom privátní certifikát ve webové aplikaci.

## <a name="move-support"></a>Přesun podpory

Chcete-li zjistit, které prostředky App Service lze přesunout, přečtěte si téma Přesunutí stavu podpory pro:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
