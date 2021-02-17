---
title: Správa prostředků v Azure Red Hat OpenShift | Microsoft Docs
description: Správa projektů, šablon a datových proudů imagí v clusteru Azure Red Hat OpenShift
services: openshift
keywords: projekty Red Hat OpenShift žádají samoobslužného zřizování
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.openlocfilehash: bf2cf5a0d41af15821035c615fe071c8580e125f
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633100"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Správa projektů, šablon a datových proudů imagí v clusteru Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

V kontejnerové platformě OpenShift se projekty používají k seskupení a izolaci souvisejících objektů. Jako správce můžete vývojářům udělit přístup ke konkrétním projektům, umožnit jim vytvářet vlastní projekty a udělovat jim práva správce pro jednotlivé projekty.

## <a name="self-provisioning-projects"></a>Projekty pro samoobslužné zřizování

Můžete povolit vývojářům vytvářet své vlastní projekty. Koncový bod rozhraní API zodpovídá za zřízení projektu na základě šablony s názvem Project-Request. Webová konzola a `oc new-project` příkaz používají tento koncový bod, když vývojář vytvoří nový projekt.

Při odeslání žádosti o projekt nahradí rozhraní API následující parametry v šabloně:

| Parametr               | Popis                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Název projektu. Povinná hodnota.             |
| PROJECT_DISPLAYNAME     | Zobrazovaný název projektu. Může být prázdné. |
| PROJECT_DESCRIPTION     | Popis projektu Může být prázdné.  |
| PROJECT_ADMIN_USER      | Uživatelské jméno uživatele pro správu       |
| PROJECT_REQUESTING_USER | Uživatelské jméno žádajícího uživatele.           |

Přístup k rozhraní API se uděluje vývojářům s vazbou role clusteru samoobslužné zřizování. Tato funkce je ve výchozím nastavení dostupná všem ověřeným vývojářům.

## <a name="modify-the-template-for-a-new-project"></a>Úprava šablony nového projektu 

1. Přihlaste se jako uživatel s `customer-admin` oprávněními.

2. Upravte výchozí šablonu projekt – požadavek.

   ```
   oc edit template project-request -n openshift
   ```

3. Odeberte výchozí šablonu projektu z procesu aktualizace služby Azure Red Hat OpenShift (ARO) přidáním následující poznámky: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Šablona projektu – požadavek nebude aktualizována procesem aktualizace ARO společnosti. To zákazníkům umožňuje přizpůsobit šablonu a zachovat tato přizpůsobení při aktualizaci clusteru.

## <a name="disable-the-self-provisioning-role"></a>Zakázání role samoobslužného zřizování

Ověřenou skupinu uživatelů můžete zabránit v samoobslužných zřizováních nových projektů.

1. Přihlaste se jako uživatel s `customer-admin` oprávněními.

2. Upravte vazbu role clusteru samoobslužné zřizování.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Odeberte roli z procesu aktualizace ARO a přidejte následující poznámku: `openshift.io/reconcile-protect: "true"` .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Změnou vazby role clusteru zabráníte `system:authenticated:oauth` vytváření projektů:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Správa výchozích šablon a imageStreams

V Azure Red Hat OpenShift můžete zakázat aktualizace pro jakékoli výchozí šablony a datové proudy imagí v rámci `openshift` oboru názvů.
Zakázání aktualizací pro všechny `Templates` a `ImageStreams` v `openshift` oboru názvů:

1. Přihlaste se jako uživatel s `customer-admin` oprávněními.

2. Upravit `openshift` obor názvů:

   ```
   oc edit namespace openshift
   ```

3. Odeberte `openshift` obor názvů z procesu aktualizace ARO a přidejte následující poznámku: `openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Jakýkoli jednotlivý objekt v `openshift` oboru názvů lze odebrat z procesu aktualizace přidáním poznámky `openshift.io/reconcile-protect: "true"` .

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si kurz:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
