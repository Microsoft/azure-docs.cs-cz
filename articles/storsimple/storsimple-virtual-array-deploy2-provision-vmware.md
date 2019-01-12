---
title: StorSimple Virtual Array ve službě VMware zřizovat | Dokumentace Microsoftu
description: Této druhé části kurzu v sérii virtuálních polí StorSimple nasazení zahrnuje zřizování virtuálního zařízení v prostředí VMware.
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
ms.date: 01/11/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c9fe597957057dc61da5c2b1cf6f9216711764a
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54247839"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Nasazení virtuálních polí StorSimple – zřizování v prostředí VMware
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Přehled
Tento kurz popisuje, jak zřídit a připojte se k StorSimple Virtual Array v hostitelském systému, s VMware ESXi 5.0, 5.5, 6.0 nebo 6.5. Tento článek se týká k nasazení virtuálních polí StorSimple v cloudu Microsoft Azure Government a webu Azure portal.

Ke zřízení virtuálního zařízení a připojení k němu potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Zřizování požadavky
Požadavky na zřízení virtuálního zařízení v systému hostitele VMware ESXi 5.0, 5.5, 6.0 nebo 6.5, jsou následující.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Jste dokončili všechny kroky v [připravit na portálu pro StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md).
* Na webu Azure Portal jste si stáhli image virtuálního zařízení pro VMware. Další informace najdete v tématu **krok 3: Stáhnout image virtuálního zařízení** z [připravit na portálu pro StorSimple Virtual Array průvodce](storsimple-virtual-array-deploy1-portal-prep.md).

### <a name="for-the-storsimple-virtual-device"></a>Pro virtuální zařízení StorSimple
Před nasazením virtuálního zařízení se ujistěte, že:

* Máte přístup k hostitelskému systému s technologií Hyper-V (2008 R2 nebo novější), které jde použít zřizování zařízení.
* Hostitelský systém může pro zřízení virtuálního zařízení vyhradit následující prostředky:

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud chcete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Je třeba 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní
  * 500 GB virtuální disk pro data systému.

### <a name="for-the-network-in-datacenter"></a>Síť v datacentru
Než začnete, ujistěte se, že:

* Zkontrolujete požadavky na síť pro nasazení virtuálního zařízení StorSimple a nakonfigurovaný podle požadavků sítě datového centra. 

## <a name="step-by-step-provisioning"></a>Krok za krokem zřizování
Zřízení a připojit se k virtuálnímu zařízení, budete muset provést následující kroky:

1. Zajistěte, aby hostitelského systému dostatek prostředků ke splnění těchto požadavků minimální virtuálního zařízení.
2. Můžete zřídíte virtuální zařízení ve vaší hypervisoru.
3. Spuštění virtuálního zařízení a získejte IP adresu.

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Ujistěte se, že hostitelský systém splňuje požadavky na minimální virtuální zařízení
Pokud chcete vytvořit virtuální zařízení, budete potřebovat:

* Přístup k hostitelskému systému s VMware ESXi Server 5.0, 5.5, 6.0 nebo 6.5.
* Klienta VMware vSphere ve vašem systému pro správu hostitele ESXi.

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud chcete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 miliony souborů. Je třeba 16 GB paměti RAM pro podporu 2 – 4 miliony souborů.
  * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. Minimální šířky pásma Internetu by měl být 5 MB/s na povolit pro optimální práci zařízení.
  * 500 GB virtuální disk pro data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: Zřízení virtuálního zařízení v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte virtuální zařízení.

1. Zkopírujte do svého systému image virtuálního zařízení. Jste si stáhli tuto virtuální image na webu Azure portal.

   1. Zkontrolujte, jestli jste si stáhli nejnovější soubor bitové kopie. Pokud jste image stáhli dříve, stáhněte si ho znovu a ujistěte se, že máte nejnovější image. Nejnovější image obsahuje dva soubory (místo jednoho).
   2. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.

2. Pomocí klienta vSphere se přihlaste k serveru ESXi. K vytvoření virtuálního počítače potřebujete mít oprávnění správce.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. V klientovi vSphere, v části inventáře v levém podokně vyberte ESXi Server.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Nahrajte VMDK na server ESXi. Přejděte **konfigurace** kartu v pravém podokně. V části **hardwaru**vyberte **úložiště**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. V pravém podokně v části **Úložiště dat** vyberte úložiště dat, do kterého chcete VMDK nahrát. Úložiště musí mít dostatek volného místa pro operační systém a datové disky.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klikněte pravým tlačítkem a vyberte **Procházet úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zobrazí se okno **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na panelu nástrojů klikněte na tlačítko ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) ikonu a vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Název této složky použijete později při vytváření virtuálního počítače (doporučený osvědčený postup). Klikněte na **OK**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nová složka se zobrazí v levém podokně okna **Prohlížeč úložiště dat**.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klikněte na ikonu nahrání ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png) a vyberte **nahrát soubor**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Přejděte k souborům VMDK, které jste si stáhli. Zobrazí se dva soubory. Vyberte soubor k nahrání.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klikněte na **Otevřít**. Spustí se nahrávání souboru VMDK do zadaného úložiště dat. Nahrání souboru může několik minut trvat.
13. Po dokončení nahrávání se soubor zobrazí v úložišti dat ve složce, kterou jste vytvořili.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teď do stejného úložiště dat nahrajte druhý soubor VMDK.
14. Vraťte se do okna klienta vSphere. S ESXi server vybraný, klikněte pravým tlačítkem a vyberte **nový virtuální počítač**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. A **vytvořit nový virtuální počítač** otevře se okno. Na **konfigurace** stránky, vyberte **vlastní** možnost. Klikněte na **Další**.
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na **název a umístění** stránky, zadejte název vašeho virtuálního počítače. Tento název by měl odpovídat názvu složky (doporučený osvědčený postup), které jste zadali dříve v kroku 8.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na **úložiště** vyberte úložiště dat, kterou chcete použít ke zřízení virtuálního počítače.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na **verze virtuálního počítače** stránce **verze virtuálního počítače: 8**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na **hostovaný operační systém** stránky, vyberte **hostovaný operační systém** jako **Windows**. Pro **verze**, z rozevíracího seznamu vyberte **Microsoft Windows Server 2012 (64-bit)**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na **procesory** stránky, upravte **virtuální soketů** a **počet jader na virtuální soketu** tak, aby **celkový počet jader** je 4 (nebo více). Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na **paměti** stránky, zadejte 8 GB (nebo víc) paměti RAM. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na **sítě** stránky, zadejte počet síťových rozhraní. Požadavek na minimální je jedno síťové rozhraní.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na **řadič SCSI** stránce, přijměte výchozí nastavení **řadič Adaptér LSI Logic SAS**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na **vyberte Disk** zvolte **použít stávající virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na **vybrat stávající Disk** stránce v části **cesta k souboru disku**, klikněte na tlačítko **Procházet**. Tím se otevře **procházet úložiště** dialogového okna. Přejděte do umístění, kam jste odeslali, VMDK. Nyní uvidíte pouze jeden soubor v úložišti dat, jako dva soubory, které jste původně nahráli byly sloučeny. Vyberte soubor a klikněte na tlačítko **OK**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na **rozšířené možnosti** stránce, přijměte výchozí nastavení a klikněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stránce **Připraveno k dokončení** zkontrolujte všechna nastavení přidružená k novému virtuálnímu počítači. Zkontrolujte **upravit nastavení virtuálního počítače před dokončením**. Klikněte na **Pokračovat**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na **vlastnosti virtuální počítače** stránku, **hardwaru** kartu, vyhledejte hardwaru zařízení. Vyberte **nový pevný Disk**. Klikněte na tlačítko **Add** (Přidat).

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobrazí **přidat Hardware** okna. Na **typ zařízení** stránce v části **vyberte typ zařízení, které chcete přidat**vyberte **pevného disku**a klikněte na tlačítko **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na **vyberte Disk** zvolte **vytvořit nový virtuální disk**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na **vytvoření disku** stránce, změnit **velikost disku** 500 GB (nebo více). Zatímco je požadavek na minimální 500 GB, můžete zřídit vždy větší disk. Všimněte si, že nelze zvětšení nebo zmenšení na disk po zřízení. Další informace o velikosti disku, který se zřizování, najdete v tématu v části Určení velikosti [osvědčené postupy pro dokument](storsimple-ova-best-practices.md). V části **zřizování disku**vyberte **zajišťováním**. Klikněte na **Další**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na **rozšířené možnosti** stránce, přijměte výchozí nastavení.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na **připravení Dokončit** stránce, projděte si možnosti disku. Klikněte na **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Vraťte se na stránku vlastností virtuálního počítače. K virtuálnímu počítači se přidá nový pevný disk. Klikněte na **Dokončit**.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. S virtuálním počítačem v pravém podokně vybrán, přejděte **Souhrn** kartu. Zkontrolujte nastavení pro váš virtuální počítač.

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Váš virtuální počítač je teď zřízený. Dalším krokem je počítač zapnout a získat jeho IP adresu.

> [!NOTE]
> Doporučujeme vám, neinstalujte nástroje VMware na virtuální pole, (podle doporučení výše). Instalace nástrojů VMware bude mít za následek nepodporovanou konfiguraci.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: Spuštění virtuálního zařízení a získání IP adresy
Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení. V vSphere nástroje Configuration Manager, v levém podokně vyberte zařízení a kliknutím pravým tlačítkem v kontextové nabídce. Vyberte **Napájení** a pak vyberte **Zapnout**. Tím by se měl virtuální počítač zapnout. V dolní části můžete zobrazit stav **poslední úkoly** podokně klientovi vSphere.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Nastavení úlohy bude trvat několik minut. Jakmile je na zařízení spuštěný, přejděte do **konzoly** kartu. Odešlete Ctrl + Alt + Delete pro přihlášení k zařízení. Alternativně můžete bodu kurzoru v okně konzoly a stisknutím klávesy Ctrl + Alt + Insert. Výchozí uživatel *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí obsahovat 3 ze 4 z těchto požadavků: velká písmena, malá písmena, číselné a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Budete informováni, že ke změně hesla.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po heslo úspěšně změněno, může restartování virtuálního zařízení. Čekání restart a dokončení. Konzole Windows Powershellu pro zařízení, může se zobrazit indikátor průběhu.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud je spuštěn vaše zařízení v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   V dalším kroku Nakonfigurujte síť.
7. Použití `Get-HcsIpAddress` seznam síťových rozhraní na virtuálním zařízení povolené rozhraní příkazu. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Příklad najdete níže:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Tuto IP adresu použijete k připojení do webového uživatelského rozhraní vašeho virtuálního zařízení a dokončit místní nastavování a registraci.

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. (Volitelné) Tento krok proveďte jenom v případě, že nasazujete své zařízení do cloudu státní správy. Režim USA federální informace o zpracování Standard (FIPS) vám umožní nyní na vašem zařízení. Standard FIPS 140 definuje schválené pro použití podle federální vlády počítačové systémy pro ochranu citlivých dat kryptografické algoritmy.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS tak, aby se projevily kryptografických ověření až po restartování zařízení.

       > [!NOTE]
       > Můžete povolit nebo zakázat režim FIPS ve vašem zařízení. Alternující zařízení mezi režimu FIPS a podle standardu FIPS se nepodporuje.
       >
       >

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba (viz níže). Budete muset upravit konfiguraci zařízení tak, aby mělo dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Přečtěte si požadavky na minimální konfiguraci v [krok 1: Ujistěte se, že hostitel systém splňuje požadavky na minimální virtuální zařízení](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Pokud budete mít jiná chyba během počáteční konfigurace prostřednictvím místního webového uživatelského rozhraní, najdete v následujících pracovních postupů:

* Spustit diagnostické testy, abyste [řešení potíží s instalací webové uživatelské rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Vygenerujte balíček pro protokolu a zobrazit soubory protokolů](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další postup
* [Nastavit službu StorSimple Virtual Array jako souborový server](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavit službu StorSimple Virtual Array jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
