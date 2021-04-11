---
title: Nasazení jarního cloudu Azure ve virtuální síti
description: Nasazení jarního cloudu Azure ve virtuální síti (vkládání virtuální sítě)
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 82dcd8c59c55a2866b51fd6dee896ea1298b6cf6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877839"
---
# <a name="deploy-azure-spring-cloud-in-a-virtual-network"></a>Nasazení jarního cloudu Azure ve virtuální síti

**Tento článek se týká:** ✔️ Java ✔️ C #

V tomto kurzu se dozvíte, jak nasadit instanci cloudu Azure ve vaší virtuální síti. Toto nasazení se někdy nazývá vkládání virtuální sítě.

Nasazení umožňuje:

* Izolace aplikací Azure jaře Cloud a modulu runtime služby z Internetu ve vaší podnikové síti.
* Interakce s jarním cloudem v Azure se systémy v místních datových centrech nebo službách Azure v jiných virtuálních sítích.
* Umožňuje zákazníkům řídit příchozí a odchozí síťovou komunikaci pro jarní cloud Azure.

> [!Note]
> Virtuální síť Azure si můžete vybrat jenom v případě, že vytvoříte novou instanci služby Azure jaře Cloud Service. Po vytvoření Azure jarního cloudu se nedá změnit na použití jiné virtuální sítě.

## <a name="prerequisites"></a>Požadavky

Podle pokynů v části [registrace poskytovatele prostředků v Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spuštěním následujícího příkazu rozhraní příkazového řádku Azure Zaregistrujte poskytovatele prostředků jarních cloudů Azure **Microsoft. AppPlatform** a **Microsoft. ContainerService** :

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```

## <a name="virtual-network-requirements"></a>Požadavky na virtuální síť

Virtuální síť, do které nasazujete instanci vaší jarního cloudu Azure, musí splňovat následující požadavky:

* **Umístění**: virtuální síť se musí nacházet ve stejném umístění jako instance Azure jaře cloudu.
* **Předplatné**: virtuální síť musí být ve stejném předplatném jako instance Azure jaře cloudu.
* **Podsítě**: virtuální síť musí zahrnovat dvě podsítě vyhrazené pro instanci cloudu jarní Azure:

    * Jednu pro modul runtime služby.
    * Jednu pro aplikace mikroslužeb pro spouštění pomocí pružiny.
    * Mezi těmito podsítěmi a instancí Azure jarního cloudu existuje vztah 1:1. Pro každou nasazenou instanci služby použijte novou podsíť. Každá podsíť může zahrnovat jenom jednu instanci služby.
* **Adresní prostor**: CIDR blokuje až */28* pro podsíť modulu runtime služby a pro podsíť aplikací mikroslužeb pro pružinové spouštění.
* **Tabulka směrování**: ve výchozím nastavení podsítě nepotřebují přidružené existující směrovací tabulky. Můžete [uvést vlastní směrovací tabulku](#bring-your-own-route-table).

Následující postupy popisují nastavení virtuální sítě tak, aby obsahovalo instanci Azure jaře cloudu.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Pokud už máte virtuální síť, která je hostitelem instance jarního cloudu Azure, přeskočte kroky 1, 2 a 3. Pro přípravu podsítí pro virtuální síť můžete začít od kroku 4.

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Z Azure Marketplace vyberte **síť**  >  **virtuální síť**.

1. V dialogovém okně **vytvořit virtuální síť** zadejte nebo vyberte následující informace:

    |Nastavení          |Hodnota                                             |
    |-----------------|--------------------------------------------------|
    |Předplatné     |Vyberte své předplatné.                         |
    |Skupina prostředků   |Vyberte skupinu prostředků nebo vytvořte novou.  |
    |Name             |Zadejte **Azure-jaře-Cloud-VNet**.                 |
    |Umístění         |Vyberte **USA – východ**.                               |

1. Vyberte **Další: IP adresy**.

1. Jako adresní prostor IPv4 zadejte **10.1.0.0/16**.

1. Vyberte **Přidat podsíť**. Pak zadejte **Service-runtime-Subnet** pro **název podsítě** a zadejte **10.1.0.0/28** pro **Rozsah adres podsítě**. Pak vyberte **Přidat**.

1. Vyberte možnost **Přidat podsíť** znovu a pak zadejte **název podsítě** a **Rozsah adres podsítě**. Zadejte například **aplikace-podsíť** a **10.1.1.0/28**. Pak vyberte **Přidat**.

1. Vyberte **Zkontrolovat a vytvořit**. Ponechte zbytek jako výchozí a vyberte **vytvořit**.

## <a name="grant-service-permission-to-the-virtual-network"></a>Udělení oprávnění ke službě virtuální síti
Služba Azure jaře Cloud vyžaduje oprávnění **vlastníka** k vaší virtuální síti, aby bylo možné udělit vyhrazené a dynamické instanční objekty ve virtuální síti k dalšímu nasazení a údržbě.

Vyberte virtuální síť **Azure-jaře-Cloud-VNet** , kterou jste vytvořili dříve.

1. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat**  >  **Přidat přiřazení role**.

    ![Snímek obrazovky, který zobrazuje obrazovku řízení přístupu.](./media/spring-cloud-v-net-injection/access-control.png)

1. V dialogovém okně **Přidat přiřazení role** zadejte nebo vyberte následující informace:

    |Nastavení  |Hodnota                                             |
    |---------|--------------------------------------------------|
    |Role     |Vyberte **Owner (vlastník**).                                 |
    |Vyberte   |Zadejte **poskytovatele prostředků pro jarní cloud Azure**.   |

    Pak vyberte **cloudový poskytovatel prostředků Azure** a vyberte **Uložit**.

    ![Snímek obrazovky zobrazující výběr poskytovatele prostředků pro jarní cloudy Azure](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Tento krok můžete provést také spuštěním následujícího příkazu rozhraní příkazového řádku Azure:

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

## <a name="deploy-an-azure-spring-cloud-instance"></a>Nasazení instance jarního cloudu Azure

Nasazení instance jarního cloudu Azure ve virtuální síti:

1. Otevřete [Azure Portal](https://portal.azure.com).

1. V horním vyhledávacím poli vyhledejte **Azure jaře Cloud**. Z výsledku vyberte **Azure jaře Cloud** .

1. Na stránce **jarní cloud Azure** vyberte **+ Přidat**.

1. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.

1. Vyberte stejnou skupinu prostředků a oblast jako virtuální síť.

1. V části **Podrobnosti o službě** **název** vyberte **Azure-jaře-Cloud-VNet**.

1. Vyberte kartu **síť** a vyberte následující hodnoty:

    |Nastavení                                |Hodnota                                             |
    |---------------------------------------|--------------------------------------------------|
    |Nasazení ve vlastní virtuální síti     |Vyberte **Ano**.                                   |
    |Virtuální síť                        |Vyberte **Azure-jaře-Cloud-VNet**.               |
    |Podsíť modulu runtime služby                 |Vyberte **Služba – modul runtime-podsíť**.                |
    |Podsíť pro aplikace s pružinou spouštěcích mikroslužeb   |Vyberte **aplikace – podsíť**.                           |

    ![Snímek obrazovky, který zobrazuje kartu sítě na stránce pro vytvoření jarního cloudu Azure.](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Vyberte možnost **zkontrolovat a vytvořit**.

1. Ověřte vaše specifikace a vyberte **vytvořit**.

    ![Snímek obrazovky, který ukazuje ověřování specifikací.](./media/spring-cloud-v-net-injection/verify-specifications.png)

Po nasazení se ve vašem předplatném vytvoří dvě další skupiny prostředků, které budou hostovat síťové prostředky pro instanci cloudu pro jaře Azure. Přejděte na **Domů** a pak v horní nabídce vyberte **skupiny prostředků** , abyste našli následující nové skupiny prostředků.

Skupina prostředků s názvem **AP-svc-RT_ {název instance služby} _ {region instance služby}** obsahuje síťové prostředky pro modul runtime služby instance služby.

  ![Snímek obrazovky, který ukazuje modul runtime služby.](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Skupina prostředků s názvem **AP-App_ {Service instance Service} _ {region instance služby}** obsahuje síťové prostředky pro aplikace instance služby pružinového spouštění.

  ![Snímek obrazovky, který zobrazuje skupinu prostředků aplikace](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Tyto síťové prostředky jsou připojené k vaší virtuální síti vytvořené v předchozím obrázku.

  ![Snímek obrazovky, který zobrazuje virtuální síť s připojenými zařízeními.](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Skupiny prostředků jsou plně spravované cloudovou službou Azure na jaře. *Neodstraňujte ručně ani* neměňte žádný prostředek uvnitř.

## <a name="using-smaller-subnet-ranges"></a>Používání menších rozsahů podsítí

Tato tabulka uvádí maximální počet instancí aplikace, které Azure jaře Cloud podporuje, pomocí menších rozsahů podsítí.

| CIDR podsítě aplikace | Celkový počet IP adres | Dostupné IP adresy | Maximální počet instancí aplikace                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| za 28             | 16        | 8             | <p> Aplikace s 1 jádrem: 96 <br/> Aplikace se 2 jádry: 48<br/>  Aplikace se 3 jádry: 32 <br/> Aplikace se 4 jádry: 24 </p> |
| /27             | 32        | 24            | <p> Aplikace s 1 jádrem: 228<br/> Aplikace se 2 jádry: 144<br/>  Aplikace se 3 jádry: 96 <br/>  Aplikace se 4 jádry: 72</p> |
| za 26             | 64        | 56            | <p> Aplikace s 1 jádrem: 500<br/> Aplikace se 2 jádry: 336<br/>  Aplikace se 3 jádry: 224<br/>  Aplikace se 4 jádry: 168</p> |
| za 25             | 128       | 120           | <p> Aplikace s 1 jádrem: 500<br> Aplikace se 2 jádry: 500<br>  Aplikace se 3 jádry: 480<br>  Aplikace se 4 jádry: 360</p> |
| za 24             | 256       | 248           | <p> Aplikace s 1 jádrem: 500<br/> Aplikace se 2 jádry: 500<br/>  Aplikace se 3 jádry: 500<br/>  Aplikace se 4 jádry: 500</p> |

V případě podsítí je pro Azure rezervováno pět IP adres a pro Azure jaře Cloud vyžaduje aspoň čtyři adresy. Vyžaduje se aspoň devět IP adres, takže/29 a/30 jsou neprovozované.

V případě podsítě modulu runtime služby je minimální velikost/28. Tato velikost nemá žádný vliv na počet instancí aplikace.

## <a name="bring-your-own-route-table"></a>Přineste si vlastní směrovací tabulku.

Azure jaře Cloud podporuje používání stávajících podsítí a směrovacích tabulek.

Pokud vaše vlastní podsítě neobsahují směrovací tabulky, Azure jaře Cloud je vytvoří pro každou z těchto podsítí a v průběhu životního cyklu instance přidá jejich pravidla. Pokud vaše vlastní podsítě obsahují směrovací tabulky, Azure jaře Cloud potvrdí existující směrovací tabulky během operací instance a přidá nebo aktualizuje a podle toho příslušné pravidla provede operace.

> [!Warning] 
> Vlastní pravidla je možné přidat do vlastních směrovacích tabulek a aktualizovat je. Tato pravidla se ale přidávají pomocí Azure jaře cloudu, takže je nemusíte aktualizovat ani odebrat. Pravidla, jako je například 0.0.0.0/0, musí existovat vždy v dané směrovací tabulce a mapovat na cíl vaší internetové brány, jako je například síťové virtuální zařízení nebo jiná výstupní brána. Při aktualizaci pravidel, když upravujete pouze vaše vlastní pravidla, buďte opatrní.


### <a name="route-table-requirements"></a>Požadavky na směrovací tabulku

Směrovací tabulky, ke kterým je přidružená vaše vlastní virtuální síť, musí splňovat následující požadavky:

* Tabulky směrování Azure můžete přidružit k virtuální síti jenom v případě, že vytvoříte novou instanci služby Azure jaře Cloud Service. Po vytvoření Azure jarního cloudu se nedá změnit na použití jiné směrovací tabulky.
* Podsíť aplikace mikroslužeb i podsíť modulu runtime musí přidružit různé směrovací tabulky nebo ani žádná z nich.
* Před vytvořením instance je třeba přiřadit oprávnění. Nezapomeňte všem směrovacím tabulkám udělit oprávnění *vlastníka cloudu* pro Azure.
* Po vytvoření clusteru nelze aktualizovat prostředek přidružené směrovací tabulky. I když nelze prostředek směrovací tabulky aktualizovat, lze upravit vlastní pravidla v tabulce směrování.
* Nemůžete opakovaně použít směrovací tabulku s více instancemi kvůli potenciálním konfliktním pravidlům směrování.

## <a name="next-steps"></a>Další kroky

[Nasazení aplikace do jarního cloudu Azure ve vaší virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Viz také

- [Řešení potíží s jarním cloudem Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Odpovědnosti zákazníků při provozování jarního cloudu Azure ve virtuální síti](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
