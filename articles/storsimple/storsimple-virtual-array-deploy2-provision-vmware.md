---
title: Zřídit pole virtuální zařízení StorSimple ve službě VMware | Microsoft Docs
description: V tomto kurzu druhé pole virtuální zařízení StorSimple řady nasazení zahrnuje zřizování virtuálního zařízení ve službě VMware.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be737550aa1cff22bc413ee8ce243f8d8588f6a5
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
ms.locfileid: "29972336"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Nasazení zařízení StorSimple virtuální pole - zřídit ve službě VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Přehled
Tento kurz popisuje, jak zřídit a připojte se k poli virtuální zařízení StorSimple v hostitelském systému, systémem VMware ESXi 5.0, 5.5 nebo 6.0. Tento článek se týká nasazení pole virtuální zařízení StorSimple v portálu Azure a cloudu Microsoft Azure Government.

Potřebovat oprávnění správce k poskytování a připojte se k virtuálnímu zařízení. Zřizování a počáteční instalace může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování požadavky
Požadavky pro zřízení virtuálního zařízení v systému hostitele, který používá 5.0 VMware ESXi 5.5 nebo 6.0, jsou následující.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [připravit na portálu pro pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Bitovou kopii virtuálního zařízení pro VMware jste si stáhli z portálu Azure. Další informace najdete v tématu **krok 3: stáhnout bitovou kopii virtuálního zařízení** z [připravit na portálu pro Průvodce pole virtuální zařízení StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Pro virtuální zařízení StorSimple
Před nasazením virtuálního zařízení, ujistěte se, že:

* Máte přístup k systému hostitele s technologií Hyper-V (2008 R2 nebo novější), lze použít k zřízení zařízení.
* Systém hostitele je možné vyhradit následující prostředky pro zřízení virtuálního zařízení:

  * Minimálně 4 jádra.
  * Alespoň 8 GB paměti RAM. Pokud chcete konfigurovat virtuální pole jako souborový server, 8 GB podporuje menší než 2 miliony souborů. Je nutné 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní.
  * 500 GB virtuální disk pro data systému.

### <a name="for-the-network-in-datacenter"></a>Pro síť datového centra.
Než začnete, ujistěte se, že:

* Zkontrolovat požadavky na sítě k nasazení virtuálního zařízení StorSimple a nakonfigurovaný podle požadavků na síti datového centra. 

## <a name="step-by-step-provisioning"></a>Krok za krokem zřizování
Pokud chcete zřídit a připojení k virtuálnímu zařízení, musíte provést následující kroky:

1. Zajistěte, aby hostitelský systém dostatek prostředků ke splňují požadavky na minimální virtuální zařízení.
2. Zřídit virtuální zařízení ve vaší hypervisoru.
3. Spusťte virtuální zařízení a získat IP adresu.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje požadavky na minimální virtuálního zařízení
Pokud chcete vytvořit virtuální zařízení, budete potřebovat:

* Přístup k systému hostitele, který používá VMware ESXi Server 5.0, 5.5 nebo 6.0.
* VMware vSphere klienta v systému pro správu hostitele ESXi.

  * Minimálně 4 jádra.
  * Alespoň 8 GB paměti RAM. Pokud chcete konfigurovat virtuální pole jako souborový server, 8 GB podporuje menší než 2 miliony souborů. Je nutné 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní připojené k síti podporující směrování provoz do Internetu. Minimální šířky pásma Internetu by měl být 5 MB/s na povolit pro optimální fungování zařízení.
  * 500 GB virtuální disk pro data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Zřídit virtuální zařízení v hypervisoru
Proveďte následující kroky pro zřízení virtuálního zařízení ve vaší hypervisoru.

1. Zkopírujte bitovou kopii virtuálního zařízení ve vašem systému. Jste si stáhli tuto bitovou kopii virtuálního prostřednictvím portálu Azure.

   1. Ujistěte se, že jste si stáhli nejnovější soubor bitové kopie. Pokud jste předtím stáhli bitovou kopii, stáhněte ho znovu a ujistěte se, že máte nejnovější bitovou kopii. Nejnovější bitové kopie má dva soubory (místo toho).
   2. Poznamenejte si umístění, kam můžete zkopírovat bitovou kopii jako používáte tuto bitovou kopii později v postupu.

2. Přihlaste se k serveru ESXi pomocí vSphere klienta. Musíte mít oprávnění správce k vytvoření virtuálního počítače.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. V klientovi vSphere v sekci inventáře v levém podokně vyberte Server, ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Nahrajte na server ESXi VMDK. Přejděte na **konfigurace** kartě v pravém podokně. V části **hardwaru**, vyberte **úložiště**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. V pravém podokně v části **Datastores**, vyberte úložiště dat, kam chcete nahrát VMDK. S úložištěm, musí mít dostatek volného místa pro disky operačního systému a data.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klikněte pravým tlačítkem a vyberte **procházet úložiště**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. A **úložiště prohlížeče** se zobrazí v okně.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na panelu nástrojů klikněte na tlačítko ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonu vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Tento název složky bude používat později, při vytváření virtuálního počítače (doporučeno osvědčený postup). Klikněte na **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Do nové složky se zobrazí v levém podokně **úložiště prohlížeče**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klikněte na ikonu nahrávání ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) a vyberte **nahrát soubor**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Procházet a přejděte na soubory VMDK, které jste stáhli. Existují dva soubory. Vyberte soubor k odeslání.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klikněte na tlačítko **otevřete**. Spustí se nahrávání souboru VMDK zadaný úložištěm. To může trvat několik minut, než soubor k odeslání.
13. Po dokončení nahrávání se zobrazí souboru v úložišti dat ve složce, kterou jste vytvořili.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teď nahrajte druhý soubor VMDK na stejné úložiště.
14. Vraťte se do okna vSphere klienta. S ESXi server vybraný, klikněte pravým tlačítkem a vyberte **nový virtuální počítač**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **vytvořit nový virtuální počítač** se okno. Na **konfigurace** vyberte **vlastní** možnost. Klikněte na **Další**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na **název a umístění** stránky, zadejte název virtuálního počítače. Tento název by měl odpovídat názvu složky (osvědčeného postupu), který jste zadali v kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na **úložiště** vyberte úložiště, kterou chcete použít ke zřízení virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na **verze virtuálního počítače** vyberte **verze virtuálního počítače: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na **hostovaný operační systém** vyberte **hostovaný operační systém** jako **Windows**. Pro **verze**, z rozevíracího seznamu vyberte **Microsoft Windows Server 2012 (64 bitů)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na **procesory** stránky, upravte **virtuální soketů** a **počet jader na virtuální soketu** tak, aby **celkový počet jader** je 4 (nebo více). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na **paměti** zadejte 8 GB (nebo více) paměti RAM. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na **sítě** stránky, zadejte počet síťových rozhraní. Minimální požadavek je jedno síťové rozhraní.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na **řadič SCSI** přijměte výchozí **řadič LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na **vyberte Disk** vyberte **použijte existující virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na **vyberte stávající Disk** v části **cesta k souboru disku**, klikněte na tlačítko **Procházet**. Tím se otevře **procházet Datastores** dialogové okno. Přejděte do umístění, kde jste nahráli VMDK. Nyní uvidíte pouze jeden soubor v úložišti dat jako sloučeným dva soubory, které jste původně nahráli. Vyberte soubor a klikněte na tlačítko **OK**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na **pokročilé možnosti** , přijměte výchozí nastavení a klikněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na **připravení Dokončit** zkontrolujte všechna nastavení, které jsou přidružené k novému virtuálnímu počítači. Zkontrolujte **upravit nastavení virtuálního počítače před dokončením**. Klikněte na tlačítko **pokračovat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na **vlastnosti virtuálního počítače** stránky v **hardwaru** najděte hardwarové zařízení. Vyberte **nový pevný Disk**. Klikněte na tlačítko **Add** (Přidat).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobrazí **přidat Hardware** okno. Na **typ zařízení** v části **vyberte typ zařízení, které chcete přidat**, vyberte **pevný Disk**a klikněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na **vyberte Disk** vyberte **vytvořit nový virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na **vytvoření disku** změňte **velikost disku** 500 GB (nebo více). Požadavek na minimální při 500 GB můžete zřídit vždy větší disku. Všimněte si, že nelze rozbalit nebo zmenšení disku jednou zřízený. Další informace o velikosti disku a zajišťují, projděte si část nastavení velikosti v [osvědčené postupy dokumentu](storsimple-ova-best-practices.md). V části **disku zřizování**, vyberte **dynamickým zajišťováním**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na **pokročilé možnosti** přijměte výchozí nastavení.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na **připravení Dokončit** stránky, projděte si možnosti disku. Klikněte na **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Návrat na stránku vlastností virtuálního počítače. K virtuálnímu počítači se přidá nový pevný disk. Klikněte na **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. S virtuální počítač vybrali v pravém podokně přejděte do **Souhrn** kartě. Zkontrolujte nastavení pro virtuální počítač.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Virtuální počítač je nyní zajištěna. Dalším krokem je spotřeby na tomto počítači a získat IP adresu.

> [!NOTE]
> Doporučujeme vám, neinstalujte nástroje VMware na virtuální pole, (jak zřídit výše). Instalace nástroje VMware bude mít za následek nepodporované konfigurace.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Spuštění virtuální zařízení a získat IP adresu
Proveďte následující kroky ke spuštění virtuálního zařízení a k nim připojit.

#### <a name="to-start-the-virtual-device"></a>Při spuštění virtuálního zařízení
1. Spuštění virtuálního zařízení. V vSphere nástroje Configuration Manager, v levém podokně vyberte zařízení a klikněte pravým tlačítkem na zprovoznit v místní nabídce. Vyberte **Power** a pak vyberte **zapnout**. To by měl power ve virtuálním počítači. Můžete zobrazit stav v dolní části **poslední úkoly** podokně vSphere klienta.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Instalační program úlohy bude trvat několik minut. Jakmile je na zařízení spuštěný, přejděte na **konzoly** kartě. Odešlete Ctrl + Alt + Delete pro přihlášení k zařízení. Alternativně můžete bodu kurzoru v okně konzoly a stiskněte klávesu Ctrl + Alt + Insert. Výchozí uživatel se *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Z bezpečnostních důvodů vyprší platnost hesla správce zařízení při prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Zadejte heslo obsahující alespoň 8 znaků. Heslo musí obsahovat 3 z 4 z těchto požadavků: velká písmena, malá písmena, číselné a speciální znaky. Zadejte znovu heslo k potvrzení této akce. Budete informováni, že došlo ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po heslo úspěšně změněno, může restartování virtuálního zařízení. Počkejte na dokončení restartování. Konzole prostředí Windows PowerShell pro zařízení, může se zobrazit společně s indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroky 6 až 8 se projeví pouze při dalším spuštění v prostředí bez služby DHCP. Pokud jste v prostředí s DHCP, tyto kroky přeskočte a přejděte ke kroku 9. Pokud je spouštěn nahoru zařízení v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   V dalším kroku Nakonfigurujte síť.
7. Použití `Get-HcsIpAddress` seznam rozhraní sítě povolené na virtuálním zařízení příkazu. Pokud vaše zařízení má jedno síťové rozhraní povolena, je výchozí název, který je přiřazen k tomuto rozhraní `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Použití `Set-HcsIpAddress` rutiny pro konfiguraci sítě. Níže je uveden příklad:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po dokončení počáteční nastavení a zařízení má spuštění nahoru, zobrazí se text hlavičky zařízení. Poznamenejte si IP adresu a adresu URL zobrazené v text hlavičky ke správě zařízení. Tuto IP adresu budete používat pro připojení k webového uživatelského rozhraní vašeho virtuální zařízení a místní instalaci a registraci dokončit.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Volitelné) Tento krok proveďte jenom v případě, že nasazujete zařízení v Cloud vlády. Nyní povolíte režimu Spojených států informace o zpracování Standard FIPS (Federal) ve vašem zařízení. Standard FIPS 140 definuje kryptografické algoritmy pro použití schváleno nám Federal government počítačových systémů pro ochranu citlivá data.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Restartování zařízení po povolení režimu FIPS tak, aby kryptografických ověření vstoupí v platnost.

       > [!NOTE]
       > Můžete povolit nebo zakázat režim FIPS ve vašem zařízení. Střídání zařízení mezi režim FIPS a standardu FIPS není podporována.
       >
       >

Pokud zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se chyba v text hlavičky (zobrazené dole). Potřebujete upravit konfiguraci zařízení tak, aby ho nemá odpovídající prostředky pro splňují minimální požadavky. Potom můžete restartovat a připojte k zařízení. Přečtěte si požadavky minimální konfigurace v [krok 1: Ujistěte se, že hostitelský systém splňuje požadavky na minimální virtuální zařízení](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Pokud jste čelí chybě během počáteční konfigurace provedená prostřednictvím místního webového uživatelského rozhraní, podívejte se na následující pracovních postupů:

* Spustit diagnostické testy na [řešení potíží s instalací webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generovat balíček protokolu a prohlížení soubory protokolů](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další postup
* [Nastavení pole virtuální zařízení StorSimple jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení pole virtuální zařízení StorSimple jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
