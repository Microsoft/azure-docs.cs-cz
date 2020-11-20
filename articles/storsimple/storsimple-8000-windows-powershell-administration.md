---
title: PowerShell pro správu zařízení StorSimple
description: Naučte se používat Windows PowerShell pro StorSimple ke správě zařízení StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 65e9657c3948d8ce5883cd33ca8720f501352105
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950665"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Správa zařízení pomocí rozhraní Windows PowerShell pro StorSimple

## <a name="overview"></a>Přehled

Windows PowerShell pro StorSimple poskytuje rozhraní příkazového řádku, které můžete použít ke správě zařízení Microsoft Azure StorSimple. Jak název navrhuje, jedná se o rozhraní příkazového řádku založené na Windows PowerShellu, které je sestavené v omezeném prostředí runspace. V perspektivě uživatele v příkazovém řádku se omezené prostředí runspace jeví jako omezené verze Windows PowerShellu. Při zachování některých základních funkcí prostředí Windows PowerShell má toto rozhraní další vyhrazené rutiny, které jsou zaměřené na správu zařízení Microsoft Azure StorSimple.

Tento článek popisuje funkce Windows PowerShell pro StorSimple, včetně toho, jak se můžete připojit k tomuto rozhraní, a obsahuje odkazy na podrobné postupy nebo pracovní postupy, které můžete provádět pomocí tohoto rozhraní. Pracovní postupy zahrnují, jak zaregistrovat zařízení, nakonfigurovat síťové rozhraní na vašem zařízení, nainstalovat aktualizace, které vyžadují, aby bylo zařízení v režimu údržby, aby se změnil stav zařízení, a vyřešte všechny problémy, které se mohou vyskytnout.

Po přečtení tohoto článku budete moct:

* Připojte se k zařízení StorSimple pomocí Windows PowerShell pro StorSimple.
* Spravujte zařízení StorSimple pomocí Windows PowerShell pro StorSimple.
* Získejte pomoc v Windows PowerShell pro StorSimple.

> [!NOTE]
> * Rutiny Windows PowerShell pro StorSimple umožňují spravovat zařízení StorSimple pomocí sériové konzoly nebo vzdáleně pomocí vzdálené komunikace Windows PowerShellu. Další informace o jednotlivých rutinách, které se dají použít v tomto rozhraní, najdete v referenčních informacích k [rutině pro Windows PowerShell pro StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).
> * Rutiny Azure PowerShell StorSimple jsou jinou kolekcí rutin, které umožňují automatizovat úlohy na úrovni služby a migrace StorSimple z příkazového řádku. Další informace o rutinách Azure PowerShell pro StorSimple najdete v referenčních informacích k [rutinám Azure StorSimple](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


K Windows PowerShell pro StorSimple můžete přistupovat pomocí jedné z následujících metod:

* [Připojení ke konzole sériového portu zařízení StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Vzdálené připojení k StorSimple pomocí Windows PowerShellu](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Připojení k Windows PowerShell pro StorSimple přes konzolu sériového zařízení

Pro připojení k Windows PowerShell pro StorSimple si můžete [Stáhnout](https://www.putty.org/) nebo podobný software pro emulaci terminálu. Pro přístup k Microsoft Azure StorSimplemu zařízení je potřeba nakonfigurovat konkrétní. Následující témata obsahují podrobné pokyny ke konfiguraci výstupu a připojení k zařízení. Také jsou vysvětleny různé možnosti nabídky v konzole sériového portu.

### <a name="putty-settings"></a>Nastavení PuTTY

Pokud se chcete připojit k rozhraní Windows PowerShellu z konzoly sériového portu, ujistěte se, že používáte následující nastavení pro výstup.

#### <a name="to-configure-putty"></a>Konfigurace výstupu

1. V dialogovém okně **rekonfigurace** výstupu vyberte v podokně **kategorie** možnost **klávesnice**.
2. Ujistěte se, že jsou vybrány následující možnosti (při spuštění nové relace se jedná o výchozí nastavení).
   
   | Položka klávesnice | Vybrat |
   | --- | --- |
   | Klávesa Backspace |Ovládací prvek –? (127) |
   | Domovské a koncové klávesy |Standard |
   | Klávesy funkcí a klávesnice |ESC [n ~ |
   | Počáteční stav kláves kurzoru |Normální |
   | Počáteční stav numerické klávesnice |Normální |
   | Povolit dodatečné funkce klávesnice |Control-Alt se liší od klávesy AltGr. |
   
    ![Podporovaná nastavení výstupu](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klikněte na **Použít**.
4. V podokně **kategorie** vyberte možnost **Překlad**.
5. V poli se seznamem **vzdálených znakových sad** vyberte **UTF-8**.
6. V části **manipulace se znaky kreslení čáry** vyberte **použít body kódu pro vykreslování spojnice Unicode**. Na následujícím snímku obrazovky vidíte správné možnosti výstupu.
   
    ![Nastavení kódování UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klikněte na **Použít**.

K připojení ke konzole sériového portu zařízení teď můžete použít výstup pomocí následujících kroků.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>O konzole sériového portu

Při přístupu k rozhraní Windows PowerShellu zařízení StorSimple prostřednictvím konzoly sériového portu se zobrazí zpráva s nápisem, po kterém následují možnosti nabídky.

Zpráva banneru obsahuje základní informace o zařízení StorSimple, jako je model, název, verze nainstalovaného softwaru a stav řadiče, ke kterému přistupujete. Následující obrázek ukazuje příklad zprávy banneru.

![Zpráva sériového proužku](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Pomocí zprávy banner můžete zjistit, zda je řadič, ke kterému jste připojeni, _aktivní_ nebo _pasivní_.

Následující obrázek znázorňuje různé možnosti prostředí runspace, které jsou k dispozici v nabídce konzoly sériového prostředí.

![Registrace zařízení 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Můžete si vybrat z následujících nastavení:

1. **Přihlášení s úplným přístupem** Tato možnost umožňuje připojit (se správnými přihlašovacími údaji) k prostředí runspace **SSAdminConsole** na místním řadiči. (Místní řadič je kontroler, ke kterému aktuálně přistupujete přes sériovou konzolu zařízení StorSimple.) Tato možnost slouží taky k tomu, aby podpora Microsoftu mohl získat přístup k neomezenému prostředí runspace (relace podpory) a vyřešit případné problémy se zařízením. Po použití možnosti 1 pro přihlášení můžete podpora Microsoftu inženýrovi dovolit přístup k neomezenému prostředí runspace spuštěním konkrétní rutiny. Podrobnosti najdete v tématu [spuštění relace podpory](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Přihlášení k partnerskému řadiči s úplným přístupem** Tato možnost je stejná jako možnost 1, s tím rozdílem, že se můžete připojit (se správnými přihlašovacími údaji) do prostředí runspace pro **SSAdminConsole** na partnerském řadiči. Vzhledem k tomu, že zařízení StorSimple je zařízení s vysokou dostupností se dvěma řadiči v konfiguraci s aktivním pasivním vztahem, odkazuje na druhý kontroler v zařízení, ke kterému přistupujete prostřednictvím konzoly sériového portu.
   Podobně jako u možnosti 1 lze tuto možnost použít také k povolení přístupu podpora Microsoftu k neomezenému prostředí runspace na partnerském řadiči.

3. **Připojení s omezeným přístupem** Tato možnost slouží k přístupu k rozhraní Windows PowerShell v omezeném režimu. Nebudete vyzváni k zadání přihlašovacích údajů k přístupu. Tato možnost se v porovnání s možnostmi 1 a 2 připojuje k více dostupným prostředím runspace.  Některé úlohy, které jsou dostupné prostřednictvím možnosti 1, kterou **nejde* v tomto prostředí runspace provést:
   
   * Obnovit do továrního nastavení
   * Změnit heslo
   * Povolit nebo zakázat přístup k podpoře
   * Instalace aktualizací
   * Instalace oprav hotfix

     > [!NOTE]
     > Tato možnost je upřednostňovaná, pokud jste zapomněli heslo správce zařízení a nemůžete se připojit prostřednictvím možnosti 1 nebo 2.

4. **Změnit jazyk** Tato možnost umožňuje změnit jazyk zobrazení v rozhraní Windows PowerShell. Podporované jazyky jsou angličtina, japonština, ruština, francouzština, Jižní korejština, španělština, italština, němčina, čínština a portugalština.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Vzdálené připojení k StorSimple pomocí Windows PowerShell pro StorSimple

Pomocí vzdálené komunikace Windows PowerShellu se můžete připojit k zařízení StorSimple. Když se připojíte tímto způsobem, neuvidíte nabídku. (Nabídka se zobrazí jenom v případě, že se k připojení použije konzola sériového zařízení. Vzdálené připojení vás provede přímo na ekvivalent "možnosti 1 – úplný přístup" v konzole sériového portu.) Pomocí vzdálené komunikace Windows PowerShellu se připojíte ke konkrétnímu prostředí runspace. Můžete také zadat jazyk zobrazení.

Jazyk zobrazení je nezávislý na jazyku, který jste nastavili pomocí možnosti **změnit jazyk** v nabídce konzoly sériového portu. Vzdálené prostředí PowerShell automaticky vybere národní prostředí zařízení, ze kterého se připojujete, pokud není zadané žádné.

> [!NOTE]
> Pokud pracujete s Microsoft Azure virtuálních hostitelů a StorSimple cloudových zařízení, můžete použít vzdálenou komunikaci Windows PowerShellu a virtuálního hostitele pro připojení ke cloudovým zařízením. Pokud jste nastavili umístění sdílené složky na hostiteli, na kterém chcete ukládat informace z relace Windows PowerShellu, měli byste si uvědomit, že hlavní objekt _Everyone_ obsahuje jenom ověřené uživatele. Pokud jste nastavili sdílení tak, aby umožňovalo přístup _všem uživatelům_ , a Vy se připojíte bez zadání přihlašovacích údajů, použije se neověřený anonymní objekt zabezpečení a zobrazí se chyba. Pokud chcete tento problém vyřešit, na hostiteli sdílené složky musíte povolit účet Guest a pak mu přidělit úplný přístup ke sdílené složce nebo musíte zadat platné přihlašovací údaje spolu s rutinou Windows PowerShellu.


Pomocí protokolu HTTP nebo HTTPS se můžete připojit prostřednictvím vzdálené komunikace Windows PowerShellu. Postupujte podle pokynů v následujících kurzech:

* [Vzdálené připojení pomocí protokolu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Vzdálené připojení pomocí protokolu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Požadavky na zabezpečení připojení

Při rozhodování, jak se připojit k Windows PowerShell pro StorSimple, vezměte v úvahu následující skutečnosti:

* Přímé připojení ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není. Buďte opatrní v bezpečnostním riziku při připojování k zařízení pomocí síťových přepínačů.
* Připojení prostřednictvím relace HTTP může nabídnout větší zabezpečení než připojení prostřednictvím sériové konzoly přes síť. I když se nejedná o nejbezpečnější metodu, je přijatelné v důvěryhodných sítích.
* Připojení prostřednictvím relace HTTPS je nejbezpečnější a doporučená možnost.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Správa zařízení StorSimple pomocí Windows PowerShell pro StorSimple

Následující tabulka obsahuje souhrn všech běžných úloh správy a složitých pracovních postupů, které se dají provádět v rozhraní Windows PowerShell zařízení StorSimple. Chcete-li získat další informace o jednotlivých pracovních postupech, klikněte na příslušnou položku v tabulce.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Pracovní postupy Windows PowerShell pro StorSimple

| Pokud to chcete provést... | Použijte tento postup. |
| --- | --- |
| Registrace zařízení |[Konfigurace a registrace zařízení pomocí Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurace webového proxy serveru</br>Zobrazit nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro zařízení StorSimple](storsimple-8000-configure-web-proxy.md) |
| Úprava nastavení síťového rozhraní DATA 0 na zařízení |[Úprava síťového rozhraní DATA 0 pro zařízení StorSimple](storsimple-8000-modify-data-0.md) |
| Zastavení kontroleru </br> Restartování nebo vypnutí kontroleru </br> Vypnutí zařízení</br>Resetování zařízení do výchozího továrního nastavení |[Správa řadičů zařízení](storsimple-8000-manage-device-controller.md) |
| Nainstalovat aktualizace a opravy hotfix v režimu údržby |[Aktualizace zařízení](storsimple-update-device.md) |
| Zadat režim údržby </br>Ukončit režim údržby |[Režimy zařízení StorSimple](storsimple-8000-device-modes.md) |
| Vytvoření balíčku pro podporu</br>Dešifrování a úprava balíčku pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md) |
| Spustit relaci podpory</br> |[Spuštění relace podpory v Windows PowerShell pro StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Získat pomoc v Windows PowerShell pro StorSimple

V Windows PowerShell pro StorSimple je k dispozici nápovědě k rutinám. K dispozici je také online, aktualizovaná verze této aplikace, kterou můžete použít k aktualizaci sady Help v systému.

Získání pomocníka v tomto rozhraní je podobné jako v prostředí Windows PowerShell a většina rutin souvisejících s touto podporou bude fungovat. Nápovědu k prostředí Windows PowerShell online najdete v tématu [Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

Následuje stručný popis typů nápovědu pro toto rozhraní Windows PowerShell, včetně toho, jak aktualizovat nápovědu.

### <a name="to-get-help-for-a-cmdlet"></a>Získání nápovědu pro rutinu

* Nápovědu k jakékoli rutině nebo funkci získáte pomocí následujícího příkazu: `Get-Help <cmdlet-name>`
* Pokud chcete získat online nápovědu pro libovolnou rutinu, použijte předchozí rutinu s `-Online` parametrem: `Get-Help <cmdlet-name> -Online`
* Pro úplnou nápovědu můžete použít `–Full` parametr a příklady použít `–Examples` parametr.

### <a name="to-update-help"></a>Aktualizace pomocníka

V rozhraní Windows PowerShell můžete snadno aktualizovat tuto nápovědě. Chcete-li aktualizovat aplikaci v systému, proveďte následující kroky.

#### <a name="to-update-cmdlet-help"></a>Aktualizace pomocníka s rutinou
1. Spusťte prostředí Windows PowerShell s možností **Spustit jako správce** .
2. Do příkazového řádku zadejte:  `Update-Help`
3. Dojde k instalaci aktualizovaných souborů help.
4. Po instalaci souborů s příponou Help zadejte: `Get-Help Get-Command` . Zobrazí se seznam rutin, pro které je k dispozici nápovědu.

> [!NOTE]
> Pokud chcete získat seznam všech dostupných rutin v prostředí runspace, přihlaste se k odpovídající možnosti nabídky a spusťte `Get-Command` rutinu.


## <a name="next-steps"></a>Další kroky

Pokud při provádění některého z výše uvedených pracovních postupů dojde k problémům se zařízením s StorSimple, přečtěte si téma [Nástroje pro řešení potíží s nasazeními StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).