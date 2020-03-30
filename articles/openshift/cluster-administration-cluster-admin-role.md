---
title: Role správce clusteru Azure Red Hat OpenShift | Dokumenty společnosti Microsoft
description: Přiřazení a využití role správce clusteru Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139092"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Role správce zákazníků Azure Red Hat OpenShift

Jste správceclusteru clusteru Azure Red Hat OpenShift clusteru. Váš účet zvýšil oprávnění a přístup ke všem projektům vytvořeným uživateli.

Pokud má váš účet vázán roli autorizace customer-admin-cluster, může automaticky spravovat projekt.

> [!Note] 
> Role clusteru clusteru zákazník-admin není stejná jako role clusteru správce clusteru.

Můžete například provést akce přidružené k sadě`create`sloves ( ) pro provoz`templates`se sadou názvů prostředků ( ). Chcete-li zobrazit podrobnosti o těchto rolích a jejich sadách sloves a prostředků, spusťte následující příkaz:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Názvy sloves nemusí být nutně `oc` všechny mapovány přímo na příkazy. Obecněji se přirovnávají k typům operací v ykontismu, které lze provádět. 

Například `list` sverb znamená, že můžete zobrazit seznam všech objektů`oc get`názvu prostředku ( ). Sloveso `get` znamená, že můžete zobrazit podrobnosti o konkrétním`oc describe`objektu, pokud znáte jeho název ( ).

## <a name="configure-the-customer-administrator-role"></a>Konfigurace role správce zákazníka

Roli clusteru customer-admin-cluster můžete konfigurovat pouze při `--customer-admin-group-id`vytváření clusteru poskytnutím příznaku . Toto pole není aktuálně konfigurovatelné na webu Azure Portal. Informace o konfiguraci služby Azure Active Directory a skupiny Administrators najdete v [tématu Integrace Služby Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potvrzení členství v roli správce zákazníka

Chcete-li potvrdit své členství ve skupině správců zákazníků, `oc get nodes` `oc projects`vyzkoušejte příkazy příkazu Příkazy příkazu OpenShift nebo . `oc get nodes`zobrazí seznam uzlů, pokud máte roli clusteru zákazníka a správce, a chybu oprávnění, pokud máte pouze roli zákazníka a správce projektu. `oc projects`zobrazí všechny projekty v clusteru na rozdíl od pouze projekty, ve kterých pracujete.

Chcete-li dále prozkoumat role a oprávnění [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) v clusteru, můžete použít příkaz.

## <a name="next-steps"></a>Další kroky

Konfigurace role clusteru zákazník-admin-cluster:
> [!div class="nextstepaction"]
> [Integrace Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md)
