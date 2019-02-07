---
title: StorSimple Virtual Array technologie Hyper-v zřídit | Dokumentace Microsoftu
description: Této druhé části kurzu ve StorSimple Virtual Array nasazení zahrnuje zřizování virtuálního pole v Hyper-V.
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
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5104d630e2b4e97b80a6fedfb6d863061c2722fb
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55810537"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Nasazení virtuálních polí StorSimple – zřízení technologie Hyper-v
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Přehled
Tento kurz popisuje, jak zřídit StorSimple Virtual Array v hostitelském systému na Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 s technologií Hyper-V. Tento článek se týká k nasazení virtuálních polí StorSimple v cloudu Microsoft Azure Government a webu Azure portal.

Potřebovat oprávnění správce k poskytování a konfigurace virtuálního pole. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování požadavky
Tady najdete požadavky na zřízení virtuálního pole v hostitelském systému na Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 s technologií Hyper-V.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [připravit na portálu pro StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Jste si stáhli image virtuálního pole pro Hyper-V na webu Azure Portal. Další informace najdete v tématu **krok 3: Stáhněte si image virtuálního pole** z [připravit na portálu pro StorSimple Virtual Array průvodce](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Software pro StorSimple Virtual Array může použít jenom ve službě Správce zařízení StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Pro StorSimple Virtual Array
Před nasazením virtuálního pole, ujistěte se, že:

* Máte přístup k hostitelskému systému technologii Hyper-V v systému Windows Server 2008 R2 nebo novější, kterou lze použít na základě zařízení.
* Systém hostitele je možné vyhradit následující prostředky pro zřízení virtuálního pole:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud chcete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Je třeba 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní
  * 500 GB virtuální disk pro data.

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru
Než začnete, projděte si požadavky na síť pro nasazení virtuálních polí StorSimple a odpovídajícím způsobem nakonfigurovat síť datových center. Další informace najdete v tématu [StorSimple Virtual Array požadavky na síť](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Krok za krokem zřizování
Zřízení a připojit se k virtuální pole, je třeba provést následující kroky:

1. Zajistěte, aby hostitelského systému dostatek prostředků ke splnění těchto požadavků minimální virtuální pole.
2. Zřízení virtuálního pole v vaše hypervisoru.
3. Spustit virtuální pole a získejte IP adresu.

Každý z těchto kroků je vysvětlené v následujících částech.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Ujistěte se, že hostitel systém splňuje požadavky na minimální virtuální pole
Pokud chcete vytvořit virtuální pole, budete potřebovat:

* Role Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 SP1 nainstalován.
* Správce technologie Microsoft Hyper-V na klientovi Microsoft Windows připojeném k hostiteli.

Ujistěte se, že základního hardwaru (hostitele systému), na kterém vytváříte virtuální pole je možné vyhradit pro vaše virtuální pole na následujících odkazech:

* Minimálně 4 jádra
* Minimálně 8 GB paměti RAM Pokud chcete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Je třeba 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
* Jedno síťové rozhraní
* 500 GB virtuální disk pro data systému.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Zřízení virtuálního pole v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte zařízení.

#### <a name="to-provision-a-virtual-array"></a>Ke zřízení virtuálního pole
1. Na hostitele Windows serveru zkopírujte image virtuálního pole na místní disk. Jste si stáhli tuto image (VHD nebo VHDX) na webu Azure portal. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.
2. Otevřete **Správce serveru**. V pravém horním rohu klikněte na **Nástroje** a vyberte **Správce technologie Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Pokud používáte Windows Server 2008 R2, otevřete Správce technologie Hyper-V. Ve Správci serveru klikněte na tlačítko **role > Hyper-V > Správce technologie Hyper-V**.
3. V podokně oboru **Správce technologie Hyper-V** kliknutím pravým tlačítkem na uzel systému otevřete místní nabídku a pak klikněte na **Nový** > **Virtuální počítač**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na stránce **Než začnete** Průvodce novým virtuálním počítačem klikněte na **Další**.
5. Na **zadejte název a umístění** zadejte **název** pro vaše virtuální pole. Klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na **zadat generaci** stránce, zvolte typ obrázku zařízení a potom klikněte na tlačítko **Další**. Tato stránka se nezobrazí, pokud používáte Windows Server 2008 R2.

   * Zvolte **2. generace** Pokud jste si stáhli image vhdx pro Windows Server 2012 nebo novější.
   * Zvolte **1. generace** Pokud jste si stáhli image .vhd pro systém Windows Server 2008 R2 nebo novější.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na stránce **Přiřadit paměť** zadejte jako **Spouštěcí paměť** alespoň **8 192 MB**, dynamickou paměť nepovolujte, a pak klikněte **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na stránce **Konfigurace sítí** zadejte virtuální přepínač připojený k internetu a pak klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na **připojit virtuální pevný disk** zvolte **použít existující virtuální pevný disk**, zadejte umístění image virtuálního pole (vhdx nebo VHD) a potom klikněte na tlačítko **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Zkontrolujte **Souhrn** a pak kliknutím na **Dokončit** vytvořte virtuální počítač.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Ke splnění minimálních požadavků potřebujete 4 jádra. Pokud chcete přidat 4 virtuální procesory, v okně **Správce technologie Hyper-V** vyberte váš hostitelský systém. V pravém podokně v seznamu **Virtuální počítače** vyhledejte virtuální počítač, který jste právě vytvořili. Vyberte název počítače, klikněte na něj pravým tlačítkem a vyberte **Nastavení**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na stránce **Nastavení** klikněte v levém podokně na **Procesor**. V pravém podokně nastavte **počet virtuálních procesorů** na 4 (nebo více). Klikněte na tlačítko **Použít**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Pro splnění minimálních požadavků na, musíte také přidat virtuální datový disk 500 GB. Na stránce **Nastavení**:

    1. V levém podokně vyberte **Řadič SCSI**.
    2. V pravém podokně vyberte **Pevný disk** a klikněte na **Přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na stránce **Pevný disk** vyberte možnost **Virtuální pevný disk** a klikněte na **Nový**. Spustí se **Průvodce novým virtuálním pevným diskem**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na stránce **Než začnete** Průvodce novým virtuálním pevným diskem klikněte na **Další**.
16. Na stránce **Zvolit formát disku** přijměte výchozí možnost formátu **VHDX**. Klikněte na **Další**. Tato obrazovka se nezobrazí, pokud se systémem Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na stránce **Zvolit typ disku** jako typ virtuálního pevného disku nastavte **Dynamicky se zvětšující** (doporučeno). Disk **pevné velikosti** by také fungoval, ale možná byste museli dlouho čekat. Doporučujeme nepoužívat možnost **Rozdílový**. Klikněte na **Další**. V systému Windows Server 2012 R2 a Windows Server 2012 **dynamicky se zvětšující** je výchozí možnost, že ve Windows serveru 2008 R2, výchozí hodnota je **pevnou velikost**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na stránce **Zadejte název a umístění** zadejte **název** a **umístění** (k umístění můžete přejít) datového disku. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na **konfigurovat Disk** stránky, vyberte možnost **vytvořit nový prázdný virtuální pevný disk** a určete velikost jako **500 GB** (nebo více). Zatímco je požadavek na minimální 500 GB, můžete zřídit vždy větší disk. Všimněte si, že nelze zvětšení nebo zmenšení na disk po zřízení. Další informace o velikosti disku, který se zřizování, najdete v tématu v části Určení velikosti [osvědčené postupy pro dokument](storsimple-ova-best-practices.md). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na stránce **Souhrn** zkontrolujte podrobnosti virtuálního pevného disku, a pokud budete spokojeni, kliknutím na **Dokončit** disk vytvořte. Průvodce se zavře a do vašeho počítače se přidá virtuální pevný disk.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Vraťte se na stránku **Nastavení**. Kliknutím na **OK** zavřete stránku **Nastavení** a vraťte se do okna Správce technologie Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Spustit virtuální pole a získejte IP
Proveďte následující kroky a spustit virtuální pole a k němu připojit.

#### <a name="to-start-the-virtual-array"></a>Spustit virtuální pole
1. Spusťte virtuální pole.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Jakmile bude zařízení spuštěné, vyberte ho, klikněte na něj pravým tlačítkem a vyberte **Připojit**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Bude pravděpodobně nutné počkejte 5 až 10 minut, než se zařízení bude připravené. V konzole se zobrazí zpráva o stavu značící průběh. Jakmile bude zařízení připravené, přejděte do části **Akce**. Stisknutím klávesy `Ctrl + Alt + Delete` k přihlášení do virtuálního pole. Výchozí uživatel *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí splňovat alespoň 3 z následujících 4 požadavků: velká písmena, malá písmena, číslice a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Zobrazí se oznámení o změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po úspěšné změny hesla virtuální pole se může restartovat. Počkejte, až se zařízení spustí.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Zobrazí se konzola Windows PowerShellu zařízení a indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud je spuštěn vaše zařízení v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    V dalším kroku Nakonfigurujte síť.
7. Použití `Get-HcsIpAddress` zobrazte seznam síťových rozhraní na vaše virtuální pole povolená. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Prohlédněte si následující příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Připojení k webovému rozhraní virtuálního pole a dokončete místní nastavování a registraci použijte tuto IP adresu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Volitelné) Tento krok proveďte jenom v případě, že nasazujete své zařízení do cloudu státní správy. Režim USA federální informace o zpracování Standard (FIPS) vám umožní nyní na vašem zařízení. Standard FIPS 140 definuje schválené pro použití podle federální vlády počítačové systémy pro ochranu citlivých dat kryptografické algoritmy.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS tak, aby se projevily kryptografických ověření až po restartování zařízení.

       > [!NOTE]
       > Můžete povolit nebo zakázat režim FIPS ve vašem zařízení. Alternující zařízení mezi režimu FIPS a podle standardu FIPS se nepodporuje.
       >
       >

Pokud zařízení nesplňuje požadavky na minimální konfiguraci, zobrazí se následující chyba v textu hlavičky (viz dole). Upravte konfiguraci zařízení tak, aby měl počítač dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Podívejte se minimální požadavky na konfiguraci v kroku 1: Ujistěte se, že hostitel systém splňuje požadavky na minimální virtuální pole.

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Pokud budete mít jiná chyba během počáteční konfigurace prostřednictvím místního webového uživatelského rozhraní, najdete v následujících pracovních postupů:

* Spustit diagnostické testy, abyste [řešení potíží s instalací webové uživatelské rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Vygenerujte balíček pro protokolu a zobrazit soubory protokolů](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další postup
* [Nastavit službu StorSimple Virtual Array jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavit službu StorSimple Virtual Array jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
