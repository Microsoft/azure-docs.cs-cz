---
title: Synchronizace více zařízení Azure s Kinect DK
description: V tomto článku se seznámíte s výhodami synchronizace s více zařízeními a nastavením zařízení, která se mají synchronizovat.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: Azure, Kinect, specifikace, hardware, DK, možnosti, Hloubka, barva, RGB, IMU, pole, Hloubka, více, synchronizace
ms.openlocfilehash: 7c79101de5e5455ae2ff9fd8b5d8369a3832631c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361156"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Synchronizace více zařízení Azure s Kinect DK

Každé zařízení Azure Kinect DK zahrnuje 3,5 až mm synchronizačních portů (**synchronizovaných** a **synchronizovaných**), které můžete použít k propojení více zařízení dohromady. Po připojení zařízení může váš software koordinovat časování triggeru mezi nimi. 

Tento článek popisuje, jak připojit a synchronizovat zařízení.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Výhody používání více zařízení Azure Kinect DK

Existuje mnoho důvodů, proč použít více zařízení Azure Kinect DK, včetně následujících:

- Vyplňte Occlusions. I když transformace dat v Azure Kinect DK vytvoří jeden obrázek, dva kamery (hloubka a RGB) jsou ve skutečnosti malé vzdálenosti. Posun způsobuje Occlusions. K překrytí dojde, když objekt popředí zablokuje zobrazení části objektu na pozadí pro jednu ze dvou kamer na zařízení. Ve výsledné barvě obrázku se zdá, že objekt popředí přetypování stín na objekt na pozadí.  
   Například v následujícím diagramu se u kamery na levé straně zobrazuje šedý pixel "P2". Ale bílý objekt na popředí blokuje paprskový paprsek kamery na pravé straně. Kamera na pravé straně nemá žádná data pro P2.  
   ![Diagram znázorňuje dva kamery směrované na stejný bod s jedním z nich blokovaných.](./media/occlusion.png)  
   Další synchronizovaná zařízení můžou poskytovat zastíněna data.
- Naskenujte objekty ve třech rozměrech.
- Zvyšte efektivní kmitočet snímků na hodnotu, která je větší než 30 snímků za sekundu (FPS).
- Zachyťte více 4K barevných obrázků stejné scény, vše zarovnaných během 100 mikrosekund &mu; od středu vystavení.
- Zvyšte pokrytí kamery v prostoru.

## <a name="plan-your-multi-device-configuration"></a>Plánování konfigurace pro více zařízení

Než začnete, ujistěte se, že jste zkontrolovali [specifikace hardwaru Azure Kinect DK](hardware-specification.md) a [hloubkovou kameru Azure Kinect DK](depth-camera.md).

### <a name="select-a-device-configuration"></a>Vyberte konfiguraci zařízení.

Pro konfiguraci zařízení můžete použít některý z následujících přístupů:

- **Konfigurace s typovým řetězcem**. Synchronizujte jedno hlavní zařízení a až osm podřízených zařízení.  
   ![Diagram, který ukazuje, jak připojit zařízení s Kinect DK v zauzavřené konfiguraci.](./media/multicam-sync-daisychain.png)
- **Konfigurace hvězdičky**. Proveďte synchronizaci jednoho hlavního zařízení a až dvou podřízených zařízení.  
   ![Diagram, který ukazuje, jak nastavit několik zařízení s Azure DK v konfiguraci hvězdičky.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Použití triggeru externí synchronizace

V obou konfiguracích poskytuje hlavní zařízení aktivační signál pro podřízená zařízení. Můžete ale použít vlastní externí zdroj pro aktivační událost synchronizace. Tuto možnost můžete například použít k synchronizaci zachycení imagí s jinými zařízeními. V konfiguraci uzavřeného řetězu nebo v konfiguraci hvězdičky se externí zdroj triggeru připojí k hlavnímu zařízení.

Váš externí zdroj triggeru musí fungovat stejným způsobem jako hlavní zařízení. Musí doručovat signál synchronizace s následujícími charakteristikami:

- Aktivní vysoká
- Šířka Pulse: větší než 8 &mu; s
- SNÍMAČ 5V SÍŤOVÉHO TTL/CMOS
- Maximální kapacita jízdy: ne méně než 8 milliamps (mA)
- Podpora četnosti: přesně 30 snímků, 15 FPS a 5 FPS (frekvence hlavního signálu VSYNC Color Camera)

Zdroj aktivační události musí doručovat signál do hlavního zařízení **synchronizace v** portu pomocí zvukového kabelu 3,5-mm. Můžete použít kabel stereo nebo mono. Ve službě Azure Kinect DK jsou všechny rukávy a okruhy zvukového kabelu zapojeny společně a jejich důvody. Jak je znázorněno na následujícím diagramu, zařízení obdrží synchronní signál pouze z tipu konektoru.

![Konfigurace kabelů pro vnější signál triggeru](./media/resources/camera-trigger-signal.jpg)

Další informace o tom, jak pracovat s externím vybavením, najdete v tématu [použití služby Azure Kinect Recorder s externími synchronizovanými zařízeními](record-external-synchronized-units.md) .

### <a name="plan-your-camera-settings-and-software-configuration"></a>Plánování nastavení kamery a konfigurace softwaru

Informace o tom, jak nastavit software pro řízení kamer a používání dat imagí, najdete v [sadě Azure Kinect snímače SDK](about-sensor-sdk.md).

Tato část popisuje několik faktorů, které ovlivňují synchronizovaná zařízení (ale ne jediná zařízení). Váš software by měl brát v úvahu tyto faktory.

#### <a name="exposure-considerations"></a>Požadavky na expozici
Pokud chcete řídit přesné časování jednotlivých zařízení, doporučujeme použít nastavení ruční expozice. V nastavení automatické expozice může každá barevná kamera dynamicky měnit skutečnou expozici. Vzhledem k tomu, že expozice ovlivňuje časování, takové změny rychle doručí kamery ze synchronizace.

V cyklu zachycení bitové kopie Vyhněte opakovanému nastavení stejného nastavení expozice. Volejte rozhraní API jenom jednou, když je potřeba.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Zamezení rušení mezi více hloubkových kamerami

Když je u více hloubkových kamer překrývajících se pole zobrazení, musí každý fotoaparát naobrazovat svoji přidruženou laserovou. Aby nedošlo k tomu, aby se Lasers navzájem nenarušila, zachycení kamery by měla být od sebe od sebe posunuta o 160 μs nebo více.

Pro každou podobu zachycení kamery se laser zapne devět časů a v každém okamžiku je aktivní jenom pro 125 &mu; s. Laser je pak nečinný buď v 14505 &mu; s nebo 23905 &mu; s, v závislosti na režimu operace. Toto chování znamená, že výchozí bod pro výpočet posunutí je 125 &mu; s.

Rozdíly mezi hodinami kamery a firmwarem zařízení navíc zvyšují minimální posun na 160 &mu; s. Pokud chcete vypočítat přesnější posun konfigurace, poznamenejte si režim hloubky, který používáte, a podívejte se na [tabulku nezpracovaného časování senzoru hloubky](hardware-specification.md#depth-sensor-raw-timing). Pomocí dat z této tabulky můžete vypočítat minimální posun (dobu expozice každého fotoaparátu) pomocí následující rovnice:

> *Doba expozice* = (*IR Pulses* &times; *Tloušťka impulsů*IR) + (*Doba nečinnosti nečinných dob* &times; *Idle Time*)

Když použijete posun od 160 &mu; s, můžete nakonfigurovat až devět dalších hloubkových fotoaparátů, aby se každý laser zapíná, zatímco ostatní Lasers jsou nečinné.

Ve svém softwaru použijte ```depth_delay_off_color_usec``` nebo ```subordinate_delay_off_master_usec``` k zajistěte, aby se každé infračervené laserové prostředí &mu; vyvolalo ve vlastním okně 160 s, nebo má jiné pole zobrazení.

## <a name="prepare-your-devices-and-other-hardware"></a>Příprava zařízení a dalšího hardwaru

Kromě několika zařízení s Azure Kinect DK možná budete muset získat další hostitelské počítače a další hardware, aby se podporovala konfigurace, kterou chcete sestavit. Pomocí informací v této části se ujistěte, že jsou všechna zařízení a hardware připravená předtím, než začnete s nastavením.

### <a name="azure-kinect-dk-devices"></a>Zařízení Azure Kinect DK

Pro každé zařízení Azure Kinect DK, které chcete synchronizovat, udělejte toto:

- Ujistěte se, že je na zařízení nainstalovaný nejnovější firmware. Další informace o tom, jak aktualizovat zařízení, najdete v článku o [aktualizaci firmwaru Azure Kinect DK](update-device-firmware.md). 
- Odeberte kryt zařízení a odhalte tak porty synchronizace.
- Poznamenejte si sériové číslo pro každé zařízení. Toto číslo budete používat později v procesu instalace.

### <a name="host-computers"></a>Hostitelské počítače

Každé prostředí Azure Kinect DK obvykle používá svůj vlastní hostitelský počítač. Můžete použít vyhrazený hostitelský řadič v závislosti na tom, jakým způsobem zařízení používáte a kolik dat se přenáší přes připojení USB. 

Ujistěte se, že je na každém hostitelském počítači nainstalovaná sada Kinect sady senzorů Azure. Další informace o tom, jak nainstalovat sadu SDK sady senzorů, najdete v tématu [rychlý Start: nastavení služby Azure Kinect DK](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Počítače se systémem Linux: paměť USB v Ubuntu

Ve výchozím nastavení hostitelské počítače se systémem Linux přidělují řadiči USB pouze 16 MB paměti jádra pro zpracování přenosů USB. Tato hodnota je obvykle dostatečná pro podporu jedné Azure Kinect DK. Pro podporu více zařízení ale musí mít řadič USB více paměti. Chcete-li zvýšit velikost paměti, postupujte podle následujících kroků:

1. Upravit/**atd/default/grub**.
1. Vyhledejte následující řádek:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Nahraďte ji pomocí tohoto řádku:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Tyto příkazy nastaví paměť USB na 32 MB. Toto je příklad nastavení ve dvou časech jako výchozí hodnota. Můžete nastavit mnohem větší hodnotu, jak je to vhodné pro vaše řešení.
1. Spusťte **sudo update-grub**.
1. Restartujte počítač.

### <a name="cables"></a>Kabely

Pokud chcete zařízení připojit k druhému a k hostitelským počítačům, musíte použít kabely typu 3,5-mm od kolíku do pylové (označuje se také jako zvukový kabel 3,5-mm). Kabely by měly být kratší než 10 metrů a můžou být stereo nebo mono.

Počet kabelů, které musíte mít, závisí na počtu zařízení, která používáte, a také na konkrétní konfiguraci zařízení. Do boxu Azure Kinect DK nepatří kabely. Musíte je koupit samostatně.

Pokud zařízení připojíte v konfiguraci hvězdičky, musíte mít také jeden rozdělovač sluchátka.

## <a name="connect-your-devices"></a>Připojení zařízení

**Připojení zařízení se systémem Azure Kinect DK v uzavřené řetězové konfiguraci**

1. Připojte si každou službu Azure Kinect DK k napájení.
1. Připojte každé zařízení k hostitelskému počítači. 
1. Vyberte jedno zařízení, které bude hlavním zařízením, a zapojte zvukový kabel 3,5-mm do svého portu pro **synchronizaci** .
1. Připojte druhý konec kabelu do části **synchronizovat v** portu prvního podřízeného zařízení.
1. Pokud chcete připojit jiné zařízení, připojte jiný **kabel k odpojenému portu prvního** podřízeného zařízení a do části **synchronizovat v** portu dalšího zařízení.
1. Opakujte předchozí krok, dokud nebudou připojena všechna zařízení. Poslední zařízení by mělo mít jenom jedno připojení kabelem. Port jeho **synchronizace** by měl být prázdný.

**Připojení zařízení se systémem Azure Kinect DK v konfiguraci s hvězdičkami**

1. Připojte si každou službu Azure Kinect DK k napájení.
1. Připojte každé zařízení k hostitelskému počítači. 
1. Vyberte jedno zařízení, které bude hlavním zařízením, a zapojte jeden konec rozdělovače sluchátka do jeho **synchronizačního** portu.
1. Připojte zvukové kabely 3,5-mm k koncovým koncům rozdělovače sluchátek.
1. Zapojte druhý konec každého kabelu do části **synchronizovat v** portu jednoho z podřízených zařízení.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Ověřte, že jsou zařízení připojená a komunikuje.

Pokud chcete ověřit, jestli jsou zařízení správně připojená, použijte [Azure Kinect Viewer](azure-kinect-viewer.md). Tento postup opakujte, pokud je to nutné pro otestování všech podřízených zařízení v kombinaci s hlavním zařízením.

> [!IMPORTANT]  
> Pro účely tohoto postupu musíte znát sériové číslo každé služby Azure Kinect DK.

1. Otevřete dvě instance aplikace Azure Kinect Viewer.
1. V části **otevřít zařízení**vyberte sériové číslo podřízeného zařízení, které chcete testovat.  
   ![Otevřít zařízení](./media/open-devices.png)
   > [!IMPORTANT]  
   > Aby bylo možné dosáhnout přesného zachytávání imagí mezi všemi zařízeními, je nutné spustit hlavní zařízení jako poslední.  
1. V části **externí synchronizace**vyberte **Sub**.  
   ![Spuštění podřízené kamery](./media/sub-device-start.png)
1.  Vyberte **Spustit**.  
    > [!NOTE]  
    > Vzhledem k tomu, že se jedná o podřízené zařízení, Azure Kinect Viewer po spuštění zařízení nezobrazí obrázek. Nezobrazuje se žádný obrázek, dokud podřízené zařízení neobdrží z hlavního zařízení signál synchronizace.
1. Po spuštění podřízeného zařízení můžete k otevření hlavního zařízení použít jinou instanci Azure Kinect Vieweru.
1. V části **externí synchronizace**vyberte **hlavní**.
1. Vyberte **Spustit**.

Po spuštění hlavního zařízení Azure Kinect by měly být v obou instancích aplikace Azure Kinect Viewer zobrazeny obrázky.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Kalibrovat zařízení jako synchronizovanou sadu

Jakmile ověříte, že zařízení správně komunikují, jste připraveni je kalibrovat, aby se image vytvořily v jedné doméně.

V jednom zařízení je hloubka a kamery RGB v rámci výroby rozkalibrovány pro práci dohromady. Pokud ale více zařízení musí spolupracovat, musí být kalibrována, aby bylo možné určit, jak transformovat image z domény kamery, která ji zachytil, do domény kamery, kterou chcete použít ke zpracování imagí.

K dispozici je několik možností pro různé kalibrace zařízení. Microsoft poskytuje [ukázku kódu zelené obrazovky GitHubu](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), která používá metodu OpenCV. Další podrobnosti a pokyny k kalibraci zařízení najdete v souboru Readme pro tuto ukázku kódu.

Další informace o kalibraci najdete v tématu [použití funkcí kalibrace Azure Kinect](use-calibration-functions.md).

## <a name="next-steps"></a>Další kroky

Když nastavíte synchronizovaná zařízení, můžete se také dozvědět, jak používat
> [!div class="nextstepaction"]
> [Rozhraní API pro záznam a přehrávání sady SDK pro Azure Kinect snímače](record-playback-api.md)

## <a name="related-topics"></a>Související témata

- [O sadě Azure Kinect snímače SDK](about-sensor-sdk.md)
- [Specifikace hardwaru pro Azure Kinect DK](hardware-specification.md) 
- [Rychlý Start: nastavení Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [Aktualizovat firmware Azure Kinect DK](update-device-firmware.md) 
- [Resetování Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect Viewer](azure-kinect-viewer.md) 
