---
title: Konfigurace funkce MPIO pro zařízení StorSimple | Dokumenty společnosti Microsoft
description: Popisuje, jak nakonfigurovat multipatové vstupně-va (MPIO) pro zařízení StorSimple připojené k hostiteli se systémem Windows Server 2012 R2.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: alkohli
ms.openlocfilehash: eda134257edb851eea076459b44e02fc59028f46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60363245"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurace vícecestné vstupně-do/o pro vaše zařízení StorSimple

Tento kurz popisuje kroky, které byste měli postupovat při instalaci a použití funkce Multipath I/O (MPIO) na hostiteli se systémem Windows Server 2012 R2 a připojeném k fyzickému zařízení StorSimple. Pokyny v tomto článku platí pouze pro fyzikální zařízení řady StorSimple 8000. MPIO není aktuálně podporováno na zařízení StorSimple Cloud Appliance.

Společnost Microsoft vytvořila podporu funkce Multipath I/O (MPIO) v systému Windows Server, která pomáhá vytvářet vysoce dostupné konfigurace sítě iSCSI odolné proti chybám. Funkce MPIO používá redundantní součásti fyzické cesty – adaptéry, kabely a přepínače – k vytvoření logických cest mezi serverem a úložným zařízením. Pokud dojde k selhání součásti, což způsobuje selhání logické cesty, logika vícecestných používá alternativní cestu pro vstupně-va, aby aplikace mohly stále přistupovat ke svým datům. Navíc v závislosti na konfiguraci MPIO můžete také zlepšit výkon vyvažováním zatížení přes všechny tyto cesty. Další informace naleznete v tématu [PŘEHLED MPIO](https://technet.microsoft.com/library/cc725907.aspx "Přehled a funkce funkce funkce MPIO").

Pro vysokou dostupnost vašeho řešení StorSimple by měl být mpio nakonfigurován na vašem zařízení StorSimple. Pokud je funkce MPIO nainstalována na hostitelských serverech se systémem Windows Server 2012 R2, mohou servery tolerovat selhání propojení, sítě nebo rozhraní.

## <a name="mpio-configuration-summary"></a>Souhrn konfigurace technologie MPIO

Funkce MPIO je volitelná funkce systému Windows Server a ve výchozím nastavení není nainstalována. Je nutné ji nainstalovat jako funkci pomocí Správce serveru.

Chcete-li na zařízení StorSimple nakonfigurovat funkce MPIO, postupujte takto:

* Krok 1: Instalace funkce MPIO na hostiteli systému Windows Server
* Krok 2: Konfigurace funkce MPIO pro svazky StorSimple
* Krok 3: Připojení StorSimple svazky na hostiteli
* Krok 4: Konfigurace mpio pro vysokou dostupnost a vyrovnávání zatížení

Každý z předchozích kroků je popsán v následujících částech.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalace funkce MPIO na hostiteli systému Windows Server

Chcete-li tuto funkci nainstalovat do hostitele systému Windows Server, proveďte následující postup.

#### <a name="to-install-mpio-on-the-host"></a>Instalace mpio na hostiteli

1. Sem Správce serveru na hostiteli systému Windows Server. Ve výchozím nastavení se Správce serveru spustí, když se člen skupiny Administrators přihlásí k počítači se systémem Windows Server 2012 R2 nebo Windows Server 2012. Pokud správce serveru ještě není otevřen, klepněte na **tlačítko Spustit > Správce serveru**.
   
   ![Server Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klikněte na **Správce serveru > řídicí panel > Přidat role a funkce**. Tím se spustí Průvodce **přidáním rolí a funkcí.**
   
   ![Průvodce přidáním rolí a funkcí 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. V Průvodci **přidáním rolí a funkcí** proveďte následující kroky:
   
   1. Na stránce **Než začnete** klikněte na **Další**.
   2. Na stránce **Vybrat typ instalace** přijměte výchozí nastavení instalace založené na **rolích nebo funkcích.** Klikněte na **Další**.
   
       ![Průvodce přidáním rolí a funkcí 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na stránce **Vybrat cílový server** zvolte Vybrat server z fondu **serverů**. Hostitelský server by měl být zjištěn automaticky. Klikněte na **Další**.
   4. Na stránce **Vybrat role serveru** klikněte na **Další**.
   5. Na stránce **Vybrat funkce** vyberte **vstupně-krokem Multipath a**klepněte na tlačítko **Další**.
   
       ![Průvodce přidáním rolí a funkcí 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na stránce **Potvrdit výběr instalace** potvrďte výběr a v případě potřeby automaticky vyberte **Restartovat cílový server**, jak je znázorněno níže. Klepněte na tlačítko **Instalovat**.
   
       ![Průvodce přidáním rolí a funkcí 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Po dokončení instalace budete upozorněni. Průvodce zavřete kliknutím na **Zavřít**.
   
       ![Průvodce přidáním rolí a funkcí 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurace funkce MPIO pro svazky StorSimple

FUNKCE MPIO musí být nakonfigurována tak, aby identifikovala svazky StorSimple. Chcete-li nakonfigurovat mpio rozpoznat StorSimple svazky, proveďte následující kroky.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurace funkce MPIO pro svazky StorSimple

1. Otevřete **konfiguraci MPIO**. Klepněte na **položku Správce serveru > nástroje > řídicího panelu > funkce MPIO**.
2. V dialogovém okně **Vlastnosti mpio** vyberte kartu **Objevit více cest.**
3. Vyberte **Přidat podporu pro zařízení iSCSI**a klepněte na tlačítko **Přidat**.  
   ![Vlastnosti mpio objevte více cest](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Po zobrazení výzvy restartujte server.
5. V dialogovém okně **Vlastnosti mpio** klepněte na **Add**kartu **Zařízení MPIO.**
    </br>![Zařízení MPIO Vlastnosti MPIO](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. V dialogovém okně **Přidat podporu mpio** zadejte v části **ID hardwaru zařízení**sériové číslo zařízení. Chcete-li získat sériové číslo zařízení, přeziťte službu Správce zařízení StorSimple. Přejděte na **ovládací panel zařízení >**. Sériové číslo zařízení se zobrazí v pravém podokně **Rychlý přehled** na řídicím panelu zařízení.
    </br>
    ![Přidat podporu MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Po zobrazení výzvy restartujte server.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: Připojení StorSimple svazky na hostiteli

Po konfiguraci funkce MPIO v systému Windows Server lze připojit svazek vytvořený na zařízení StorSimple a využít výhod funkce MPIO pro redundanci. Chcete-li připojit svazek, proveďte následující kroky.

#### <a name="to-mount-volumes-on-the-host"></a>Připojení svazků na hostiteli

1. Otevřete okno **Vlastnosti iniciátoru iSCSI** na hostiteli systému Windows Server. Klepněte **na tlačítko Správce serveru > nástroje > řídicího panelu > iniciátoru iSCSI**.
2. V dialogovém okně **Vlastnosti iniciátoru iSCSI** klepněte na kartu Zjišťování a potom klepněte na tlačítko **Zjistit cílový portál**.
3. V dialogovém okně **Zjistit cílový portál** proveďte následující kroky:
   
   1. Zadejte IP adresu datového portu vašeho zařízení StorSimple (například zadejte DATA 0).
   2. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna **Vlastnosti iniciátoru iSCSI.**
     
      > [!IMPORTANT]
      > **Pokud používáte privátní síť pro připojení iSCSI, zadejte adresu IP datového portu připojeného k privátní síti.**
    
4. Opakujte kroky 2-3 pro druhé síťové rozhraní (například DATA 1) v zařízení. Mějte na paměti, že tato rozhraní by měla být povolena pro iSCSI. Další informace naleznete v [tématu Modify network interfaces](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. V dialogovém okně **Vlastnosti iniciátoru iSCSI** vyberte kartu **Cíle.** Měli byste vidět StorSimple zařízení cíl IQN v části **zjištěné cíle**.

   ![Karta Cíle vlastností iniciátoru iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Kliknutím na **Připojit** vytvořte relaci iSCSI se zařízením StorSimple. Zobrazí se dialogové okno **Připojit k cíli.**
7. V dialogovém okně **Připojit k cíli** zaškrtněte políčko **Povolit více cest.** Klikněte na tlačítko **Upřesnit**.
8. V dialogovém okně **Upřesnit nastavení** proveďte následující kroky:
   
   1. V rozevíracím seznamu **Místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP iniciátoru** vyberte adresu IP hostitele.
   3. V rozevíracím seznamu IP **cílového portálu** vyberte IP adresu rozhraní zařízení.
   4. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna **Vlastnosti iniciátoru iSCSI.**
9. Klikněte na **Vlastnosti**. V dialogovém okně **Vlastnosti** klepněte na **tlačítko Přidat relaci**.
10. V dialogovém okně **Připojit k cíli** zaškrtněte políčko **Povolit více cest.** Klikněte na tlačítko **Upřesnit**.
11. V dialogovém okně **Upřesnit nastavení:**

    1. V rozevíracím seznamu **Místní adaptér** vyberte iniciátor iSCSI společnosti Microsoft.
    2. V rozevíracím seznamu **IP iniciátoru** vyberte adresu IP odpovídající hostiteli. V takovém případě připojujete dvě síťová rozhraní v zařízení k jednomu síťovému rozhraní na hostiteli. Proto toto rozhraní je stejný jako poskytované pro první relaci.
    3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu pro druhé datové rozhraní povolené v zařízení.
    4. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna Vlastnosti iniciátoru iSCSI. Přidali jste druhou relaci do cíle.
12. **Svírejte správu počítače** přejdeme na **Správce serveru > Řídicí panel > správa počítače**. V levém podokně klepněte na **položku Úložiště > Správa disků**. Svazek vytvořený na zařízení StorSimple, které jsou viditelné pro tohoto hostitele, se zobrazí v části **Správa disků** jako nový disk(ů).
13. Inicializovat disk a vytvořit nový svazek. Během procesu formátování vyberte velikost bloku 64 KB.
    
    ![Správa disků](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. V části **Správa disků**klepněte pravým tlačítkem myši na **položku Disk** a vyberte příkaz **Vlastnosti**.
15. V dialogovém okně Vlastnosti diskového zařízení StorSimple #### **Vlastnosti diskového zařízení s více cestami** klepněte na kartu **MPIO.**
    
    ![StorSimple 8100 Vícecestný disk DeviceProp.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. V části **Název dsm** klikněte na **Podrobnosti** a ověřte, zda jsou parametry nastaveny na výchozí parametry. Výchozí parametry jsou:
    
    * Doba ověření cesty = 30
    * Počet opakování = 3
    * Doba odstranění PDO = 20
    * Interval opakování = 1
    * Ověření cesty povoleno = Nezaškrtnuto.

> [!NOTE]
> **Neměnte výchozí parametry.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurace mpio pro vysokou dostupnost a vyrovnávání zatížení

Pro vysokou dostupnost založenou na více cestách a vyrovnávání zatížení musí být ručně přidáno více relací, aby bylo možné deklarovat různé cesty, které jsou k dispozici. Pokud má například hostitel dvě rozhraní připojená k síti iSCSI a zařízení má dvě rozhraní připojená k síti iSCSI, budete potřebovat čtyři relace nakonfigurované s vlastními permutacemi cesty (pokud každé datové rozhraní a hostitelské rozhraní je v jiné podsíti IP a není směrovatelné).

**Doporučujeme mít alespoň 8 aktivních paralelních relací mezi zařízením a hostitelem aplikace.** Toho lze dosáhnout povolením 4 síťových rozhraní v systému Windows Server. Používejte fyzická síťová rozhraní nebo virtuální rozhraní prostřednictvím technologií virtualizace sítě na úrovni hardwaru nebo operačního systému na hostiteli systému Windows Server. Se dvěma síťovými rozhraními v zařízení by tato konfigurace vedla k 8 aktivním relacím. Tato konfigurace pomáhá optimalizovat propustnost zařízení a cloudu.

> [!IMPORTANT]
> **Doporučujeme nekombinovat síťová rozhraní 1 GbE a 10 GbE. Pokud používáte dvě síťová rozhraní, obě rozhraní by měla být stejného typu.**

Následující postup popisuje, jak přidat relace, když je zařízení StorSimple se dvěma síťovými rozhraními připojeno k hostiteli se dvěma síťovými rozhraními. To vám dává pouze 4 sezení. Stejný postup použijte u zařízení StorSimple se dvěma síťovými rozhraními připojenými k hostiteli se čtyřmi síťovými rozhraními. Budete muset nakonfigurovat 8 místo 4 relací popsaných zde.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurace technologie MPIO pro vysokou dostupnost a vyrovnávání zatížení

1. Proveďte zjišťování cíle: V dialogovém okně **Vlastnosti iniciátoru iSCSI** klikněte na kartě **Zjišťování** na **zjistit portál**.
2. V dialogovém okně **Připojit k cíli** zadejte IP adresu jednoho ze síťových rozhraní zařízení.
3. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna **Vlastnosti iniciátoru iSCSI.**
4. V dialogovém okně **Vlastnosti iniciátoru iSCSI** vyberte kartu **Cíle,** zvýrazněte zjištěný cíl a klepněte na tlačítko **Připojit**. Zobrazí se dialogové okno **Připojit k cíli.**
5. V dialogovém okně **Připojit k cíli:**
   
   1. Ponechte výchozí vybrané nastavení cíle pro **Přidat toto připojení** do seznamu oblíbených cílů. To způsobí, že se zařízení automaticky pokusí restartovat připojení při každém restartování tohoto počítače.
   2. Zaškrtněte políčko **Povolit více cest.**
   3. Klikněte na tlačítko **Upřesnit**.
6. V dialogovém okně **Upřesnit nastavení:**
   
   1. V rozevíracím seznamu **Místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP iniciátoru** vyberte adresu IP odpovídající prvnímu rozhraní na hostiteli (rozhraní iSCSI).
   3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu pro první datové rozhraní povolené v zařízení.
   4. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna Vlastnosti iniciátoru iSCSI.
7. Klepněte na **příkaz Vlastnosti**a v dialogovém **okně Vlastnosti** klepněte na **tlačítko Přidat relaci**.
8. V dialogovém okně **Připojit k cíli** zaškrtněte políčko **Povolit více cest** a klepněte na tlačítko **Upřesnit**.
9. V dialogovém okně **Upřesnit nastavení:**
   
   1. V rozevíracím seznamu **Místní adaptér** vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. V rozevíracím seznamu **IP iniciátoru** vyberte adresu IP odpovídající druhému rozhraní iSCSI na hostiteli.
   3. V rozevíracím seznamu **IP cílového portálu** vyberte IP adresu pro druhé datové rozhraní povolené v zařízení.
   4. Klepnutím na **tlačítko OK** se vrátíte do dialogového okna **Vlastnosti iniciátoru iSCSI.** Nyní jste přidali druhou relaci do cíle.
10. Opakováním kroků 8-10 přidejte k cíli další relace (cesty). Se dvěma rozhraními na hostiteli a dvěma v zařízení můžete přidat celkem čtyři relace.
11. Po přidání požadovaných relací (cest) vyberte v dialogovém okně **Vlastnosti iniciátoru iSCSI** cíl a klepněte na **příkaz Vlastnosti**. Na kartě Relace v dialogovém okně **Vlastnosti** si všimněte čtyř identifikátorů relací, které odpovídají možným permutacím cesty. Chcete-li relaci zrušit, zaškrtněte políčko vedle identifikátoru relace a klepněte na tlačítko **Odpojit**.
12. Chcete-li zobrazit zařízení prezentovaná v rámci relací, vyberte kartu **Zařízení.** Chcete-li nakonfigurovat zásady FUNKCE MPIO pro vybrané zařízení, klepněte na tlačítko **MPIO**. Zobrazí se dialogové okno **Podrobnosti o zařízení.** Na kartě **MPIO** můžete vybrat příslušné nastavení **zásad vyrovnávání zatížení.** Můžete také zobrazit typ **cesty Aktivní** nebo **Pohotovostní režim.**

## <a name="next-steps"></a>Další kroky

Další informace o [použití služby StorSimple Device Manager k úpravě konfigurace zařízení StorSimple](storsimple-8000-modify-device-config.md).

