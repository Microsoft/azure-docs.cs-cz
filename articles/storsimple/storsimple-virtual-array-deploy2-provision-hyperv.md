---
title: Zřídit pole virtuální zařízení StorSimple technologie Hyper-v | Microsoft Docs
description: Tento druhý kurz v nasazení pole virtuální zařízení StorSimple zahrnuje zřizování virtuální pole Hyper-v.
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
ms.openlocfilehash: bad431c8958f7d381bb9c0410caa3a57c6e75c19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876590"
---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>Nasazení zařízení StorSimple virtuální pole - zřízení technologie Hyper-v
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>Přehled
Tento kurz popisuje, jak zřídit o pole virtuální zařízení StorSimple v systému hostitele s technologií Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2. Tento článek se týká nasazení pole virtuální zařízení StorSimple v portálu Azure a cloudu Microsoft Azure Government.

Můžete potřebovat oprávnění správce zřídit a nakonfigurovat virtuální pole. Zřizování a počáteční instalace může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování požadavky
Tady najdete požadavky zřídit virtuální pole v systému hostitele s technologií Hyper-V v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [připravit na portálu pro pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Obrázek virtuální pole pro Hyper-V jste si stáhli z portálu Azure. Další informace najdete v tématu **krok 3: stáhnout bitovou kopii virtuálního pole** z [připravit na portálu pro Průvodce pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

  > [!IMPORTANT]
  > Software spuštěný na poli virtuální zařízení StorSimple lze použít pouze se službou Správce zařízení StorSimple.
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>Pro pole virtuální zařízení StorSimple
Před nasazením virtuální pole, ujistěte se, že:

* Máte přístup k systému hostitele, který používá technologie Hyper-V v systému Windows Server 2008 R2 nebo novější, lze použít k zřízení zařízení.
* Systém hostitele je možné vyhradit následující prostředky pro zřízení virtuální pole:

  * Minimálně 4 jádra.
  * Alespoň 8 GB paměti RAM. Pokud chcete konfigurovat virtuální pole jako souborový server, 8 GB podporuje menší než 2 miliony souborů. Je nutné 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní.
  * 500 GB virtuální disk pro data.

### <a name="for-the-network-in-the-datacenter"></a>Síť v datovém centru
Než začnete, zkontrolujte požadavky na sítě k nasazení pole virtuální zařízení StorSimple a odpovídajícím způsobem nakonfigurovat síti datového centra. Další informace najdete v tématu [pole virtuální zařízení StorSimple sítě požadavky](storsimple-ova-system-requirements.md#networking-requirements).

## <a name="step-by-step-provisioning"></a>Krok za krokem zřizování
Pokud chcete zřídit a připojit k virtuální poli, musíte provést následující kroky:

1. Zajistěte, aby hostitelský systém dostatek prostředků ke splňují požadavky na minimální virtuální pole.
2. Zřídit virtuální pole ve vašem hypervisoru.
3. Spusťte virtuální pole a získat IP adresu.

Každý z těchto kroků je vysvětlené v následujících částech.

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje požadavky na minimální virtuální pole
Chcete-li vytvořit virtuální pole, je třeba:

* Role Hyper-V nainstalovaná v systému Windows Server 2012 R2, Windows Server 2012 nebo Windows Server 2008 R2 SP1.
* Microsoft Hyper-V Manager v klientském počítači Microsoft Windows, který je připojen k hostiteli.

Ujistěte se, že je možné vyhradit v následujících zdrojích informací na vaše virtuální pole základní hardware (hostitelský systém), na kterém vytváříte virtuální pole:

* Minimálně 4 jádra.
* Alespoň 8 GB paměti RAM. Pokud chcete konfigurovat virtuální pole jako souborový server, 8 GB podporuje menší než 2 miliony souborů. Je nutné 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
* Jedno síťové rozhraní.
* 500 GB virtuální disk pro data systému.

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>Krok 2: Zřídit virtuální pole v hypervisoru
Proveďte následující kroky pro zřízení zařízení ve vaší hypervisoru.

#### <a name="to-provision-a-virtual-array"></a>Zřizování virtuální pole
1. Na hostiteli s Windows Server zkopírujte bitovou kopii virtuálního pole na místní disk. Jste si stáhli tuto bitovou kopii (VHD nebo VHDX) prostřednictvím portálu Azure. Poznamenejte si umístění, kam můžete zkopírovat bitovou kopii jako používáte tuto bitovou kopii později v postupu.
2. Otevřete **správce serveru**. V pravém horním rohu, klikněte na **nástroje** a vyberte **Správce technologie Hyper-V**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   Pokud používáte Windows Server 2008 R2, otevřete Správce technologie Hyper-V. Ve Správci serveru klikněte na tlačítko **role > Hyper-V > Správce technologie Hyper-V**.
3. V **Správce technologie Hyper-V**, v podokně oboru klikněte pravým tlačítkem na uzel váš systém otevřete kontextu nabídku a pak klikněte na tlačítko **nový** > **virtuální počítač**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. Na **před zahájením** stránky Průvodce novým virtuálním počítačem, klikněte na tlačítko **Další**.
5. Na **zadejte název a umístění** zadejte **název** pro vaše virtuální pole. Klikněte na **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. Na **určit generaci** stránky, vyberte typ obrázku zařízení a pak klikněte na tlačítko **Další**. Tato stránka se nezobrazí, pokud používáte Windows Server 2008 R2.

   * Zvolte **generace 2** Pokud jste stáhli bitovou kopii .vhdx pro Windows Server 2012 nebo novější.
   * Zvolte **generace 1** Pokud jste stáhli VHD image pro Windows Server 2008 R2 nebo novější.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. Na **přiřazení paměti** stránky, zadejte **spouštěcí paměť** z alespoň **8192 MB**, nemáte povolte dynamickou paměť a pak klikněte na tlačítko **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. Na **konfigurace sítě** zadejte virtuální přepínač, který je připojený k Internetu a pak klikněte na tlačítko **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. Na **připojit virtuální pevný disk** vyberte **použít existující virtuální pevný disk**, zadejte umístění bitové kopie virtuálních pole (vhdx nebo VHD) a pak klikněte na tlačítko **Další**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. Zkontrolujte **Souhrn** a pak klikněte na **Dokončit** k vytvoření virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. Chcete-li splňují minimální požadavky, je nutné 4 jádra. Chcete-li přidat 4 virtuální procesory, vyberte vašeho hostitelského systému v **Správce technologie Hyper-V** okno. V pravém podokně v části seznamu **virtuální počítače**, najít virtuálního počítače, kterou jste právě vytvořili. Vyberte a klikněte pravým tlačítkem na název počítače a vyberte **nastavení**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. Na **nastavení** stránky, v levém podokně klikněte na tlačítko **procesoru**. V pravém podokně, nastavte **počet virtuálních procesorů** 4 (nebo více). Klikněte na tlačítko **Použít**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. Aby splňoval minimální požadavky, musíte taky přidat 500 GB virtuální datový disk. V **nastavení** stránky:

    1. V levém podokně vyberte **řadič SCSI**.
    2. V pravém podokně vyberte **pevný disk,** a klikněte na tlačítko **přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. Na **pevný disk** vyberte **virtuální pevný disk** možnost a klikněte na tlačítko **nový**. **Průvodce vytvořením virtuálního pevného disku** spustí.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. Na **před zahájením** stránky Průvodce vytvořením virtuálního pevného disku, klikněte na tlačítko **Další**.
16. Na **stránky výběr formátu disku**, přijměte výchozí možnost **VHDX** formátu. Klikněte na **Další**. Tato obrazovka není uveden, pokud systém Windows Server 2008 R2.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. Na **zvolit typ disku stránky**, nastavte typ virtuálního pevného disku jako **dynamicky se zvětšující** (doporučeno). **Pevná velikost** disk by pracovat, ale budete muset počkat dlouhou dobu. Doporučujeme vám, že nepoužíváte **Differencing** možnost. Klikněte na **Další**. V systému Windows Server 2012 R2 a Windows Server 2012 **dynamicky se zvětšující** je výchozí možnost, zatímco ve Windows serveru 2008 R2, výchozí hodnota je **pevnou velikost**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. Na **zadat název a umístění** zadejte **název** a také **umístění** (můžete vyhledat jednoho) pro datový disk. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. Na **konfigurovat Disk** vyberte možnost **vytvořit nový prázdný virtuální pevný disk** a zadejte velikost jako **500 GB** (nebo více). Požadavek na minimální při 500 GB můžete zřídit vždy větší disku. Všimněte si, že nelze rozbalit nebo zmenšení disku jednou zřízený. Další informace o velikosti disku a zajišťují, projděte si část nastavení velikosti v [osvědčené postupy dokumentu](storsimple-ova-best-practices.md). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. Na **Souhrn** , zkontrolujte podrobnosti virtuální datový disk a pokud splnit, klikněte na tlačítko **Dokončit** disk vytvoříte tak. Průvodce se zavře a virtuální pevný disk se přidá do vašeho počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. Vraťte se na **nastavení** stránky. Klikněte na tlačítko **OK** zavřete **nastavení** stránky a vraťte se do Správce technologie Hyper-V.

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>Krok 3: Spuštění virtuální pole a získat IP adresu
Proveďte následující postup spusťte virtuální pole a připojte se k němu.

#### <a name="to-start-the-virtual-array"></a>Spustit virtuální pole
1. Spusťte virtuální pole.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. Po spuštění zařízení vyberte zařízení, klikněte pravým tlačítkem a vyberte **Connect**.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. Možná budete muset počkat 5 až 10 minut, než se zařízení bude připravená. Stavová zpráva se zobrazí v konzole informace o průběhu. Když jsou připravena, přejít na **akce**. Stiskněte klávesu `Ctrl + Alt + Delete` k přihlášení do virtuální pole. Výchozí uživatel se *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. Z bezpečnostních důvodů vyprší platnost hesla správce zařízení při prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   Zadejte heslo obsahující alespoň 8 znaků. Heslo musí splňovat minimálně 3 z následujících požadavků 4: velká písmena, malá písmena, číselné a speciální znaky. Zadejte znovu heslo k potvrzení této akce. Zobrazí upozornění, že došlo ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. Po heslo úspěšně změněno, může restartovat virtuální pole. Počkejte, než zařízení spustit.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    Konzole prostředí Windows PowerShell pro zařízení se zobrazí spolu s indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. Kroky 6 až 8 se projeví pouze při dalším spuštění v prostředí bez služby DHCP. Pokud jste v prostředí s DHCP, tyto kroky přeskočte a přejděte ke kroku 9. Pokud je spouštěn nahoru zařízení v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    V dalším kroku Nakonfigurujte síť.
7. Použití `Get-HcsIpAddress` seznam rozhraní sítě povolené na vaše virtuální pole příkazu. Pokud vaše zařízení má jedno síťové rozhraní povolena, je výchozí název, který je přiřazen k tomuto rozhraní `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. Použití `Set-HcsIpAddress` rutiny pro konfiguraci sítě. Podívejte se na následující příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. Po dokončení počáteční nastavení a zařízení má spuštění nahoru, zobrazí se text hlavičky zařízení. Poznamenejte si IP adresu a adresu URL zobrazené v text hlavičky ke správě zařízení. Použijte tuto IP adresu připojit k serveru webového uživatelského rozhraní vaše virtuální pole a dokončete místní instalaci a registraci.

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. (Volitelné) Tento krok proveďte jenom v případě, že nasazujete zařízení v Cloud vlády. Nyní povolíte režimu Spojených států informace o zpracování Standard FIPS (Federal) ve vašem zařízení. Standard FIPS 140 definuje kryptografické algoritmy pro použití schváleno nám Federal government počítačových systémů pro ochranu citlivá data.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Restartování zařízení po povolení režimu FIPS tak, aby kryptografických ověření vstoupí v platnost.

       > [!NOTE]
       > Můžete povolit nebo zakázat režim FIPS ve vašem zařízení. Střídání zařízení mezi režim FIPS a standardu FIPS není podporována.
       >
       >

Pokud zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí následující chyba v text hlavičky (zobrazené dole). Upravte konfiguraci zařízení tak, aby tento počítač nemá odpovídající prostředky pro splňují minimální požadavky. Potom můžete restartovat a připojte k zařízení. Přečtěte si požadavky minimální konfigurace v [krok 1: Ujistěte se, že hostitelský systém splňuje požadavky na minimální virtuální pole](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

Pokud jste čelí chybě během počáteční konfigurace provedená prostřednictvím místního webového uživatelského rozhraní, podívejte se na následující pracovních postupů:

* Spustit diagnostické testy na [řešení potíží s instalací webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generovat balíček protokolu a prohlížení soubory protokolů](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další kroky
* [Nastavení pole virtuální zařízení StorSimple jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení pole virtuální zařízení StorSimple jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
