---
title: Správa řadičů zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Přečtěte si, jak zastavit, restartovat, vypnout nebo resetovat ovladače zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: ce49dcaa06288ba9e7a4d232338c727064d59685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267765"
---
# <a name="manage-your-storsimple-device-controllers"></a>Správa ovladačů zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje různé operace, které lze provést na řadičích zařízení StorSimple. Řadiče ve vašem zařízení StorSimple jsou redundantní (peer) řadiče v konfiguraci aktivní pasivní. V daném okamžiku je aktivní pouze jeden řadič, který zpracovává všechny operace na disku a síti. Druhý řadič je v pasivním režimu. Pokud aktivní řadič selže, pasivní řadič se automaticky aktivuje.

Tento kurz obsahuje podrobné pokyny ke správě řadičů zařízení pomocí:

* **Ovladače** pro vaše zařízení ve službě StorSimple Device Manager.
* Prostředí Windows PowerShell pro StorSimple.

Doporučujeme spravovat ovladače zařízení prostřednictvím služby StorSimple Device Manager. Pokud akci lze provést pouze pomocí prostředí Windows PowerShell pro StorSimple, kurz provede poznámku.

Po přečtení tohoto výukového programu, budete moci:

* Restartování nebo vypnutí ovladače zařízení StorSimple
* Vypnutí zařízení StorSimple
* Obnovení výchozího nastavení zařízení StorSimple z výroby

## <a name="restart-or-shut-down-a-single-controller"></a>Restartování nebo vypnutí jednoho ovladače
Restartování nebo vypnutí řadiče není vyžadováno jako součást normálního provozu systému. Vypnutí pro jeden řadič zařízení jsou běžné pouze v případech, kdy selhání hardwarové součásti zařízení vyžaduje výměnu. Restartování řadiče může být také vyžadováno v situaci, kdy je výkon ovlivněn nadměrným využitím paměti nebo nefunkčním řadičem. Pokud chcete povolit a otestovat vyměněný řadič, může být také nutné restartovat řadič po úspěšné výměně řadiče.

Za předpokladu, že je k dispozici pasivní kontroler, není restartování zařízení rušivé pro připojené iniciátory. Pokud pasivní kontroler není k dispozici nebo je vypnutý, restartování aktivního kontroleru může způsobit přerušení služby a výpadek.

> [!IMPORTANT]
> * **Běžící řadič by nikdy neměl být fyzicky odebrán, protože by to mělo za následek ztrátu redundance a zvýšené riziko prostojů.**
> * Následující postup platí pouze pro fyzické zařízení StorSimple. Informace o tom, jak spustit, zastavit a restartovat storsimple cloudové zařízení, naleznete v [tématu Práce s cloudovým zařízením](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Můžete restartovat nebo vypnout jeden řadič zařízení prostřednictvím portálu Azure služby StorSimple Device Manager nebo Windows PowerShell pro StorSimple.

Pokud chcete spravovat ovladače zařízení z webu Azure Portal, proveďte následující kroky.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Restartování nebo vypnutí řadiče na webu Azure Portal
1. Ve službě StorSimple Device Manager přejděte na **Zařízení**. Vyberte zařízení ze seznamu zařízení. 

    ![Zvolte zařízení](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Přejděte na **Nastavení > řadiče**.
   
    ![Ověřte, zda jsou řadiče zařízení StorSimple v pořádku.](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. V okně **Řadiče** ověřte, zda je stav obou ovladačů v zařízení **V pořádku**. Vyberte ovladač, klepněte pravým tlačítkem myši a potom vyberte **restartovat** nebo **vypnout**.

    ![Výběr restartování nebo vypnutí řadičů zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Úloha je vytvořena k restartování nebo vypnutí řadiče a zobrazí se příslušná upozornění, pokud existuje. Chcete-li sledovat restartování nebo vypnutí, přejděte na **protokoly aktivit služby >** a potom filtrujte podle parametrů specifických pro vaši službu. Pokud byl ovladač vypnut, budete muset stisknout tlačítko napájení, abyste ovladač zapnuli, abyste jej zapnuli.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Restartování nebo vypnutí řadiče v prostředí Windows PowerShell pro StorSimple
Provedením následujících kroků vypněte nebo restartujte jeden řadič na zařízení StorSimple z prostředí Windows PowerShell pro StorSimple.

1. Přístup k zařízení prostřednictvím sériové konzole nebo relace telnet ze vzdáleného počítače. Chcete-li se připojit k ovladači 0 nebo řadiči 1, postupujte podle pokynů v [části Použití putty pro připojení ke sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**.
3. Ve zprávě s hlavičkou si poznamenejte ovladač, ke kterým jste připojeni (řadič 0 nebo řadič 1) a zda se jedná o aktivní nebo pasivní (pohotovostní) řadič.
   
   * Chcete-li vypnout jeden řadič, zadejte na řádku:
     
       `Stop-HcsController`
     
       Tím vypnete řadič, ke kterému jste připojeni. Pokud zastavíte aktivní řadič, zařízení převezme služby při selhání pasivního řadiče.

   * Chcete-li řadič restartovat, zadejte na výzvu:
     
       `Restart-HcsController`
     
       Tím se restartuje kontroler, ke kterému jste připojeni. Pokud restartujete aktivní řadič, převezme služby před restartováním pasivní řadič.

## <a name="shut-down-a-storsimple-device"></a>Vypnutí zařízení StorSimple

Tato část vysvětluje, jak vypnout spuštěné nebo neúspěšné zařízení StorSimple ze vzdáleného počítače. Po vypnutí obou ovladačů zařízení je zařízení vypnuto. Vypnutí zařízení se provádí, když je zařízení fyzicky přesunuto nebo je vyřazeno z provozu.

> [!IMPORTANT]
> Před vypnutím zařízení zkontrolujte stav součástí zařízení. Přejděte do zařízení a klepněte na tlačítko **Nastavení > Stav hardwaru**. V okně **Stav a stav hardwaru** ověřte, zda je stav LED všech součástí zelený. Pouze zařízení v pořádku má zelený stav. Pokud je zařízení vypínano, aby se nahradila nefunkční součást, zobrazí se pro příslušné součásti stav se nezdařilo (červená) nebo zhoršená (žlutá).


#### <a name="to-shut-down-a-storsimple-device"></a>Vypnutí zařízení StorSimple

1. Pomocí [postupu restartování nebo vypnutí řadiče](#restart-or-shut-down-a-single-controller) můžete identifikovat a vypnout pasivní ovladač v zařízení. Tuto operaci můžete provést na webu Azure Portal nebo v prostředí Windows PowerShell pro StorSimple.
2. Opakováním výše uvedeného kroku vypnete aktivní ovladač.
3. Nyní se musíte podívat na zadní rovinu zařízení. Po úplném vypnutí obou regulátorů by měly blikat červené diody stavu led na obou ovladačích. Pokud potřebujete zařízení v tomto okamžiku zcela vypnout, překlopte vypínače na napájecích i chladicích modulech (PCM) do polohy OFF. To by mělo vypnout zařízení.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetování zařízení do výchozího továrního nastavení

> [!IMPORTANT]
> Pokud potřebujete zařízení obnovit na výchozí tovární nastavení, obraťte se na podporu společnosti Microsoft. Postup popsaný níže by měl být používán pouze ve spojení s podporou společnosti Microsoft.

Tento postup popisuje, jak obnovit výchozí nastavení zařízení Microsoft Azure StorSimple na výchozí nastavení z výroby pomocí prostředí Windows PowerShell pro StorSimple.
Obnovením zařízení odeberete všechna data a nastavení z celého clusteru ve výchozím nastavení.

Chcete-li obnovit výchozí nastavení zařízení Microsoft Azure StorSimple z výroby, proveďte následující kroky:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Obnovení výchozího nastavení zařízení v prostředí Windows PowerShell pro StorSimple
1. Přístup k zařízení prostřednictvím sériové konzole. Zkontrolujte bannerovou zprávu a ujistěte se, že jste připojeni k **aktivnímu** ovladači.
2. V nabídce sériové konzole zvolte možnost 1, **Přihlaste se s úplným přístupem**.
3. Na výzvu zadejte následující příkaz pro obnovení celého clusteru a odebrání všech dat, metadat a nastavení řadiče:
   
    `Reset-HcsFactoryDefault`
   
    Chcete-li místo toho obnovit jeden řadič, použijte rutinu `-scope` [Reset-HcsFactoryDefault](https://technet.microsoft.com/library/dn688132.aspx) s parametrem.)
   
    Systém se restartuje několikrát. Po úspěšném dokončení obnovení budete upozorněni. V závislosti na modelu systému může trvat 45-60 minut pro zařízení 8100 a 60-90 minut pro 8600 k dokončení tohoto procesu.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Otázky a odpovědi týkající se správy řadičů zařízení
V této části jsme shrnuli některé často kladené otázky týkající se správy řadičů zařízení StorSimple.

**Otázka:** Co se stane, když jsou oba ovladače na mém zařízení v pořádku a zapnuté a restartuji nebo vypívám aktivní ovladač?

**A.** Pokud jsou oba ovladače v zařízení v pořádku a zapnuté, budete vyzváni k potvrzení. Můžete se rozhodnout:

* **Restartujte aktivní řadič** – Budete upozorněni, že restartování aktivního řadiče způsobilo převzetí služeb při selhání zařízení pasivnímu řadiči. Řadič se restartuje.
* **Vypnutí aktivního řadiče** – Budete upozorněni, že vypnutí aktivního řadiče má za následek prostoje. Chcete-li ovladač zapnout, musíte také stisknout tlačítko napájení na zařízení.

**Otázka:** Co se stane, když pasivní ovladač na mém zařízení není k dispozici nebo je vypnutý a restartuji nebo vypívám aktivní ovladač?

**A.** Pokud pasivní ovladač na vašem zařízení není k dispozici nebo je vypnutý a vy se rozhodnete:

* **Restartujte aktivní řadič** – Budete upozorněni, že pokračování operace bude mít za následek dočasné přerušení služby a budete vyzváni k potvrzení.
* **Vypnutí aktivního řadiče** – Budete upozorněni, že pokračování operace má za následek prostoje. Chcete-li zařízení zapnout, musíte také stisknout tlačítko napájení na jednom nebo obou ovladačích. Budete vyzváni k potvrzení.

**Otázka:** Kdy se nepodaří pokračovat v restartování nebo vypnutí řadiče?

**A.** Restartování nebo vypnutí řadiče může selhat, pokud:

* Probíhá aktualizace zařízení.
* Restartování řadiče již probíhá.
* Vypnutí řadiče již probíhá.

**Otázka:** Jak můžete zjistit, zda byl řadič restartován nebo vypnut?

**A.** Stav ovladače můžete zkontrolovat na noži ovladače. Stav řadiče označuje, zda je řadič v procesu restartování nebo vypnutí. Okno **Alerts** navíc obsahuje informační výstrahu, pokud je řadič restartován nebo vypnut. Operace restartování a vypnutí řadiče jsou také zaznamenány v protokolech aktivit. Další informace o protokolech aktivit naleznete v části [Zobrazení protokolů aktivit](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Otázka:** Existuje nějaký dopad na vstupně-to v důsledku převzetí služeb při selhání řadiče?

**A.** Připojení TCP mezi iniciátory a aktivním řadičem budou resetována v důsledku převzetí služeb při selhání řadiče, ale budou obnovena, když pasivní řadič převezme operaci. Během této operace může dojít k dočasnému (méně než 30 sekund) pozastavení vstupně-vanové aktivity mezi iniciátory a zařízením.

**Otázka:** Jak mohu vrátit ovladač do služby poté, co byl vypnut a odebrán?

**A.** Chcete-li ovladač vrátit do servisu, musíte jej vložit do šasi, jak je popsáno v [části Výměna modulu ovladače na zařízení StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Další kroky
* Pokud narazíte na nějaké problémy s řadiči zařízení StorSimple, které nelze vyřešit pomocí postupů uvedených v tomto kurzu, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
* Další informace o používání služby StorSimple Device Manager naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

