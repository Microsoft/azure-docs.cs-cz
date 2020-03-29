---
title: Prostředí PowerShell pro správu zařízení StorSimple
description: Přečtěte si, jak pomocí prostředí Windows PowerShell pro StorSimple spravovat zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7d59f00d655bc7b2395c46713a56f52c61ffa42c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277111"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Správa zařízení pomocí rozhraní Windows PowerShell pro StorSimple

## <a name="overview"></a>Přehled

Prostředí Windows PowerShell for StorSimple poskytuje rozhraní příkazového řádku, které můžete použít ke správě zařízení Microsoft Azure StorSimple. Jak název napovídá, jedná se o rozhraní příkazového řádku založené na prostředí Windows PowerShell, které je vytvořeno v omezeném runspace. Z pohledu uživatele na příkazovém řádku se omezený prostor runspace zobrazí jako omezená verze prostředí Windows PowerShell. Při zachování některých základních funkcí prostředí Windows PowerShell má toto rozhraní další vyhrazené rutiny, které jsou zaměřené na správu vašeho zařízení Microsoft Azure StorSimple.

Tento článek popisuje funkce prostředí Windows PowerShell for StorSimple, včetně toho, jak se můžete připojit k tomuto rozhraní, a obsahuje odkazy na podrobné postupy nebo pracovní postupy, které lze provádět pomocí tohoto rozhraní. Pracovní postupy zahrnují, jak zaregistrovat zařízení, nakonfigurovat síťové rozhraní v zařízení, nainstalovat aktualizace, které vyžadují, aby zařízení bylo v režimu údržby, změnit stav zařízení a řešit případné problémy.

Po přečtení tohoto článku budete moci:

* Připojte se k zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple.
* Spravujte zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple.
* Získejte nápovědu v prostředí Windows PowerShell pro StorSimple.

> [!NOTE]
> * Rutiny prostředí Windows PowerShell for StorSimple umožňují spravovat zařízení StorSimple ze sériové konzole nebo vzdáleně prostřednictvím vzdálené komunikace prostředí Windows PowerShell. Další informace o jednotlivých rutinách, které lze použít v tomto rozhraní, naleznete v [odkazu na rutinu prostředí Windows PowerShell for StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Rutiny Azure PowerShell StorSimple jsou různé kolekce rutin, které umožňují automatizovat úkoly na úrovni služby StorSimple a migrace z příkazového řádku. Další informace o rutinách Prostředí Azure PowerShell pro StorSimple najdete v [odkazu na rutinu Rutiny Azure StorSimple](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


K prostředí Windows PowerShell pro StorSimple můžete přistupovat jedním z následujících způsobů:

* [Připojení k sériové konzoli zařízení StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Vzdálené připojení k prostředí StorSimple pomocí prostředí Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Připojení k prostředí Windows PowerShell pro StorSimple prostřednictvím sériové konzole zařízení

Zde si můžete [stáhnout PuTTY](https://www.putty.org/) nebo podobný terminál emulace software pro připojení k Windows PowerShell pro StorSimple. Je třeba nakonfigurovat PuTTY speciálně pro přístup k zařízení Microsoft Azure StorSimple. Následující témata obsahují podrobné kroky o konfiguraci PuTTy a připojení k zařízení. Různé možnosti nabídky v konzole sériového portu jsou také vysvětleny.

### <a name="putty-settings"></a>Nastavení PuTTY

Ujistěte se, že používáte následující nastavení PuTTY pro připojení k rozhraní prostředí Windows PowerShell ze sériové konzole.

#### <a name="to-configure-putty"></a>Konfigurace PuTTY

1. V dialogovém okně **Změna konfigurace** PuTTY vyberte v podokně **Kategorie** **položku Klávesnice**.
2. Ujistěte se, že jsou vybrány následující možnosti (toto jsou výchozí nastavení při spuštění nové relace).
   
   | Položka klávesnice | Vyberte |
   | --- | --- |
   | Klávesa Backspace |Ovládání-? (127) |
   | Klíče Domů a Konec |Standard |
   | Funkční klávesy a klávesnice |ESC[n~ |
   | Počáteční stav kurzorových kláves |Normální |
   | Počáteční stav numerické klávesnice |Normální |
   | Povolení dalších funkcí klávesnice |Control-Alt se liší od AltGr |
   
    ![Podporovaná nastavení tmelu](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klikněte na **Použít**.
4. V podokně **Kategorie** vyberte **Překlad**.
5. V seznamu **Vzdálená sada znaků** vyberte **Možnost UTF-8**.
6. V části **Zpracování znaků kreslení čar**vyberte Použít body **kreslicího kódu čáry Unicode**. Následující snímek obrazovky ukazuje správné putty výběry.
   
    ![Nastavení tmelu UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klikněte na **Použít**.

Nyní můžete použít PuTTY pro připojení k sériové konzoli zařízení pomocí následujících kroků.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>O sériové konzole

Při přístupu k rozhraní prostředí Windows PowerShell zařízení StorSimple prostřednictvím sériové konzole se zobrazí zpráva s nápisem následovanou možnostmi nabídky.

Zpráva banneru obsahuje základní informace o zařízení StorSimple, jako je model, název, nainstalovaná verze softwaru a stav řadiče, ke které přistupujete. Následující obrázek ukazuje příklad bannerové zprávy.

![Sériová bannerová zpráva](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pomocí bannerové zprávy můžete určit, zda je řadič, ke který jste připojeni, _aktivní_ nebo _pasivní_.

Následující obrázek znázorňuje různé možnosti runspace, které jsou k dispozici v nabídce konzoly sériového portu.

![Registrace zařízení 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Můžete si vybrat z následujících nastavení:

1. **Přihlášení s plným přístupem** Tato možnost umožňuje připojit (se správnými přihlašovacími údaji) k runspace **SSAdminConsole** na místním řadiči. (Místní řadič je řadič, ke kterému aktuálně přistupujete prostřednictvím sériové konzole zařízení StorSimple.) Tuto možnost lze také použít k povolení podpory společnosti Microsoft pro přístup k neomezenému prostoru runspace (relace podpory) k řešení případných problémů se zařízením. Po použití možnosti 1 k přihlášení můžete povolit pracovníkovi podpory společnosti Microsoft přístup k neomezenému prostoru runspace spuštěním určité rutiny. Podrobnosti naleznete [v](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)části Spuštění relace podpory .
   
2. **Přihlášení k peer řadiči s úplným přístupem** Tato možnost je stejná jako možnost 1, s tím rozdílem, že se můžete připojit (se správnými přihlašovacími údaji) k runspace **SSAdminConsole** na řadiči druhé strany. Vzhledem k tomu, že zařízení StorSimple je zařízení s vysokou dostupností se dvěma řadiči v konfiguraci aktivní pasivní, partner odkazuje na jiný řadič v zařízení, ke kterému přistupujete prostřednictvím konzoly serial).
   Podobně jako možnost 1, tuto možnost lze také povolit podporu společnosti Microsoft pro přístup k neomezené runspace na peer řadič.

3. **Připojte se s omezeným přístupem** Tato možnost se používá pro přístup k rozhraní prostředí Windows PowerShell v omezeném režimu. Nebudete vyzváni k zadání přístupových pověření. Tato možnost se připojí k omezenějšímu prostoru runspace ve srovnání s možnostmi 1 a 2.  Některé z úloh, které jsou k dispozici prostřednictvím možnosti 1, která **nelze* provést v tomto prostoru runspace jsou:
   
   * Obnovení továrního nastavení
   * Změna hesla
   * Povolení nebo zakázání přístupu podpory
   * Instalace aktualizací
   * Instalace oprav hotfix

     > [!NOTE]
     > Toto je upřednostňovaná možnost, pokud jste zapomněli heslo správce zařízení a nemůžete se připojit prostřednictvím možnosti 1 nebo 2.

4. **Změna jazyka** Tato možnost umožňuje změnit jazyk zobrazení v rozhraní prostředí Windows PowerShell. Podporované jazyky jsou angličtina, japonština, ruština, francouzština, jihokorejská, španělská, italská, německá, čínská a brazilská portugalština.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Vzdáleně se připojte k prostředí StorSimple pomocí prostředí Windows PowerShell pro StorSimple

Vzdálené připojení prostředí Windows PowerShell můžete použít k připojení k zařízení StorSimple. Když se připojíte tímto způsobem, nezobrazí se nabídka. (Nabídku se zobrazí pouze v případě, že k připojení používáte sériovou konzolu na zařízení. Vzdálené připojení vás zavede přímo na ekvivalent "možnost i – plný přístup" na sériové konzoli.) S windows powershellovou vzdálené komunikace se připojíte ke konkrétnímu runspace. Můžete také určit jazyk zobrazení.

Jazyk zobrazení je nezávislý na jazyku, který jste nastavili pomocí možnosti **Změnit jazyk** v nabídce sériové konzole. Vzdálené prostředí PowerShell automaticky převezme národní prostředí zařízení, ze které se připojujete, pokud není zadáno žádné.

> [!NOTE]
> Pokud pracujete s virtuálními hostiteli Microsoft Azure a StorSimple Cloud Appliances, můžete se připojit ke cloudovému zařízení pomocí vzdálené komunikace prostředí Windows PowerShell a virtuálního hostitele. Pokud jste nastavili umístění sdílené složky na hostiteli, na kterém chcete uložit informace z relace prostředí Windows PowerShell, měli byste si být vědomi toho, že _objekt Zabezpečení Everyone_ zahrnuje pouze ověřené uživatele. Proto pokud jste nastavili sdílenou složku povolit přístup _Everyone_ a připojit bez zadání pověření, neověřené anonymní objekt zabezpečení bude použit a zobrazí se chyba. Chcete-li tento problém vyřešit, na hostiteli sdílené složky musíte povolit účet Guest a pak udělit účet Guest úplný přístup ke sdílené položce nebo je nutné zadat platná pověření spolu s rutinou prostředí Windows PowerShell.


Pomocí protokolu HTTP nebo HTTPS se můžete připojit prostřednictvím vzdálené komunikace prostředí Windows PowerShell. Postupujte podle pokynů v následujících kurzech:

* [Vzdálené připojení pomocí protokolu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Vzdálené připojení pomocí protokolu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Důležité informace o zabezpečení připojení

Při rozhodování o tom, jak se připojit k prostředí Windows PowerShell pro StorSimple, zvažte následující:

* Připojení přímo k sériové konzoli zařízení je bezpečné, ale připojení k konzoli sériového zařízení prostředzací přes síťové přepínače není. Při připojování k sériovému zařízení přes síťové přepínače buďte opatrní před bezpečnostním rizikem.
* Připojení prostřednictvím relace PROTOKOLU HTTP může nabídnout větší zabezpečení než připojení prostřednictvím sériové konzoly prostřednictvím sítě. I když se nejedná o nejbezpečnější metodu, je přijatelná v důvěryhodných sítích.
* Připojení prostřednictvím relace HTTPS je nejbezpečnější a doporučená možnost.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Správa zařízení StorSimple pomocí prostředí Windows PowerShell pro StorSimple

V následující tabulce je uveden souhrn všech běžných úloh správy a složitých pracovních postupů, které lze provádět v rámci rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple. Další informace o jednotlivých pracovních postupech získáte klepnutím na příslušnou položku v tabulce.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell pro pracovní postupy StorSimple

| Pokud chcete udělat ... | Použijte tento postup. |
| --- | --- |
| Registrace zařízení |[Konfigurace a registrace zařízení pomocí prostředí Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurace webového proxy serveru</br>Zobrazení nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro zařízení StorSimple](storsimple-8000-configure-web-proxy.md) |
| Změna nastavení síťového rozhraní DATA 0 v zařízení |[Úprava síťového rozhraní DATA 0 pro zařízení StorSimple](storsimple-8000-modify-data-0.md) |
| Zastavení ovladače </br> Restartování nebo vypnutí ovladače </br> Vypnutí zařízení</br>Resetování zařízení do výchozího továrního nastavení |[Správa řadičů zařízení](storsimple-8000-manage-device-controller.md) |
| Instalace aktualizací režimu údržby a oprav hotfix |[Aktualizace zařízení](storsimple-update-device.md) |
| Vstup do režimu údržby </br>Ukončit režim údržby |[Režimy zařízení StorSimple](storsimple-8000-device-modes.md) |
| Vytvoření balíčku podpory</br>Dešifrování a úprava balíčku podpory |[Vytvoření a správa balíčku podpory](storsimple-8000-create-manage-support-package.md) |
| Zahájení relace podpory</br> |[Spuštění relace podpory v prostředí Windows PowerShell pro StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Získání nápovědy v prostředí Windows PowerShell pro StorSimple

V prostředí Windows PowerShell pro StorSimple je k dispozici nápověda rutiny. K dispozici je také online a aktuální verze této nápovědy, kterou můžete použít k aktualizaci nápovědy v systému.

Získání nápovědy v tomto rozhraní je podobné jako v prostředí Windows PowerShell a většina rutin souvisejících s nápovědou bude fungovat. Nápovědu k prostředí Windows PowerShell najdete online: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Následuje stručný popis typů nápovědy pro toto rozhraní prostředí Windows PowerShell, včetně aktualizace nápovědy.

### <a name="to-get-help-for-a-cmdlet"></a>Získání nápovědy k rutině

* Chcete-li získat nápovědu pro libovolnou rutinu nebo funkci, použijte následující příkaz:`Get-Help <cmdlet-name>`
* Chcete-li získat online nápovědu pro libovolnou rutinu, použijte předchozí rutinu s parametrem: `-Online``Get-Help <cmdlet-name> -Online`
* Pro úplnou nápovědu `–Full` můžete použít parametr a například parametr. `–Examples`

### <a name="to-update-help"></a>Aktualizace nápovědy

Nápovědu můžete snadno aktualizovat v rozhraní prostředí Windows PowerShell. Chcete-li aktualizovat nápovědu v systému, proveďte následující kroky.

#### <a name="to-update-cmdlet-help"></a>Aktualizace nápovědy k rutině
1. Spusťte prostředí Windows PowerShell s možností **Spustit jako správce.**
2. Na příkazovém řádku zadejte:`Update-Help`
3. Budou nainstalovány aktualizované soubory nápovědy.
4. Po instalaci souborů nápovědy `Get-Help Get-Command`zadejte: . Zobrazí se seznam rutin, pro které je k dispozici nápověda.

> [!NOTE]
> Chcete-li získat seznam všech dostupných rutin v prostoru runspace, přihlaste `Get-Command` se k odpovídající možnosti nabídky a spusťte rutinu.


## <a name="next-steps"></a>Další kroky

Pokud při provádění některého z výše uvedených pracovních postupů dojde k problémům se zařízením StorSimple, přečtěte si [informace o nástrojích pro řešení potíží s nasazeními StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

