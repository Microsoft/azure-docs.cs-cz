---
title: Konfigurace funkce MPIO pro zařízení StorSimple | Dokumentace Microsoftu
description: Popisuje postup konfigurace funkce Multipath I/O (MPIO) pro zařízení StorSimple připojené k hostiteli se systémem Windows Server 2012 R2.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363245"
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Nakonfigurujte funkci Multipath I/O pro zařízení StorSimple

Tento kurz popisuje kroky, které by měly dodržovat nainstalovat a používat funkci Multipath I/O (MPIO) na hostitele se systémem Windows Server 2012 R2 a připojen k fyzickým zařízením StorSimple. Pokyny v tomto článku platí pro StorSimple řady 8000 jenom fyzické zařízení. Funkce MPIO není aktuálně podporována u řešení StorSimple Cloud Appliance.

Společnost Microsoft vyvinula podporu pro funkci Multipath I/O (MPIO) ve Windows serveru, které vám pomůžou vytvářet vysoce dostupné a odolné proti chybám iSCSI konfigurace sítě. Funkce MPIO používá redundantní komponenty fyzických cesty – adaptéry, kabely a přepínače – k vytvoření logických cest mezi serverem a úložné zařízení. Pokud dojde k selhání součásti, způsobí logickou cestu k selhání, logika více cest používá alternativní cesty pro vstupně-výstupních operací tak, aby aplikace můžete přístup k datům. Kromě toho v závislosti na vaší konfiguraci funkce MPIO můžete také zvýšit výkon Vyrovnávání zatížení mezi všechny tyto cesty. Další informace najdete v tématu [Přehled funkce MPIO](https://technet.microsoft.com/library/cc725907.aspx "funkce MPIO přehled a funkce").

Pro vysokou dostupnost vašeho řešení StorSimple by měl být nakonfigurovaný funkce MPIO na zařízení StorSimple. Při instalaci funkce MPIO na hostitelských serverech s Windows serverem 2012 R2, servery tolerovat pak odkaz, sítě nebo rozhraní.

## <a name="mpio-configuration-summary"></a>Souhrn konfigurace funkce MPIO

Funkci MPIO je volitelná funkce v systému Windows Server a ve výchozím nastavení nainstalované není. Je nutné ji nainstalovat jako funkci pomocí Správce serveru.

Použijte následující postup konfigurace funkce MPIO na zařízení StorSimple:

* Krok 1: Instalaci funkce MPIO na hostiteli s Windows serverem
* Krok 2: Konfigurace funkce MPIO pro svazky zařízení StorSimple
* Krok 3: StorSimple připojit svazky na hostiteli
* Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Všechny předchozí kroky jsou popsány v následujících částech.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalaci funkce MPIO na hostiteli s Windows serverem

K instalaci této funkce na hostiteli s Windows serverem, proveďte následující postup.

#### <a name="to-install-mpio-on-the-host"></a>K instalaci funkce MPIO na hostiteli

1. Otevřete správce serveru na hostiteli s Windows serverem. Ve výchozím nastavení správce serveru začíná členem skupiny Administrators přihlásí k počítači, na kterém běží Windows Server 2012 R2 nebo Windows Server 2012. Pokud správce serveru ještě není otevřený, klikněte na tlačítko **Start > Správce serveru**.
   
   ![Server Manager](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klikněte na tlačítko **správce serveru > řídicí panel > Přidat role a funkce**. Tím se spustí **přidat role a funkce** průvodce.
   
   ![Přidat role a funkce Průvodce 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. V **přidat role a funkce** průvodce, proveďte následující kroky:
   
   1. Na **před zahájením** klikněte na **Další**.
   2. Na **vybrat typ instalace** stránce, přijměte výchozí nastavení **na základě rolí nebo na základě funkcí** instalace. Klikněte na **Další**.
   
       ![Přidat role a funkce Průvodce 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na **vybrat cílový server** zvolte **vyberte server z fondu serverů**. Hostitelský server by měly být zjištěny automaticky. Klikněte na **Další**.
   4. Na **vybrat role serveru** klikněte na **Další**.
   5. Na **zvolte funkce, které** stránce **Multipath I/O**a klikněte na tlačítko **Další**.
   
       ![Přidat role a funkce Průvodce 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na **potvrdit vybrané možnosti instalace** stránce Potvrďte výběr a pak vyberte **cílový server automaticky restartovat, pokud je to nutné**, jak je znázorněno níže. Klikněte na **Nainstalovat**.
   
       ![Přidat role a funkce Průvodce 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Po dokončení instalace, se zobrazí oznámení. Průvodce zavřete kliknutím na **Zavřít**.
   
       ![Přidat role a funkce Průvodce 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurace funkce MPIO pro svazky zařízení StorSimple

Funkce MPIO, musí být nakonfigurovaný k identifikaci svazky zařízení StorSimple. Konfigurace funkce MPIO rozpoznat svazky zařízení StorSimple, proveďte následující kroky.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurace funkce MPIO pro svazky zařízení StorSimple

1. Otevřít **konfigurace funkce MPIO**. Klikněte na tlačítko **správce serveru > řídicí panel > Nástroje > funkce MPIO**.
2. V **vlastnosti funkce MPIO** dialogové okno, vyberte **zjistit více cest** kartu.
3. Vyberte **přidanou podporou pro zařízení iSCSI**a potom klikněte na tlačítko **přidat**.  
   ![Vlastnosti funkce MPIO zjistit více cest](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Restartujte server po zobrazení výzvy.
5. V **vlastnosti funkce MPIO** dialogové okno, klikněte na tlačítko **funkce MPIO zařízení** kartu. Klikněte na tlačítko **Add** (Přidat).
    </br>![Funkce MPIO vlastnosti funkce MPIO zařízení](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. V **přidat podporu funkce MPIO** dialogovém okně **ID hardwaru zařízení**, zadejte sériové číslo zařízení. Sériové číslo zařízení získáte přístup ke službě Správce zařízení StorSimple. Přejděte do **zařízení > řídicí panel**. Sériové číslo zařízení se zobrazí v pravém **rychlý přehled** podokně řídicího panelu zařízení.
    </br>
    ![Přidání podpory funkce MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Restartujte server po zobrazení výzvy.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: StorSimple připojit svazky na hostiteli

Po dokončení konfigurace funkce MPIO v systému Windows Server, je možné připojit svazky vytvořené na zařízení StorSimple a potom můžete využít výhod funkce MPIO pro zajištění redundance. Připojení svazku, postupujte následovně.

#### <a name="to-mount-volumes-on-the-host"></a>Připojit svazky na hostiteli

1. Otevřít **vlastnosti iniciátoru iSCSI** okno na hostiteli s Windows serverem. Klikněte na tlačítko **správce serveru > řídicí panel > Nástroje > iniciátor iSCSI**.
2. V **vlastnosti iniciátoru iSCSI** dialogové okno, klikněte na kartu zjišťování a pak klikněte na tlačítko **zjistit cílový portál**.
3. V **zjistit cílový portál** dialogové okno pole, proveďte následující kroky:
   
   1. Zadejte IP adresu datovém portu zařízení StorSimple (třeba zadat DATA 0).
   2. Klikněte na tlačítko **OK** se vrátíte **vlastnosti iniciátoru iSCSI** dialogové okno.
     
      > [!IMPORTANT]
      > **Pokud používáte privátní sítě pro připojení k iSCSI, zadejte IP adresu datovém portu, která je připojena k privátní síti.**
    
4. Opakujte kroky 2 až 3 pro druhé síťové rozhraní (například DATA 1) na vašem zařízení. Uvědomte si, že tato rozhraní, byste měli povolit pro iSCSI. Další informace najdete v tématu [změnit síťová rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Vyberte **cíle** kartu **vlastnosti iniciátoru iSCSI** dialogové okno. Měli byste vidět zařízení StorSimple cílové IQN pod **zjištěné cíle**.

   ![Karta cíle vlastnosti iniciátoru iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klikněte na tlačítko **připojit** k vytvoření relace iSCSI pro zařízení StorSimple. A **připojit k cíli** zobrazí se dialogové okno.
7. V **připojit k cíli** dialogové okno, vyberte **povolit více cest** zaškrtávací políčko. Klikněte na tlačítko **Advanced**.
8. V **Upřesnit nastavení** dialogové okno pole, proveďte následující kroky:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátoru iSCSI od Microsoftu**.
   2. Na **iniciátoru IP** rozevíracího seznamu vyberte IP adresu hostitele.
   3. Na **cílový portál** IP rozevírací seznam, vyberte IP adresu z rozhraní zařízení.
   4. Klikněte na tlačítko **OK** se vrátíte **vlastnosti iniciátoru iSCSI** dialogové okno.
9. Klikněte na **Vlastnosti**. V **vlastnosti** dialogové okno, klikněte na tlačítko **Přidat relaci**.
10. V **připojit k cíli** dialogové okno, vyberte **povolit více cest** zaškrtávací políčko. Klikněte na tlačítko **Advanced**.
11. V **Upřesnit nastavení** dialogové okno:

    1. Na **místní adaptér** rozevíracího seznamu vyberte iniciátoru iSCSI od Microsoftu.
    2. Na **iniciátoru IP** rozevíracího seznamu vyberte IP adresu odpovídající hostitele. V takovém případě se připojujete dvě síťová rozhraní na zařízení jedním síťovým rozhraním na hostiteli. Proto se toto rozhraní je stejný jako zadaný pro první relace.
    3. Na **IP cílového portálu** rozevíracího seznamu vyberte IP adresu pro druhé síťové rozhraní data na zařízení povolená.
    4. Klikněte na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI. Druhý relace jste přidali do cíle.
12. Otevřít **Správa počítače** tak, že přejdete do **správce serveru > řídicí panel > Správa počítače**. V levém podokně klikněte na tlačítko **úložiště > Správa disků**. Svazek vytvořený v zařízení StorSimple, které jsou viditelné pro tohoto hostitele se zobrazí v části **Správa disků** jako nové disky.
13. Inicializujte disk a vytvořte nový svazek. Během procesu formátu vyberte velikost bloku 64 kB.
    
    ![Správa disků](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. V části **Správa disků**, klikněte pravým tlačítkem myši **disku** a vyberte **vlastnosti**.
15. V modelu StorSimple ### **vlastnosti zařízení Disk s více cestami** dialogové okno, klikněte na tlačítko **funkce MPIO** kartu.
    
    ![StorSimple 8100 DeviceProp Disk s více cestami.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. V **DSM název** klikněte na tlačítko **podrobnosti** a ověřte, že parametry nastavené na výchozí parametry. Výchozí parametry jsou:
    
    * Cesta k ověření období = 30
    * Retry Count = 3
    * Odebrat PDO období = 20
    * Interval opakování = 1
    * Ověřte cestu povoleno = není zaškrtnuto.

> [!NOTE]
> **Neprovádějte žádné změny výchozích parametrů.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Pro vysokou dostupnost a vyrovnávání zatížení na základě více cest, více relací je nutné ručně přidat k deklarování různých cest k dispozici. Například pokud má hostitel dvě rozhraní připojené k síti iSCSI a zařízení má dvě rozhraní připojené k síti iSCSI, je nutné nakonfigurovat pomocí správné cesty permutací čtyřech relacích (pouze dvě relace se bude vyžadovat, pokud každý dat rozhraní a rozhraní hostitele je v jiné podsíti protokolu IP a není směrovatelný).

**Doporučujeme, abyste měli aspoň 8 paralelních aktivních relací mezi zařízením a vaší aplikační hostitel systému.** Toho lze dosáhnout tím, že 4 síťových rozhraní v systému Windows Server. Na úroveň hardwaru nebo operačního systému na hostiteli s Windows serverem pomocí fyzická síťová rozhraní nebo virtuální rozhraní pomocí technologie virtualizace sítě. Se dvěma síťovými rozhraními na zařízení tato konfigurace způsobí 8 aktivní relace. Tato konfigurace umožňuje optimalizovat propustnost zařízení a cloudu.

> [!IMPORTANT]
> **Doporučujeme vám, že jste Nekombinujte 1 GbE a rozhraní sítí 10 GbE. Pokud používáte dvě síťová rozhraní, obě rozhraní by měl být identické typu.**

Následující postup popisuje, jak přidat relace při připojení zařízení StorSimple se dvěma síťovými rozhraními na hostitele se dvěma síťovými rozhraními. To vám dává pouze 4 relace. Použijte stejný postup se zařízením StorSimple s dvě síťová rozhraní připojená k hostiteli čtyři síťových rozhraní. Je potřeba nakonfigurovat 8 namísto 4 relace je zde popsáno.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

1. Provést zjišťování cíle: v **vlastnosti iniciátoru iSCSI** dialogovém okně **zjišťování** klikněte na tlačítko **vyhledat portál**.
2. V **připojit k cíli** dialogového okna zadejte IP adresu jednoho ze síťových rozhraní zařízení.
3. Klikněte na tlačítko **OK** se vrátíte **vlastnosti iniciátoru iSCSI** dialogové okno.
4. V **vlastnosti iniciátoru iSCSI** dialogové okno, vyberte **cíle** kartu, zvýrazněte zjištěné cíle a pak klikněte na **připojit**. **Připojit k cíli** zobrazí se dialogové okno.
5. V **připojit k cíli** dialogové okno:
   
   1. Ponechte výchozí vybrané nastavení cíle pro **přidat toto připojení** do seznamu oblíbených cíle. To umožňuje zařízení automaticky pokusí o restart připojení pokaždé, když se tento počítač se restartuje.
   2. Vyberte **povolit více cest** zaškrtávací políčko.
   3. Klikněte na tlačítko **Advanced**.
6. V **Upřesnit nastavení** dialogové okno:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátoru iSCSI od Microsoftu**.
   2. Na **iniciátoru IP** rozevíracího seznamu vyberte IP adresu odpovídající první rozhraní na hostiteli (rozhraní iSCSI).
   3. Na **IP cílového portálu** rozevíracího seznamu vyberte IP adresu pro první na zařízení povolené rozhraní data.
   4. Klikněte na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI.
7. Klikněte na tlačítko **vlastnosti**a **vlastnosti** dialogové okno, klikněte na tlačítko **Přidat relaci**.
8. V **připojit k cíli** dialogové okno, vyberte **povolit více cest** zaškrtněte políčko a potom klikněte na tlačítko **Upřesnit**.
9. V **Upřesnit nastavení** dialogové okno:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátoru iSCSI od Microsoftu**.
   2. Na **iniciátoru IP** rozevíracího seznamu vyberte IP adresu odpovídající druhé rozhraní iSCSI na hostiteli.
   3. Na **IP cílového portálu** rozevíracího seznamu vyberte IP adresu pro druhé síťové rozhraní data na zařízení povolená.
   4. Klikněte na tlačítko **OK** se vrátíte **vlastnosti iniciátoru iSCSI** dialogové okno. Právě jste přidali druhý relace k cíli.
10. Opakujte kroky 8-10 přidat další relace (cest) k cíli. Dvě rozhraní na hostiteli a dva na zařízení můžete přidat celkem čtyři relace.
11. Po přidání požadovaná relace (cest), v **vlastnosti iniciátoru iSCSI** dialogové okno pole, vyberte cíl a klikněte na tlačítko **vlastnosti**. Na kartě relací **vlastnosti** dialogovém okně Poznámka čtyři relace identifikátory, které odpovídají permutací možné cesty. Zrušit relaci, zaškrtněte políčko vedle identifikátor relace a pak klikněte na tlačítko **odpojit**.
12. Chcete-li zobrazit zařízení zobrazí v rámci relací, vyberte **zařízení** kartu. Konfigurace zásad funkce MPIO pro vybrané zařízení, klikněte na tlačítko **funkce MPIO**. **Podrobnosti o zařízení** zobrazí se dialogové okno. Na **funkce MPIO** kartu, můžete vybrat odpovídající **zásady vyrovnávání zatížení** nastavení. Můžete také zobrazit **aktivní** nebo **pohotovostní** typ cesty.

## <a name="next-steps"></a>Další postup

Další informace o [upravit konfiguraci zařízení StorSimple pomocí služby Správce zařízení StorSimple](storsimple-8000-modify-device-config.md).

