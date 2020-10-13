---
title: Podpora služby Azure IoT Device Provisioning Service (DPS) pro virtuální sítě
description: Jak používat vzor připojení k virtuálním sítím se službou Azure IoT Device Provisioning (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: 43d7b3ae906909312a9e9ec4517061a788267a0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612774"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Podpora Azure IoT Hub Device Provisioning Service (DPS) pro virtuální sítě

Tento článek představuje vzor připojení k virtuální síti (VNET) pro zařízení IoT se zřizováním pomocí centra IoT pomocí DPS. Tento model poskytuje privátní připojení mezi zařízeními, DPS a centrem IoT v rámci virtuální sítě Azure vlastněné zákazníkem. 

Ve většině scénářů, kdy je pro DPS nakonfigurovaná virtuální síť, se vaše IoT Hub nakonfigurují i ve stejné virtuální síti. Podrobnější informace o podpoře a konfiguraci virtuálních sítí pro centra IoT najdete v tématu [IoT Hub podpora virtuální sítě](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Úvod

Ve výchozím nastavení se názvy hostitelů DPS mapují na veřejný koncový bod s veřejně směrovatelný IP adresou přes Internet. Tento veřejný koncový bod je viditelný pro všechny zákazníky. Přístup k veřejnému koncovému bodu je možné provést prostřednictvím zařízení IoT přes sítě v různých oblastech i v místních sítích.

Z několika důvodů můžou zákazníci chtít omezit připojení k prostředkům Azure, jako je DPS. Mezi tyto důvody patří:

* Zabraňte expozici připojení veřejnému Internetu. Vystavení se dá snížit díky zavedení dalších vrstev zabezpečení přes izolaci na úrovni sítě pro Centrum IoT a prostředcích DPS.

* Díky možnosti privátního připojení z vašich místních síťových prostředků zajistíte přenos dat a přenosů přímo do páteřní sítě Azure.

* Prevence útoků exfiltrace z citlivých místních sítí. 

* Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md)navázali vzory připojení na úrovni Azure.

Mezi běžné přístupy k omezení připojení patří [pravidla filtru DPS protokolu IP](./iot-dps-ip-filtering.md) a virtuální síť (VNET) s [privátními koncovými body](../private-link/private-endpoint-overview.md). Tento cíl tohoto článku popisuje přístup k virtuální síti pro DPS pomocí privátních koncových bodů. 

Zařízení, která pracují v místních sítích, můžou pomocí [virtuální privátní sítě (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo privátního partnerského vztahu [ExpressRoute](https://azure.microsoft.com/services/expressroute/) se připojit k virtuální síti v Azure a přistupovat k prostředkům DPS prostřednictvím soukromých koncových bodů. 

Privátní koncový bod je privátní IP adresa přidělená v rámci virtuální sítě vlastněné zákazníkem, pomocí které je k dispozici prostředek Azure. Když budete mít privátní koncový bod pro prostředek DPS, budete moct povolit zařízením, která pracují v rámci vaší virtuální sítě, vyžádat si zřizování pomocí prostředku DPS bez povolení provozu do veřejného koncového bodu.


## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že jsou splněné následující předpoklady:

* Váš prostředek DPS je už vytvořený a propojený s vašimi centry IoT. Pokyny k nastavení nového prostředku DPS najdete v tématu nastavení [IoT Hub Device Provisioning Service pomocí Azure Portal](./quick-setup-auto-provision.md)

* Zřídili jste virtuální síť Azure s podsítí, ve které se vytvoří privátní koncový bod. Další informace najdete v tématu [vytvoření virtuální sítě pomocí Azure CLI](../virtual-network/quick-create-cli.md).

* Pro zařízení, která pracují v místních sítích, nastavte [virtuální privátní síť (VPN)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) nebo privátní partnerský vztah [ExpressRoute](https://azure.microsoft.com/services/expressroute/) do virtuální sítě Azure.

## <a name="private-endpoint-limitations"></a>Omezení privátního koncového bodu

Při používání privátních koncových bodů Pamatujte na tato aktuální omezení:

* Pokud je prostředek DPS a propojené centrum v různých cloudech, nebudou privátní koncové body fungovat s DPS. Například [Azure Government a globální Azure](../azure-government/documentation-government-welcome.md).

* V současné době [vlastní zásady přidělování s Azure Functions](how-to-use-custom-allocation-policies.md) pro DPS nebudou fungovat, když je funkce Azure uzamčená na virtuální síť a soukromé koncové body. 

* Aktuální podpora DPS pro virtuální sítě je jenom pro příchozí přenosy dat do DPS. Výstup dat, což je přenos z DPS do IoT Hub, používá interní mechanizmus služby pro službu, nikoli vyhrazenou virtuální síť. Podpora fulltextového uzamčení založeného na virtuální síti mezi DPS a IoT Hub není aktuálně k dispozici.

* Nejnižší zásada přidělení latence se používá k přiřazení zařízení ke službě IoT Hub s nejnižší latencí. Tyto zásady přidělování nejsou spolehlivé ve virtuálním síťovém prostředí. 

## <a name="set-up-a-private-endpoint"></a>Nastavení privátního koncového bodu

K nastavení privátního koncového bodu použijte následující postup:

1. V [Azure Portal](https://portal.azure.com/)otevřete prostředek DPS a klikněte na kartu **síť** . Klikněte na **připojení privátního koncového bodu** a **+ soukromý koncový bod**.

    ![Přidání nového privátního koncového bodu pro DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Na stránce _vytvořit základy privátního koncového bodu_ zadejte informace uvedené v následující tabulce.

    ![Vytváření základních informací o privátních koncových bodech](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Pole | Hodnota |
    | :---- | :-----|
    | **Předplatné** | Vyberte požadované předplatné Azure, které bude obsahovat privátní koncový bod.  |
    | **Skupina prostředků** | Vyberte nebo vytvořte skupinu prostředků, která bude obsahovat privátní koncový bod. |
    | **Název**       | Zadejte název privátního koncového bodu. |
    | **Oblast**     | Vybraná oblast musí být stejná jako oblast, která obsahuje virtuální síť, ale nemusí být stejná jako u prostředku DPS. |

    Klikněte na **Další: prostředek** pro konfiguraci prostředku, na který bude odkazovat privátní koncový bod.

3. Na stránce _vytvořit prostředek privátního koncového bodu_ zadejte informace uvedené v následující tabulce.

    ![Vytvořit prostředek privátního koncového bodu](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Pole | Hodnota |
    | :---- | :-----|
    | **Předplatné**        | Vyberte předplatné Azure, které obsahuje prostředek DPS, na který bude váš privátní koncový bod odkazovat.  |
    | **Typ prostředku**       | Vyberte **Microsoft. Devices/ProvisioningServices**. |
    | **Prostředek**            | Vyberte prostředek DPS, na který se bude mapovat privátní koncový bod. |
    | **Cílový dílčí prostředek** | Vyberte **iotDps**. |

    > [!TIP]
    > Informace o nastavení **připojit k prostředku Azure podle ID prostředku nebo aliasu** najdete v části [žádost o soukromý koncový bod](#request-a-private-endpoint) v tomto článku.


    Kliknutím na **Další: Konfigurace** nakonfigurujte virtuální síť pro privátní koncový bod.

4. Na stránce _vytvořit konfiguraci privátního koncového bodu_ vyberte virtuální síť a podsíť, ve které chcete vytvořit privátní koncový bod.
 
    Klikněte na **Další: značky**a volitelně poskytněte pro svůj prostředek všechny značky.

    ![Konfigurace privátního koncového bodu](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Klikněte na tlačítko **zkontrolovat + vytvořit** a pak **vytvořit** a vytvořte prostředek privátního koncového bodu.


## <a name="request-a-private-endpoint"></a>Vyžádání privátního koncového bodu

Pomocí ID prostředku můžete požádat o privátní koncový bod na prostředek DPS. Aby bylo možné tuto žádost vytvořit, potřebujete vlastníka prostředku, který vám poskytne ID prostředku. 

1. ID prostředku je k dispozici na kartě Vlastnosti pro prostředek DPS, jak je uvedeno níže.

    ![Karta vlastnosti DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Mějte na paměti, že ID prostředku obsahuje ID předplatného. 

2. Jakmile budete mít ID prostředku, postupujte podle kroků uvedených v části [nastavení privátního koncového bodu](#set-up-a-private-endpoint) ke kroku 3 na stránce _vytvoření prostředku privátního koncového bodu_ . Klikněte na **připojit k prostředku Azure podle ID prostředku nebo aliasu** a zadejte informace v následující tabulce. 

    | Pole | Hodnota |
    | :---- | :-----|
    | **ID prostředku nebo alias** | Zadejte ID prostředku pro prostředek DPS. |
    | **Cílový dílčí prostředek** | Zadejte **iotDps** |
    | **Zpráva požadavku** | Zadejte zprávu požadavku pro vlastníka prostředku DPS.<br>Příklad: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Kliknutím na **Další: Konfigurace** nakonfigurujte virtuální síť pro privátní koncový bod.

3. Na stránce _vytvořit konfiguraci privátního koncového bodu_ vyberte virtuální síť a podsíť pro vytvoření privátního koncového bodu v nástroji.
 
    Klikněte na **Další: značky**a volitelně poskytněte pro svůj prostředek všechny značky.

4. Kliknutím na tlačítko **zkontrolovat + vytvořit** **a vytvořit vytvořte požadavek na privátní** koncový bod.

5. Vlastník DPS uvidí požadavek na soukromý koncový bod na seznamu **připojení privátního koncového bodu** na kartě DPS sítě. Vlastník může na této stránce **schválit** nebo **odmítnout** požadavek na privátní koncový bod, jak je znázorněno níže.

    ![Schválení DPS](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Soukromé koncové body pro ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Další kroky

K získání dalších informací o funkcích zabezpečení DPS použijte odkazy níže:

* [Zabezpečení](concepts-security.md)
* [Podpora TLS 1,2](tls-support.md)
