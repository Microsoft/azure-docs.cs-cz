---
title: Prostředí PowerShell pro správu zařízení StorSimple | Microsoft Docs
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
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743711"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Ke správě zařízení pomocí Windows Powershellu pro StorSimple

## <a name="overview"></a>Přehled

Prostředí Windows PowerShell pro StorSimple poskytuje rozhraní příkazového řádku, které můžete použít ke správě zařízení s Microsoft Azure StorSimple. Doporučuje se název, je založené na prostředí Windows PowerShell, rozhraní příkazového řádku, která je součástí omezené prostředí runspace. Z pohledu uživatele na příkazovém řádku omezené prostředí runspace s omezeným přístupem verzi prostředí Windows PowerShell se zobrazí. Toto rozhraní při zachování některé základní funkce prostředí Windows PowerShell, má další vyhrazený rutin, které jsou s ohledem na správu zařízení s Microsoft Azure StorSimple.

Tento článek popisuje Windows PowerShell pro StorSimple funkce, včetně toho, jak je možné připojit k tomuto rozhraní a obsahuje odkazy na podrobné postupy nebo pracovní postupy, které můžete provádět pomocí tohoto rozhraní. Pracovní postupy zahrnují jak zaregistrovat zařízení, konfigurace síťového rozhraní na vašem zařízení a nainstalovat aktualizace, které vyžadují zařízení se v režimu údržby, změňte stav zařízení a vyřešte všechny problémy, které mohou nastat.

Po přečtení tohoto článku, budete moci:

* Připojte k zařízení StorSimple pomocí Windows Powershellu pro StorSimple.
* Spravovat zařízení StorSimple pomocí Windows Powershellu pro StorSimple.
* Získejte nápovědu ve Windows Powershellu pro StorSimple.

> [!NOTE]
> * Prostředí Windows PowerShell pro StorSimple rutiny umožňují spravovat zařízení StorSimple, z konzoly sériového portu nebo vzdáleně přes vzdálenou komunikaci prostředí Windows PowerShell. Další informace o jednotlivých jednotlivých rutin, které lze použít v tomto rozhraní, přejděte na [reference k rutině pro prostředí Windows PowerShell pro StorSimple](https://technet.microsoft.com/library/dn688168.aspx).
> * Jsou rutiny Azure PowerShell StorSimple jinou kolekci rutin, které umožňují automatizovat StorSimple úrovně služby a úlohy migrace z příkazového řádku. Další informace o rutiny Azure Powershellu pro StorSimple, přejděte na [reference k rutině Azure StorSimple](https://docs.microsoft.com/powershell/servicemanagement/azure.storsimple/v3.1.0/azure.storsimple).


Prostředí Windows PowerShell se můžete dostat pro StorSimple pomocí jedné z následujících metod:

* [Připojení ke konzole sériového portu zařízení StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Vzdálené připojení k zařízení StorSimple pomocí prostředí Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Připojení k Windows Powershellu pro StorSimple prostřednictvím konzole sériového portu zařízení

Můžete [stáhněte si PuTTY](http://www.putty.org/) nebo podobné softwaru pro emulaci terminálu pro připojení k Windows Powershellu pro StorSimple. Musíte nakonfigurovat PuTTY konkrétně k přístupu k zařízení Microsoft Azure StorSimple. Následující témata obsahují podrobné pokyny o tom, jak nakonfigurovat PuTTy a připojte se k zařízení. Rovněž jsou vysvětleny různé možnosti nabídky v konzole sériového portu.

### <a name="putty-settings"></a>Nastavení PuTTY

Ujistěte se, že používáte následující nastavení PuTTY se připojit k rozhraní Windows PowerShell z konzole sériového portu.

#### <a name="to-configure-putty"></a>Konfigurace PuTTY

1. V PuTTY **Rekonfigurace** dialogovém **kategorie** podokně, vyberte **klávesnice**.
2. Ujistěte se, že jsou vybrány následující možnosti (jedná se o výchozí nastavení, při spuštění relace).
   
   | Položka klávesnice | Vyberte |
   | --- | --- |
   | BACKSPACE klíč |Ovládací prvek-? (127) |
   | Home a End klíče |Standard |
   | Funkční klávesy a klávesnici |ESC [n ~ |
   | Počáteční stav kurzoru klíče |Normální |
   | Počáteční stav numerické klávesnici |Normální |
   | Povolení funkcí navíc klávesnice |CTRL + ALT + se liší od AltGr |
   
    ![Podporovaná nastavení Putty](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klikněte na tlačítko **Použít**.
4. V **kategorie** podokně, vyberte **překlad**.
5. V **vzdáleného znakovou sadu** pole se seznamem, vyberte **UTF-8**.
6. V části **zpracování řádku kreslení znaků**, vyberte **body kódu Unicode pomocí řádku kreslení**. Následující snímek obrazovky ukazuje správný výběr PuTTY.
   
    ![Nastavení Putty UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klikněte na tlačítko **Použít**.

Nyní můžete PuTTY k připojení ke konzole sériového portu zařízení provedením následujících kroků.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Informace o konzole sériového portu

Při přístupu k prostředí Windows PowerShell se zobrazí rozhraní zařízení StorSimple pomocí konzoly sériového portu, zpráva hlavičky, následovaný možností v nabídce.

Zpráva hlavičky obsahuje základní informace zařízení StorSimple jako model, název, verze nainstalovaného softwaru a stavu na zařízení, které se připojujete. Následující obrázek ukazuje příklad zpráva hlavičky.

![Zpráva sériové hlavičky](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Hlavička zprávu můžete použít k určení, zda je řadič jste připojeni k _Active_ nebo _pasivní_.

Následující obrázek ukazuje různé možnosti prostředí runspace, které jsou k dispozici v nabídce konzoly sériového portu.

![Zaregistrovat zařízení 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Můžete zvolit z následujících nastavení:

1. **Přihlaste se pomocí úplný přístup** tato možnost vám umožňuje připojit (se správnými přihlašovacími údaji) k **SSAdminConsole** prostředí runspace na místní řadiči. (Je místní řadič kontroler, který se právě používají prostřednictvím konzole sériového portu zařízení StorSimple.) Tuto možnost lze také povolit Microsoft Support pro přístup k neomezený prostředí runspace (relaci podpory) k řešení potíží všech možných zařízení. Když použijete možnost 1 pro přihlášení, můžete povolit Microsoft Support engineer pro přístup k prostředí runspace neomezený spuštěním ke konkrétní rutině. Podrobnosti najdete v části [spustit relaci podporu](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Přihlaste se k řadiči sdílené s úplným přístupem** tato možnost je stejná jako možnost 1, s tím rozdílem, že se můžete připojit (se správnými přihlašovacími údaji) k **SSAdminConsole** prostředí runspace na řadiči partnera. Vzhledem k tomu, že zařízení StorSimple je vysoká dostupnost zařízení s dva řadiče v konfiguraci aktivní pasivní, sdílené odkazuje na jiný řadič v zařízení, ke kterému se připojujete pomocí konzoly sériového portu).
   Podobně jako možnost 1, tuto možnost lze také povolit Microsoft Support pro přístup k neomezený prostředí runspace na řadiči partnera.

3. **Připojení s omezeným přístupem** tato možnost se používá pro přístup k rozhraní Windows PowerShell v režimu omezené. Zobrazí se výzva k zadání pověření pro přístup k. Tato možnost se připojí k více omezeném prostředí runspace ve srovnání s možností 1 a 2.  Některé úlohy, které jsou k dispozici prostřednictvím možnost 1, **nelze* provést v této prostředí runspace jsou:
   
   * Obnovit do továrního nastavení
   * Změnit heslo
   * Povolit nebo zakázat podporu přístupu
   * Instalace aktualizací
   * Instalaci oprav hotfix

    > [!NOTE]
    > Toto je upřednostňovanou možnost, pokud jste zapomněli jste heslo správce zařízení a nelze se připojit prostřednictvím možnost 1 nebo 2.

4. **Změnit jazyk** této možnosti lze změnit jazyk zobrazení v rozhraní Windows PowerShell. Podporované jazyky jsou angličtina, japonština, ruština, francouzština, korejština – Jih, španělština, italština, němčina, čínština a Brazilská portugalština.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Vzdálené připojení k zařízení StorSimple pomocí Windows Powershellu pro StorSimple

Vzdálená komunikace prostředí Windows PowerShell můžete použít pro připojení k zařízení StorSimple. Když připojíte tímto způsobem, neuvidíte nabídky. (Zobrazí nabídky pouze v případě, že použijete konzole sériového portu v zařízení pro připojení. Vzdálené připojení přejdete přímo na ekvivalent "možnost 1 – úplný přístup" v konzole sériového portu.) Díky vzdálenou komunikaci prostředí Windows PowerShell můžete připojit k konkrétní prostředí runspace. Můžete také zadat jazyk zobrazení.

Jazyk zobrazení je nezávislé na jazyk, který nastavíte pomocí **změnit jazyk** možnost v nabídce konzoly sériového portu. Vzdáleného prostředí PowerShell automaticky převezmou ze se připojujete, pokud není zadaný žádný národního prostředí zařízení.

> [!NOTE]
> Pokud pracujete s Microsoft Azure virtuální hostitele a cloudu zařízení StorSimple, můžete použít vzdálenou komunikaci prostředí Windows PowerShell a virtuální hostitel pro připojení k zařízení cloudu. Pokud jste nastavili umístění sdílené složky na hostiteli, na které chcete uložit informace z relace prostředí Windows PowerShell, byste měli vědět, _Everyone_ hlavní zahrnuje jen ověření uživatelé. Proto pokud jste nastavili tak sdílenou složku pro povolení přístupu podle _Everyone_ a připojit bez zadání pověření, se použije neověřené anonymní objekt zabezpečení a zobrazí se chyba. Chcete-li tento problém vyřešit, ve sdílené složce hostitele je musíte povolit účet Guest a pak umožnit Host účet úplný přístup ke sdílené složce nebo je nutné zadat platné přihlašovací údaje společně s rutinu prostředí Windows PowerShell.


Připojení přes vzdálenou komunikaci prostředí Windows PowerShell můžete použít protokol HTTP nebo HTTPS. Postupujte podle pokynů v následujících kurzech:

* [Připojit vzdáleně pomocí protokolu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Vzdálené připojení pomocí protokolu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Aspekty zabezpečení připojení

Při výběru jak se připojit k Windows Powershellu pro StorSimple, zvažte následující:

* Připojení přímo ke konzole sériového portu zařízení je bezpečné, ale připojující se ke konzole sériového portu přes síťové přepínače není. Buďte opatrní rizika zabezpečení, při připojení k zařízení sériový přes síťové přepínače.
* Připojení přes relaci protokolu HTTP může nabízí lepší zabezpečení než připojení prostřednictvím sériové konzoly přes síť. I když to není nejbezpečnější metodou, je přijatelné v důvěryhodných sítích.
* Připojení prostřednictvím relace HTTPS je nejbezpečnější a doporučená možnost.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Spravovat zařízení StorSimple pomocí Windows Powershellu pro StorSimple

Následující tabulka obsahuje souhrn všechny běžné úlohy správy a komplexní pracovní postupy, které lze provést v rámci rozhraní Windows PowerShell zařízení StorSimple. Další informace o každém pracovním postupu klikněte na příslušnou položku v tabulce.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Prostředí Windows PowerShell pro pracovní postupy StorSimple

| Pokud chcete to udělat... | Pomocí tohoto postupu. |
| --- | --- |
| Registrace zařízení |[Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurace webového proxy serveru</br>Nastavení proxy serveru webové zobrazení |[Konfigurace webového proxy serveru pro zařízení StorSimple](storsimple-8000-configure-web-proxy.md) |
| Upravit nastavení 0 síťového rozhraní DATA na zařízení |[Upravit síťového rozhraní DATA 0 pro zařízení StorSimple](storsimple-8000-modify-data-0.md) |
| Zastavit řadič </br> Restartování nebo vypnutí řadič </br> Vypněte zařízení</br>V zařízení resetovat výchozí tovární nastavení |[Správa řadiče zařízení](storsimple-8000-manage-device-controller.md) |
| Nainstalujte aktualizace režimu údržby a opravy hotfix |[Aktualizace zařízení](storsimple-update-device.md) |
| Přechod do režimu údržby </br>Ukončení režimu údržby |[Režimy zařízení StorSimple](storsimple-8000-device-modes.md) |
| Vytvoření balíčku pro podporu</br>Dešifrování a upravit balíčku pro podporu |[Vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md) |
| Spusťte relaci podpory</br> |[Spusťte relaci podpory v prostředí Windows PowerShell pro StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Získání nápovědy ve Windows Powershellu pro StorSimple

Rutina nápovědy ve Windows Powershellu pro StorSimple je k dispozici. Online, aktuální verzi této nápovědy je také k dispozici, který můžete použít k aktualizaci v nápovědě systému.

Získání nápovědy v toto rozhraní je podobné jako v prostředí Windows PowerShell a většina rutin souvisejících s bude fungovat. Nápověda pro prostředí Windows PowerShell online můžete najít v knihovně TechNet: [skriptování v prostředí Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Následuje stručný popis typů nápovědy pro toto rozhraní prostředí Windows PowerShell, včetně toho, jak aktualizovat v nápovědě.

### <a name="to-get-help-for-a-cmdlet"></a>Jak získat nápovědu pro rutinu

* Chcete-li získat nápovědu pro všechny rutiny nebo funkce, použijte následující příkaz:`Get-Help <cmdlet-name>`
* Získat online nápovědu k jakékoli rutině, použijte rutinu předchozí s `-Online` parametr:`Get-Help <cmdlet-name> -Online`
* Pro úplnou nápovědu, můžete použít `–Full` parametr a příklady, použijte `–Examples` parametr.

### <a name="to-update-help"></a>Abyste mohli aktualizovat nápovědu

Můžete snadno aktualizovat v nápovědě v rozhraní Windows PowerShell. Proveďte následující kroky, abyste mohli aktualizovat nápovědu ve vašem systému.

#### <a name="to-update-cmdlet-help"></a>Chcete-li aktualizovat rutiny nápovědy
1. Spusťte Windows PowerShell **spustit jako správce** možnost.
2. Na příkazovém řádku zadejte:`Update-Help`
3. Budou nainstalovány soubory nápovědy.
4. Když jsou instalovány soubory nápovědy, zadejte: `Get-Help Get-Command`. Tím zobrazíte seznam rutin, pro který je k dispozici nápověda.

> [!NOTE]
> Chcete-li získat seznam všech dostupných rutin v prostředí runspace, přihlaste se na odpovídající položku nabídky a spusťte `Get-Command` rutiny.


## <a name="next-steps"></a>Další postup

Pokud máte problémy s vaším zařízením StorSimple při provádění výše pracovních postupů, podívejte se na [nástroje pro řešení potíží s nasazením StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

