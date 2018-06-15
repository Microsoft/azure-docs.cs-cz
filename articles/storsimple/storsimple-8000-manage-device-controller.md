---
title: Správa řadiče zařízení řady StorSimple 8000 | Microsoft Docs
description: Naučte se zastavit, vypnout, restartovat nebo obnovit řadičů zařízení StorSimple.
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
ms.openlocfilehash: 75c1bdb570967b6d1902697597f0b5bf3f4ffb7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875113"
---
# <a name="manage-your-storsimple-device-controllers"></a>Spravovat vaše řadiče zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje různé operace, které lze provést na řadiče zařízení StorSimple. Řadiče v zařízení StorSimple jsou řadiče redundantní (sdílené) v konfiguraci aktivní pasivní. V daném okamžiku jenom jeden řadič je aktivní a zpracovává všechny diskových a síťových operací. Jiný řadič je v pasivním režimu. V případě selhání řadič active řadičem pasivní automaticky stane aktivní.

Tento kurz zahrnuje podrobné pokyny ke správě řadiče zařízení pomocí:

* **Řadiče** okna pro vaše zařízení ve službě StorSimple Manager zařízení.
* Prostředí Windows PowerShell pro StorSimple.

Doporučujeme vám, že spravujete řadiče zařízení prostřednictvím služby StorSimple Manager zařízení. Pokud se akce lze provést pouze pomocí prostředí Windows PowerShell pro StorSimple, tento kurz provede poznamenejte si ho.

Po přečtení tohoto kurzu, budete moci:

* Restartování nebo vypnutí řadič zařízení StorSimple
* Vypněte zařízení StorSimple
* Obnovit výchozí tovární nastavení v zařízení StorSimple

## <a name="restart-or-shut-down-a-single-controller"></a>Restartování nebo vypnutí jediného kontroleru
Řadič restartování nebo vypnutí není vyžadován jako součást operace normální systému. Operace vypnutí pro jedno zařízení řadiče jsou běžné pouze v případech, ve kterých hardwarová komponenta selhání zařízení vyžaduje nahrazení. V situaci, ve kterém ovlivňuje výkon nadměrného využití paměti nebo chybně fungující řadič může také vyžadovat restartování řadiče. Může také musíte restartovat řadič po úspěšné řadiče náhradní, pokud chcete povolit a testovací nahrazené kontroler.

Restartování zařízení není rušivé iniciátorům připojené, za předpokladu, že je k dispozici pasivní řadič. Pokud není pasivní řadič k dispozici nebo zapnuté vypnout a potom restartování řadič active může vést k přerušení služby a výpadky.

> [!IMPORTANT]
> * **Běžícího řadiče nikdy odeberte fyzicky jako to může vést ke ztrátě redundance a k vyššímu riziku výpadku.**
> * Následující postup platí jenom pro fyzického zařízení StorSimple. Informace o tom, jak spuštění, zastavení a restartování zařízení StorSimple cloudu najdete v tématu [pracovat s cloud zařízení](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Můžete restartovat nebo vypnout řadič jedno zařízení prostřednictvím portálu Azure service Manager zařízení StorSimple nebo prostředí Windows PowerShell pro StorSimple.

Ke správě řadičů zařízení z portálu Azure, proveďte následující kroky.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>Chcete-li restartovat nebo vypnout řadič v portálu Azure
1. Přejděte ve službě StorSimple Manager zařízení **zařízení**. Vyberte zařízení ze seznamu zařízení. 

    ![Vyberte zařízení](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Přejděte na **Nastavení > řadiče**.
   
    ![Ověřte, zda jsou v pořádku řadiče zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. V **řadiče** okno, ověřte, zda je stav oběma řadičům na vašem zařízení **stavu v pořádku**. Vyberte zařízení, klikněte pravým tlačítkem myši a pak vyberte **restartujte** nebo **vypnout**.

    ![Vyberte restartování nebo vypnutí řadiče zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. Úloha je vytvořena na restartování nebo vypnutí kontroleru a se zobrazí upozornění použít, pokud existuje. Chcete-li monitorovat restartování nebo vypnutí, přejděte na **služby > protokoly aktivity** a vyfiltrujte parametry, které jsou specifické pro vaši službu. Pokud řadič byl vypnutý, pak bude muset stisknutím tlačítka napájení zapnout řadičem ji zapnout.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Chcete-li restartovat nebo vypnout řadič ve Windows Powershellu pro StorSimple
Pomocí následujících kroků vypnout nebo restartovat jedním řadičem zařízení StorSimple z prostředí Windows PowerShell pro StorSimple.

1. Přístup k zařízení prostřednictvím konzoly sériového portu nebo relace Telnetu ze vzdáleného počítače. Pro připojení k řadič 0 nebo 1 řadiče, postupujte podle kroků v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce konzoly sériového portu, zvolte možnost 1, **přihlásit úplný přístup**.
3. Zpráva hlavičky, poznamenejte si řadiče jste připojeni k (řadič 0 nebo 1 řadič) a jestli je aktivní nebo pasivní řadič (pohotovostní).
   
   * Chcete-li vypnout jeden řadič, v příkazovém řádku zadejte:
     
       `Stop-HcsController`
     
       Vypnutí řadiče, které jsou připojené do. Pokud zastavíte řadič active, pak zařízení převezme řadičem pasivní.

   * Restartujte řadič do příkazového řádku zadejte:
     
       `Restart-HcsController`
     
       Tím dojde k restartování řadiče, které jsou připojené do. Pokud restartujete řadič active, nezdaří se přes pasivní řadiče před restartováním počítače.

## <a name="shut-down-a-storsimple-device"></a>Vypněte zařízení StorSimple

Tato část vysvětluje, jak vypnout spuštěný nebo selhání zařízení StorSimple ze vzdáleného počítače. Zařízení je vypnutý, poté, co se oba řadiče zařízení vypnut. Vypnutí zařízení se provádí, když zařízení, fyzicky přesunete nebo je vyřazeno z provozu.

> [!IMPORTANT]
> Před vypnutím zařízení, zkontrolujte stav součásti zařízení. Přejděte do zařízení a pak klikněte na tlačítko **Nastavení > stavu hardwaru**. V **stav a hardwaru stavu** okno, ověřte, zda je stav DIODU všechny součásti zelená. Pouze v pořádku zařízení má zelený stav. Pokud vaše zařízení je vypínán dolů k nahrazení chybně fungující komponenty, zobrazí se sníženou (žlutý) stav pro příslušné součásti nebo se nezdařilo (červený).


#### <a name="to-shut-down-a-storsimple-device"></a>Vypnutí zařízení StorSimple

1. Použití [restartování nebo vypnutí řadič](#restart-or-shut-down-a-single-controller) postup identifikovat a vypněte pasivní řadič na vašem zařízení. Tuto operaci může provést na portálu Azure nebo v prostředí Windows PowerShell pro StorSimple.
2. Opakujte předchozí krok vypnout aktivní řadiče.
3. Podívejte se musí nyní na pozadí roviny tohoto zařízení. Po dva řadiče jsou zcela vypnout, by měl být stav LED na obou řadičích blikat red. Pokud potřebujete vypnout zařízení zcela v tuto chvíli překlopit hlavní přepínače na napájení a chlazení moduly (PCMs) na pozici OFF. To by měl vypnout zařízení.

## <a name="reset-the-device-to-factory-default-settings"></a>V zařízení resetovat výchozí tovární nastavení

> [!IMPORTANT]
> Pokud potřebujete obnovit v zařízení výchozí tovární nastavení, obraťte se na Microsoft Support. Níže popsaný postup by měl použít pouze ve spojení s Microsoft Support.

Tento postup popisuje postup resetování zařízení s Microsoft Azure StorSimple výchozí tovární nastavení pomocí Windows Powershellu pro StorSimple.
Když zařízení resetujete, odeberete všechna data a nastavení z celý cluster ve výchozím nastavení.

Proveďte následující postup resetování zařízení s Microsoft Azure StorSimple výchozí tovární nastavení:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Chcete-li obnovit zařízení do výchozího nastavení v prostředí Windows PowerShell pro StorSimple
1. Přístup k zařízení pomocí jeho konzoly sériového portu. Zkontrolujte zprávy a informační zpráva k zajištění, že jste připojeni k **Active** řadiče.
2. V nabídce konzoly sériového portu, zvolte možnost 1, **přihlásit úplný přístup**.
3. Do příkazového řádku zadejte následující příkaz k resetování celý cluster, odebrání všech nastavení řadiče, metadat a dat:
   
    `Reset-HcsFactoryDefault`
   
    Chcete-li místo toho resetovat jediného kontroleru, použijte [resetování HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) rutiny s `-scope` parametr.)
   
    Systém se restartuje vícekrát. Při obnovení byla úspěšně dokončena, budete upozorněni. V závislosti na modelu systému může trvat 45 – 60 minut pro zařízení s 8100 a 60 až 90 minut pro 8600 k dokončení tohoto procesu.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Otázky a odpovědi týkající se správy řadiče zařízení
V této části jsme vytvořili souhrn některé nejčastější dotazy ohledně správy řadiče zařízení StorSimple.

**Otázka:** Co se stane, když oběma řadičům na zařízení jsou v pořádku a zapnuté na a I restartování nebo vypnutí řadič active?

**Odpověď:** Pokud jsou oba řadiče v zařízení v pořádku a zapnuté, zobrazí se výzva k potvrzení. Můžete se rozhodnout pro:

* **Restartujte řadič active** – oznamuje, že restartování řadič služby active způsobí převzetí služeb při selhání pasivní řadiče zařízení. Restartuje počítač řadiče.
* **Vypnout aktivní řadiče** – oznamuje, že vypíná řadič active výsledkem výpadku. Musíte také stisknout tlačítko napájení v zařízení zapnout na řadiči.

**Otázka:** Co se stane, pokud je k dispozici nebo je zapnuté pasivní řadič na zařízení vypnout a I restartování nebo vypnutí řadič active?

**Odpověď:** Pokud je pasivní řadič na vašem zařízení není k dispozici nebo zapnuté vypnout a zvolíte možnost:

* **Restartujte řadič active** – oznamuje, že budete pokračovat operaci způsobí dočasné přerušení služby, a budete vyzváni k potvrzení.
* **Vypnout aktivní řadiče** – upozornění se zobrazí, budete pokračovat operaci výsledkem výpadku. Musíte taky tak, aby nabízel tlačítka napájení na jeden nebo oba řadiče zapnutí zařízení. Zobrazí se výzva k potvrzení.

**Otázka:** Pokud nemá řadič restartování nebo vypnutí nepodaří průběhu?

**Odpověď:** Restartování nebo vypnutí řadič může selhat, pokud:

* Probíhá aktualizace zařízení.
* Již probíhá restartování řadiče.
* Vypnutí řadiče již probíhá.

**Otázka:** Jak můžete zjistit, pokud byl restartován nebo vypnut řadič?

**Odpověď:** Můžete zkontrolovat stav řadiče v okně řadiče. Stav řadiče označí, zda řadič Probíhá restartování nebo vypnutí. Kromě toho **výstrahy** okno obsahovat informační výstrahu, pokud je řadičem restartování nebo vypnutí. Operace restart a vypnutí řadiče se také zaznamenávají v protokolech jsou. Další informace o jsou protokoly, přejděte na [zobrazit protokoly aktivity](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Otázka:** Je k dispozici žádný vliv na vstupy/výstupy v důsledku řadiče převzetí služeb při selhání?

**Odpověď:** Připojení TCP mezi iniciátorů a aktivní řadiče se resetuje v důsledku řadiče převzetí služeb při selhání, ale se znovu vytvoří, když je pasivní řadič předpokládá operace. Během této operace může být pozastavení dočasných (méně než 30 sekund) v vstupně-výstupní aktivitu mezi iniciátorů a zařízení.

**Otázka:** Jak se vrátit Moje řadič služby po byla vypnout a odebrat?

**Odpověď:** Pokud chcete vrátit řadič služby, je nutné vložit ji do skříň jak je popsáno v [nahradit modul řadiče zařízení StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Další kroky
* Pokud narazíte na potíže s řadičů zařízení StorSimple, které nelze vyřešit pomocí postupů uvedených v tomto kurzu [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Další informace o používání služby StorSimple Manager zařízení, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

