---
title: Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3.11 | Dokumenty společnosti Microsoft
description: Vytvoření privátního clusteru s Azure Red Hat OpenShift 3.11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: aro, openshift, privátní cluster, červený klobouk
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399416"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Vytvoření privátního clusteru s Azure Red Hat OpenShift 3.11

> [!IMPORTANT]
> Privátní clustery Azure Red Hat OpenShift (ARO) jsou momentálně dostupné jenom v privátní verzi Preview v usa 2 – východ. Přijetí soukromé verze preview je pouze na pozvání. Než se pokusíte tuto funkci povolit, nezapomeňte předplatné zaregistrovat.

Privátní clustery poskytují následující výhody:

* Privátní clustery nezveřejňují součásti roviny clusteru (například servery rozhraní API) na veřejné IP adrese.
* Virtuální síť privátního clusteru je konfigurovatelná zákazníky, což umožňuje nastavit síť tak, aby umožňovala partnerský vztah s jinými virtuálními sítěmi, včetně prostředí ExpressRoute. Můžete také nakonfigurovat vlastní DNS ve virtuální síti pro integraci s interními službami.

## <a name="before-you-begin"></a>Než začnete

> [!NOTE]
> Tato funkce vyžaduje verzi 2019-10-27-preview rozhraní HTTP API ARO. V azure cli ještě není podporovaná.

Pole v následujícím fragmentu konfigurace jsou nová a musí být zahrnuta do konfigurace clusteru. `managementSubnetCidr`musí být v rámci virtuální sítě clusteru a používá Azure ke správě clusteru.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Privátní cluster lze nasadit pomocí ukázkových skriptů uvedených níže. Po nasazení clusteru spusťte `cluster get` příkaz `properties.FQDN` a zobrazte vlastnost a určete privátní IP adresu serveru OpenShift API.

Virtuální síť clusteru bude vytvořena s oprávněními, abyste ji mohli upravit. Potom můžete nastavit síť pro přístup k virtuální síti (ExpressRoute, VPN, partnerský vztah virtuální sítě) podle potřeby.

Pokud změníte názvové servery DNS ve virtuální síti clusteru, budete muset `properties.RefreshCluster` vydat `true` aktualizaci v clusteru s vlastností nastavenou tak, aby virtuální počítače mohly být reimaged. Tato aktualizace jim umožní vyzvednout nové názvové servery.

## <a name="sample-configuration-scripts"></a>Ukázkové konfigurační skripty

Ukázkové skripty v této části slouží k nastavení a nasazení privátního clusteru.

### <a name="environment"></a>Prostředí

Vyplňte proměnné prostředí níže jako pomocí vlastních hodnot.

> [!NOTE]
> Umístění musí být `eastus2` nastaveno na, protože se aktuálně jedná o jediné podporované umístění pro privátní clustery.

``` bash
export CLUSTER_NAME=
export LOCATION=eastus2
export TOKEN=$(az account get-access-token --query 'accessToken' -o tsv)
export SUBID=
export TENANT_ID=
export ADMIN_GROUP=
export CLIENT_ID=
export SECRET=
```

### <a name="private-clusterjson"></a>private-cluster.json

Pomocí výše definovaných proměnných prostředí je zde ukázková konfigurace clusteru s povoleným privátním clusterem.

```json
{
   "location": "$LOCATION",
   "name": "$CLUSTER_NAME",
   "properties": {
       "openShiftVersion": "v3.11",
       "networkProfile": {
           "vnetCIDR": "10.0.0.0/8",
           "managementSubnetCIDR" : "10.0.1.0/24"
       },
       "authProfile": {
           "identityProviders": [
               {
                   "name": "Azure AD",
                   "provider": {
                       "kind": "AADIdentityProvider",
                       "clientId": "$CLIENT_ID",
                       "secret": "$SECRET",
                       "tenantId": "$TENANT_ID",
                       "customerAdminGroupID": "$ADMIN_GROUP"
                   }
               }
           ]
       },
       "masterPoolProfile": {
           "name": "master",
           "count": 3,
           "vmSize": "Standard_D4s_v3",
           "osType": "Linux",
           "subnetCIDR": "10.0.0.0/24",
           "apiProperties": {
                "privateApiServer": true
            }
       },
       "agentPoolProfiles": [
           {
               "role": "compute",
               "name": "compute",
               "count": 1,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           },
           {
               "role": "infra",
               "name": "infra",
               "count": 3,
               "vmSize": "Standard_D4s_v3",
               "osType": "Linux",
               "subnetCIDR": "10.0.0.0/24"
           }
       ],
       "routerProfiles": [
           {
               "name": "default"
           }
       ]
   }
}
```

## <a name="deploy-a-private-cluster"></a>Nasazení privátního clusteru

Po konfiguraci privátního clusteru pomocí ukázkových skriptů výše spusťte následující příkaz pro nasazení privátního clusteru.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Další kroky

Informace o přístupu ke konzole OpenShift naleznete v [tématu Návod k webové konzoli](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
