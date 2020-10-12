---
title: Správa prostředků v Azure Red Hat OpenShift | Microsoft Docs
description: Správa projektů, šablon a datových proudů imagí v clusteru Azure Red Hat OpenShift
services: openshift
keywords: projekty Red Hat OpenShift žádají samoobslužného zřizování
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 35474df96254b158a39b6d913171c553bf30aea4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88816435"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Správa projektů, šablon a datových proudů imagí v clusteru Azure Red Hat OpenShift

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
