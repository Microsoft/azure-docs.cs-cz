---
title: Přesunutí prostředků služby Azure App Service do nové předplatné nebo skupinu prostředků
description: Použití Azure Resource Manageru pro přesun prostředků App Service pro novou skupinu prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: c1a09ff4c29a2fedfea2c165a95c042985b3c83a
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723568"
---
# <a name="move-guidance-for-app-service-resources"></a>Přesunout pokyny pro prostředky App Service

Kroky pro přesun prostředků App Service se liší na základě na, jestli přesouváte prostředky v rámci předplatného nebo do nového předplatného.

## <a name="move-in-same-subscription"></a>Přesunout v rámci stejného předplatného

Při přesunu webovou aplikaci _v rámci stejného předplatného_, certifikátů SSL třetí strany se nedá přesunout. Však webové aplikace můžete přesunout do nové skupiny prostředků bez přesouvání svůj certifikát třetí strany a funkce vaší aplikace SSL stále funguje.

Pokud chcete přesunout certifikát SSL s webovou aplikací, postupujte podle těchto kroků:

1. Odstranit certifikát třetí strany z webové aplikace, ale ponechat si kopii certifikátu
2. Přesun webové aplikace.
3. Nahrajte certifikát třetí strany do přesunutý webové aplikace.

## <a name="move-across-subscriptions"></a>Přesun mezi předplatnými

Při přesunu webovou aplikaci _napříč předplatnými_, platí následující omezení:

- Cílová skupina prostředků nesmí mít nějaké stávající prostředky App Service. Prostředky App Service patří:
    - Web Apps
    - Plány služby App Service
    - Nahrané nebo importované certifikáty SSL
    - Prostředí App Service
- Všechny prostředky App Service ve skupině prostředků daly přesunout najednou.
- Prostředky App Service lze pouze přesunout ze skupiny prostředků, ve kterém byly původně vytvořeny. Pokud prostředek služby App Service je už v jeho původní skupiny prostředků, přesuňte ho zpátky do původní skupiny prostředků. Přesuňte prostředek napříč předplatnými.

Pokud si nepamatujete původní skupiny prostředků, najdete ho pomocí diagnostiky. Pro vaši webovou aplikaci, vyberte **diagnostikovat a řešit problémy**. Vyberte **konfiguraci a správě**.

![Vyberte diagnostiky](./media/app-service-move-limitations/select-diagnostics.png)

Vyberte **možnosti migrace**.

![Vybrat možnosti migrace](./media/app-service-move-limitations/select-migration.png)

Vyberte možnost pro doporučené kroky pro přesun webové aplikace.

![Vyberte doporučený postup](./media/app-service-move-limitations/recommended-steps.png)

Zobrazí pomocí doporučených akcí je potřeba provést před přesunutím prostředků. Tyto informace zahrnují původní skupiny prostředků pro webovou aplikaci.

![Doporučení](./media/app-service-move-limitations/recommendations.png)

## <a name="move-app-service-certificate"></a>Přesunout certifikát App Service

Certifikát App Service můžete přesunout do nové skupiny prostředků nebo předplatného. Pokud certifikát App Service je svázaný s webovou aplikací, je nutné provést některé kroky před přesunutím prostředků do nového předplatného. Odstraňte vazbu SSL a privátní certifikát z webové aplikace před přesunutím prostředků. App Service Certificate se nemusí odstranit, stačí privátního certifikátu ve webové aplikaci.

## <a name="move-support"></a>Přesun podpory

Pokud chcete zjistit, jaké prostředky App Service je možné přesunout, najdete v článku přesunout stavu podpory pro:

- [Microsoft.AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft.CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft.DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft.Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Další postup

Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../resource-group-move-resources.md).
