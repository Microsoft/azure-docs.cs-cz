---
title: Zveřejňujte aplikace na internetu pomocí Application Gateway a Azure Firewall
description: Jak vystavit aplikace pro Internet pomocí Application Gateway a Azure Firewall
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101738714"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Zveřejňujte aplikace na internetu pomocí Application Gateway a Azure Firewall

Tento dokument vysvětluje, jak vystavit aplikace na internetu pomocí Application Gateway a Azure Firewall. Když je instance služby jarní cloudová služba Azure nasazená ve vaší virtuální síti, aplikace v instanci služby jsou dostupné jenom v privátní síti. Aby bylo možné aplikace zpřístupnit v Internetu, je nutné integraci s **Azure Application Gateway** a volitelně i s **Azure firewall**.

## <a name="prerequisites"></a>Předpoklady

- [Azure CLI verze 2.0.4 nebo novější](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definování proměnných

Definujte proměnné pro skupinu prostředků a virtuální síť, které jste vytvořili jako směrované v [nasazení Azure jarního cloudu ve službě Azure Virtual Network (vkládání virtuální sítě)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Přizpůsobte hodnoty na základě reálného prostředí.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure CLI a vyberte své aktivní předplatné.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Vytvoření síťových prostředků

Služba **azure Application Gateway** , která se má vytvořit, se připojí ke stejné virtuální síti jako-nebo s partnerskými virtuálními sítěmi do instance cloudové cloudové služby Azure. Nejprve vytvořte novou podsíť pro Application Gateway ve virtuální síti pomocí `az network vnet subnet create` a také Vytvořte veřejnou IP adresu jako front-endu Application Gateway pomocí `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Vytvoření služby Application Gateway

Vytvořte Aplikační bránu pomocí `az network application-gateway create` a zadejte privátní plně kvalifikovaný název domény (FQDN) vaší aplikace jako servery ve fondu back-endu. Pak aktualizujte nastavení HTTP pomocí `az network application-gateway http-settings update` , aby se použil název hostitele z back-endu fondu.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Může trvat až 30 minut, než Azure vytvoří Aplikační bránu. Po vytvoření ověřte stav back-endu pomocí `az network application-gateway show-backend-health` .  Tím se ověří, zda Aplikační brána dosáhne vaší aplikace prostřednictvím svého privátního plně kvalifikovaného názvu domény.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

Výstup indikuje dobrý stav fondu back-end.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Přístup k aplikaci pomocí veřejné IP adresy front-endu služby Application Gateway

Získejte veřejnou IP adresu aplikační brány pomocí `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Zkopírujte veřejnou IP adresu a vložte ji do adresního řádku prohlížeče.

  ![Aplikace ve veřejné IP adrese](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Viz také

- [Řešení potíží s jarním cloudem Azure ve virtuální síti](spring-cloud-troubleshooting-vnet.md)
- [Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti](spring-cloud-vnet-customer-responsibilities.md)