---
title: Správa prostředků v Azure Red Hat OpenShift | Dokumenty společnosti Microsoft
description: Správa projektů, šablon, datových proudů bitových obrázků v clusteru Azure Red Hat OpenShift
services: openshift
keywords: red hat openshift projekty požaduje self-provisioner
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139109"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Správa projektů, šablon, datových proudů bitových obrázků v clusteru Azure Red Hat OpenShift 

V platformě kontejnerů OpenShift se projekty používají k seskupení a izoluje související objekty. Jako správce můžete vývojářům poskytnout přístup ke konkrétním projektům, umožnit jim vytvářet vlastní projekty a udělit jim práva správce pro jednotlivé projekty.

## <a name="self-provisioning-projects"></a>Projekty samozásobitení

Můžete povolit vývojářům vytvářet své vlastní projekty. Koncový bod rozhraní API je zodpovědný za zřizování projektu podle šablony s názvem žádost o projekt. Webová konzola `oc new-project` a příkaz používají tento koncový bod, když vývojář vytvoří nový projekt.

Při odeslání žádosti o projekt rozhraní API nahradí následující parametry v šabloně:

| Parametr               | Popis                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Název projektu. Povinná hodnota.             |
| PROJECT_DISPLAYNAME     | Zobrazovaný název projektu. Může být prázdný. |
| PROJECT_DESCRIPTION     | Popis projektu. Může být prázdný.  |
| PROJECT_ADMIN_USER      | Uživatelské jméno uživatele spravujícího.       |
| PROJECT_REQUESTING_USER | Uživatelské jméno žádajícího uživatele.           |

Přístup k rozhraní API je udělen vývojářům s vazbou role clusteru self-provisioners. Tato funkce je ve výchozím nastavení dostupná všem ověřeným vývojářům.

## <a name="modify-the-template-for-a-new-project"></a>Úprava šablony pro nový projekt 

1. Přihlaste se `customer-admin` jako uživatel s oprávněními.

2. Upravte výchozí šablonu žádosti o projekt.

   ```
   oc edit template project-request -n openshift
   ```

3. Odeberte výchozí šablonu projektu z procesu aktualizace Azure Red Hat OpenShift (ARO) přidáním následující poznámky:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Šablona žádosti o projekt nebude aktualizována procesem aktualizace ARO. To umožňuje zákazníkům přizpůsobit šablonu a zachovat tato vlastní nastavení při aktualizaci clusteru.

## <a name="disable-the-self-provisioning-role"></a>Zakázání role samozřivace

Můžete zabránit ověřené skupině uživatelů v samozřivacím novém ustavování nových projektů.

1. Přihlaste se `customer-admin` jako uživatel s oprávněními.

2. Upravte vazbu role clusteru vlastních zřízenců.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Odeberte roli z procesu aktualizace ARO přidáním následující poznámky: `openshift.io/reconcile-protect: "true"`.

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Změňte vazbu role `system:authenticated:oauth` clusteru, abyste zabránili vytváření projektů:

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

## <a name="manage-default-templates-and-imagestreams"></a>Správa výchozích šablon a datových proudů obrázků

V Azure Red Hat OpenShift můžete zakázat aktualizace pro všechny `openshift` výchozí šablony a datové proudy bitových obrázků uvnitř oboru názvů.
Zakázání aktualizací `Templates` pro `ImageStreams` `openshift` všechny a v oboru názvů:

1. Přihlaste se `customer-admin` jako uživatel s oprávněními.

2. Upravit `openshift` obor názvů:

   ```
   oc edit namespace openshift
   ```

3. Přidejte následující poznámku, která odebere `openshift` obor názvů z procesu aktualizace ARO:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Každý jednotlivý objekt `openshift` v oboru názvů lze odebrat z `openshift.io/reconcile-protect: "true"` procesu aktualizace přidáním poznámky k němu.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte výukový program:
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md)
