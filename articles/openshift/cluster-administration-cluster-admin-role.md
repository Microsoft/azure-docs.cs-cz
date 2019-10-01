---
title: Role Správce clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Přiřazení a používání role Správce clusteru Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709954"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Role správce zákazníka pro Azure Red Hat OpenShift

Správce clusteru OpenShift ve formě ARO (Azure Red Hat OpenShift) má účet zvýšené oprávnění a přístup ke všem uživatelsky vytvořeným projektům.

Pokud má váš účet k tomuto účtu navázanou roli autorizace osa-zákazník-správci, může automaticky spravovat projekt.

> [!Note] 
> osa – zákazník – clusterrole správce není stejný jako clusterrole Správce clusteru.


Můžete například provádět akce spojené se sadou operací (`create`), které budou fungovat na sadě názvů prostředků (`templates`). Chcete-li zobrazit podrobnosti o těchto rolích a jejich sadách operací a prostředků, spusťte následující příkaz:

`$ oc describe clusterrole/osa-customer-admin`

Názvy operací nemusí nutně namapovat přímo na oC příkazy, ale místo toho budou všeobecně odpovídat typům operací CLI, které můžete provádět. Například při operaci `list` znamená, že můžete zobrazit seznam všech objektů daného názvu prostředku (`oc get`), zatímco příkaz `get` znamená, že můžete zobrazit podrobnosti konkrétního objektu, pokud znáte jeho název (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Jak nakonfigurovat roli správce zákazníka

Role správce zákazníka se dá nakonfigurovat jenom během vytváření clusteru zadáním příznaku `--customer-admin-group-id`. Postup při konfiguraci Azure Active Directory a správců najdete v článku postup pro příručku: [Azure Active Directory Integration for Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Další kroky

Postup konfigurace role osa-zákazník-správce:
> [!div class="nextstepaction"]
> [Integrace Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md)
