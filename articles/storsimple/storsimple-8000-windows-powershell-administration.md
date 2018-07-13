---
title: Prostředí PowerShell pro správu zařízení StorSimple | Dokumentace Microsoftu
description: Další informace o použití prostředí Windows PowerShell pro StorSimple ke správě zařízení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli@microsoft.com
ms.openlocfilehash: 291de21d3d98bf9eb65adc7d506f3549171ee0a0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38696816"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Správa zařízení pomocí Windows Powershellu pro StorSimple

## <a name="overview"></a>Přehled

Windows PowerShell pro StorSimple nabízí rozhraní příkazového řádku, který můžete použít ke správě zařízení Microsoft Azure StorSimple. Jak název napovídá, je založené na prostředí Windows PowerShell, rozhraní příkazového řádku, který je součástí prostředí runspace omezeným. Z pohledu uživatele na příkazovém řádku prostředí runspace omezeným se zobrazí jako omezená verze prostředí Windows PowerShell. Při zachování některé základní funkce Windows Powershellu, toto rozhraní má další jednoúčelové rutiny, které jsou zaměřené na správě zařízení Microsoft Azure StorSimple.

Tento článek popisuje prostředí Windows PowerShell pro StorSimple funkcí, včetně toho, jak můžete připojit k tomuto rozhraní a obsahuje odkazy na podrobné postupy nebo pracovní postupy, které můžete provést pomocí tohoto rozhraní. Pracovní postupy popsané v registraci vašeho zařízení, konfigurace síťového rozhraní na zařízení, instalace aktualizací, které vyžadují zařízení v režimu údržby, změnit stav zařízení, a vyřešte všechny problémy, které mohou nastat.

Po přečtení tohoto článku, budete moct:

* Připojte se k zařízení StorSimple pomocí Windows Powershellu pro StorSimple.
* Spravovat zařízení StorSimple pomocí Windows Powershellu pro StorSimple.
* Získejte pomoc ve Windows Powershellu pro StorSimple.

> [!NOTE]
> * Prostředí Windows PowerShell pro StorSimple rutiny umožňují spravovat zařízení StorSimple z konzoly sériového portu nebo vzdáleně přes vzdálenou komunikaci prostředí Windows PowerShell. Další informace o všech jednotlivých rutin, které lze použít v tomto rozhraní naleznete v části [referenční informace o rutinách prostředí Windows PowerShell pro StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Rutiny Azure Powershellu StorSimple jsou jinou kolekci rutin, které umožňují automatizovat StorSimple úrovně služby a úlohy migrace z příkazového řádku. Další informace o rutinách prostředí Azure PowerShell pro StorSimple, přejděte [Reference k rutinám Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


Lze použít rutinu prostředí Windows PowerShell pro StorSimple pomocí jedné z následujících metod:

* [Připojení ke konzole sériového portu zařízení StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Vzdálené připojení k řešení StorSimple pomocí Windows Powershellu](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Připojení k prostředí Windows PowerShell pro StorSimple prostřednictvím konzoly sériového portu zařízení

Je možné [stáhněte si PuTTY](http://www.putty.org/) nebo podobné software pro emulaci terminálu pro připojení k prostředí Windows PowerShell pro StorSimple. Musíte nakonfigurovat PuTTY speciálně pro přístup k zařízení Microsoft Azure StorSimple. Následující témata obsahují podrobné pokyny o tom, jak konfigurace PuTTy a připojení k zařízení. Také jsou vysvětlené různé možnosti nabídky v konzole sériového portu.

### <a name="putty-settings"></a>Nastavení PuTTY

Ujistěte se, použijte následující nastavení PuTTY k připojení k rozhraní Windows PowerShell z konzoly sériového portu.

#### <a name="to-configure-putty"></a>Konfigurace PuTTY

1. V PuTTY **Rekonfigurace** v dialogu **kategorie** vyberte **klávesnice**.
2. Ujistěte se, zda jsou vybrány následující možnosti (jedná se o výchozí nastavení, když spustíte novou relaci).
   
   | Položka klávesnice | Vyberte |
   | --- | --- |
   | Klávesa BACKSPACE |Ovládací prvek-? (127) |
   | Domovská stránka a End klíče |Standard |
   | Funkční klávesy a klávesnici |ESC [n ~ |
   | Počáteční stav kurzoru klíče |Normální |
   | Počáteční stav numerické klávesnice |Normální |
   | Povolení funkcí navíc klávesnice |CTRL + ALT + se liší od AltGr |
   
    ![Podporovaná nastavení Putty](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klikněte na tlačítko **Použít**.
4. V **kategorie** vyberte **překlad**.
5. V **Vzdálená znaková sada** pole se seznamem, vyberte **UTF-8**.
6. V části **zpracování znaků Perokresba**vyberte **body kódu Unicode pomocí Perokresba**. Následující snímek obrazovky ukazuje správný výběr PuTTY.
   
    ![Nastavení Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klikněte na tlačítko **Použít**.

Nyní můžete použití klienta PuTTY k připojení ke konzole sériového portu zařízení podle následujícího postupu.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Informace o konzole sériového portu

Při přístupu k Windows Powershellu se zobrazí rozhraní zařízení StorSimple prostřednictvím konzoly sériového portu, zpráva hlavičky, za nímž následuje možnosti nabídky.

Zpráva hlavičky obsahuje základní informace zařízení StorSimple jako je model, název, verze nainstalovaného softwaru a stav řadiče, ke kterému přistupujete. Následující obrázek ukazuje příklad hlavičky zprávy.

![Zpráva hlavičky sériového portu](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Můžete zjistit, jestli jste připojení k řadiči zprávě _aktivní_ nebo _pasivní_.

Následující obrázek ukazuje různé možnosti prostředí runspace, které jsou k dispozici v nabídce konzoly sériového portu.

![Registrace zařízení 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Můžete vybrat z následujících nastavení:

1. **Přihlaste se pomocí úplný přístup** této možnosti lze připojit k (se správnými přihlašovacími údaji) **SSAdminConsole** prostředí runspace na místním řadiči. (Je místní řadič kontroler, který jste právě používají prostřednictvím konzoly sériového portu zařízení StorSimple.) Tuto možnost lze také povolit Microsoft Support pro přístup k neomezené prostředí runspace (relaci) k řešení potíží, je to možné zařízení. Když použijete možnost 1 pro přihlášení, můžete povolit Microsoft Support technik pro přístup k neomezené prostředí runspace spuštěním ke konkrétní rutině. Podrobnosti najdete v [spustit relaci](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Přihlaste se k řadiči sdílené s úplným přístupem** tato možnost je stejná jako možnost 1, s tím rozdílem, že se můžete připojit (se správnými přihlašovacími údaji) k **SSAdminConsole** prostředí runspace řadiči partnera. Protože zařízení StorSimple je zařízení s dva řadiče v konfiguraci aktivní pasivní vysokou dostupnost, peer odkazuje na druhý kontroler v zařízení, ke které přistupujete prostřednictvím konzoly sériového portu).
   Podobně jako možnost 1, tato možnost také umožňuje povolit Microsoft Support pro přístup k neomezené prostředí runspace řadiči peer.

3. **Spojte se s omezeným přístupem** tato možnost se používá pro přístup k rozhraní Windows PowerShell v režimu omezené. Zobrazí se výzva k zadání přihlašovacích údajů pro přístup. Tato možnost se připojí k více omezeném prostředí runspace ve srovnání s možností 1 a 2.  Některé úlohy, které jsou k dispozici prostřednictvím možnost 1, který **nelze* provést v tomto prostředí runspace jsou:
   
   * Obnovení továrního nastavení
   * Změna hesla
   * Povolit nebo zakázat přístup k podpoře
   * Instalace aktualizací
   * Instalace opravy hotfix

    > [!NOTE]
    > Toto je upřednostňovanou možnost, pokud jste zapomněli heslo správce zařízení a nemůžete připojit přes možnost 1 nebo 2.

4. **Změnit jazyk** tato možnost vám umožní změnit jazyk zobrazení v rozhraní Windows PowerShell. Podporované jazyky jsou angličtina, japonština, ruština, francouzština, Jižní Koreu, španělština, italština, němčina, čínština a brazilské portugalštině.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Vzdálené připojení k řešení StorSimple pomocí Windows Powershellu pro StorSimple

Vzdálenou komunikaci prostředí Windows PowerShell můžete použít pro připojení k zařízení StorSimple. Když se připojíte tímto způsobem, nabídka se nezobrazí. (Uvidíte nabídku pouze v případě, že použijete konzole sériového portu zařízení pro připojení. Vzdálené připojení přejdete přímo na ekvivalentní "možnost 1 – úplný přístup" v konzole sériového portu.) Pomocí vzdálené komunikace Windows Powershellu připojit k určité prostředí runspace. Můžete také určit jazyk zobrazení.

Jazyk zobrazení je nezávislé na jazyku, který jste nastavili pomocí **změnit jazyk** v nabídce konzoly sériového portu. Vzdálený PowerShell automaticky převezmou národní prostředí zařízení, ze kterého se připojujete Pokud není zadaný žádný.

> [!NOTE]
> Pokud pracujete s virtuální hostitele Microsoft Azure a StorSimple Cloud Appliance, můžete použít vzdálenou komunikaci prostředí Windows PowerShell a virtuální hostitel pro připojení ke cloudovému zařízení. Pokud jste nastavili umístění sdílené složky na hostiteli, na kterého chcete uložit informace z relace prostředí Windows PowerShell, byste měli vědět, který _Everyone_ instanční objekt obsahuje pouze ověřeným uživatelům. Proto pokud jste nastavili sdílenou složku, pokud chcete povolit přístup podle _Everyone_ a připojit bez zadávání přihlašovacích údajů, neověřené anonymní objekt zabezpečení se použije a zobrazí se chyba. Chcete-li tento problém vyřešit, ve sdílené složce můžete hostovat musí povolte účet Guest a poté poskytnout Host úplný přístup k účtu ke sdílené složce nebo je nutné zadat platné přihlašovací údaje spolu s rutinu prostředí Windows PowerShell.


Připojit přes vzdálenou komunikaci prostředí Windows PowerShell můžete použít protokol HTTP nebo HTTPS. Postupujte podle pokynů v následujících kurzech:

* [Vzdálené připojení pomocí protokolu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Vzdálené připojení pomocí protokolu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Aspekty zabezpečení připojení

Když se rozhodujete, jak se připojit k prostředí Windows PowerShell pro StorSimple, zvažte následující:

* Připojení přímo ke konzole sériového portu zařízení je zabezpečené, ale připojení ke konzole sériového portu přes síťové přepínače není. Buďte opatrní rizika zabezpečení při připojování k zařízení sériový přes síťové přepínače.
* Připojení přes relaci protokolu HTTP může nabízet vyšší bezpečnost než připojení prostřednictvím sériové konzoly přes síť. I když to není nejbezpečnější metodou, je přijatelné v důvěryhodných sítích.
* Připojení přes relaci protokolu HTTPS je nejbezpečnější a doporučená možnost.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Spravovat zařízení StorSimple pomocí Windows Powershellu pro StorSimple

Následující tabulka uvádí přehled všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v rámci rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple. Další informace o každém pracovním postupu klikněte na příslušnou položku v tabulce.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Prostředí Windows PowerShell pro StorSimple pracovních postupů

| Pokud chcete udělat... | Pomocí tohoto postupu. |
| --- | --- |
| Registrace zařízení |[Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurace webového proxy serveru</br>Zobrazení nastavení webového proxy serveru |[Konfigurace webového proxy serveru pro zařízení StorSimple](storsimple-8000-configure-web-proxy.md) |
| Úprava nastavení DATA 0 síťového rozhraní na zařízení |[Upravit síťového rozhraní DATA 0 pro zařízení StorSimple](storsimple-8000-modify-data-0.md) |
| Zastavit kontroleru </br> Restartování nebo vypnutí kontroleru </br> Vypněte zařízení</br>Resetování zařízení do výchozího továrního nastavení |[Spravovat kontrolery zařízení](storsimple-8000-manage-device-controller.md) |
| Nainstalovat aktualizace režimu údržby a opravy hotfix |[Aktualizace zařízení](storsimple-update-device.md) |
| Spustit režim údržby </br>Ukončete režim údržby |[Režimy zařízení StorSimple](storsimple-8000-device-modes.md) |
| Vytvořit balíček pro podporu</br>Dešifrování a upravit balíček pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md) |
| Spustit relaci</br> |[Spustit relaci prostředí Windows PowerShell pro StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Získejte pomoc ve Windows Powershellu pro StorSimple

V prostředí Windows PowerShell pro StorSimple je k dispozici rutiny nápovědy. Online a aktuální verzi této nápovědy je také k dispozici, což vám umožní aktualizovat nápovědu ve vašem systému.

Získání nápovědy v toto rozhraní je podobné jako v prostředí Windows PowerShell a většina rutin souvisejících s nápovědou bude fungovat. Nápověda pro Windows PowerShell online můžete najít na stránkách TechNet Library: [skriptování v prostředí Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Následuje stručný popis typů nápovědy pro toto rozhraní prostředí Windows PowerShell, včetně aktualizace v nápovědě.

### <a name="to-get-help-for-a-cmdlet"></a>Chcete-li získat nápovědu pro rutiny

* Chcete-li získat nápovědu pro rutiny nebo funkce, použijte následující příkaz: `Get-Help <cmdlet-name>`
* Získat online nápovědu pro všechny rutiny, použijte rutinu předchozí s `-Online` parametr: `Get-Help <cmdlet-name> -Online`
* Pro úplnou nápovědu, můžete použít `–Full` parametr a příklady, použijte `–Examples` parametru.

### <a name="to-update-help"></a>Abyste mohli aktualizovat nápovědu

Můžete snadno aktualizovat nápovědu v rozhraní Windows PowerShell. Proveďte následující kroky, abyste mohli aktualizovat nápovědu ve vašem systému.

#### <a name="to-update-cmdlet-help"></a>Aktualizace rutiny nápovědy
1. Spusťte prostředí Windows PowerShell pomocí **spustit jako správce** možnost.
2. Na příkazovém řádku zadejte:  `Update-Help`
3. Aktualizované soubory nápovědy se nainstaluje.
4. Když jsou instalovány soubory nápovědy, zadejte: `Get-Help Get-Command`. Tím zobrazíte seznam rutin, pro který je k dispozici nápověda.

> [!NOTE]
> Pokud chcete získat seznam dostupných rutin v prostředí runspace, přihlaste se k odpovídající možnost nabídky a spusťte `Get-Command` rutiny.


## <a name="next-steps"></a>Další postup

Pokud zaznamenáte problémy se zařízením StorSimple při jedné z výše uvedených pracovních postupů, podívejte se na [nástroje pro řešení potíží s nasazeními StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

