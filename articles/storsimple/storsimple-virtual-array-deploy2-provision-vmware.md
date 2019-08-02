---
title: Zřízení virtuálního pole StorSimple ve VMware | Microsoft Docs
description: Tento druhý kurz v řadě nasazení virtuálních polí StorSimple zahrnuje zřizování virtuálního zařízení ve VMware.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ab5ad8acc5d0769a19a4022c55e0461e7ce42762
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516831"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Nasazení virtuálního pole StorSimple – zřizování ve VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

V tomto kurzu se dozvíte, jak zřídit a připojit se k virtuálnímu poli StorSimple na hostitelském systému, na kterém běží VMware ESXi 5,0, 5,5, 6,0 nebo 6,5. Tento článek se týká nasazení virtuálních polí StorSimple v Azure Portal a v cloudu Microsoft Azure Government.

Ke zřízení virtuálního zařízení a připojení k němu potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Požadavky zřizování
Níže jsou uvedené požadavky na zřízení virtuálního zařízení v hostitelském systému, na kterém běží VMware ESXi 5,0, 5,5, 6,0 nebo 6,5.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Stáhli jste image virtuálního zařízení pro VMware z Azure Portal. Další informace najdete v **kroku 3: Stáhněte si image** virtuálního zařízení a [Připravte portál pro StorSimple Virtual Array Guide](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Pro virtuální zařízení StorSimple
Před nasazením virtuálního zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému, na kterém běží Hyper-V (2008 R2 nebo novější), který se dá použít k zřízení zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení vyhradit následující prostředky:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 000 000 souborů. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
  * Jedno síťové rozhraní
  * Virtuální disk 500 GB pro systémová data.

### <a name="for-the-network-in-datacenter"></a>Síť v datacentru
Než začnete, ujistěte se, že:

* Zkontrolovali jste požadavky na síť pro nasazení virtuálního zařízení StorSimple a nakonfigurovali jste síť datového centra podle požadavků. 

## <a name="step-by-step-provisioning"></a>Podrobné zřizování
Chcete-li zřídit virtuální zařízení a připojit se k němu, je nutné provést následující kroky:

1. Zajistěte, aby měl hostitelský systém dostatek prostředků, aby splňovaly minimální požadavky na virtuální zařízení.
2. Zřídit virtuální zařízení ve hypervisoru.
3. Spusťte virtuální zařízení a získejte IP adresu.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Zajistěte, aby hostitelský systém splňoval minimální požadavky na virtuální zařízení.
K vytvoření virtuálního zařízení budete potřebovat:

* Přístup k hostitelskému systému se systémem VMware ESXi Server 5,0, 5,5, 6,0 nebo 6,5.
* Klienta VMware vSphere ve vašem systému pro správu hostitele ESXi.

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 000 000 souborů. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
  * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. Minimální šířka pásma internetu by měla být 5 MB/s, aby bylo možné optimálně pracovat na zařízení.
  * Virtuální disk 500 GB pro data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Zřízení virtuálního zařízení v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte virtuální zařízení.

1. Zkopírujte do svého systému image virtuálního zařízení. Tuto virtuální bitovou kopii jste stáhli pomocí Azure Portal.

   1. Ujistěte se, že jste stáhli nejnovější soubor obrázku. Pokud jste dříve stáhli bitovou kopii, stáhněte ji znovu, abyste měli jistotu, že máte nejnovější obrázek. Poslední obrázek má dva soubory (místo jednoho).
   2. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.

2. Pomocí klienta vSphere se přihlaste k serveru ESXi. K vytvoření virtuálního počítače potřebujete mít oprávnění správce.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. V klientovi vSphere v části inventář v levém podokně vyberte server ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Nahrajte VMDK na server ESXi. V pravém podokně přejděte na kartu **Konfigurace** . V části **hardware**vyberte **úložiště**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. V pravém podokně v části **Úložiště dat** vyberte úložiště dat, do kterého chcete VMDK nahrát. Úložiště dat musí mít dostatek volného místa pro operační systém a datové disky.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klikněte pravým tlačítkem a vyberte **Procházet úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zobrazí se okno **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na panelu nástrojů klikněte na ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonu a vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Název této složky použijete později při vytváření virtuálního počítače (doporučený osvědčený postup). Klikněte na **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nová složka se zobrazí v levém podokně okna **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klikněte na ikonu ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) nahrát a vyberte **nahrát soubor**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Přejděte k souborům VMDK, které jste si stáhli. Zobrazí se dva soubory. Vyberte soubor k nahrání.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klikněte na **Otevřít**. Spustí se nahrávání souboru VMDK do zadaného úložiště dat. Nahrání souboru může několik minut trvat.
13. Po dokončení nahrávání se soubor zobrazí v úložišti dat ve složce, kterou jste vytvořili.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teď do stejného úložiště dat nahrajte druhý soubor VMDK.
14. Vraťte se do okna klienta vSphere. Když je vybraný server ESXi, klikněte pravým tlačítkem myši a vyberte **Nový virtuální počítač**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zobrazí se okno **vytvořit nový virtuální počítač** . Na stránce **Konfigurace** vyberte možnost **vlastní** . Klikněte na **Další**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stránce **název a umístění** zadejte název vašeho virtuálního počítače. Tento název by měl odpovídat názvu složky (doporučený osvědčený postup), který jste zadali dříve v kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stránce **úložiště** vyberte úložiště dat, které chcete použít ke zřízení virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stránce **verze virtuálního počítače** vyberte **verze virtuálního počítače: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stránce **hostovaný operační systém** vyberte **hostovaný operační systém** jako **Windows**. V rozevíracím seznamu **verze**vyberte možnost **Microsoft Windows Server 2012 (64-bit)** .

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stránce **procesory** upravte **počet virtuálních soketů** a **počet jader na virtuální soket** , aby **Celkový počet jader** byl 4 (nebo více). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stránce **paměť** zadejte 8 GB (nebo více) paměti RAM. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stránce **síť** zadejte počet síťových rozhraní. Minimálním požadavkem je jedno síťové rozhraní.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stránce **řadič SCSI** přijměte výchozí KONTROLer **SAS pro adaptér LSI Logic**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stránce **vybrat disk** zvolte možnost **použít existující virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stránce **Vybrat existující disk** klikněte v části **cesta k souboru na disku**na **Procházet**. Tím se otevře dialogové okno **Procházet úložiště dat** . Přejděte do umístění, kam jste nahráli VMDK. V úložišti dat teď uvidíte jenom jeden soubor, protože se sloučily dva soubory, které jste původně nahráli. Vyberte soubor a klikněte na **OK**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stránce **Upřesnit možnosti** přijměte výchozí nastavení a klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stránce **Připraveno k dokončení** zkontrolujte všechna nastavení přidružená k novému virtuálnímu počítači. **Před dokončením upravte nastavení virtuálního počítače**. Klikněte na **Pokračovat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stránce **vlastnosti Virtual Machines** na kartě **hardware** vyhledejte hardware zařízení. Vyberte **nový pevný disk**. Klikněte na **Přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobrazí se okno **Přidat hardware** . Na stránce **typ zařízení** v části **Zvolte typ zařízení, které chcete přidat**, vyberte možnost **pevný disk**a klikněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stránce **vybrat disk** zvolte možnost **vytvořit nový virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stránce **vytvořit disk** změňte **velikost disku** na 500 GB (nebo více). I když je minimální požadavek 500 GB, můžete vždy zřídit větší disk. Všimněte si, že po zřízení nelze disk rozbalit ani zmenšit. Další informace o velikosti disku, který se má zřídit, najdete v části Změna velikosti v [dokumentu osvědčené postupy](storsimple-ova-best-practices.md). V části **zřízení disku**vyberte **dynamické zajišťování**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stránce **Upřesnit možnosti** přijměte výchozí nastavení.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stránce **připraveno k dokončení** Zkontrolujte možnosti disku. Klikněte na tlačítko **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Vraťte se na stránku vlastností virtuálního počítače. Do virtuálního počítače se přidá nový pevný disk. Klikněte na tlačítko **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. V pravém podokně vyberte virtuální počítač a přejděte na kartu **Souhrn** . Zkontrolujte nastavení virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Váš virtuální počítač je teď zřízený. Dalším krokem je počítač zapnout a získat jeho IP adresu.

> [!NOTE]
> Doporučujeme, abyste nástroje VMware neinstalovali ve vašem virtuálním poli (jak jsme zřídili výše). Instalace nástrojů VMware bude mít za následek nepodporovanou konfiguraci.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Spuštění virtuálního zařízení a získání IP adresy
Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení. V vSphere Configuration Manager v levém podokně vyberte zařízení a kliknutím pravým tlačítkem otevřete místní nabídku. Vyberte **Napájení** a pak vyberte **Zapnout**. Tím by se měl virtuální počítač zapnout. Stav můžete zobrazit v podokně **nedávné úlohy** v klientovi vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Dokončení úloh instalace bude trvat několik minut. Jakmile je zařízení spuštěné, přejděte na kartu **Konzola** . Pokud se chcete přihlásit k zařízení, odešlete kombinaci kláves CTRL + ALT + DELETE. Alternativně můžete ukazatel myši nasměrovat na okno konzoly a stisknout kombinaci kláves CTRL + ALT + INSERT. Výchozí uživatel je *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí obsahovat 3 z 4 z těchto požadavků: velká písmena, malá písmena, číslice a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Zobrazí se oznámení o změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po úspěšném dokončení změny hesla se virtuální zařízení může restartovat. Počkejte, než se restart dokončí. Konzola prostředí Windows PowerShell v zařízení se může zobrazit spolu s indikátorem průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud jste zařízení spouštěli v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Dále nakonfigurujte síť.
7. `Get-HcsIpAddress` Pomocí příkazu uveďte síťová rozhraní povolená na virtuálním zařízení. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Příklad najdete níže:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Pomocí této IP adresy se připojíte k webovému uživatelskému rozhraní virtuálního zařízení a dokončete místní nastavení a registraci.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Volitelné Tento krok proveďte jenom v případě, že nasazujete zařízení v cloudu pro státní správu. Teď na svém zařízení povolíte USA režim FIPS (Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití v počítačových systémech USA federální správy pro ochranu citlivých dat.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS restartujte zařízení, aby se kryptografické ověřování projevilo.

       > [!NOTE]
       > V zařízení můžete zapnout nebo vypnout režim FIPS. Střídání zařízení mezi režimy FIPS a non-FIPS není podporováno.
       >
       >

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba (viz níže). Budete muset upravit konfiguraci zařízení tak, aby mělo dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci [v kroku 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)na virtuální zařízení.

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Pokud se při počáteční konfiguraci s použitím místního webového uživatelského rozhraní zobrazí nějaká chyba, přečtěte si následující pracovní postupy:

* Spusťte diagnostické testy pro [řešení potíží s instalací webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generování souboru protokolu a zobrazení souborů protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další postup
* [Nastavení virtuálního pole StorSimple jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení StorSimple virtuálního pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
