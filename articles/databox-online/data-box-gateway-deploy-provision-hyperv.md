---
title: Kurz zřízení služby Azure Data Box Gateway v Hyper-V | Microsoft Docs
description: Druhý kurz k nasazení služby Azure Data Box Gateway se zabývá zřízením virtuálního zařízení v Hyper-V.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: alkohli
ms.openlocfilehash: cf47919ead890f0ad0e89646dde26276ebfb1127
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109737"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-hyper-v-preview"></a>Kurz: Zřízení Azure Data Box brány technologie Hyper-v (Preview)

## <a name="overview"></a>Přehled

Tento kurz popisuje, jak zřídit službu Data Box Gateway v hostitelském systému s Hyper-V ve Windows Serveru 2016, Windows Serveru 2012 R2 nebo Windows Serveru 2012. 

Ke zřízení a konfiguraci virtuálního zařízení potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zajištění, aby hostitel splňoval minimální požadavky na zařízení
> * Zřízení virtuálního zařízení v hypervisoru
> * Spuštění virtuálního zařízení a získání IP adresy

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> - Data Box Gateway je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

Požadavky na zřízení virtuálního zařízení v hostitelském systému s Hyper-V ve Windows Serveru 2016 nebo Windows Serveru 2012 R2 jsou následující.

### <a name="for-the-data-box-gateway-resource"></a>Prostředek Data Box Gateway

Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v tématu [Příprava portálu pro službu Data Box Gateway](data-box-gateway-deploy-prep.md).
* Z webu Azure Portal jste si stáhli image virtuálního zařízení pro Hyper-V, jak je popsáno v tématu [Příprava portálu pro službu Data Box Gateway](data-box-gateway-deploy-prep.md).

  > [!IMPORTANT]
  > Software ve službě Data Box Gateway je možné používat pouze s prostředkem Data Box Gateway.
 
### <a name="for-the-data-box-gateway-virtual-device"></a>Virtuální zařízení Data Box Gateway

Před nasazením zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému s Hyper-V ve Windows Serveru 2012 R2 nebo novějším, který můžete použít ke zřízení zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení vyhradit následující prostředky:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM 
  * Jedno síťové rozhraní
  * 250GB disk s operačním systémem
  * 2TB virtuální disk pro data

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru

Než začnete:

- Projděte si požadavky na síť pro nasazení služby Data Box Gateway a nakonfigurujte podle těchto požadavků síť datacentra. Další informace najdete v tématu popisujícím [požadavky na síť pro službu Data Box Gateway](data-box-gateway-system-requirements.md#networking-port-requirements).
- Ujistěte se, že je šířka internetového pásma minimálně 20 Mb/s, aby zařízení mohlo fungovat optimálně.


## <a name="check-the-host-system"></a>Kontrola hostitelského systému

K vytvoření virtuálního zařízení potřebujete:

* Nainstalovanou roli Hyper-V ve Windows Serveru 2016, Windows Serveru 2012 R2 nebo Windows Serveru 2012.
* Správce technologie Microsoft Hyper-V na klientovi Microsoft Windows připojeném k hostiteli.
* Zajistit, aby základní hardware (hostitelský systém), na kterém virtuální zařízení vytváříte, mohl pro vaše virtuální zařízení vyhradit následující prostředky:

    * Minimálně 4 jádra
    * Minimálně 8 GB paměti RAM
    * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. 
    * 250GB disk s operačním systémem
    * 2TB virtuální disk pro data systému

## <a name="provision-a-virtual-device-in-hypervisor"></a>Zřízení virtuálního zařízení v hypervisoru

Pomocí následujících kroků ve svém hypervisoru zřiďte zařízení.

1. Na hostiteli s Windows Serverem zkopírujte na místní disk image virtuálního zařízení. Tuto image VHDX jste si stáhli přes Azure Portal. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.
2. Otevřete **Správce serveru**. V pravém horním rohu klikněte na **Nástroje** a vyberte **Správce technologie Hyper-V**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image1.png)  
  
3. V podokně oboru **Správce technologie Hyper-V** kliknutím pravým tlačítkem na uzel systému otevřete místní nabídku a pak klikněte na **Nový** > **Virtuální počítač**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image2.png)
4. Na stránce **Než začnete** Průvodce novým virtuálním počítačem klikněte na **Další**.
5. Na stránce **Zadejte název a umístění** zadejte **Název** virtuálního zařízení. Klikněte na **Další**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image3.png)
6. Na stránce **Zadat generaci** zvolte jako typ image zařízení .vhdx **Generace 2** a pak klikněte na **Další**.    

   ![](./media/data-box-gateway-deploy-provision-hyperv/image4.png)
7. Na stránce **Přiřadit paměť** zadejte jako **Spouštěcí paměť** alespoň **8 192 MB**, dynamickou paměť nepovolujte, a pak klikněte **Další**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image5.png) 
8. Na stránce **Konfigurace sítí** zadejte virtuální přepínač připojený k internetu a pak klikněte na **Další**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image6.png)
9. Na stránce **Připojit virtuální pevný disk** zvolte **Použít existující virtuální pevný disk**, zadejte umístění image virtuálního zařízení a pak klikněte na **Další**.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image7.png)
10. Zkontrolujte **Souhrn** a pak kliknutím na **Dokončit** vytvořte virtuální počítač.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image8.png)
11. Ke splnění minimálních požadavků potřebujete 4 jádra. Pokud chcete přidat 4 virtuální procesory, v okně **Správce technologie Hyper-V** vyberte váš hostitelský systém. V pravém podokně v seznamu **Virtuální počítače** vyhledejte virtuální počítač, který jste právě vytvořili. Vyberte název počítače, klikněte na něj pravým tlačítkem a vyberte **Nastavení**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image9.png)
12. Na stránce **Nastavení** klikněte v levém podokně na **Procesor**. V pravém podokně nastavte **počet virtuálních procesorů** na 4 (nebo více). Klikněte na tlačítko **Použít**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image10.png)
13. Ke splnění minimálních požadavků je také potřeba přidat 2TB virtuální datový disk. Na stránce **Nastavení**:

    1. V levém podokně vyberte **Řadič SCSI**.
    2. V pravém podokně vyberte **Pevný disk** a klikněte na **Přidat**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image11.png)
14. Na stránce **Pevný disk** vyberte možnost **Virtuální pevný disk** a klikněte na **Nový**. Spustí se **Průvodce novým virtuálním pevným diskem**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image12.png)
1. Na stránce **Než začnete** Průvodce novým virtuálním pevným diskem klikněte na **Další**.
2. Na stránce **Zvolit formát disku** přijměte výchozí možnost formátu **VHDX**. Klikněte na **Další**.
   
17. Na stránce **Zvolit typ disku** jako typ virtuálního pevného disku nastavte **Dynamicky se zvětšující** (doporučeno). Disk **pevné velikosti** by také fungoval, ale možná byste museli dlouho čekat. Doporučujeme nepoužívat možnost **Rozdílový**. Klikněte na **Další**. 

    ![](./media/data-box-gateway-deploy-provision-hyperv/image13.png)
18. Na stránce **Zadejte název a umístění** zadejte **název** a **umístění** (k umístění můžete přejít) datového disku. Klikněte na **Další**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image14.png)
19. Na stránce **Konfigurovat disk** vyberte možnost **Vytvořit nový prázdný virtuální pevný disk** a jako velikost zadejte **2 TB** (nebo více). Minimální požadavek je 2 TB, ale vždy můžete zřídit větší disk. Mějte na paměti, že disk po zřízení už není možné zmenšit.  Můžete ho však zvětšit přidáním datového disku. Klikněte na **Další**.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image15.png)
20. Na stránce **Souhrn** zkontrolujte podrobnosti virtuálního pevného disku, a pokud budete spokojeni, kliknutím na **Dokončit** disk vytvořte. Průvodce se zavře a do vašeho počítače se přidá virtuální pevný disk.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image16.png)
21. Vraťte se na stránku **Nastavení**. Kliknutím na **OK** zavřete stránku **Nastavení** a vraťte se do okna Správce technologie Hyper-V.

    ![](./media/data-box-gateway-deploy-provision-hyperv/image17.png)

## <a name="start-the-virtual-device-and-get-the-ip"></a>Spuštění virtuálního zařízení a získání IP adresy
Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image18.png)
2. Jakmile bude zařízení spuštěné, vyberte ho, klikněte na něj pravým tlačítkem a vyberte **Připojit**.

3. Možná budete muset 10 až 15 minut počkat, než se zařízení připraví. V konzole se zobrazí zpráva o stavu značící průběh. Jakmile bude zařízení připravené, přejděte do části **Akce**. Stiskněte `Ctrl + Alt + Delete` a přihlaste se k virtuálnímu zařízení. Výchozí uživatel je *EdgeUser* a výchozí heslo je *Password1*.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image21.png)
   
6. Kroky 5 až 7 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky. Pokud jste zařízení spustili v jiném prostředí než DHCP, zobrazí se o tom zpráva.
    
7. Pokud chcete nakonfigurovat síť, použijte příkaz `Get-HcsIpAddress`, který vypíše povolená síťová rozhraní na vašem virtuálním zařízení. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Prohlédněte si následující příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`
    
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Pomocí této IP adresy se připojte k webovému uživatelskému rozhraní vašeho virtuálního zařízení a dokončete místní nastavení a aktivaci.

   ![](./media/data-box-gateway-deploy-provision-hyperv/image23.png)
      

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba. Upravte konfiguraci zařízení tak, aby měl počítač dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci v části věnované [kontrole, jestli hostitelský systém splňuje minimální požadavky na virtuální zařízení](#check-the-host-system).

<!--If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o tématech spojených se službou Data Box Gateway, jako jsou:

> [!div class="checklist"]
> * Zajištění, aby hostitel splňoval minimální požadavky na zařízení
> * Zřízení virtuálního zařízení v hypervisoru
> * Spuštění virtuálního zařízení a získání IP adresy

V dalším kurzu se dozvíte, jak připojit, nastavit a aktivovat virtuální zařízení.

> [!div class="nextstepaction"]
> [Připojení a nastavení služby Data Box Gateway](./data-box-gateway-deploy-connect-setup-activate.md)


