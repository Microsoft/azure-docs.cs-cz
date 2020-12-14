---
title: Kurz – nasazení jarního cloudu Azure ve virtuální síti
description: Nasaďte ve virtuální síti Azure jarní Cloud (v-NET injektáže).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1e16c984e48c11961dba0c977d3bdbddbd6bdf36
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400315"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Kurz: nasazení jarního cloudu Azure ve službě Azure Virtual Network (vkládání virtuální sítě)

**Tento článek se týká:** ✔️ Java ✔️ C #

V tomto kurzu se dozvíte, jak nasadit instanci služby pružinové cloudové služby Azure ve vaší virtuální síti. Tato situace se někdy označuje jako vkládání virtuální sítě.  

Nasazení umožňuje:

* Izolace aplikací Azure jaře Cloud a modulu runtime služby z Internetu ve vaší podnikové síti
* Interakce jarního cloudu Azure se systémy v místních datových centrech nebo službách Azure v jiných virtuálních sítích
* Podpora zákazníků pro řízení příchozí a odchozí síťové komunikace pro jarní cloud Azure

## <a name="prerequisites"></a>Předpoklady
Musíte zaregistrovat poskytovatele prostředků cloudu Azure pro *Microsoft. AppPlatform* a *Microsoft. ContainerService* podle pokynů [v článku registrace poskytovatele prostředků v Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spuštěním následujícího příkazu AZ CLI:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Požadavky na virtuální síť
Virtuální síť, do které nasazujete instanci služby jarní cloudová služba Azure, musí splňovat následující požadavky:

* **Umístění**: virtuální síť se musí nacházet ve stejném umístění jako instance služby jarní cloudová služba Azure.
* **Předplatné**: virtuální síť musí být ve stejném předplatném jako instance služby jarní cloudová služba Azure.
* **Podsítě**: virtuální síť musí zahrnovat dvě podsítě vyhrazené pro instanci Azure jaře Cloud Service: 
    * Jeden pro modul runtime služby
    * Jednu pro aplikace mikroslužeb pro spouštění pomocí pružiny. 
    * Mezi těmito podsítěmi a instancí Azure jaře Cloud Service existuje vztah 1:1. Pro každou instanci služby, kterou nasazujete, musíte použít novou podsíť a Každá podsíť může zahrnovat jenom jednu instanci služby.
* **Adresní prostor**: CIDR blokuje až **/28** pro podsíť modulu runtime služby a pro podsíť aplikací mikroslužeb pro spouštění pružiny.
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
    |Název             |Přechod do *Azure-jaře-Cloud-VNet*                   |
    |Umístění         |Vyberte **východní USA**                                |

1. Klikněte na **Další: IP adresy >**. 
 
1. V případě adresního prostoru IPv4 zadejte 10.1.0.0/16.

1. Vyberte **Přidat podsíť** a pak zadejte *Service-runtime-Subnet* pro **název podsítě** a 10.1.0.0/28 pro **Rozsah adres podsítě**. Pak klikněte na **Přidat**.

1. Vyberte **Přidat podsíť** znovu a pak zadejte **název podsítě** a **Rozsah adres podsítě**, například *aplikace-podsíť* a a 10.1.1.0/28.  Klikněte na **Přidat**.

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

    Pak vyberte *cloudový poskytovatel prostředků Azure* a klikněte na **Uložit**.

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

1. Otevřete Azure Portal pomocí https://portal.azure.com .

1. V horním vyhledávacím poli vyhledejte **Azure jaře Cloud** a z výsledku vyberte **Azure jaře Cloud** .

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

    ![Ověřit specifikace](./media/spring-cloud-v-net-injection/verify-specifications.png)

Po nasazení se ve vašem předplatném vytvoří dvě další skupiny prostředků, které budou hostovat síťové prostředky pro instanci Azure jaře Cloud Service.  Přejděte na **domovskou stránku** a v horních položkách nabídky vyberte **skupiny prostředků** , abyste našli následující nové skupiny prostředků.

Skupina prostředků s názvem *AP-svc-RT_ {název instance služby} _ {region instance služby}* obsahuje síťové prostředky pro modul runtime služby instance služby.

  ![Modul runtime služby](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Skupina prostředků s názvem *AP-App_ {Service instance Service} _ {region instance služby}* obsahuje síťové prostředky pro aplikace instance služby pružinového spouštění.

  ![Skupina prostředků aplikace](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Tyto síťové prostředky jsou připojené k vaší virtuální síti vytvořené výše.

  ![V-NET s připojeným zařízením](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Skupiny prostředků jsou plně spravovány službou Azure jaře Cloud Service. Neodstraňujte prosím ručně ani neměňte žádný prostředek uvnitř.

## <a name="limitations"></a>Omezení

Rozsah malých podsítí ukládá IP adresy, ale přináší omezení maximálního počtu instancí aplikace, které může cloudové úložiště Azure uchovávat. 

| CIDR podsítě aplikace | Celkový počet IP adres | Dostupné IP adresy | Maximální počet instancí aplikace                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| za 28             | 16        | 8             | <p> Aplikace s 1 jádrem: 96 <br/> Aplikace se 2 jádry: 48<br/>  Aplikace se 3 jádry: 32 <br/> Aplikace se 4 jádry: 24 </p> |
| /27             | 32        | 24            | <p> Aplikace s 1 jádrem: 228<br/> Aplikace se 2 jádry: 144<br/>  Aplikace se 3 jádry: 96 <br/>  Aplikace se 4 jádry: 72</p> |
| za 26             | 64        | 56            | <p> Aplikace s 1 jádrem: 500<br/> Aplikace se 2 jádry: 336<br/>  Aplikace se 3 jádry: 224<br/>  Aplikace se 4 jádry: 168</p> |
| za 25             | 128       | 120           | <p> Aplikace s 1 jádrem: 500<br> Aplikace se 2 jádry: 500<br>  Aplikace se 3 jádry: 480<br>  Aplikace se 4 jádry: 360</p> |
| za 24             | 256       | 248           | <p> Aplikace s 1 jádrem: 500<br/> Aplikace se 2 jádry: 500<br/>  Aplikace se 3 jádry: 500<br/>  Aplikace se 4 jádry: 500</p> |

Pro podsítě je 5 IP adres rezervovaných Azure a minimálně 4 adresy vyžaduje Azure jaře Cloud. Vyžaduje se aspoň 9 IP adres, takže/29 a/30 nejsou funkční.

Pro podsíť modulu runtime služby je minimální velikost a/28 a nemá žádný vliv na počet instancí aplikace.
## <a name="next-steps"></a>Další kroky

[Nasazení aplikace do jarního cloudu Azure ve vaší virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Viz také

- [Řešení potíží s jarním cloudem Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
