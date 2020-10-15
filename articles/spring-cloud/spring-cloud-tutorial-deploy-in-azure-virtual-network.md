---
title: Kurz – nasazení jarního cloudu Azure ve virtuální síti
description: Nasaďte ve virtuální síti Azure jarní Cloud (v-NET injektáže).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: f1a6a99285e54338b0020aad63fef2944ce3469d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92088665"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Kurz: nasazení jarního cloudu Azure ve službě Azure Virtual Network (vkládání virtuální sítě)

**Tento článek se týká:** ✔️ Java ✔️ C #

V tomto kurzu se dozvíte, jak nasadit instanci služby pružinové cloudové služby Azure ve vaší virtuální síti. Tato situace se někdy označuje jako vkládání virtuální sítě.  

Nasazení umožňuje:

* Izolace aplikací Azure jaře Cloud a modulu runtime služby z Internetu ve vaší podnikové síti
* Interakce jarního cloudu Azure se systémy v místních datových centrech nebo službách Azure v jiných virtuálních sítích
* Podpora zákazníků pro řízení příchozí a odchozí síťové komunikace pro jarní cloud Azure

## <a name="prerequisites"></a>Předpoklady
`Microsoft.AppPlatform`Podle pokynů [v tématu registrace poskytovatele prostředků v Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spuštěním následujícího příkazu AZ CLI je nutné zaregistrovat poskytovatele prostředků služby Azure jaře Cloud:

```azurecli
az provider register --namespace Microsoft.AppPlatform
```
## <a name="virtual-network-requirements"></a>Požadavky na virtuální síť
Virtuální síť, do které nasazujete instanci služby jarní cloudová služba Azure, musí splňovat následující požadavky:

* **Umístění**: virtuální síť se musí nacházet ve stejném umístění jako instance služby jarní cloudová služba Azure.
* **Předplatné**: virtuální síť musí být ve stejném předplatném jako instance služby jarní cloudová služba Azure.
* **Podsítě**: virtuální síť musí zahrnovat dvě podsítě vyhrazené pro instanci Azure jaře Cloud Service: 
    * Jeden pro modul runtime služby
    * Jednu pro aplikace mikroslužeb pro spouštění pomocí pružiny. 
    * Mezi těmito podsítěmi a instancí Azure jaře Cloud Service existuje vztah 1:1. Pro každou instanci služby, kterou nasazujete, musíte použít novou podsíť a Každá podsíť může zahrnovat jenom jednu instanci služby.
* **Adresní prostor**: jeden blok CIDR až/28 pro podsíť modulu runtime služby a další blok CIDR až/24 pro podsíť aplikací mikroslužby pro jaře booting.
* **Směrovací tabulka**: podsítě nesmí mít přidruženou stávající směrovací tabulku.

Následující postupy popisují nastavení virtuální sítě tak, aby obsahovalo instanci Azure jaře cloudu.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
Pokud již máte virtuální síť pro hostování instance služby Azure jaře Cloud Service, přeskočte kroky 1, 2 a 3. Pro přípravu podsítí pro virtuální síť můžete začít od kroku 4.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Z Azure Marketplace vyberte **síť**  >  **virtuální síť**.

1. V dialogovém okně **vytvořit virtuální síť** zadejte nebo vyberte následující informace:

    |Nastavení          |Hodnota                                             |
    |-----------------|--------------------------------------------------|
    |Předplatné     |Vyberte své předplatné.                         |
    |Skupina prostředků   |Vyberte skupinu prostředků nebo vytvořte novou.  |
    |Name             |Přechod do *Azure-jaře-Cloud-VNet*                   |
    |Umístění         |Vyberte **východní USA**                                |

1. Klikněte na **Další: IP adresy >**. 
 
1. V případě adresního prostoru IPv4 zadejte 10.1.0.0/16.

1. Vyberte **Přidat podsíť**a pak zadejte *Service-runtime-Subnet* pro **název podsítě** a 10.1.0.0/24 pro **Rozsah adres podsítě**. Pak klikněte na **Přidat**.

1. Vyberte **Přidat podsíť** znovu a pak zadejte *aplikace – podsíť* pro **název podsítě** a 10.1.1.0/24 pro **Rozsah adres podsítě**.  Klikněte na **Přidat**.

1. Klikněte na **Zkontrolovat a vytvořit**. Nechejte zbývající výchozí hodnoty a klikněte na **vytvořit**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Udělení oprávnění ke službě virtuální síti

Vyberte virtuální síť *Azure-jaře-Cloud-VNet* , kterou jste vytvořili dříve.

1. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat > přidat přiřazení role**.

    ![Řízení přístupu pro v-NET](./media/spring-cloud-v-net-injection/access-control.png)

2. V dialogu **Přidat přiřazení role** zadejte nebo vyberte tyto informace:

    |Nastavení  |Hodnota                                             |
    |---------|--------------------------------------------------|
    |Role     |Vybrat **vlastníka**                                  |
    |Vyberte   |Zadejte *poskytovatele prostředků pro jarní cloud Azure*      |

    Pak vyberte *cloudový poskytovatel prostředků Azure*a klikněte na **Uložit**.

    ![Udělení poskytovatele prostředků pro jarní cloud Azure do v-NET](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Toho můžete dosáhnout také spuštěním následujícího příkazu AZ CLI

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Nasazení instance služby Azure jaře Cloud Service ve virtuální síti

1. Otevřete Azure Portal pomocí https://ms.portal.azure.com .

1. V horním vyhledávacím poli vyhledejte **Azure jaře Cloud**a z výsledku vyberte **Azure jaře Cloud** .

1. Na stránce **jarní cloud Azure** vyberte **+ Přidat**.

1. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure. 

1. Vyberte stejnou skupinu prostředků a oblast jako virtuální síť.

1. V části **Podrobnosti o službě** **název** vyberte *Azure-jaře-Cloud-VNet*.

1. Vyberte kartu **sítě** a vyberte následující:

    |Nastavení                                |Hodnota                                             |
    |---------------------------------------|--------------------------------------------------|
    |Nasazení ve vlastní virtuální síti     |Vyberte **Ano**.                                    |
    |Virtuální síť                        |Výběr *Azure-jaře-Cloud-VNet*                  |
    |Podsíť modulu runtime služby                 |Výběr *služby – modul runtime-podsíť*                   |
    |Podsíť pro aplikace s pružinou spouštěcích mikroslužeb   |Vybrat *aplikace – podsíť*                              |

    ![Karta vytvořit síť](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Klikněte na **Zkontrolovat a vytvořit**.

1. Ověřte vaše specifikace a klikněte na **vytvořit**.

Po nasazení se ve vašem předplatném vytvoří dvě další skupiny prostředků, které budou hostovat síťové prostředky pro instanci Azure jaře Cloud Service.  Přejděte na **domovskou stránku** a v horních položkách nabídky vyberte **skupiny prostředků** , abyste našli následující nové skupiny prostředků.

Skupina prostředků s názvem *Azure-jaře-Cloud-Service-runtime_ {název instance služby} _ {region instance služby}* obsahuje síťové prostředky pro modul runtime služby instance služby.

  ![Modul runtime služby](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Skupina prostředků s názvem *Azure-jaře-Cloud-Service-runtime_ {název instance služby} _ {region instance služby}* obsahuje síťové prostředky pro aplikace instance služby jarního spuštění.

  ![Skupina prostředků aplikace](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Tyto síťové prostředky jsou připojené k vaší virtuální síti vytvořené výše.

  ![V-NET s připojeným zařízením](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Skupiny prostředků jsou plně spravovány službou Azure jaře Cloud Service. Neodstraňujte prosím ručně ani neměňte žádný prostředek uvnitř.

## <a name="next-steps"></a>Další kroky

[Nasazení aplikace do jarního cloudu Azure ve vaší virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Viz také

- [Řešení potíží s jarním cloudem Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)