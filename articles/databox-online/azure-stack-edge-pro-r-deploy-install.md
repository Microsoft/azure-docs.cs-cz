---
title: Kurz instalace fyzického zařízení Azure Stack Edge pro R | Microsoft Docs
description: Druhý kurz o instalaci Azure Stack Edge pro R zahrnuje způsob, jak kabelovat fyzické zařízení pro napájení a síť.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro R in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: b67da2607d206424f69f53645eda148495ea58ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96468159"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Kurz: instalace Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak nainstalovat fyzické zařízení Azure Stack Edge pro R. Postup instalace zahrnuje kabely zařízení.

Dokončení instalace může trvat přibližně 30 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Kontrola zařízení
> * Zapojení kabeláže zařízení

## <a name="prerequisites"></a>Požadavky

Požadavky pro instalaci fyzického zařízení jsou následující:

### <a name="for-the-azure-stack-edge-resource"></a>Pro prostředek Azure Stack Edge

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava na nasazení Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Vytvořili jste prostředek Azure Stack Edge pro nasazení zařízení.
    * Vygenerovali jste aktivační klíč pro aktivaci zařízení pomocí Azure Stackho hraničního prostředku.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Pro fyzické zařízení Azure Stack Edge pro R

Před nasazením zařízení:

- Ujistěte se, že zařízení funguje bezpečně na plochém, stabilním a úrovni pracovního prostoru.
- Ověřte, že lokalita, kterou chcete nastavit, disponuje jedním z následujícího:
    - Standardní AC výkon z nezávislého zdroje

        - nebo -
    - Jednotka PDU napájení stojanu. Zařízení se dodává s nepřerušitelným zdrojem napájení (UPS).
    

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Projděte si požadavky na síť pro nasazení Azure Stack Edge pro R a konfigurujte síť datacenter podle požadavků. Další informace najdete v článku [požadavky na síť Azure Stack Edge pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Ujistěte se, že minimální šířka pásma internetu je 20 MB/s pro optimální fungování zařízení.


## <a name="inspect-the-device"></a>Kontrola zařízení

Toto zařízení se dodává jako jediná jednotka. Zařízení rozbalíte pomocí následujících kroků.

1. Umístěte krabici na rovný povrch.
2. Zkontrolujte případná poškození v případě zařízení. Otevřete případ a zkontrolujte zařízení. Pokud se zdá, že se jedná o poškozený případ nebo zařízení, kontaktujte podpora Microsoftu, abyste pomohli posoudit, jestli je zařízení v dobrém pořadí.
3. Po otevření případ se ujistěte, že máte následující:
    - Jedna skříň Azure Stack hraniční zařízení pro R
    - Jeden nepřerušitelný nepřerušitelný zdroj napájení (UPS)
    - 2 krátké napájecí kabely pro připojení zařízení ke zdroji UPS
    - 1 napájecí kabel pro připojení zdroje dat ke zdroji napájení

Pokud jste neobdrželi všechny uvedené položky, obraťte se na podporu Azure Stack Edge pro R. Dalším krokem je zapojení zařízení do kabelu.


## <a name="cable-the-device"></a>Zapojení kabeláže zařízení

Následující postupy vysvětlují, jak se zařízení Azure Stack Edge pro R pro napájení a síť kabelem zapojte.

Než začnete kabelovat vaše zařízení, budete potřebovat následující:

- Fyzické zařízení Azure Stack Edge pro R na instalačním webu.
- Jeden napájecí kabel.
- Alespoň jeden síťový kabel 1 GbE RJ-45 pro připojení k rozhraní pro správu. Na zařízení jsou dvě síťová rozhraní 1 GbE – jedno pro správu a druhé pro data.
- Jeden 10/25-GbE SFP + Copper kabel pro každé rozhraní datové sítě, které se má nakonfigurovat. Aspoň jedno rozhraní datové sítě z portu 3 nebo 4 musí být připojené k Internetu (s připojením k Azure).  
- Přístup k jedné jednotce pro distribuci napájení (doporučeno)

> [!NOTE]
> - Pokud se připojujete pouze k jednomu síťovému rozhraní, doporučujeme, abyste pro posílání dat do Azure používali síťové rozhraní s 25 nebo 10 GbEmi, jako je PORT 3 nebo PORT 4. 
> - Pro zajištění nejlepšího výkonu a zpracování velkých objemů dat zvažte připojení všech datových portů.
> - Zařízení Azure Stack Edge pro R by mělo být připojeno k síti datacenter, aby mohl ingestovat data ze serverů zdrojů dat.

Na zařízení Azure Stack Edge pro R:

- Přední panel má diskové jednotky a tlačítko napájení.

    - Před zařízením je 8 diskových slotů.
    - Zařízení má také 2 X M. 2 disky SATA uvnitř, které slouží jako disky operačního systému. 

- Zadní rovina zahrnuje nadbytečné jednotky napájení (PSUs).
- Zadní rovina má čtyři síťová rozhraní:

    - Rozhraní 2 1 až GB/s.
    - Rozhraní 2 25-GB, která můžou sloužit taky jako rozhraní s rychlostí 10 GB/s.
    - Řadič pro správu základní desky (BMC).

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Proveďte následující kroky, které zařízení zapojte do sítě pro napájení a síť.

1. Identifikujte různé porty v zadní rovině zařízení.

    ![Zadní rovina kabelového zařízení](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Vyhledejte diskové sloty a tlačítko napájení na přední straně zařízení.

    ![Přední rovina zařízení](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Připojte jeden konec napájecího kabelu ke zdroji UPS. Připojte druhý konec napájecího kabelu k jednotce jednotek napájení stojanu. 
5. Kliknutím na tlačítko napájení zapněte zařízení.
6. Připojte PORT 1 síťového rozhraní 1-GbE k počítači, který se používá ke konfiguraci fyzického zařízení. PORT 1 představuje vyhrazené rozhraní pro správu.
7. Připojte jednu nebo více portů 2, PORT 3 nebo PORT 4 k síti datacenter nebo Internetu.

    - Pokud připojujete PORT 2, použijte síťový kabel RJ-45.
    - Pro síťová rozhraní 10/25 až GbE použijte kabely SFP + Copper.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech Azure Stack Edge pro R, jako je například:

> [!div class="checklist"]
> * Rozbalení zařízení
> * Zapojení kabeláže zařízení

Přejděte k dalšímu kurzu, kde se dozvíte, jak se připojit k vašemu zařízení.

> [!div class="nextstepaction"]
> [Připojení k Azure Stack Edge pro R](./azure-stack-edge-pro-r-deploy-connect.md)
