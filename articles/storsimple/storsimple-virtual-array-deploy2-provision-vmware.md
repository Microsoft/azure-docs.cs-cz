---
title: Zřízení virtuálního pole StorSimple ve společnosti VMware
description: Tento druhý kurz v sérii nasazení StorSimple Virtual Array zahrnuje zřizování virtuálního zařízení ve společnosti VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f5ded3faec3a080022eea70de2cca5d27529c4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76272100"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Nasazení virtuálního pole StorSimple – zřízení ve vmware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Tento kurz popisuje, jak zřídit a připojit se k virtuálnímu poli StorSimple v hostitelském systému se systémem VMware ESXi 5.0, 5.5, 6.0 nebo 6.5. Tento článek se týká nasazení virtuálních polí StorSimple na webu Azure Portal a Microsoft Azure Government Cloud.

Ke zřízení virtuálního zařízení a připojení k němu potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování předpokladů
Předpoklady pro zřízení virtuálního zařízení v hostitelském systému se systémem VMware ESXi 5.0, 5.5, 6.0 nebo 6.5 jsou následující.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v [části Příprava portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Stáhli jste image virtuálního zařízení pro VMware z webu Azure Portal. Další informace naleznete **v tématu Krok 3: Stažení bitové kopie virtuálního zařízení** připravit portál pro průvodce [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Pro virtuální zařízení StorSimple
Před nasazením virtuálního zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému se systémem Hyper-V (2008 R2 nebo novějším), který lze použít k zřízení zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení vyhradit následující prostředky:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Potřebujete 16 GB PAMĚTI RAM pro podporu 2 - 4 miliony souborů.
  * Jedno síťové rozhraní
  * 500 GB virtuální disk pro systémová data.

### <a name="for-the-network-in-datacenter"></a>Síť v datacentru
Než začnete, ujistěte se, že:

* Zkontrolovali jste požadavky na síť pro nasazení virtuálního zařízení StorSimple a nakonfigurovali síť datového centra podle požadavků. 

## <a name="step-by-step-provisioning"></a>Podrobné zřizování
Chcete-li zřídit a připojit se k virtuálnímu zařízení, musíte provést následující kroky:

1. Ujistěte se, že hostitelský systém má dostatek prostředků pro splnění minimálních požadavků na virtuální zařízení.
2. Zřídit virtuální zařízení v hypervisoru.
3. Spusťte virtuální zařízení a získejte IP adresu.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje minimální požadavky na virtuální zařízení
Chcete-li vytvořit virtuální zařízení, budete potřebovat:

* Přístup k hostitelskému systému se systémem VMware ESXi Server 5.0, 5.5, 6.0 nebo 6.5.
* Klienta VMware vSphere ve vašem systému pro správu hostitele ESXi.

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Potřebujete 16 GB PAMĚTI RAM pro podporu 2 - 4 miliony souborů.
  * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. Minimální šířka pásma internetu by měla být 5 Mb/s, aby bylo možné optimální fungování zařízení.
  * 500 GB virtuální disk pro data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Zřízení virtuálního zařízení v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte virtuální zařízení.

1. Zkopírujte do svého systému image virtuálního zařízení. Tuto virtuální bitovou kopii jste stáhli prostřednictvím portálu Azure.

   1. Ujistěte se, že jste stáhli nejnovější soubor obrázku. Pokud jste obrázek stáhli dříve, stáhněte si ho znovu, abyste měli k tomu nejnovější obrázek. Nejnovější obrázek má dva soubory (namísto jednoho).
   2. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.

2. Pomocí klienta vSphere se přihlaste k serveru ESXi. K vytvoření virtuálního počítače potřebujete mít oprávnění správce.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. V klientovi vSphere vyberte v části zásob v levém podokně server ESXi.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Nahrajte VMDK na server ESXi. Přejděte na kartu **Konfigurace** v pravém podokně. V části **Hardware**vyberte **Storage**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. V pravém podokně v části **Úložiště dat** vyberte úložiště dat, do kterého chcete VMDK nahrát. Úložiště dat musí mít dostatek volného místa pro operační systém a datové disky.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klikněte pravým tlačítkem a vyberte **Procházet úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zobrazí se okno **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na panelu nástrojů ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) kliknutím na ikonu vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Název této složky použijete později při vytváření virtuálního počítače (doporučený osvědčený postup). Klikněte na tlačítko **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nová složka se zobrazí v levém podokně okna **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klepněte na ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) ikonu Nahrát a vyberte **Nahrát soubor**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Přejděte k souborům VMDK, které jste si stáhli. Zobrazí se dva soubory. Vyberte soubor k nahrání.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klikněte na **Otevřít**. Spustí se nahrávání souboru VMDK do zadaného úložiště dat. Nahrání souboru může několik minut trvat.
13. Po dokončení nahrávání se soubor zobrazí v úložišti dat ve složce, kterou jste vytvořili.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teď do stejného úložiště dat nahrajte druhý soubor VMDK.
14. Vraťte se do okna klienta vSphere. S vybraným serverem ESXi klepněte pravým tlačítkem myši a vyberte **nový virtuální počítač**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zobrazí se okno **Vytvořit nový virtuální počítač.** Na stránce **Konfigurace** vyberte možnost **Vlastní.** Klikněte na **Další**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stránce **Název a umístění** zadejte název virtuálního počítače. Tento název by se měl shodovat s názvem složky (doporučeným osvědčeným postupem), který jste zadali dříve v kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stránce **Úložiště** vyberte úložiště dat, které chcete použít k zřízení virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stránce **Verze virtuálního počítače** vyberte **Verze virtuálního počítače: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stránce **Hostovaný operační systém** vyberte **hostovaný operační systém** jako **Windows**. V **části Verze**vrozené části vyberte možnost Microsoft Windows Server **2012 (64bit)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stránce **procesory** upravte **počet virtuálních soketů** a **počet jader na virtuální soket** tak, aby **celkový počet jader** byl 4 (nebo více). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stránce **Paměť** zadejte 8 GB (nebo více) paměti RAM. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stránce **Síť** zadejte číslo síťových rozhraní. Minimálním požadavkem je jedno síťové rozhraní.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stránce **Řadič SCSI** přijměte výchozí **řadič LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stránce **Vybrat disk** zvolte Použít existující **virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stránce **Vybrat existující disk** klepněte v části Cesta k **disku**na **tlačítko Procházet**. Otevře se dialogové **okno Procházet dataúložiště.** Přejděte do umístění, kam jste nahráli vmdk. Nyní se v úložišti dat zobrazí pouze jeden soubor, protože dva soubory, které jste původně nahráli, byly sloučeny. Vyberte soubor a klepněte na **tlačítko OK**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stránce **Upřesnit možnosti** přijměte výchozí hodnotu a klepněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stránce **Připraveno k dokončení** zkontrolujte všechna nastavení přidružená k novému virtuálnímu počítači. Před **dokončením zaškrtněte políčko Upravit nastavení virtuálního počítače**. Klikněte na **Pokračovat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stránce **Vlastnosti virtuálních počítačů** vyhledejte na kartě **Hardware** hardware zařízení. Vyberte **nový pevný disk**. Klikněte na **Přidat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobrazí se okno **Přidat hardware.** Na stránce **Typ zařízení** v yberte **Vyberte typ zařízení, které chcete přidat**, vyberte Pevný **disk**a klepněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stránce **Vybrat disk** zvolte Vytvořit nový **virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stránce **Vytvořit disk** změňte **velikost disku** na 500 GB (nebo více). Zatímco 500 GB je minimální požadavek, můžete vždy zřídit větší disk. Všimněte si, že po zřízení nelze disk rozbalit ani zmenšit. Další informace o velikosti disku zřídit, naleznete v části velikosti v [dokumentu doporučené postupy](storsimple-ova-best-practices.md). V části **Zřizování disku**vyberte **Položku Tenké zřizování**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stránce **Upřesnit možnosti** přijměte výchozí nastavení.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stránce **Připraveno k dokončení** zkontrolujte možnosti disku. Klikněte na **Finish** (Dokončit).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Vraťte se na stránku Vlastnosti virtuálního počítače. Do virtuálního počítače se přidá nový pevný disk. Klikněte na **Finish** (Dokončit).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. Když je váš virtuální počítač vybraný v pravém podokně, přejděte na kartu **Souhrn.**

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Váš virtuální počítač je teď zřízený. Dalším krokem je počítač zapnout a získat jeho IP adresu.

> [!NOTE]
> Doporučujeme neinstalovat nástroje VMware do virtuálního pole (jak je uvedeno výše). Instalace nástrojů VMware bude mít za následek nepodporovanou konfiguraci.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Spuštění virtuálního zařízení a získání IP
Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení. Ve správci konfigurace vSphere v levém podokně vyberte zařízení a kliknutím pravým tlačítkem myši zobrazíte místní nabídku. Vyberte **Napájení** a pak vyberte **Zapnout**. Tím by se měl virtuální počítač zapnout. Stav můžete zobrazit v dolním podokně **Poslední úkoly** klienta vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Dokončení úloh nastavení bude trvat několik minut. Po spuštění zařízení přejděte na kartu **Konzola.** Případně můžete nasměrovat kurzor na okno konzoly a stisknout kombinaci kláves Ctrl+Alt+Insert. Výchozí uživatel je *StorSimpleAdmin* a výchozí heslo je *Password1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí obsahovat 3 ze 4 z těchto požadavků: velká, malá, číselná a speciální znatá. Znovu zadejte heslo, abyste ho potvrdili. Budete upozorněni, že heslo bylo změněno.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po úspěšné změně hesla může dojít k restartování virtuálního zařízení. Počkejte na dokončení restartování. Konzola prostředí Windows PowerShell zařízení může být zobrazena spolu s indikátorem průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud jste zařízení nastartovali v prostředí jiného než DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Dále nakonfigurujte síť.
7. Pomocí `Get-HcsIpAddress` příkazu můžete uvést síťová rozhraní povolená na vašem virtuálním zařízení. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Příklad najdete níže:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Tuto IP adresu použijete k připojení k webovému uživatelskému rozhraní virtuálního zařízení a k dokončení místního nastavení a registrace.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Nepovinné) Tento krok proveďte pouze v případě, že nasazujete zařízení ve službě Government Cloud. Nyní na svém zařízení povolíte režim FIPS (United States Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití počítačovými systémy federální vlády USA pro ochranu citlivých dat.

    1. Chcete-li režim FIPS povolit, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS restartujte zařízení tak, aby se kryptografická ověření projevila.

       > [!NOTE]
       > V zařízení můžete režim FIPS povolit nebo zakázat. Střídání zařízení mezi režimem FIPS a režimem mimo FIPS není podporováno.
       >
       >

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba (viz níže). Budete muset upravit konfiguraci zařízení tak, aby mělo dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci v [kroku 1: Zajištění, aby hostitelský systém splňoval minimální požadavky na virtuální zařízení](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Pokud se během počáteční konfigurace pomocí místního webového uživatelského rozhraní zobrazí jiná chyba, podívejte se na následující pracovní postupy:

* Spusťte diagnostické testy [k řešení potíží s nastavením webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generovat balíček protokolu a zobrazit soubory protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další kroky
* [Nastavení virtuálního pole StorSimple jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení virtuálního pole StorSimple jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
