---
title: Spravovat řadiče zařízení řady StorSimple 8000 | Microsoft Docs
description: Přečtěte si, jak zastavit, restartovat, vypnout nebo resetovat řadiče zařízení StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2017
ms.author: alkohli
ms.openlocfilehash: 090797549cc61aa27945114e5ef8b666226b66e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94956989"
---
# <a name="manage-your-storsimple-device-controllers"></a>Správa řadičů zařízení StorSimple

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte o různých operacích, které můžete provádět na řadičích zařízení StorSimple. Řadiče v zařízení StorSimple jsou redundantní (partnerské) řadiče v konfiguraci s aktivním pasivním režimem. V daném okamžiku je aktivní jenom jeden kontroler a zpracovává všechny operace disku a sítě. Druhý kontroler je v pasivním režimu. Pokud aktivní kontroler neproběhne, pasivní kontroler se automaticky aktivuje.

Tento kurz obsahuje podrobné pokyny pro správu řadičů zařízení pomocí těchto kroků:

* Okno **řadiče** pro vaše zařízení ve službě StorSimple Správce zařízení.
* Windows PowerShell pro StorSimple.

Doporučujeme spravovat řadiče zařízení prostřednictvím služby StorSimple Správce zařízení. Pokud se akce dá provést jenom pomocí Windows PowerShell pro StorSimple, kurz si ho poznamenejte.

Po přečtení tohoto kurzu budete moct:

* Restartování nebo vypnutí řadiče zařízení StorSimple
* Vypnutí zařízení StorSimple
* Resetování zařízení StorSimple do továrního nastavení

## <a name="restart-or-shut-down-a-single-controller"></a>Restartování nebo vypnutí jednoho kontroleru
Restartování nebo vypnutí řadiče není nutné jako součást běžné systémové operace. Operace vypnutí pro jeden řadič zařízení jsou běžné jenom v případech, kdy neúspěšná hardwarová součást zařízení vyžaduje nahrazení. V situaci, kdy je ovlivněn výkon nadměrného využití paměti nebo řadičem, může být vyžadováno restartování řadiče. Pokud chcete povolit a otestovat nahrazený kontroler, budete možná muset restartovat kontroler i po úspěšném nahrazení kontroleru.

Za předpokladu, že je k dispozici pasivní kontroler, není restartování zařízení rušivé pro připojené iniciátory. Pokud pasivní kontroler není k dispozici nebo je vypnutý, restartování aktivního kontroleru může způsobit přerušení služby a výpadek.

> [!IMPORTANT]
> * **Spuštěný kontroler by se nikdy neměl fyzicky odebrat, protože by došlo ke ztrátě redundance a zvýšenému rizika výpadku.**
> * Následující postup se vztahuje jenom na fyzické zařízení StorSimple. Informace o tom, jak spustit, zastavit a restartovat StorSimple Cloud Appliance, najdete v tématu [práce s cloudovým zařízením](storsimple-8000-cloud-appliance-u2.md#work-with-the-storsimple-cloud-appliance).

Jeden řadič zařízení můžete restartovat nebo vypnout pomocí Azure Portal služby Správce zařízení StorSimple nebo Windows PowerShell pro StorSimple.

Pokud chcete spravovat řadiče zařízení z Azure Portal, proveďte následující kroky.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Restartování nebo vypnutí řadiče v Azure Portal
1. Ve službě StorSimple Správce zařízení můžete přejít na **zařízení**. Vyberte zařízení ze seznamu zařízení. 

    ![Zvolte zařízení](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Přejít na **nastavení > řadiče**.
   
    ![Ověřte, jestli jsou řadiče zařízení StorSimple v pořádku.](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. V okně **řadiče** ověřte, že stav obou řadičů v zařízení je v **pořádku**. Vyberte kontroler, klikněte na něj pravým tlačítkem a vyberte **restartovat** nebo **vypnout**.

    ![Výběr restartování nebo vypnutí řadičů zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Vytvoří se úloha pro restartování nebo vypnutí kontroleru a v případě potřeby se zobrazí příslušná upozornění. Pokud chcete monitorovat restartování nebo vypnutí, vyhledejte **protokoly aktivit služby >** a pak proveďte filtrování podle parametrů, které jsou specifické pro vaši službu. Pokud byl kontroler vypnutý, budete muset zapnout tlačítko napájení, aby se kontroler zapnul.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Restartování nebo vypnutí řadiče v Windows PowerShell pro StorSimple
Provedením následujících kroků vypnete nebo restartujete jeden kontroler na zařízení StorSimple z Windows PowerShell pro StorSimple.

1. Přístup k zařízení prostřednictvím konzoly sériového portu nebo relace Telnet ze vzdáleného počítače. Pokud se chcete připojit k řadiči 0 nebo k řadiči 1, postupujte podle kroků v části [použití výstupu k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**.
3. V hlavičce zprávy si poznamenejte kontroler, ke kterému jste připojeni (řadič 0 nebo Controller 1), a zda se jedná o aktivní nebo pasivní (pohotovostní) řadič.
   
   * Pokud chcete vypnout jeden kontroler, zadejte na příkazovém řádku tento příkaz:
     
       `Stop-HcsController`
     
       Tím se vypne kontroler, ke kterému jste připojeni. Pokud zastavíte aktivní kontroler, zařízení převezme služby při selhání pasivní kontroler.

   * Pokud chcete restartovat kontroler, zadejte na příkazovém řádku:
     
       `Restart-HcsController`
     
       Tím se restartuje kontroler, ke kterému jste připojeni. Pokud restartujete aktivní kontroler, převezme služby při selhání pasivní kontroler před restartováním.

## <a name="shut-down-a-storsimple-device"></a>Vypnutí zařízení StorSimple

V této části se dozvíte, jak vypnout běžící nebo neúspěšné zařízení StorSimple ze vzdáleného počítače. Zařízení je vypnuté po vypnutí obou řadičů zařízení. Vypnutí zařízení se provede, když se zařízení fyzicky přesune, nebo se převezme mimo provoz.

> [!IMPORTANT]
> Před vypnutím zařízení ověřte stav součástí zařízení. Přejděte do zařízení a pak klikněte na **nastavení > stav hardwaru**. V okně **stav a stav hardwaru** ověřte, že stav LED všech komponent je zelený. Zelený stav má pouze zařízení v pořádku. Pokud se zařízení vypíná, aby nahradilo nefunkční komponentu, zobrazí se neúspěšný (červený) nebo snížený (žlutý) stav pro příslušné součásti (y).


#### <a name="to-shut-down-a-storsimple-device"></a>Vypnutí zařízení StorSimple

1. K identifikaci a vypnutí pasivního kontroleru v zařízení použijte [restart nebo ukončete](#restart-or-shut-down-a-single-controller) proceduru kontroleru. Tuto operaci můžete provést v Azure Portal nebo v Windows PowerShell pro StorSimple.
2. Pokud chcete vypnout aktivní kontroler, opakujte výše uvedený krok.
3. Nyní se musíte podívat na zadní rovinu zařízení. Po úplném vypnutí těchto dvou řadičů se stav LED v obou řadičích musí posvítit červeně. Pokud potřebujete zařízení úplně vypnout, překlopte přepínače napájení v modulech napájení a chladicích modulech (PCMs) do VYPNUTé polohy. To by mělo vypnout zařízení.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetování zařízení do výchozího továrního nastavení

> [!IMPORTANT]
> Pokud budete potřebovat resetovat zařízení do výchozího továrního nastavení, kontaktujte podpora Microsoftu. Postup popsaný níže by měl být používán pouze ve spojení s podpora Microsoftu.

Tento postup popisuje, jak obnovit Microsoft Azure StorSimple zařízení do výchozího továrního nastavení pomocí Windows PowerShell pro StorSimple.
Při resetování zařízení se ve výchozím nastavení odstraní všechna data a nastavení z celého clusteru.

Provedením následujících kroků resetujete Microsoft Azure StorSimple zařízení do továrního nastavení:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Postup obnovení výchozích nastavení v zařízení Windows PowerShell pro StorSimple
1. Přístup k zařízení prostřednictvím své sériové konzoly. Zkontrolujte bannerovou zprávu a ujistěte se, že jste připojeni k **aktivnímu** řadiči.
2. V nabídce Konzola sériového portu klikněte na možnost 1, **Přihlaste se s úplným přístupem**.
3. Na příkazovém řádku zadejte následující příkaz pro resetování celého clusteru, odebrání všech dat, metadat a nastavení kontroléru:
   
    `Reset-HcsFactoryDefault`
   
    Chcete-li místo toho obnovit jeden kontroler, použijte rutinu  [reset-HcsFactoryDefault](/previous-versions/windows/powershell-scripting/dn688132(v=wps.630)) s `-scope` parametrem.)
   
    Systém bude restartován několikrát. Po úspěšném dokončení resetování budete upozorněni. V závislosti na modelu systému může trvat 45-60 minut pro zařízení 8100 a 60-90 minut, než se tento proces dokončí na 8600.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Otázky a odpovědi týkající se správy řadičů zařízení
V této části jsme si vyložili některé z nejčastějších dotazů týkajících se správy řadičů zařízení StorSimple.

**Č.** Co se stane, když jsou oba řadiče v zařízení v pořádku a zapnuté a že restartujete nebo vypnete aktivní kontroler?

**Určitého.** Pokud jsou oba řadiče v zařízení v pořádku a jsou zapnuté, zobrazí se výzva k potvrzení. Můžete se rozhodnout:

* **Restartujte aktivní kontroler** – budete upozorněni, že restartování aktivního řadiče způsobilo převzetí služeb při selhání pasivním kontrolérem. Kontroler se restartuje.
* **Vypnout aktivní kontroler** – zobrazí se oznámení, že vypnutí aktivního řadiče způsobí výpadek. Také je potřeba na zařízení zapnout tlačítko napájení, aby se kontroler zapnul.

**Č.** Co se stane, když pasivní kontroler v mém zařízení není dostupný nebo je vypnutý a nerestartujete nebo vypnete aktivní kontroler?

**Určitého.** Pokud je pasivní kontroler v zařízení nedostupný nebo vypnutý, a rozhodnete se:

* **Restartujte aktivní kontroler** – budete upozorněni, že pokračování operace bude mít za následek dočasné přerušení služby a budete vyzváni k potvrzení.
* **Vypnout aktivní kontroler** – budete upozorněni, že pokračování operace bude mít za následek výpadky. Pokud chcete zařízení zapnout, musíte taky na jednom nebo obou řadičích tlačítko napájení. Zobrazí se výzva k potvrzení.

**Č.** Když dojde k neúspěšnému restartování nebo vypnutí řadiče?

**Určitého.** Restartování nebo vypnutí řadiče může selhat, pokud:

* Probíhá aktualizace zařízení.
* Restartování řadiče již probíhá.
* Vypnutí kontroleru už probíhá.

**Č.** Jak se dá zjistit, jestli se kontroler restartoval nebo vypnul?

**Určitého.** V okně kontroleru můžete zaškrtnout možnost stav kontroleru. Stav kontroléru určuje, zda je řadič právě restartován nebo ukončován. Kromě toho okno **výstrahy** obsahuje informativní výstrahu v případě restartování nebo vypnutí kontroleru. V protokolech aktivit se zaznamenávají i operace restartování a vypnutí řadiče. Další informace o protokolech aktivit najdete [v části zobrazení protokolů aktivit](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Č.** Existují v důsledku převzetí služeb při selhání kontroleru nějaký dopad na vstupně-výstupní operace?

**Určitého.** Připojení TCP mezi iniciátory a aktivním řadičem budou resetována v důsledku převzetí služeb při selhání řadiče, ale budou obnovena v případě, že pasivní kontroler předpokládá operaci. V rámci aktivity v/v mezi iniciátory a zařízením v průběhu této operace může docházet k pauzě za dočasné (méně než 30 sekund).

**Č.** Návody vrátit můj kontroler do služby po jeho vypnutí a odebrání?

**Určitého.** Pokud chcete vrátit kontroler do služby, musíte ho vložit do skříně, jak je popsáno v tématu [Výměna modulu Controller na zařízení StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Další kroky
* Pokud narazíte na problémy s řadiči zařízení StorSimple, které nemůžete vyřešit pomocí postupů uvedených v tomto kurzu, [kontaktujte podpora Microsoftu](storsimple-8000-contact-microsoft-support.md).
* Další informace o používání služby StorSimple Správce zařízení najdete v části [používání služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).