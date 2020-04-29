---
title: Zřizování virtuálních polí StorSimple v Hyper-V | Microsoft Docs
description: Tento druhý kurz v nasazení virtuálních polí StorSimple zahrnuje zřízení virtuálního pole v Hyper-V.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79267531"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Nasazení virtuálního pole StorSimple – zřizování v Hyper-V
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

V tomto kurzu se dozvíte, jak zřídit virtuální pole StorSimple v hostitelském systému, na kterém běží Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. Tento článek se týká nasazení virtuálních polí StorSimple v cloudu Azure Portal a Microsoft Azure Government.

K zřizování a konfiguraci virtuálního pole potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Požadavky zřizování
Tady najdete předpoklady pro zřízení virtuálního pole v hostitelském systému, na kterém běží technologie Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Stáhli jste image virtuálního pole pro Hyper-V z Azure Portal. Další informace najdete v části **Krok 3: stažení image virtuálního pole** pro [přípravu portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Software spuštěný ve virtuálním poli StorSimple se dá použít jenom u služby StorSimple Device Manager.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Pro virtuální pole StorSimple
Před nasazením virtuálního pole se ujistěte, že:

* Máte přístup k hostitelskému systému, na kterém běží Hyper-V na Windows serveru 2008 R2 nebo novějším, který se dá použít k zřízení zařízení.
* Hostitelský systém dokáže vyhradit následující prostředky pro zřízení vašeho virtuálního pole:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 000 000 souborů. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
  * Jedno síťové rozhraní
  * Virtuální disk 500 GB pro data.

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru
Než začnete, Projděte si požadavky na síť a nasaďte virtuální pole StorSimple a nakonfigurujte síť datacenter správně. Další informace najdete v tématu [požadavky na síť virtuálních polí StorSimple](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Podrobné zřizování
Chcete-li zřídit a připojit se k virtuálnímu poli, je nutné provést následující kroky:

1. Zajistěte, aby měl hostitelský systém dostatek prostředků pro splnění minimálních požadavků na virtuální pole.
2. Zřídí ve hypervisoru virtuální pole.
3. Spusťte virtuální pole a získejte IP adresu.

Každý z těchto kroků je vysvětlen v následujících částech.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky na virtuální pole.
Chcete-li vytvořit virtuální pole, budete potřebovat:

* Role technologie Hyper-V nainstalovaná v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 SP1.
* Správce technologie Microsoft Hyper-V na klientovi Microsoft Windows připojeném k hostiteli.

Ujistěte se, že základní hardware (hostitelský systém), na kterém vytváříte virtuální pole, dokáže vyhradit následující prostředky ve virtuálním poli:

* Minimálně 4 jádra
* Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 000 000 souborů. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
* Jedno síťové rozhraní
* Virtuální disk 500 GB pro systémová data.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: zřízení virtuálního pole v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte zařízení.

#### <a name="to-provision-a-virtual-array"></a>Zřízení virtuálního pole
1. Na hostiteli s Windows serverem zkopírujte image virtuálního pole na místní disk. Tuto image jste stáhli (VHD nebo VHDX) prostřednictvím Azure Portal. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.
2. Otevřete **Správce serveru**. V pravém horním rohu klikněte na **Nástroje** a vyberte **Správce technologie Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Pokud používáte systém Windows Server 2008 R2, otevřete Správce technologie Hyper-V. V Správce serveru klikněte na **role > Hyper-v > Správce technologie Hyper-v**.
3. V podokně oboru **Správce technologie Hyper-V** kliknutím pravým tlačítkem na uzel systému otevřete místní nabídku a pak klikněte na **Nový** > **Virtuální počítač**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stránce **Než začnete** Průvodce novým virtuálním počítačem klikněte na **Další**.
5. Na stránce **zadat název a umístění** zadejte **název** vašeho virtuálního pole. Klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na stránce **zadat generaci** zvolte typ obrázku zařízení a pak klikněte na **Další**. Tato stránka se nezobrazí, pokud používáte systém Windows Server 2008 R2.

   * Pokud jste stáhli bitovou kopii. vhdx pro Windows Server 2012 nebo novější, vyberte **2. generace** .
   * Vyberte možnost **generace 1** , pokud jste stáhli bitovou kopii VHD pro systém Windows Server 2008 R2 nebo novější.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stránce **Přiřadit paměť** zadejte jako **Spouštěcí paměť** alespoň **8 192 MB**, dynamickou paměť nepovolujte, a pak klikněte **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stránce **Konfigurace sítí** zadejte virtuální přepínač připojený k internetu a pak klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na stránce **připojit virtuální pevný disk** zvolte **použít existující virtuální pevný disk**, zadejte umístění image virtuálního pole (. vhdx nebo. VHD) a pak klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Zkontrolujte **Souhrn** a pak kliknutím na **Dokončit** vytvořte virtuální počítač.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Ke splnění minimálních požadavků potřebujete 4 jádra. Pokud chcete přidat 4 virtuální procesory, v okně **Správce technologie Hyper-V** vyberte váš hostitelský systém. V pravém podokně v seznamu **Virtuální počítače** vyhledejte virtuální počítač, který jste právě vytvořili. Vyberte název počítače, klikněte na něj pravým tlačítkem a vyberte **Nastavení**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stránce **Nastavení** klikněte v levém podokně na **Procesor**. V pravém podokně nastavte **počet virtuálních procesorů** na 4 (nebo více). Klikněte na **Použít**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby bylo možné splnit minimální požadavky, je nutné přidat virtuální datový disk 500 GB. Na stránce **Nastavení**:

    1. V levém podokně vyberte **Řadič SCSI**.
    2. V pravém podokně vyberte **Pevný disk** a klikněte na **Přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stránce **Pevný disk** vyberte možnost **Virtuální pevný disk** a klikněte na **Nový**. Spustí se **Průvodce novým virtuálním pevným diskem**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stránce **Než začnete** Průvodce novým virtuálním pevným diskem klikněte na **Další**.
16. Na stránce **Zvolit formát disku** přijměte výchozí možnost formátu **VHDX**. Klikněte na **Další**. Tato obrazovka se nezobrazuje, pokud používáte systém Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stránce **Zvolit typ disku** jako typ virtuálního pevného disku nastavte **Dynamicky se zvětšující** (doporučeno). Disk **pevné velikosti** by také fungoval, ale možná byste museli dlouho čekat. Doporučujeme nepoužívat možnost **Rozdílový**. Klikněte na **Další**. V systémech Windows Server 2012 R2 a Windows Server 2012 je výchozím nastavením výchozí **možnost, že** ve windows serveru 2008 R2 je výchozí **Velikost pevná**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stránce **Zadejte název a umístění** zadejte **název** a **umístění** (k umístění můžete přejít) datového disku. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na stránce **Konfigurovat disk** vyberte možnost **vytvořit nový prázdný virtuální pevný disk** a zadejte velikost **500 GB** (nebo více). I když je minimální požadavek 500 GB, můžete vždy zřídit větší disk. Všimněte si, že po zřízení nelze disk rozbalit ani zmenšit. Další informace o velikosti disku, který se má zřídit, najdete v části Změna velikosti v [dokumentu osvědčené postupy](storsimple-ova-best-practices.md). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na stránce **Souhrn** zkontrolujte podrobnosti virtuálního pevného disku, a pokud budete spokojeni, kliknutím na **Dokončit** disk vytvořte. Průvodce se zavře a do vašeho počítače se přidá virtuální pevný disk.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Vraťte se na stránku **Nastavení**. Kliknutím na **OK** zavřete stránku **Nastavení** a vraťte se do okna Správce technologie Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: spuštění virtuálního pole a získání IP adresy
Provedením následujících kroků spustíte virtuální pole a připojíte se k němu.

#### <a name="to-start-the-virtual-array"></a>Spuštění virtuálního pole
1. Spusťte virtuální pole.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Jakmile bude zařízení spuštěné, vyberte ho, klikněte na něj pravým tlačítkem a vyberte **Připojit**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Možná budete muset počkat 5-10 minut, než bude zařízení připravené. V konzole se zobrazí zpráva o stavu značící průběh. Jakmile bude zařízení připravené, přejděte do části **Akce**. Stisknutím `Ctrl + Alt + Delete` klávesy se přihlaste k virtuálnímu poli. Výchozí uživatel je *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí splňovat alespoň 3 z následujících 4 požadavků: velká písmena, malá písmena, číslice a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Zobrazí se oznámení o změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po úspěšném dokončení změny hesla se virtuální pole může restartovat. Počkejte, až se zařízení spustí.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zobrazí se konzola Windows PowerShellu zařízení a indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud jste zařízení spouštěli v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    Dále nakonfigurujte síť.
7. Pomocí `Get-HcsIpAddress` příkazu můžete zobrazit seznam síťových rozhraní povolených ve virtuálním poli. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Prohlédněte si následující příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Pomocí této IP adresy se připojte k webovému uživatelskému rozhraní virtuálního pole a dokončete místní nastavení a registraci.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. Volitelné Tento krok proveďte jenom v případě, že nasazujete zařízení v cloudu pro státní správu. Teď na svém zařízení povolíte USA režim FIPS (Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití v počítačových systémech USA federální správy pro ochranu citlivých dat.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS restartujte zařízení, aby se kryptografické ověřování projevilo.

       > [!NOTE]
       > V zařízení můžete zapnout nebo vypnout režim FIPS. Střídání zařízení mezi režimy FIPS a non-FIPS není podporováno.
       >
       >

Pokud zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru následující chyba (viz níže). Upravte konfiguraci zařízení tak, aby měl počítač dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci v kroku 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky na virtuální pole.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Pokud se při počáteční konfiguraci s použitím místního webového uživatelského rozhraní zobrazí nějaká chyba, přečtěte si následující pracovní postupy:

* Spusťte diagnostické testy pro [řešení potíží s instalací webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generování souboru protokolu a zobrazení souborů protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další kroky
* [Nastavení virtuálního pole StorSimple jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení StorSimple virtuálního pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
