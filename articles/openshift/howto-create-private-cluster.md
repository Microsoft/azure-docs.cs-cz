---
title: Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11 | Microsoft Docs
description: Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/02/2020
keywords: ARO, OpenShift, soukromý cluster, Red Hat
ms.openlocfilehash: b34b5d622527742447847102526eba9ee6ca220d
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78399416"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Privátní clustery Azure Red Hat OpenShift (ARO) jsou v tuto chvíli dostupné jenom v privátní verzi Preview v Východní USA 2. Přijetí privátní verze Preview je jenom na pozvání. Před pokusem o povolení této funkce nezapomeňte zaregistrovat své předplatné.

Soukromé clustery poskytují následující výhody:

* Soukromé clustery nezveřejňují komponenty roviny řízení clusteru (například servery rozhraní API) na veřejné IP adrese.
* Virtuální síť privátního clusteru je konfigurovatelná pro zákazníky, což umožňuje nastavit síť tak, aby umožňovala vytváření partnerských vztahů s ostatními virtuálními sítěmi, včetně ExpressRoutech prostředí. Ve virtuální síti můžete také nakonfigurovat vlastní DNS pro integraci s interními službami.

## <a name="before-you-begin"></a>Než začnete

> [!NOTE]
> Tato funkce vyžaduje verzi 2019-10-27-Preview rozhraní HTTP API pro ARO. V rozhraní příkazového řádku Azure se ještě nepodporuje.

Pole v následujícím fragmentu konfigurace jsou nová a musí být zahrnutá v konfiguraci clusteru. `managementSubnetCidr` musí být v rámci virtuální sítě clusteru a služba Azure ji používá ke správě clusteru.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Privátní cluster se dá nasadit pomocí ukázkových skriptů uvedených níže. Po nasazení clusteru spusťte příkaz `cluster get` a zobrazte vlastnost `properties.FQDN` a určete tak soukromou IP adresu serveru OpenShift API.

Virtuální síť clusteru se vytvoří s oprávněními, abyste ji mohli upravovat. Pak můžete nastavit síť pro přístup k virtuální síti (ExpressRoute, VPN, partnerské vztahy virtuálních sítí) podle požadavků pro vaše potřeby.

Pokud změníte názvové servery DNS ve virtuální síti s clustery, budete muset v clusteru vydat aktualizaci s vlastností `properties.RefreshCluster` nastavenou na `true`, aby bylo možné virtuální počítače obnovit z image. Tato aktualizace jim umožní vybrat nové názvové servery.

## <a name="sample-configuration-scripts"></a>Ukázkové konfigurační skripty

K nastavení a nasazení privátního clusteru použijte ukázkové skripty v této části.

### <a name="environment"></a>Prostředí

Do těchto proměnných prostředí zadejte vlastní hodnoty.

> [!NOTE]
> Umístění musí být nastavené na `eastus2`, protože toto je aktuálně jediné podporované umístění pro privátní clustery.

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

### <a name="private-clusterjson"></a>Private-cluster. JSON

Pomocí proměnných prostředí definovaných výše je tady uvedená Ukázková konfigurace clusteru s povoleným privátním clusterem.

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

Po nakonfigurování privátního clusteru pomocí výše uvedených ukázkových skriptů spusťte následující příkaz, který nasadí váš privátní cluster.

``` bash
az group create --name $CLUSTER_NAME --location $LOCATION
cat private-cluster.json | envsubst | curl -v -X PUT \
-H 'Content-Type: application/json; charset=utf-8' \
-H 'Authorization: Bearer '$TOKEN'' -d @- \
 https://management.azure.com/subscriptions/$SUBID/resourceGroups/$CLUSTER_NAME/providers/Microsoft.ContainerService/openShiftManagedClusters/$CLUSTER_NAME?api-version=2019-10-27-preview
```

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak získat přístup ke konzole OpenShift, najdete v tématu [Názorný postup webové konzoly](https://docs.openshift.com/container-platform/3.11/getting_started/developers_console.html).
