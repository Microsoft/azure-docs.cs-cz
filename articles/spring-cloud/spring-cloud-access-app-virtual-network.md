---
title: Aplikace pro cloudový přístup k Azure ve virtuální síti
description: Přístup k aplikaci ve jarním cloudu Azure ve virtuální síti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576527"
---
# <a name="access-your-application-in-a-private-network"></a>Přístup k aplikaci v privátní síti

Tento dokument vysvětluje, jak získat přístup ke koncovému bodu pro vaši aplikaci v privátní síti.  Aby bylo možné získat přístup, musíte ve svém předplatném vytvořit **zónu Azure privátní DNS** , abyste přeložili nebo vyřešili privátní plně kvalifikovaný název domény (FQDN) na jeho IP adresu.

Když se v rámci virtuální sítě nasadí **koncový bod** pro aplikace ve službě Azure jaře Cloud Service, koncový bod je privátní plně kvalifikovaný název domény. Doména je dostupná jenom v privátní síti. Aplikace a služby používají koncový bod aplikace. Zahrnují **testovací koncový bod** popsaný v tématu [zobrazení aplikací a nasazení](spring-cloud-howto-staging-environment.md#view-apps-and-deployments). **Streamování protokolů**, které jsou popsané v tématu [streamování protokolů cloudových cloudových aplikací Azure v reálném čase](spring-cloud-howto-log-streaming.md), funguje i v rámci privátní sítě.

## <a name="create-a-private-dns-zone"></a>Vytvoření privátní zóny DNS

Následující postup slouží k vytvoření privátní zóny DNS pro aplikaci v privátní síti.

1. Otevřete web Azure Portal. V horním vyhledávacím poli vyhledejte **privátní DNS zóny** a z výsledku vyberte **privátní DNS zóny** .

2. Na stránce **privátní DNS zóny** vyberte **+ Přidat**.

3. Vyplňte formulář na stránce **vytvořit privátní DNS zónu** . Jako **název** zóny zadejte **<span>Private.azuremicroservices.IO</span>** .

4. Vyberte **Zkontrolovat a vytvořit**.

5. Vyberte **Vytvořit**.

Vytvoření zóny může trvat několik minut.

## <a name="link-the-virtual-network"></a>Propojit virtuální síť

Pokud chcete propojit privátní zónu DNS s virtuální sítí, musíte vytvořit propojení virtuální sítě.

1. Vyberte prostředek privátní zóny DNS vytvořený výše: **<span>Private.azuremicroservices.IO</span>** 

2. V levém podokně vyberte **odkazy virtuální sítě**.

3. Vyberte **Přidat**.

4. Zadejte **Azure-jaře-Cloud-DNS-Link** pro **název odkazu**.

5. V případě **virtuální sítě** vyberte virtuální síť, kterou jste vytvořili, jak je vysvětleno v tématu [nasazení Azure jaře cloudu ve službě Azure Virtual Network (vkládání virtuální sítě)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

    ![Přidat odkaz virtuální sítě](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. Klikněte na **OK**.

## <a name="create-dns-record"></a>Vytvořit záznam DNS

Pokud chcete použít privátní zónu DNS k překladu nebo překladu DNS, musíte v zóně vytvořit záznam typu "A".

1. Vyberte prostředek virtuální sítě, který jste vytvořili, jak je vysvětleno v tématu [nasazení Azure jaře cloudu ve službě Azure Virtual Network (vkládání virtuální sítě)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md).

2. Do vyhledávacího pole **připojená zařízení** zadejte *Kubernetes-Internal*.

3. Ve filtrovaném výsledku vyhledejte **zařízení** připojené k **podsíti** služby Service runtime instance služby a zkopírujte jeho **IP adresu**. V této ukázce je IP adresa *10.1.0.7*.

    [![Vytvořit záznam ](media/spring-cloud-access-app-vnet/create-dns-record.png) DNS](media/spring-cloud-access-app-vnet/create-dns-record.png)

Nebo můžete načíst IP adresu pomocí následujícího příkazu AZ CLI:

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. Vyberte prostředek privátní zóny DNS vytvořený výše: **<span>Private.azuremicroservices.IO</span>**.

5. Vyberte **+ Sada záznamů**.

6. V **Přidat sadu záznamů** zadejte nebo vyberte tyto informace:

    |Nastavení     |Hodnota                                                                      |
    |------------|---------------------------------------------------------------------------|
    |Název        |Napište *\**                                                                 |
    |Typ        |Vybrat                                                                |
    |TTL         |Zadejte *1*                                                                  |
    |Jednotka TTL    |Vybrat **hodiny**                                                           |
    |IP adresa  |Zadejte IP adresu zkopírovanou v kroku 3. V ukázce zadejte *10.1.0.7*.    |

    Pak vyberte **OK**.

    ![Přidat záznam privátní zóny DNS](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>Přiřazení privátního plně kvalifikovaného názvu domény pro vaši aplikaci

Po provedení postupu v [sestavování a nasazování aplikací mikroslužeb](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)můžete přiřadit privátní plně kvalifikovaný název domény pro vaši aplikaci.

1. Vyberte instanci služby Azure jaře Cloud Service nasazenou ve virtuální síti a otevřete kartu **aplikace** v nabídce na levé straně.

2. Vyberte aplikaci, aby se zobrazila stránka s **přehledem** .

3. Pokud chcete aplikaci přiřadit privátní plně kvalifikovaný název domény, vyberte **přiřadit koncový bod** . Může to trvat několik minut.

    ![Přiřazení privátního koncového bodu](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. Přiřazený privátní plně kvalifikovaný název domény (označený jako **URL**) je teď dostupný. Dá se k němu dostat jenom v privátní síti, ale ne na internetu.

## <a name="access-application-private-fqdn"></a>Přístup k privátnímu plně kvalifikovanému názvu domény aplikace

Po přiřazení máte přístup k privátnímu plně kvalifikovanému názvu domény vaší aplikace v privátní síti. Například můžete vytvořit počítač s JumpBox ve stejné virtuální síti nebo v partnerské virtuální síti a na daném JumpBox počítači, který má přístup k privátnímu plně kvalifikovanému názvu domény.

![Přístup k privátnímu koncovému bodu ve virtuální síti](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>Další kroky

- [Vystavení aplikací pro Internet pomocí Application Gateway a Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>Viz také

- [Řešení potíží s jarním cloudem Azure ve virtuální síti](spring-cloud-troubleshooting-vnet.md)
- [Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti](spring-cloud-vnet-customer-responsibilities.md)