---
title: Konfigurace funkce MPIO pro zařízení StorSimple | Microsoft Docs
description: Popisuje postup konfigurace funkce Multipath I/O (MPIO) pro zařízení StorSimple připojené k hostitele se systémem Windows Server 2012 R2.
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
ms.openlocfilehash: 4f2b094604f486d283574f4669fcad6f72bd4431
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="configure-multipath-io-for-your-storsimple-device"></a>Konfigurace funkce Multipath I/O pro zařízení StorSimple

Tento kurz popisuje kroky, postupujte podle nainstalovat a použít funkci Multipath I/O (MPIO) na hostitele se systémem Windows Server 2012 R2 a připojené k fyzického zařízení StorSimple. Pokyny v tomto článku se vztahuje na řady StorSimple 8000 jenom fyzické zařízení. Funkce MPIO není aktuálně podporováno v zařízení s StorSimple cloudu.

Společnost Microsoft vyvinula podporu pro funkci Multipath I/O (MPIO) ve Windows serveru k sestavení konfigurace iSCSI s vysokou dostupností, odolný proti chybám sítě. Funkce MPIO používá redundantní komponenty fyzických cesty – adaptéry, kabely a přepínače – k vytvoření logických cest mezi serverem a paměťovým zařízením. Pokud dojde k selhání součásti způsobuje logickou cestu k selhání, logika více cest použije pro vstupně-výstupní operace alternativní cestu, tak, aby aplikace můžete stále přístup k datům. Kromě toho v závislosti na vaší konfiguraci funkce MPIO lze také vylepšit výkon vyrovnává zatížení mezi tyto cesty. Další informace najdete v tématu [Přehled funkce MPIO](https://technet.microsoft.com/library/cc725907.aspx "MPIO přehled a funkce").

Vysoká-dostupnosti vašeho řešení StorSimple musí být funkce MPIO nakonfigurovaný v zařízení StorSimple. Při instalaci funkce MPIO na hostitelských serverech s Windows serverem 2012 R2 servery tolerovat klikněte na odkaz, sítě nebo selhání rozhraní.

## <a name="mpio-configuration-summary"></a>Souhrn konfigurace funkce MPIO

Funkce MPIO je volitelná funkce v systému Windows Server a není nainstalována ve výchozím nastavení. Je nutné ji nainstalovat jako funkci pomocí Správce serveru.

Použijte následující postup konfigurace funkce MPIO na zařízení StorSimple:

* Krok 1: Instalace funkce MPIO na hostitele Windows serveru
* Krok 2: Konfigurace funkce MPIO pro svazky zařízení StorSimple
* Krok 3: Připojení StorSimple svazky na hostiteli
* Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Všechny předchozí kroky popsané v následujících částech.

## <a name="step-1-install-mpio-on-the-windows-server-host"></a>Krok 1: Instalace funkce MPIO na hostitele Windows serveru

K instalaci této funkce na hostiteli s Windows Server, proveďte následující postup.

#### <a name="to-install-mpio-on-the-host"></a>K instalaci funkce MPIO na hostiteli

1. Otevřete správce serveru na hostiteli s Windows Server. Ve výchozím nastavení správce serveru spustí, když se člen skupiny Administrators přihlásí k počítači se systémem Windows Server 2012 R2 nebo Windows Server 2012. Pokud správce serveru už není otevřený, klikněte na tlačítko **Start > Správce serveru**.
   
   ![Správce serveru](./media/storsimple-configure-mpio-windows-server/IC740997.png)

2. Klikněte na tlačítko **správce serveru > řídicí panel > Přidat role a funkce**. Tím se spustí **přidat role a funkce** průvodce.
   
   ![Přidat role a funkce Průvodce 1](./media/storsimple-configure-mpio-windows-server/IC740998.png)
3. V **přidat role a funkce** průvodce proveďte následující kroky:
   
   1. Na **před zahájením** klikněte na tlačítko **Další**.
   2. Na **vybrat typ instalace** přijměte výchozí nastavení **na základě rolí nebo na základě funkcí** instalace. Klikněte na **Další**.
   
       ![Přidat role a funkce Průvodce 2](./media/storsimple-configure-mpio-windows-server/IC740999.png)
   3. Na **vybrat cílový server** vyberte **vyberte server z fondu serverů**. Hostitelský server by měly být zjištěny automaticky. Klikněte na **Další**.
   4. Na **vybrat role serveru** klikněte na tlačítko **Další**.
   5. Na **vyberte funkce,** vyberte **funkce Multipath I/O**a klikněte na tlačítko **Další**.
   
       ![Přidat role a funkce Průvodce 5](./media/storsimple-configure-mpio-windows-server/IC741000.png)
   6. Na **potvrdit vybrané možnosti instalace** , potvrďte výběr a potom vyberte **cílový server automaticky restartovat, pokud je to nutné**, jak je uvedeno níže. Klikněte na **Nainstalovat**.
   
       ![Přidat role a funkce Průvodce 8](./media/storsimple-configure-mpio-windows-server/IC741001.png)
   7. Upozornění se zobrazí po dokončení instalace. Průvodce zavřete kliknutím na **Zavřít**.
   
       ![Přidat role a funkce Průvodce 9](./media/storsimple-configure-mpio-windows-server/IC741002.png)

## <a name="step-2-configure-mpio-for-storsimple-volumes"></a>Krok 2: Konfigurace funkce MPIO pro svazky zařízení StorSimple

Funkce MPIO musí být nakonfigurované k identifikaci svazky zařízení StorSimple. Konfigurace funkce MPIO rozpoznat svazky zařízení StorSimple, proveďte následující kroky.

#### <a name="to-configure-mpio-for-storsimple-volumes"></a>Konfigurace funkce MPIO pro svazky zařízení StorSimple

1. Otevřete **konfigurace funkce MPIO**. Klikněte na tlačítko **správce serveru > řídicí panel > Nástroje > funkce MPIO**.
2. V **MPIO vlastnosti** dialogové okno, vyberte **zjistit více cest** kartě.
3. Vyberte **přidat podporu pro zařízení iSCSI**a potom klikněte na **přidat**.  
   ![Funkce MPIO vlastnosti zjistit více cest](./media/storsimple-configure-mpio-windows-server/IC741003.png)
4. Restartujte server po zobrazení výzvy.
5. V **MPIO vlastnosti** dialogové okno, klikněte na tlačítko **MPIO zařízení** kartě. Klikněte na tlačítko **přidat**.
    </br>![Funkce MPIO vlastnosti MPIO zařízení](./media/storsimple-configure-mpio-windows-server/IC741004.png)
6. V **přidat podporu funkce MPIO** dialogovém **ID hardwaru zařízení**, zadejte sériové číslo zařízení. Sériové číslo zařízení získáte přístup k službě StorSimple Manager zařízení. Přejděte na **zařízení > řídicí panel**. Sériové číslo zařízení se zobrazí v pravém **rychlý přehled** zařízení na řídicím panelu.
    </br>
    ![Přidání podpory funkce MPIO](./media/storsimple-configure-mpio-windows-server/IC741005.png)
7. Restartujte server po zobrazení výzvy.

## <a name="step-3-mount-storsimple-volumes-on-the-host"></a>Krok 3: Připojení StorSimple svazky na hostiteli

Po dokončení konfigurace funkce MPIO v systému Windows Server, svazky, které jsou vytvořené v zařízení StorSimple může být připojen a pak můžete využít výhod funkce MPIO pro redundanci. Připojení svazku, proveďte následující kroky.

#### <a name="to-mount-volumes-on-the-host"></a>Chcete-li připojit svazky na hostiteli

1. Otevřete **vlastnosti iniciátoru iSCSI** okno na hostitele Windows serveru. Klikněte na tlačítko **správce serveru > řídicí panel > Nástroje > iniciátor iSCSI**.
2. V **vlastnosti iniciátoru iSCSI** dialogové okno, klikněte na kartu zjišťování a pak klikněte na tlačítko **zjistit cílový portál**.
3. V **zjistit cílový portál** dialogové okno pole, proveďte následující kroky:
   
   1. Zadejte IP adresu DATA portu zařízení StorSimple (například zadejte DATA 0).
   2. Klikněte na tlačítko **OK** se vrátíte do **vlastnosti iniciátoru iSCSI** dialogové okno.
     
     > [!IMPORTANT]
     > **Pokud používáte privátní sítě pro připojení iSCSI, zadejte IP adresu portu DATA, která je připojena k privátní síti.**
    
4. Opakujte kroky 2 až 3 pro druhý rozhraní sítě (například DATA 1) na zařízení. Uvědomte si, že tato rozhraní mají být povoleny pro iSCSI. Další informace najdete v tématu [upravit síťových rozhraní](storsimple-8000-modify-device-config.md#modify-network-interfaces).
5. Vyberte **cíle** ve **vlastnosti iniciátoru iSCSI** dialogové okno. Měli byste vidět zařízení StorSimple cíle IQN pod **zjištěné cíle**.

   ![Karta cíle vlastnosti iniciátoru iSCSI](./media/storsimple-configure-mpio-windows-server/IC741007.png)
   
6. Klikněte na tlačítko **Connect** k vytvoření relace iSCSI s vaším zařízením StorSimple. A **připojit k cíli** zobrazí se dialogové okno.
7. V **připojit k cíli** dialogové okno, vyberte **povolit více cestami** zaškrtávací políčko. Klikněte na tlačítko **rozšířené**.
8. V **Upřesnit nastavení** dialogové okno pole, proveďte následující kroky:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. Na **iniciátor IP** rozevíracího seznamu vyberte IP adresu hostitele.
   3. Na **cílový portál** IP rozevíracího seznamu vyberte IP rozhraní zařízení.
   4. Klikněte na tlačítko **OK** se vrátíte do **vlastnosti iniciátoru iSCSI** dialogové okno.
9. Klikněte na **Vlastnosti**. V **vlastnosti** dialogové okno, klikněte na tlačítko **Přidat relaci**.
10. V **připojit k cíli** dialogové okno, vyberte **povolit více cestami** zaškrtávací políčko. Klikněte na tlačítko **rozšířené**.
11. V **Upřesnit nastavení** dialogové okno:

    1. Na **místní adaptér** rozevíracího seznamu vyberte iniciátor iSCSI společnosti Microsoft.
    2. Na **iniciátor IP** rozevíracího seznamu vyberte IP adresu odpovídající hostitele. V takovém případě jsou připojení dvě síťová rozhraní na zařízení k jedno síťové rozhraní na hostiteli. Proto toto rozhraní je stejný jako zadaná pro první relaci.
    3. Na **portál cílová IP adresa** rozevíracího seznamu, vyberte možnost IP adresa pro rozhraní druhý data v zařízení povolená.
    4. Klikněte na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI. Druhý relaci jste přidali k cíli.
12. Otevřete **Správa počítače** přechodem na **správce serveru > řídicí panel > Správa počítače**. V levém podokně klikněte na **úložiště > Správa disků**. Svazek vytvořený v zařízení StorSimple, které jsou viditelné pro tohoto hostitele se zobrazí pod **Správa disků** jako nové disky.
13. Inicializujte disk a vytvořte nový svazek. Během procesu formátu vyberte velikost bloku 64 KB.
    
    ![Správa disků](./media/storsimple-configure-mpio-windows-server/IC741008.png)
14. V části **Správa disků**, klikněte pravým tlačítkem myši **disku** a vyberte **vlastnosti**.
15. V modelu StorSimple ### **vlastnosti zařízení s více cestami disku** dialogové okno, klikněte na tlačítko **MPIO** kartě.
    
    ![Disk s více cestami DeviceProp StorSimple 8100.](./media/storsimple-configure-mpio-windows-server/IC741009.png)
16. V **DSM název** klikněte na tlačítko **podrobnosti** a ověřte, zda jsou parametry nastavena na výchozí parametry. Výchozí parametry jsou:
    
    * Cestu ověřte období = 30
    * Počet opakování = 3
    * PDO odebrat období = 20
    * Interval opakování = 1
    * Ověřte cestu povolené = není zaškrtnuto.

> [!NOTE]
> **Neměňte výchozí parametry.**


## <a name="step-4-configure-mpio-for-high-availability-and-load-balancing"></a>Krok 4: Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

Pro vysokou dostupnost a vyrovnávání zatížení na základě více cestami, je nutné více relací ručně přidat deklarovat různé cesty, které jsou k dispozici. Například pokud má hostitel dvě rozhraní připojené k síti iSCSI a zařízení má dvě rozhraní, které jsou připojené k síti iSCSI, pak je nutné nakonfigurovat pomocí správné cesty permutací čtyři relací (pouze dvě relace se bude vyžadovat, pokud každý rozhraní dat a rozhraní hostitel je v jiné podsíti protokolu IP a není směrovatelné).

**Doporučujeme, abyste měli aspoň 8 paralelních aktivních relací mezi zařízením a vaší hostitele aplikací.** Toho lze dosáhnout povolením 4 rozhraní sítě systému Windows Server. Použití fyzická síťová rozhraní nebo virtuální rozhraní pomocí technologie virtualizace sítě na úrovni hardwaru nebo operačního systému na hostiteli s Windows Server. Tato konfigurace se dvěma síťovými rozhraními na zařízení, by způsobilo 8 aktivních relací. Tato konfigurace pomáhá optimalizovat výkon zařízení a cloudu.

> [!IMPORTANT]
> **Doporučujeme vám, že nemíchat 1 GbE a 10 GbE síťových rozhraní. Pokud používáte dvě síťová rozhraní, by měla být identické typ obě rozhraní.**

Následující postup popisuje, jak přidat relace při připojení zařízení StorSimple se dvěma síťovými rozhraními na hostitele se dvěma síťovými rozhraními. To vám dává jenom 4 relací. Použijte stejný postup s zařízení StorSimple se dvěma síťovými rozhraními připojený k hostiteli s čtyři síťových rozhraní. Musíte nakonfigurovat 8 namísto 4 relací, které jsou zde popsané.

### <a name="to-configure-mpio-for-high-availability-and-load-balancing"></a>Konfigurace funkce MPIO pro zajištění vysoké dostupnosti a vyrovnávání zatížení

1. Provedení zjišťování cíle: v **vlastnosti iniciátoru iSCSI** v dialogovém **zjišťování** , klikněte na **vyhledat portál**.
2. V **připojit k cíli** dialogovém okně zadejte IP adresu jednoho z rozhraní zařízení sítě.
3. Klikněte na tlačítko **OK** se vrátíte do **vlastnosti iniciátoru iSCSI** dialogové okno.
4. V **vlastnosti iniciátoru iSCSI** dialogové okno, vyberte **cíle** , zvýrazněte zjištěné cíle a pak klikněte **Connect**. **Připojit k cíli** zobrazí se dialogové okno.
5. V **připojit k cíli** dialogové okno:
   
   1. Ponechejte výchozí nastavení vybrané nastavení cíl pro **přidat toto připojení** do seznamu oblíbených položek cíle. Díky tomu zařízení automaticky pokusí o restart připojení pokaždé, když se tento počítač restartuje.
   2. Vyberte **povolit více cestami** zaškrtávací políčko.
   3. Klikněte na tlačítko **rozšířené**.
6. V **Upřesnit nastavení** dialogové okno:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. Na **iniciátor IP** rozevíracího seznamu vyberte IP adresu odpovídající první rozhraní na hostiteli (rozhraní iSCSI).
   3. Na **portál cílová IP adresa** rozevíracího seznamu, vyberte možnost IP adresa pro rozhraní první data v zařízení povolená.
   4. Klikněte na tlačítko **OK** se vraťte do dialogového okna Vlastnosti iniciátoru iSCSI.
7. Klikněte na tlačítko **vlastnosti**a v **vlastnosti** dialogové okno, klikněte na tlačítko **Přidat relaci**.
8. V **připojit k cíli** dialogové okno, vyberte **povolit více cestami** zaškrtněte políčko a potom klikněte na **Upřesnit**.
9. V **Upřesnit nastavení** dialogové okno:
   
   1. Na **místní adaptér** rozevíracího seznamu vyberte **iniciátor iSCSI společnosti Microsoft**.
   2. Na **iniciátor IP** rozevíracího seznamu vyberte IP adresu odpovídající druhý rozhraní iSCSI, na hostiteli.
   3. Na **portál cílová IP adresa** rozevíracího seznamu, vyberte možnost IP adresa pro rozhraní druhý data v zařízení povolená.
   4. Klikněte na tlačítko **OK** se vrátíte do **vlastnosti iniciátoru iSCSI** dialogové okno. Nyní jste přidali druhý relace k cíli.
10. Opakujte kroky 8-10 přidat další relace (cest) k cíli. Dvě rozhraní na hostiteli a dva na zařízení můžete přidat celkem čtyři relací.
11. Po přidání požadovaných relací (cest), v **vlastnosti iniciátoru iSCSI** dialogovém okně vyberte cíl a klikněte na tlačítko **vlastnosti**. Na kartě relací **vlastnosti** dialogové okno, Poznámka čtyři relace identifikátory, které odpovídají permutací možné cesta. Zrušit relaci, zaškrtněte políčko vedle identifikátor relace a potom klikněte na **odpojení**.
12. Chcete-li zobrazit zařízení uvedené v rámci relací, vyberte **zařízení** kartě. Ke konfiguraci funkce MPIO zásady pro vybrané zařízení, klikněte na tlačítko **MPIO**. **Podrobnosti o zařízení** zobrazí se dialogové okno. Na **MPIO** kartě můžete vybrat odpovídající **zásad vyrovnávání zatížení** nastavení. Můžete také zobrazit **Active** nebo **pohotovostní** typ cesty.

## <a name="next-steps"></a>Další postup

Další informace o [změňte konfiguraci zařízení StorSimple pomocí služby StorSimple Manager zařízení](storsimple-8000-modify-device-config.md).

