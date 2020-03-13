---
title: Role Správce clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Přiřazení a používání role Správce clusteru Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139092"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Role správce zákazníka pro Azure Red Hat OpenShift

Jste správcem clusteru Azure Red Hat OpenShift clusteru. Váš účet má zvýšená oprávnění a přístup ke všem uživatelsky vytvořeným projektům.

Pokud má váš účet na něj vazbu role pro autorizaci zákazníka-správce a clusteru, může automaticky spravovat projekt.

> [!Note] 
> Role clusteru Customer-admin-cluster není stejná jako role clusteru Správce clusteru.

Můžete například provádět akce spojené se sadou operací (`create`), které budou fungovat na sadě názvů prostředků (`templates`). Chcete-li zobrazit podrobnosti o těchto rolích a jejich sadách operací a prostředků, spusťte následující příkaz:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Názvy operací nemusí nutně být všechna mapování přímo na `oc` příkazy. Představují obecněji pro typy operací CLI, které můžete provádět. 

Například při `list` slovesa znamená, že můžete zobrazit seznam všech objektů názvu prostředku (`oc get`). Příkaz `get` znamená, že můžete zobrazit podrobnosti konkrétního objektu, pokud znáte jeho název (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Konfigurace role správce zákazníka

Role clusteru Customer-admin-cluster můžete nakonfigurovat pouze během vytváření clusteru zadáním příznaku `--customer-admin-group-id`. Toto pole v tuto chvíli není v Azure Portal konfigurovatelné. Informace o tom, jak nakonfigurovat Azure Active Directory a skupinu správců, najdete v tématu věnovaném [integraci Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potvrdit členství v roli správce zákazníka

Pokud chcete potvrdit členství ve skupině pro správu zákazníka, zkuste `oc get nodes` nebo `oc projects`příkazy CLI OpenShift. `oc get nodes` zobrazí seznam uzlů, pokud máte roli Customer-admin-cluster, a pokud máte k dispozici chybu oprávnění pouze v případě, že máte roli Customer-admin-Project. `oc projects` zobrazí všechny projekty v clusteru na rozdíl od pouze projektů, ve kterých pracujete.

Chcete-li dále prozkoumat role a oprávnění v clusteru, můžete použít příkaz [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) .

## <a name="next-steps"></a>Další kroky

Konfigurace role clusteru Customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integrace Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md)
