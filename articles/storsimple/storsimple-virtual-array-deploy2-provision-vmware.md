---
title: Zřízení virtuálního pole StorSimple ve VMware
description: Tento druhý kurz v řadě nasazení virtuálních polí StorSimple zahrnuje zřizování virtuálního zařízení ve VMware.
author: alkohli
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9810a34021aa039354aad24f84aff373229c0190
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87021473"
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>Nasazení virtuálního pole StorSimple – zřizování ve VMware
![Diagram znázorňující kroky potřebné k nasazení virtuálního pole Druhá část druhého kroku je označena jako zřizování na VMware a zvýrazní se.](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

V tomto kurzu se dozvíte, jak zřídit a připojit se k virtuálnímu poli StorSimple na hostitelském systému, na kterém běží VMware ESXi 5,0, 5,5, 6,0 nebo 6,5. Tento článek se týká nasazení virtuálních polí StorSimple v Azure Portal a v cloudu Microsoft Azure Government.

Ke zřízení virtuálního zařízení a připojení k němu potřebujete oprávnění správce. Dokončení zřizování a počátečního nastavení může trvat přibližně 10 minut.

## <a name="provisioning-prerequisites"></a>Požadavky zřizování
Níže jsou uvedené požadavky na zřízení virtuálního zařízení v hostitelském systému, na kterém běží VMware ESXi 5,0, 5,5, 6,0 nebo 6,5.

### <a name="for-the-storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Než začnete, ujistěte se, že:

* Dokončili jste všechny kroky v části [Příprava portálu pro virtuální pole StorSimple](storsimple-virtual-array-deploy1-portal-prep.md).
* Stáhli jste image virtuálního zařízení pro VMware z Azure Portal. Další informace najdete v části **Krok 3: stažení bitové kopie virtuálního zařízení** [Příprava portálu pro StorSimple Virtual Array Guide](storsimple-virtual-array-deploy1-portal-prep.md).

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

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>Krok 1: Zajistěte, aby hostitelský systém splňoval minimální požadavky na virtuální zařízení
K vytvoření virtuálního zařízení budete potřebovat:

* Přístup k hostitelskému systému se systémem VMware ESXi Server 5,0, 5,5, 6,0 nebo 6,5.
* Klienta VMware vSphere ve vašem systému pro správu hostitele ESXi.

  * Minimálně 4 jádra
  * Minimálně 8 GB paměti RAM Pokud plánujete nakonfigurovat virtuální pole jako souborový server, 8 GB podporuje méně než 2 000 000 souborů. Pro podporu 2-4 milionů souborů potřebujete 16 GB paměti RAM.
  * Jedno síťové rozhraní připojené k síti, která podporuje směrování provozu do internetu. Minimální šířka pásma internetu by měla být 5 MB/s, aby bylo možné optimálně pracovat na zařízení.
  * Virtuální disk 500 GB pro data.

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>Krok 2: zřízení virtuálního zařízení v hypervisoru
Pomocí následujících kroků ve svém hypervisoru zřiďte virtuální zařízení.

1. Zkopírujte do svého systému image virtuálního zařízení. Tuto virtuální bitovou kopii jste stáhli pomocí Azure Portal.

   1. Ujistěte se, že jste stáhli nejnovější soubor obrázku. Pokud jste dříve stáhli bitovou kopii, stáhněte ji znovu, abyste měli jistotu, že máte nejnovější obrázek. Poslední obrázek má dva soubory (místo jednoho).
   2. Poznamenejte si umístění, do kterého jste image zkopírovali, protože tuto image použijete v pozdější části tohoto postupu.

2. Pomocí klienta vSphere se přihlaste k serveru ESXi. K vytvoření virtuálního počítače potřebujete mít oprávnění správce.

   ![Snímek obrazovky s přihlašovací stránkou klienta vSphere Pole IP adresa, uživatelské jméno a heslo obsahují hodnoty a zvýrazní se tlačítko pro přihlášení.](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. V klientovi vSphere v části inventář v levém podokně vyberte server ESXi.

   ![Snímek obrazovky s hlavní stránkou klienta vSphere V oddílu inventarizace je server ESXi zvýrazněný.](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. Nahrajte VMDK na server ESXi. V pravém podokně přejděte na kartu **Konfigurace** . V části **hardware** vyberte **úložiště**.

   ![Snímek obrazovky zobrazující kartu Konfigurace klienta vSphere V části hardware je zvýrazněné úložiště.](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. V pravém podokně v části **Úložiště dat** vyberte úložiště dat, do kterého chcete VMDK nahrát. Úložiště dat musí mít dostatek volného místa pro operační systém a datové disky.

   ![Snímek obrazovky s stránkou úložiště klienta vSphere. Karta úložiště dat je otevřená a obsahuje seznam úložišť dat. Je vybráno jedno úložiště dat.](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. Klikněte pravým tlačítkem a vyberte **Procházet úložiště dat**.

   ![Snímek obrazovky znázorňující místní nabídku úložiště dat Je vybrána položka procházet úložiště dat.](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. Zobrazí se okno **Prohlížeč úložiště dat**.

   ![Snímek obrazovky prohlížeče dat. Složky v úložišti dat jsou viditelné.](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. Na panelu nástrojů klikněte na :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png"::: ikonu a vytvořte novou složku. Zadejte název složky a poznamenejte si ho. Název této složky použijete později při vytváření virtuálního počítače (doporučený osvědčený postup). Klikněte na **OK**.

   ![Snímek obrazovky prohlížeče úložiště dat s zvýrazněnou ikonou nové složky Dialogové okno má vyplněný název složky a zvýrazněné tlačítko OK.](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. Nová složka se zobrazí v levém podokně okna **Prohlížeč úložiště dat**.

   ![Snímek obrazovky s prohlížečem úložiště dat s novou složkou, která je viditelná v hierarchii složek](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. Klikněte na ikonu nahrát :::image type="icon" source="./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png"::: a vyberte **nahrát soubor**.

    ![Snímek obrazovky s místní nabídkou ikony pro nahrání Je vybrána položka odeslat soubor.](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. Přejděte k souborům VMDK, které jste si stáhli. Zobrazí se dva soubory. Vyberte soubor k nahrání.

    ![Snímek obrazovky s dialogovým oknem, ve kterém se zobrazují složky a dva soubory V M D KB Jeden ze souborů je zvýrazněný.](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. Klikněte na **Otevřít**. Spustí se nahrávání souboru VMDK do zadaného úložiště dat. Nahrání souboru může několik minut trvat.
13. Po dokončení nahrávání se soubor zobrazí v úložišti dat ve složce, kterou jste vytvořili.

    ![Snímek obrazovky prohlížeče dat. Nová složka je zvýrazněna v hierarchii složek a nahraný soubor je v této složce viditelný.](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    Teď do stejného úložiště dat nahrajte druhý soubor VMDK.
14. Vraťte se do okna klienta vSphere. Když je vybraný server ESXi, klikněte pravým tlačítkem myši a vyberte **Nový virtuální počítač**.

    ![Snímek obrazovky místní nabídky serveru ESXi. Je vybrána položka nový virtuální počítač.](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. Zobrazí se okno **vytvořit nový virtuální počítač** . Na stránce **Konfigurace** vyberte možnost **vlastní** . Klikněte na **Next** (Další).
    ![Snímek obrazovky se stránkou konfigurace v okně vytvořit nový virtuální počítač. Je vybrána možnost vlastní a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. Na stránce **název a umístění** zadejte název vašeho virtuálního počítače. Tento název by měl odpovídat názvu složky (doporučený osvědčený postup), který jste zadali dříve v kroku 8.

    ![Snímek obrazovky se stránkou název a umístění v okně vytvořit nový virtuální počítač. Pole název se vyplní a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. Na stránce **úložiště** vyberte úložiště dat, které chcete použít ke zřízení virtuálního počítače.

    ![Snímek obrazovky se stránkou úložiště v okně vytvořit nový virtuální počítač. Je vybráno úložiště dat a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. Na stránce **verze virtuálního počítače** vyberte **verze virtuálního počítače: 8**.

    ![Snímek obrazovky se stránkou verze virtuálního počítače Je vybraná možnost virtuální počítač verze 8 a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. Na stránce **hostovaný operační systém** vyberte **hostovaný operační systém** jako **Windows**. V rozevíracím seznamu **verze** vyberte možnost **Microsoft Windows Server 2012 (64-bit)**.

    ![Snímek obrazovky se stránkou hostovaného operačního systému s vybraným systémem Windows, verze nastavená na Microsoft Windows Server 2012 (64-bit) a další zvýrazněný](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. Na stránce **procesory** upravte **počet virtuálních soketů** a **počet jader na virtuální soket** , aby **Celkový počet jader** byl 4 (nebo více). Klikněte na **Next** (Další).

    ![Snímek obrazovky se stránkou procesorů ukazující jeden virtuální soket, čtyři jádra na virtuální soket a čtyři celkem jader. Tlačítko Další je zvýrazněno.](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. Na stránce **paměť** zadejte 8 GB (nebo více) paměti RAM. Klikněte na **Next** (Další).

    ![Snímek stránky paměti Pro velikost paměti je vyplněna hodnota 8 GB.](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. Na stránce **síť** zadejte počet síťových rozhraní. Minimálním požadavkem je jedno síťové rozhraní.

    ![Snímek obrazovky se stránkou síť. Počet síťových rozhraní je nastaven na jednu a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. Na stránce **řadič SCSI** přijměte výchozí **kontroler SAS pro adaptér LSI Logic**.

    ![Snímek obrazovky se stránkou řadiče SCSI Vybere se možnost Logica S S a S a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. Na stránce **vybrat disk** zvolte možnost **použít existující virtuální disk**. Klikněte na **Next** (Další).

    ![Snímek obrazovky se stránkou vyberte disk s vybranou možností použít existující virtuální disk a zvýrazněným tlačítkem Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. Na stránce **Vybrat existující disk** klikněte v části **cesta k souboru na disku** na **Procházet**. Tím se otevře dialogové okno **Procházet úložiště dat** . Přejděte do umístění, kam jste nahráli VMDK. V úložišti dat teď uvidíte jenom jeden soubor, protože se sloučily dva soubory, které jste původně nahráli. Vyberte soubor a klikněte na **OK**. Klikněte na **Next** (Další).

    ![Snímek obrazovky se stránkou vybrat existující disk Tlačítko Procházet je zvýrazněno a dialogové okno obsahuje jeden soubor a zvýrazněné tlačítko OK.](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. Na stránce **Upřesnit možnosti** přijměte výchozí nastavení a klikněte na **Další**.

    ![Snímek obrazovky se stránkou Upřesnit možnosti. Tlačítko Další je zvýrazněno.](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. Na stránce **Připraveno k dokončení** zkontrolujte všechna nastavení přidružená k novému virtuálnímu počítači. **Před dokončením upravte nastavení virtuálního počítače**. Klikněte na **Pokračovat**.

    ![Snímek obrazovky stránky připravené k dokončení se zvýrazněným tlačítkem pokračovat Je zaškrtnuta možnost upravit nastavení virtuálního počítače před dokončením.](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. Na stránce **vlastnosti Virtual Machines** na kartě **hardware** vyhledejte hardware zařízení. Vyberte **nový pevný disk**. Klikněte na **Přidat**.

    ![Snímek obrazovky karty hardware na stránce vlastností Virtual Machines V seznamu hardwaru je vybrán nový pevný disk. Tlačítko Přidat je zvýrazněno.](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. Zobrazí se okno **Přidat hardware** . Na stránce **typ zařízení** v části **Zvolte typ zařízení, které chcete přidat**, vyberte možnost **pevný disk** a klikněte na tlačítko **Další**.

    ![Snímek obrazovky se stránkou typ zařízení v okně Přidat hardware. Vybere se zařízení na pevném disku a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. Na stránce **vybrat disk** zvolte možnost **vytvořit nový virtuální disk**. Klikněte na **Next** (Další).

    ![Snímek obrazovky se stránkou vybrat disk Je vybraná možnost vytvořit nový virtuální disk a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. Na stránce **vytvořit disk** změňte **velikost disku** na 500 GB (nebo více). I když je minimální požadavek 500 GB, můžete vždy zřídit větší disk. Všimněte si, že po zřízení nelze disk rozbalit ani zmenšit. Další informace o velikosti disku, který se má zřídit, najdete v části Změna velikosti v [dokumentu osvědčené postupy](storsimple-ova-best-practices.md). V části **zřízení disku** vyberte **dynamické zajišťování**. Klikněte na **Next** (Další).

    ![Snímek obrazovky se stránkou vytvořit disk Velikost disku je nastavená na 500 GB, je vybraná možnost dynamického zřizování a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. Na stránce **Upřesnit možnosti** přijměte výchozí nastavení.

    ![Snímek obrazovky se stránkou Upřesnit možnosti. Uzel virtuálního zařízení je nastaven na SCSI (0:0) a zvýrazní se tlačítko Další.](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. Na stránce **připraveno k dokončení** Zkontrolujte možnosti disku. Klikněte na **Finish** (Dokončit).

    ![Snímek obrazovky stránky připravené k dokončení Zobrazí se souhrn možností disku a tlačítko Dokončit se zvýrazní.](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. Vraťte se na stránku vlastností virtuálního počítače. Do virtuálního počítače se přidá nový pevný disk. Klikněte na **Finish** (Dokončit).

    ![Snímek obrazovky se stránkou vlastností virtuálního počítače Seznam hardwaru obsahuje nový pevný disk a zvýrazní se tlačítko Dokončit.](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. V pravém podokně vyberte virtuální počítač a přejděte na kartu **Souhrn** . Zkontrolujte nastavení virtuálního počítače.

    ![Snímek obrazovky s kartou vSphere Client Summary Nový virtuální počítač se zvýrazní a zobrazí se jeho prostředky a obecné vlastnosti.](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

Váš virtuální počítač je teď zřízený. Dalším krokem je počítač zapnout a získat jeho IP adresu.

> [!NOTE]
> Doporučujeme, abyste nástroje VMware neinstalovali ve vašem virtuálním poli (jak jsme zřídili výše). Instalace nástrojů VMware bude mít za následek nepodporovanou konfiguraci.

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>Krok 3: spuštění virtuálního zařízení a získání IP adresy
Pomocí následujících kroků spusťte své virtuální zařízení a připojte se k němu.

#### <a name="to-start-the-virtual-device"></a>Spuštění virtuálního zařízení
1. Spusťte virtuální zařízení. V vSphere Configuration Manager v levém podokně vyberte zařízení a kliknutím pravým tlačítkem otevřete místní nabídku. Vyberte **Napájení** a pak vyberte **Zapnout**. Tím by se měl virtuální počítač zapnout. Stav můžete zobrazit v podokně **nedávné úlohy** v klientovi vSphere.

   ![Snímek místní nabídky zařízení Je vybrána položka napájení. Zobrazí se sousední nabídka s vybranou položkou zapnout.](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. Dokončení úloh instalace bude trvat několik minut. Jakmile je zařízení spuštěné, přejděte na kartu **Konzola** . Pokud se chcete přihlásit k zařízení, klikněte na Odeslat CTRL + ALT + DELETE. Alternativně můžete ukazatel myši nasměrovat na okno konzoly a stisknout kombinaci kláves CTRL + ALT + INSERT. Výchozí uživatel je *StorSimpleAdmin* a výchozí heslo je *Heslo1*.

   ![Snímek obrazovky s kartou konzoly klienta vSphere Pole pro heslo je prázdné.](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. Z bezpečnostních důvodů platnost hesla správce zařízení vyprší po prvním přihlášení. Zobrazí se výzva ke změně hesla.

   ![Snímek obrazovky s kartou konzoly klienta vSphere Text na stránce uvádí, že je nutné změnit heslo.](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. Zadejte heslo, které obsahuje minimálně 8 znaků. Heslo musí obsahovat 3 z 4 z těchto požadavků: velká písmena, malá písmena, číslice a speciální znaky. Znovu zadejte heslo, abyste ho potvrdili. Zobrazí se oznámení o změně hesla.

   ![Snímek obrazovky s kartou konzoly klienta vSphere Text na stránce uvádí, že bylo změněno heslo.](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. Po úspěšném dokončení změny hesla se virtuální zařízení může restartovat. Počkejte, než se restart dokončí. Konzola prostředí Windows PowerShell v zařízení se může zobrazit spolu s indikátorem průběhu.

   ![Snímek obrazovky znázorňující okno konzoly s indikátorem průběhu Text v okně uvádí, že počáteční nastavení pokračuje, a požádá uživatele, aby čekal.](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. Kroky 6 až 8 proveďte pouze v případě, že zařízení spouštíte v jiném prostředí než DHCP. Pokud jste v prostředí DHCP, přeskočte tyto kroky a přejděte ke kroku 9. Pokud jste zařízení spouštěli v prostředí bez služby DHCP, zobrazí se následující obrazovka.

   ![Snímek obrazovky znázorňující okno konzoly s textem, který popisuje zařízení Příkazový řádek přečte "Controller" a zobrazí se jako připravený pro vstup.](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   Dále nakonfigurujte síť.
7. Pomocí `Get-HcsIpAddress` příkazu uveďte síťová rozhraní povolená na virtuálním zařízení. Pokud má vaše zařízené povolené jediné síťové rozhraní, výchozí název přiřazený tomuto rozhraní je `Ethernet`.

   ![Snímek obrazovky znázorňující okno konzoly s výstupem příkazu Get-HcsIpAddress. "Síť Ethernet" je uvedena jako název zařízení.](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. Pomocí rutiny `Set-HcsIpAddress` nakonfigurujte síť. Příklad najdete níže:

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![Snímek obrazovky s oknem konzoly s výstupem příkazu Get-Help Set-HcsIpAddress a správného použití příkazu Set-HcsIpAddress](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. Po dokončení počátečního nastavení a spuštění zařízení se zobrazí text banneru zařízení. Poznamenejte si IP adresu a adresu URL pro správu zařízení, které se zobrazí v textu banneru. Pomocí této IP adresy se připojíte k webovému uživatelskému rozhraní virtuálního zařízení a dokončete místní nastavení a registraci.

   ![Snímek obrazovky znázorňující okno konzoly s textem banneru zařízení Tento text obsahuje IP adresu zařízení a adresu URL.](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. Volitelné Tento krok proveďte jenom v případě, že nasazujete zařízení v cloudu pro státní správu. Teď na svém zařízení povolíte USA režim FIPS (Federal Information Processing Standard). Standard FIPS 140 definuje kryptografické algoritmy schválené pro použití v počítačových systémech USA federální správy pro ochranu citlivých dat.

    1. Pokud chcete povolit režim FIPS, spusťte následující rutinu:

        `Enable-HcsFIPSMode`
    2. Po povolení režimu FIPS restartujte zařízení, aby se kryptografické ověřování projevilo.

       > [!NOTE]
       > V zařízení můžete zapnout nebo vypnout režim FIPS. Střídání zařízení mezi režimy FIPS a non-FIPS není podporováno.
       >
       >

Pokud vaše zařízení nesplňuje minimální požadavky na konfiguraci, zobrazí se v textu banneru chyba (viz níže). Budete muset upravit konfiguraci zařízení tak, aby mělo dostatečné prostředky ke splnění minimálních požadavků. Pak můžete zařízení restartovat a připojit se k němu. Projděte si minimální požadavky na konfiguraci v [kroku 1: Zajištění, aby hostitelský systém splňoval minimální požadavky na virtuální zařízení](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements).

![Snímek obrazovky znázorňující okno konzoly s textem banneru zařízení Tento text obsahuje chybovou zprávu, která poskytuje adresu URL pro řešení tohoto problému.](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

Pokud se při počáteční konfiguraci s použitím místního webového uživatelského rozhraní zobrazí nějaká chyba, přečtěte si následující pracovní postupy:

* Spusťte diagnostické testy pro [řešení potíží s instalací webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generování souboru protokolu a zobrazení souborů protokolu](storsimple-ova-web-ui-admin.md#generate-a-log-package).

## <a name="next-steps"></a>Další kroky
* [Nastavení virtuálního pole StorSimple jako souborového serveru](storsimple-virtual-array-deploy3-fs-setup.md)
* [Nastavení StorSimple virtuálního pole jako serveru iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md)
