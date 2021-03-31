---
title: Konfigurace funkce MPIO pro zařízení StorSimple | Microsoft Docs
description: Popisuje, jak nakonfigurovat funkci Multipath I/O (MPIO) pro zařízení StorSimple připojené k hostiteli, na kterém běží Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: 3d44fada1eddf2d3f80bec085d8a5bf751197eb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94968804"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurace funkce Multipath I/O pro zařízení StorSimple

V tomto kurzu se dozvíte, jak nainstalovat a používat funkci Multipath I/O (MPIO) na hostiteli se systémem Windows Server 2012 R2 a připojeném k StorSimple fyzickému zařízení. Pokyny v tomto článku se týkají jenom fyzických zařízení řady StorSimple 8000. Funkce MPIO není v současnosti u StorSimple Cloud Appliance podporována.

Společnost Microsoft vytvořila podporu funkce Multipath I/O (MPIO) ve Windows serveru, která vám může pomoci při vytváření vysoce dostupných a odolných síťových konfigurací iSCSI. Funkce MPIO používá redundantní součásti fyzické cesty – adaptéry, kabely a přepínače – k vytvoření logických cest mezi serverem a úložným zařízením. Pokud dojde k selhání komponenty, což způsobí selhání logické cesty, logika s více cestami používá alternativní cestu pro vstup a výstup, takže aplikace budou mít stále přístup k jejich datům. V závislosti na konfiguraci může funkce MPIO také zvýšit výkon tím, že vyrovnává zatížení mezi všemi těmito cestami. Další informace najdete v tématu [Přehled funkce MPIO](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725907(v=ws.11) "Přehled a funkce funkce MPIO").

Pro zajištění vysoké dostupnosti vašeho řešení StorSimple by se měl na zařízení StorSimple nakonfigurovat funkce MPIO. Pokud je funkce MPIO nainstalovaná na hostitelských serverech se systémem Windows Server 2012 R2, pak servery můžou tolerovat selhání propojení, sítě nebo rozhraní.

## <a name="mpio-configuration-summary"></a>Shrnutí konfigurace funkce MPIO

Funkce MPIO je volitelnou funkcí na Windows serveru a není ve výchozím nastavení nainstalovaná. Je nutné ji nainstalovat jako funkci pomocí Správce serveru.

Pomocí těchto kroků nakonfigurujete funkci MPIO na zařízení StorSimple:

* Krok 1: Instalace funkce MPIO na hostiteli s Windows serverem
* Krok 2: Konfigurace funkce MPIO pro StorSimple svazky
* Krok 3: připojení svazků StorSimple na hostiteli
* Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Každý z předchozích kroků je popsán v následujících částech.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalace funkce MPIO na hostiteli s Windows serverem

Pokud chcete tuto funkci nainstalovat na hostitele Windows serveru, proveďte následující postup.

#### <a name="to-install-mpio-on-the-host"></a>Instalace funkce MPIO na hostitele

1. Otevřete Správce serveru na hostiteli s Windows serverem. Ve výchozím nastavení Správce serveru spustí, když se člen skupiny Administrators přihlásí k počítači se systémem Windows Server 2012 R2 nebo Windows Server 2012. Pokud Správce serveru ještě není otevřený, klikněte na **spustit > správce serveru**.
   
   ![Server Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klikněte na **řídicí panel > Správce serveru > přidat role a funkce**. Tím se spustí průvodce **přidáním rolí a funkcí** .
   
   ![Průvodce přidáním rolí a funkcí 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. V průvodci **přidáním rolí a funkcí** proveďte následující kroky:
   
   1. Na stránce **Než začnete** klikněte na **Další**.
   2. Na stránce **Vybrat typ instalace** přijměte výchozí nastavení instalace na **základě rolí nebo na základě funkcí** . Klikněte na **Next** (Další).
   
       ![Průvodce přidáním rolí a funkcí 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na stránce **Vybrat cílový server** zvolte **možnost vybrat server z fondu serverů**. Hostitelský server by měl být zjištěn automaticky. Klikněte na **Next** (Další).
   4. Na stránce **Vybrat role serveru** klikněte na **Další**.
   5. Na stránce **Vybrat funkce** vyberte možnost **Multipath I/O** a klikněte na tlačítko **Další**.
   
       ![Průvodce přidáním rolí a funkcí 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na stránce **Potvrdit vybrané možnosti instalace** potvrďte výběr a v **případě potřeby vyberte možnost restartovat cílový server automaticky**, jak je uvedeno níže. Klikněte na **Install** (Nainstalovat).
   
       ![Průvodce přidáním rolí a funkcí 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Po dokončení instalace se zobrazí oznámení. Průvodce zavřete kliknutím na **Zavřít**.
   
       ![Průvodce přidáním rolí a funkcí 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurace funkce MPIO pro StorSimple svazky

Aby bylo možné identifikovat svazky StorSimple, musí být nakonfigurováno funkce MPIO. Ke konfiguraci funkce MPIO pro rozpoznávání StorSimple svazků proveďte následující kroky.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurace funkce MPIO pro StorSimple svazky

1. Otevřete **konfiguraci funkce MPIO**. Klikněte na tlačítko **Správce serveru > řídicí panel > nástroje > MPIO**.
2. V dialogovém okně **vlastnosti MPIO** vyberte kartu **zjistit více cest** .
3. Vyberte **Přidat podporu pro zařízení iSCSI** a pak klikněte na **Přidat**.  
   ![Vlastnosti funkce MPIO – zjišťování více cest](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Po zobrazení výzvy restartujte server.
5. V dialogovém okně **vlastnosti MPIO** klikněte na kartu **zařízení s funkcí MPIO** . klikněte na tlačítko **Přidat**.
    </br>![MPIO – vlastnosti zařízení MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. V dialogovém okně **Přidat podporu funkce MPIO** zadejte do pole **ID hardwaru zařízení** sériové číslo zařízení. Pokud chcete získat sériové číslo zařízení, přejděte ke službě StorSimple Správce zařízení. Přejděte na **zařízení > řídicím panelu**. Sériové číslo zařízení se zobrazí v pravém podokně **rychlý přehled** řídicího panelu zařízení.
    </br>
    ![Přidat podporu funkce MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Po zobrazení výzvy restartujte server.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: připojení svazků StorSimple na hostiteli

Po nakonfigurování funkce MPIO na Windows serveru se dají připojit svazky vytvořené na zařízení StorSimple a pak můžou využít výhod funkce MPIO pro redundanci. Pokud chcete připojit svazek, proveďte následující kroky.

#### <a name="to-mount-volumes-on-the-host"></a>Připojení svazků na hostiteli

1. Otevřete okno **Vlastnosti iniciátoru iSCSI** na hostiteli Windows serveru. Klikněte na tlačítko **Správce serveru > řídicí panel > nástroje > iniciátor iSCSI**.
2. V dialogovém okně **Vlastnosti iniciátoru iSCSI** klikněte na kartu zjišťování a potom klikněte na **vyhledat cílový portál**.
3. V dialogovém okně **zjistit cílový portál** proveďte následující kroky:
   
   1. Zadejte IP adresu datového portu zařízení StorSimple (například zadejte DATA 0).
   2. Kliknutím na tlačítko **OK** se vraťte do dialogového okna **Vlastnosti iniciátoru iSCSI** .
     
      > [!IMPORTANT]
      > **Pokud používáte privátní síť pro připojení iSCSI, zadejte IP adresu datového portu, který je připojený k privátní síti.**
    
4. Opakujte kroky 2-3 pro druhé síťové rozhraní (například DATA 1) na vašem zařízení. Mějte na paměti, že tato rozhraní by měla být povolená pro iSCSI. Další informace najdete v tématu [Úprava síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. V dialogovém okně **Vlastnosti iniciátoru iSCSI** vyberte kartu **cíle** . V **zjištěných cílech** by se měl zobrazit cílový identifikátor IQN zařízení StorSimple.

   ![Karta cíle vlastností iniciátoru iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknutím na **připojit** navažte relaci iSCSI se zařízením StorSimple. Zobrazí se dialogové okno **připojit k cíli** .
7. V dialogovém okně **připojit k cílovému** poli zaškrtněte políčko **Povolit více cest** . Klikněte na tlačítko **Upřesnit**.
8. V dialogovém okně **Upřesnit nastavení** proveďte následující kroky:
   
   1. V rozevíracím seznamu **místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP adresa iniciátoru** vyberte IP adresu hostitele.
   3. V rozevíracím seznamu IP adres **cílového portálu** vyberte IP adresu rozhraní zařízení.
   4. Kliknutím na tlačítko **OK** se vraťte do dialogového okna **Vlastnosti iniciátoru iSCSI** .
9. Klikněte na **Vlastnosti**. V dialogovém okně **vlastnosti** klikněte na **Přidat relaci**.
10. V dialogovém okně **připojit k cílovému** poli zaškrtněte políčko **Povolit více cest** . Klikněte na tlačítko **Upřesnit**.
11. V dialogovém okně **Upřesnit nastavení** :

    1. V rozevíracím seznamu **místní adaptér** vyberte iniciátor iSCSI společnosti Microsoft.
    2. V rozevíracím seznamu **IP adresa iniciátoru** vyberte IP adresu, která odpovídá hostiteli. V tomto případě propojíte dvě síťová rozhraní v zařízení s jedním síťovým rozhraním hostitele. Proto je toto rozhraní stejné jako zadané pro první relaci.
    3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu pro druhé datové rozhraní povolené na zařízení.
    4. Kliknutím na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI. Přidali jste druhou relaci k cíli.
12. Otevřete **správu počítače** tak, že přejdete na **Správce serveru > řídicí panel > Správa počítače**. V levém podokně klikněte na **úložiště > Správa disků**. Svazek vytvořený na zařízení StorSimple, které jsou viditelné pro tohoto hostitele, se zobrazí v části **Správa disků** jako nové disky.
13. Inicializujte disk a vytvořte nový svazek. Během procesu formátování vyberte velikost bloku 64 KB.
    
    ![Správa disků](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. V části **Správa disků** klikněte pravým tlačítkem na **disk** a vyberte **vlastnosti**.
15. V dialogovém okně StorSimple model \ # # # **multi-Path disk Device Properties** klikněte na kartu **MPIO** .
    
    ![StorSimple 8100 multi-Path DeviceProp na disku.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. V části **název modulu DSM** klikněte na tlačítko **Podrobnosti** a ověřte, zda jsou parametry nastaveny na výchozí parametry. Výchozí parametry jsou:
    
    * Doba ověření cesty = 30
    * Počet opakování = 3
    * Období odebrání CHOP = 20
    * Interval opakování = 1
    * Cesta ověřování povolena = nezaškrtnuto.

> [!NOTE]
> **Neměňte výchozí parametry.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Pro zajištění vysoké dostupnosti a vyrovnávání zatížení na základě více cest je nutné ručně přidat více relací, aby bylo možné deklarovat různé dostupné cesty. Pokud má například hostitel dvě rozhraní připojená k síti iSCSI a zařízení má dvě rozhraní připojená k síti iSCSI, budete potřebovat čtyři relace nakonfigurované se správnými permutacemi cest (Pokud se každé datové rozhraní a hostitelské rozhraní nachází v jiné podsíti protokolu IP a není směrovatelný), vyžadují se jenom dvě relace.

**Doporučujeme, abyste měli aspoň 8 aktivních paralelních relací mezi zařízením a hostitelem vaší aplikace.** To je možné dosáhnout povolením 4 síťových rozhraní v systému Windows Server. Využijte fyzická síťová rozhraní nebo virtuální rozhraní prostřednictvím technologií virtualizace sítě na úrovni hardwaru nebo operačního systému na hostiteli s Windows serverem. U dvou síťových rozhraní v zařízení by tato konfigurace měla za následek 8 aktivních relací. Tato konfigurace pomáhá optimalizovat propustnost zařízení a cloudu.

> [!IMPORTANT]
> **Doporučujeme, abyste nekombinovaná 1 GbE a 10 GbE síťových rozhraní. Použijete-li dvě síťová rozhraní, musí být obě rozhraní stejného typu.**

Následující postup popisuje, jak přidat relace, když se zařízení StorSimple se dvěma síťovými rozhraními připojí k hostiteli se dvěma síťovými rozhraními. To vám dává jenom 4 relace. Stejný postup použijte u zařízení StorSimple se dvěma síťovými rozhraními, která jsou připojená k hostiteli se čtyřmi síťovými rozhraními. Místo 4 relací, které jsou zde popsané, budete muset nakonfigurovat 8.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

1. Proveďte zjišťování cíle: v dialogovém okně **Vlastnosti iniciátoru iSCSI** na kartě **zjišťování** klikněte na možnost **zjistit portál**.
2. V dialogovém okně **připojit k cíli** zadejte IP adresu jednoho ze síťových rozhraní zařízení.
3. Kliknutím na tlačítko **OK** se vraťte do dialogového okna **Vlastnosti iniciátoru iSCSI** .
4. V dialogovém okně **Vlastnosti iniciátoru iSCSI** vyberte kartu **cíle** , zvýrazněte zjištěnou cílovou položku a pak klikněte na tlačítko **připojit**. Zobrazí se dialogové okno **připojit k cíli** .
5. V dialogovém okně **připojit k cíli** :
   
   1. Ponechte vybrané výchozí nastavení cíle pro **přidání tohoto připojení** do seznamu oblíbených cílů. Tím se zařízení automaticky pokusí znovu spustit připojení pokaždé, když se tento počítač restartuje.
   2. Zaškrtněte políčko **Povolit více cest** .
   3. Klikněte na tlačítko **Upřesnit**.
6. V dialogovém okně **Upřesnit nastavení** :
   
   1. V rozevíracím seznamu **místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP adresa iniciátoru** vyberte IP adresu odpovídající prvnímu rozhraní hostitele (rozhraní iSCSI).
   3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu prvního povoleného datového rozhraní v zařízení.
   4. Kliknutím na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI.
7. Klikněte na **vlastnosti** a v dialogovém okně **vlastnosti** klikněte na **Přidat relaci**.
8. V dialogovém okně **připojit k cíli** zaškrtněte políčko **Povolit více cest** a pak klikněte na tlačítko **Upřesnit**.
9. V dialogovém okně **Upřesnit nastavení** :
   
   1. V rozevíracím seznamu **místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP adresa iniciátoru** vyberte IP adresu odpovídající druhému rozhraní iSCSI na hostiteli.
   3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu pro druhé datové rozhraní povolené na zařízení.
   4. Kliknutím na tlačítko **OK** se vraťte do dialogového okna **Vlastnosti iniciátoru iSCSI** . Nyní jste přidali druhou relaci k cíli.
10. Opakujte kroky 8-10 pro přidání dalších relací (cest) k cíli. U dvou rozhraní na hostiteli a dvou v zařízení můžete přidat celkem čtyři relace.
11. Po přidání požadovaných relací (cest) v dialogovém okně **Vlastnosti iniciátoru iSCSI** vyberte cíl a klikněte na **vlastnosti**. Na kartě relace v dialogovém okně **vlastnosti** si poznamenejte čtyři identifikátory relací, které odpovídají možným permutacím cesty. Pokud chcete relaci zrušit, zaškrtněte políčko vedle identifikátoru relace a pak klikněte na **Odpojit**.
12. Chcete-li zobrazit zařízení uvedená v rámci relací, vyberte kartu **zařízení** . Pokud chcete nakonfigurovat zásady funkce MPIO pro vybrané zařízení, klikněte na **MPIO**. Zobrazí se dialogové okno **Podrobnosti o zařízení** . Na kartě **MPIO** můžete vybrat odpovídající nastavení **zásad vyrovnávání zatížení** . Můžete také zobrazit **aktivní** nebo **pohotovostní** typ cesty.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [používání služby StorSimple Správce zařízení k úpravě konfigurace zařízení StorSimple](storsimple-8000-modify-device-config.md).