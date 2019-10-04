---
title: Role Správce clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Přiřazení a používání role Správce clusteru Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936929"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Role správce zákazníka pro Azure Red Hat OpenShift

Jste správcem clusteru Azure Red Hat OpenShift clusteru. Váš účet má zvýšená oprávnění a přístup ke všem uživatelsky vytvořeným projektům.

Pokud má váš účet na sebe navázaný autorizační roli osa-zákazník-správce, může automaticky spravovat projekt.

> [!Note] 
> Role clusteru osa-zákazník-správce není stejná jako role clusteru Správce clusteru.


Můžete například provádět akce spojené se sadou operací (`create`), které budou fungovat na sadě názvů prostředků (`templates`). Chcete-li zobrazit podrobnosti o těchto rolích a jejich sadách operací a prostředků, spusťte následující příkaz:

`$ oc describe clusterrole/osa-customer-admin`

Názvy operací nemusí nutně být všechna mapování přímo na příkazy `oc`. Představují obecněji pro typy operací CLI, které můžete provádět. 

Například při operaci `list` znamená, že můžete zobrazit seznam všech objektů názvu prostředku (`oc get`). Příkaz `get` znamená, že můžete zobrazit podrobnosti konkrétního objektu, pokud znáte jeho název (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Konfigurace role správce zákazníka

Roli správce zákazníka můžete nakonfigurovat jenom během vytváření clusteru, a to zadáním příznaku `--customer-admin-group-id`. Informace o tom, jak nakonfigurovat Azure Active Directory a skupinu správců, najdete v tématu věnovaném [integraci Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Další kroky

Konfigurace role osa-zákazník-správce:
> [!div class="nextstepaction"]
> [Integrace Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md)
