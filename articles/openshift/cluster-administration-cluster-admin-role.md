---
title: Role Správce clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Přiřazení a používání role Správce clusteru Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f4fc57ebe39a2169ea91423b295f4bc436746b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636234"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Role správce zákazníka pro Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

Jste správcem clusteru Azure Red Hat OpenShift clusteru. Váš účet má zvýšená oprávnění a přístup ke všem uživatelsky vytvořeným projektům.

Pokud má váš účet na něj vazbu role pro autorizaci zákazníka-správce a clusteru, může automaticky spravovat projekt.

> [!Note] 
> Role clusteru Customer-admin-cluster není stejná jako role clusteru Správce clusteru.

Například můžete provést akce spojené se sadou operací ( `create` ) pro práci na sadě názvů prostředků ( `templates` ). Chcete-li zobrazit podrobnosti o těchto rolích a jejich sadách operací a prostředků, spusťte následující příkaz:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Názvy operací nemusí nutně být všechna mapování přímo na `oc` příkazy. Představují obecněji pro typy operací CLI, které můžete provádět. 

Například `list` při operaci znamená, že můžete zobrazit seznam všech objektů názvu prostředku ( `oc get` ). `get`Příkaz znamená, že můžete zobrazit podrobnosti konkrétního objektu, pokud znáte jeho název ( `oc describe` ).

## <a name="configure-the-customer-administrator-role"></a>Konfigurace role správce zákazníka

Pomocí příznaku můžete nakonfigurovat roli Customer-admin-clustering jenom během vytváření clusteru `--customer-admin-group-id` . Toto pole v tuto chvíli není v Azure Portal konfigurovatelné. Informace o tom, jak nakonfigurovat Azure Active Directory a skupinu správců, najdete v tématu věnovaném [integraci Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Potvrdit členství v roli správce zákazníka

Pokud chcete potvrdit členství ve skupině pro správu zákazníka, zkuste použít příkazy CLI OpenShift `oc get nodes` nebo `oc projects` . `oc get nodes` Zobrazí seznam uzlů, pokud máte roli Customer-admin-cluster, a chybu oprávnění, pokud máte pouze roli Customer-admin-Project. `oc projects` zobrazí všechny projekty v clusteru na rozdíl od pouze projektů, ve kterých pracujete.

Chcete-li dále prozkoumat role a oprávnění v clusteru, můžete použít [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) příkaz.

## <a name="next-steps"></a>Další kroky

Konfigurace role clusteru Customer-admin-cluster:
> [!div class="nextstepaction"]
> [Integrace Azure Active Directory pro Azure Red Hat OpenShift](howto-aad-app-configuration.md)
