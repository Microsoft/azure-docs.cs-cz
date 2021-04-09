---
title: Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11 | Microsoft Docs
description: Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/02/2020
keywords: ARO, OpenShift, soukromý cluster, Red Hat
ms.openlocfilehash: 28a21635da770a990a21e0da2a8034c754f4f7aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633236"
---
# <a name="create-a-private-cluster-with-azure-red-hat-openshift-311"></a>Vytvoření privátního clusteru pomocí Azure Red Hat OpenShift 3,11

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

Soukromé clustery poskytují následující výhody:

* Soukromé clustery nezveřejňují komponenty roviny řízení clusteru (například servery rozhraní API) na veřejné IP adrese.
* Virtuální síť privátního clusteru je konfigurovatelná pro zákazníky, což umožňuje nastavit síť tak, aby umožňovala vytváření partnerských vztahů s ostatními virtuálními sítěmi, včetně ExpressRoutech prostředí. Ve virtuální síti můžete také nakonfigurovat vlastní DNS pro integraci s interními službami.

## <a name="before-you-begin"></a>Než začnete

Pole v následujícím fragmentu konfigurace jsou nová a musí být zahrnutá v konfiguraci clusteru. `managementSubnetCidr` musí se nacházet v rámci virtuální sítě clusteru a služba Azure ji používá ke správě clusteru.

```json
properties:
 networkProfile:
   managementSubnetCidr: 10.0.1.0/24
 masterPoolProfile:
   apiProperties:
     privateApiServer: true
```

Privátní cluster se dá nasadit pomocí ukázkových skriptů uvedených níže. Po nasazení clusteru spusťte `cluster get` příkaz a zobrazte `properties.FQDN` vlastnost a určete tak soukromou IP adresu serveru OpenShift API.

Virtuální síť clusteru se vytvoří s oprávněními, abyste ji mohli upravovat. Pak můžete nastavit síť pro přístup k virtuální síti (ExpressRoute, VPN, partnerské vztahy virtuálních sítí) podle požadavků pro vaše potřeby.

Pokud změníte názvové servery DNS ve virtuální síti clusteru, budete muset vydat aktualizaci v clusteru s `properties.RefreshCluster` vlastností nastavenou na `true` , aby bylo možné virtuální počítače obnovit z image. Tato aktualizace jim umožní vybrat nové názvové servery.

## <a name="sample-configuration-scripts"></a>Ukázkové konfigurační skripty

K nastavení a nasazení privátního clusteru použijte ukázkové skripty v této části.

### <a name="environment"></a>Prostředí

Do těchto proměnných prostředí zadejte vlastní hodnoty.

> [!NOTE]
> Umístění musí být nastavené na, `eastus2` protože toto je aktuálně jediné podporované umístění pro privátní clustery.

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

### <a name="private-clusterjson"></a>private-cluster.jsna

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
