---
title: Zřízení virtuálního pole StorSimple v technologii Hyper-V | Dokumenty společnosti Microsoft
description: Tento druhý kurz v nasazení StorSimple Virtual Array zahrnuje zřízení virtuálního pole v hyper-V.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9d3f4f4ab6cc1c928761fce740d39f3f73426e62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267531"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Nasazení virtuálního pole StorSimple – zřízení v hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento kurz popisuje, jak zřídit virtuální pole StorSimple v hostitelském systému se systémem Hyper-V v systémech Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. Tento článek se týká nasazení virtuálních polí StorSimple na webu Azure Portal a Microsoft Azure Government Cloud.

K zřízení a konfiguraci virtuálního pole potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování předpokladů
Zde naleznete předpoklady pro zřízení virtuálního pole v hostitelském systému se systémem Hyper-V v systémech Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v [části Příprava portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Stáhli jste image virtuálního pole pro Hyper-V z webu Azure Portal. Další informace naleznete **v tématu Krok 3: Stažení bitové kopie virtuálního pole** připravit portál pro průvodce [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Software spuštěný na virtuálním poli StorSimple lze použít pouze se službou StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Pro virtuální pole StorSimple
Před nasazením virtuálního pole se ujistěte, že:

* Máte přístup k hostitelskému systému se systémem Hyper-V v systému Windows Server 2008 R2 nebo novějším, který lze použít k zřízení zařízení.
* Hostitelský systém je schopen vyhradit následující prostředky pro zřízení virtuálního pole:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Potřebujete 16 GB PAMĚTI RAM pro podporu 2 - 4 miliony souborů.
  * Jedno síťové rozhraní
  * 500 GB virtuální disk pro data.

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru
Než začnete, zkontrolujte požadavky na síť pro nasazení virtuálního pole StorSimple a odpovídajícím způsobem nakonfigurujte síť datového centra. Další informace naleznete v [tématu StorSimple Virtual Array síťové požadavky](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Podrobné zřizování
Chcete-li zřídit a připojit se k virtuálnímu poli, je třeba provést následující kroky:

1. Ujistěte se, že hostitelský systém má dostatek prostředků pro splnění minimálních požadavků virtuálnípole.
2. Zřízení virtuálního pole v hypervisoru.
3. Spusťte virtuální pole a získejte IP adresu.

Každý z těchto kroků je vysvětlen v následujících částech.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky na virtuální pole
Chcete-li vytvořit virtuální pole, potřebujete:

* Role Hyper-V nainstalovaná v systémech Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 SP1.
* Správce technologie Microsoft Hyper-V na klientovi Microsoft Windows připojeném k hostiteli.

Ujistěte se, že základní hardware (hostitelský systém), na kterém vytváříte virtuální pole, je schopen vyhradit virtuálnímu poli následující prostředky:

* Minimálně 4 jádra
* Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Potřebujete 16 GB PAMĚTI RAM pro podporu 2 - 4 miliony souborů.
* Jedno síťové rozhraní
* 500 GB virtuální disk pro systémová data.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Zřízení virtuálního pole v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte zařízení.

#### <a name="to-provision-a-virtual-array"></a>Zřízení virtuálního pole
1. Na hostiteli systému Windows Server zkopírujte bitovou kopii virtuálního pole na místní jednotku. Tuto bitovou kopii (VHD nebo VHDX) jste stáhli prostřednictvím portálu Azure. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.
2. Otevřete **Správce serveru**. V pravém horním rohu klikněte na **Nástroje** a vyberte **Správce technologie Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Pokud používáte systém Windows Server 2008 R2, otevřete Správce technologie Hyper-V. Ve Správci serveru klikněte na **role > Technologie Hyper-V > Správce technologie Hyper-V**.
3. V podokně oboru **Správce technologie Hyper-V** kliknutím pravým tlačítkem na uzel systému otevřete místní nabídku a pak klikněte na **Nový** > **Virtuální počítač**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stránce **Než začnete** Průvodce novým virtuálním počítačem klikněte na **Další**.
5. Na stránce **Zadat název a umístění** zadejte **název** virtuálního pole. Klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na stránce **Zadat generování** zvolte typ obrázku zařízení a klepněte na tlačítko **Další**. Tato stránka se nezobrazí, pokud používáte systém Windows Server 2008 R2.

   * Pokud jste stáhli bitovou kopii .vhdx pro Windows Server 2012 nebo novější, zvolte **Generace 2.**
   * Pokud jste stáhli bitovou kopii .vhd pro systém Windows Server 2008 R2 nebo novější, zvolte **Generace 1.**

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stránce **Přiřadit paměť** zadejte jako **Spouštěcí paměť** alespoň **8 192 MB**, dynamickou paměť nepovolujte, a pak klikněte **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stránce **Konfigurace sítí** zadejte virtuální přepínač připojený k internetu a pak klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na stránce **Připojit virtuální pevný disk** zvolte Použít existující virtuální pevný **disk**, určete umístění bitové kopie virtuálního pole (.vhdx nebo .vhd) a klepněte na tlačítko **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Zkontrolujte **Souhrn** a pak kliknutím na **Dokončit** vytvořte virtuální počítač.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Ke splnění minimálních požadavků potřebujete 4 jádra. Pokud chcete přidat 4 virtuální procesory, v okně **Správce technologie Hyper-V** vyberte váš hostitelský systém. V pravém podokně v seznamu **Virtuální počítače** vyhledejte virtuální počítač, který jste právě vytvořili. Vyberte název počítače, klikněte na něj pravým tlačítkem a vyberte **Nastavení**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stránce **Nastavení** klikněte v levém podokně na **Procesor**. V pravém podokně nastavte **počet virtuálních procesorů** na 4 (nebo více). Klikněte na **Použít**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Chcete-li splnit minimální požadavky, je také nutné přidat virtuální datový disk o velikosti 500 GB. Na stránce **Nastavení**:

    1. V levém podokně vyberte **Řadič SCSI**.
    2. V pravém podokně vyberte **Pevný disk** a klikněte na **Přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stránce **Pevný disk** vyberte možnost **Virtuální pevný disk** a klikněte na **Nový**. Spustí se **Průvodce novým virtuálním pevným diskem**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stránce **Než začnete** Průvodce novým virtuálním pevným diskem klikněte na **Další**.
16. Na stránce **Zvolit formát disku** přijměte výchozí možnost formátu **VHDX**. Klikněte na **Další**. Tato obrazovka není zobrazena, pokud je spuštěn systém Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stránce **Zvolit typ disku** jako typ virtuálního pevného disku nastavte **Dynamicky se zvětšující** (doporučeno). Disk **pevné velikosti** by také fungoval, ale možná byste museli dlouho čekat. Doporučujeme nepoužívat možnost **Rozdílový**. Klikněte na **Další**. V systémech Windows Server 2012 R2 a Windows Server 2012 je **dynamické rozbalení** výchozí možností, zatímco v systému Windows Server 2008 R2 je výchozí hodnota **Pevná velikost**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stránce **Zadejte název a umístění** zadejte **název** a **umístění** (k umístění můžete přejít) datového disku. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na stránce **Konfigurovat disk** vyberte možnost Vytvořit nový prázdný virtuální **pevný disk** a určete velikost jako **500 GB** (nebo více). Zatímco 500 GB je minimální požadavek, můžete vždy zřídit větší disk. Všimněte si, že po zřízení nelze disk rozbalit ani zmenšit. Další informace o velikosti disku zřídit, naleznete v části velikosti v [dokumentu doporučené postupy](storsimple-ova-best-practices.md). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na stránce **Souhrn** zkontrolujte podrobnosti virtuálního pevného disku, a pokud budete spokojeni, kliknutím na **Dokončit** disk vytvořte. Průvodce se zavře a do vašeho počítače se přidá virtuální pevný disk.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Vraťte se na stránku **Nastavení**. Kliknutím na **OK** zavřete stránku **Nastavení** a vraťte se do okna Správce technologie Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Spuštění virtuálního pole a získání IP
Chcete-li spustit virtuální pole a připojit se k němu, proveďte následující kroky.

#### <a name="to-start-the-virtual-array"></a>Spuštění virtuálního pole
1. Spusťte virtuální pole.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Jakmile bude zařízení spuštěné, vyberte ho, klikněte na něj pravým tlačítkem a vyberte **Připojit**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Možná budete muset počkat 5-10 minut, než bude zařízení připraveno. V konzole se zobrazí zpráva o stavu značící průběh. Jakmile bude zařízení připravené, přejděte do části **Akce**. Stisknutím `Ctrl + Alt + Delete` klávesy se přihlaste k virtuálnímu poli. Výchozí uživatel je *StorSimpleAdmin* a výchozí heslo je *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí splňovat alespoň 3 z následujících 4 požadavků: velká písmena, malá písmena, číslice a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Zobrazí se oznámení o změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po úspěšné změně hesla může dojít k restartování virtuálního pole. Počkejte, až se zařízení spustí.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zobrazí se konzola Windows PowerShellu zařízení a indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud jste zařízení nastartovali v prostředí jiného než DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Dále nakonfigurujte síť.
7. Pomocí `Get-HcsIpAddress` příkazu můžete uvést síťová rozhraní povolená ve virtuálním poli. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Prohlédněte si následující příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Tato IP adresa slouží k připojení k webovému uživatelskému uživatelskému uživatelskému panelu virtuálního pole a k dokončení místního nastavení a registrace.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Nepovinné) Tento krok proveďte pouze v případě, že nasazujete zařízení ve službě Government Cloud. Nyní na svém zařízení povolíte režim FIPS (United States Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití počítačovými systémy federální vlády USA pro ochranu citlivých dat.

    1. Chcete-li režim FIPS povolit, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS restartujte zařízení tak, aby se kryptografická ověření projevila.

       > [!NOTE]
       > V zařízení můžete režim FIPS povolit nebo zakázat. Střídání zařízení mezi režimem FIPS a režimem mimo FIPS není podporováno.
       >
       >

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru následující chyba (viz níže). Upravte konfiguraci zařízení tak, aby měl počítač dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Podívejte se na minimální požadavky na konfiguraci v kroku 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky na virtuální pole.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Pokud se během počáteční konfigurace pomocí místního webového uživatelského rozhraní zobrazí jiná chyba, podívejte se na následující pracovní postupy:

* Spusťte diagnostické testy [k řešení potíží s nastavením webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generovat balíček protokolu a zobrazit soubory protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další kroky
* [Nastavení virtuálního pole StorSimple jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení virtuálního pole StorSimple jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
