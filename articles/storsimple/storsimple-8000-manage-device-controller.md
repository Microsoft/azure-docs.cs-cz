---
title: Správa řadiče zařízení řady StorSimple 8000 | Dokumentace Microsoftu
description: Zjistěte, jak zastavit, restartovat, vypnout nebo obnovit vaše řadiče zařízení StorSimple.
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
ms.openlocfilehash: 9a5be85b917682afeaecd67a4176edb7040d5b4b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215425"
---
# <a name="manage-your-storsimple-device-controllers"></a>Správa řadičů zařízení StorSimple

## <a name="overview"></a>Přehled

Tento kurz popisuje různé operace, které lze provést u řadičů zařízení StorSimple. Kontrolery v zařízení StorSimple jsou řadiče redundantní (sdílené) v konfiguraci aktivní pasivní. Jenom jeden řadič v daném okamžiku aktivní a zpracovává všechny operace disku a sítě. Druhý kontroler je v pasivním režimu. Pokud se nezdaří aktivní kontroler, pasivní kontroler automaticky stane aktivním.

Tento kurz obsahuje podrobné pokyny pro správu pomocí řadiče zařízení:

* **Kontrolery** okna pro vaše zařízení ve službě Správce zařízení StorSimple.
* Windows PowerShell pro StorSimple.

Doporučujeme, abyste při správě řadiče zařízení prostřednictvím služby Správce zařízení StorSimple. Pokud akci lze provést pouze pomocí prostředí Windows PowerShell pro StorSimple, tento kurz vytvoří poznamenejte si ho.

Po přečtení tohoto kurzu, budete moct:

* Restartování nebo vypnutí řadiče zařízení StorSimple
* Vypněte zařízení StorSimple
* Obnoví výchozí tovární nastavení zařízení StorSimple

## <a name="restart-or-shut-down-a-single-controller"></a>Restartování nebo vypnutí jednoho kontroleru
Kontroler restartování nebo vypnutí se nevyžaduje jako součást operace normální systému. Operace vypnutí pro jedno zařízení řadiče jsou běžné pouze v případech, kdy zařízení se nezdařilo hardwarová komponenta vyžaduje nahrazení. Může také vyžadovat restartování řadiče v situaci, ve kterém výkon ovlivněn nadměrného využití paměti nebo chybně fungující kontroleru. Může také musíte restartovat řadič po úspěšné kontroleru nahrazení, pokud chcete povolit a nahradil kontroler testů.

Restartování zařízení není rušivé připojených iniciátory, za předpokladu, že ale pasivní kontroler není k dispozici. Pokud pasivní kontroler není k dispozici nebo není nastavená vypnout, pak restartovat aktivní kontroler může vést k přerušení služeb a výpadků.

> [!IMPORTANT]
> * **Spuštěné kontroleru by nikdy odebrat fyzicky jako by to vést ke ztrátě redundanci a vyššímu riziku výpadku.**
> * Následující postup platí jenom pro fyzickým zařízením StorSimple. Informace o tom, jak spuštění, zastavení a restartování cloudového zařízení StorSimple, naleznete v tématu [pracovat s cloudovým zařízením](storsimple-8000-cloud-appliance-u2.md##work-with-the-storsimple-cloud-appliance).

Může restartovat nebo vypnout kontroler jedno zařízení prostřednictvím webu Azure portal služby StorSimple Device Manager nebo prostředí Windows PowerShell pro StorSimple.

Ke správě řadičů zařízení z portálu Azure portal, postupujte následovně.

#### <a name="to-restart-or-shut-down-a-controller-in-azure-portal"></a>K restartování nebo vypnutí řadiče na webu Azure portal
1. Přejděte ve službě Správce zařízení StorSimple **zařízení**. Ze seznamu zařízení vyberte své zařízení. 

    ![Zvolte zařízení](./media/storsimple-8000-manage-device-controller/manage-controller1.png)

2. Přejděte na **Nastavení > řadiče**.
   
    ![Ověřte, zda jsou v dobrém stavu řadiče zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller2.png)
3. V **řadiče** okno, ověřte, zda je stav řadiče na zařízení s **pořádku**. Vyberte kontroler, klikněte pravým tlačítkem a pak vyberte **restartovat** nebo **vypnout**.

    ![Vyberte restartování nebo vypnutí řadiče zařízení StorSimple](./media/storsimple-8000-manage-device-controller/manage-controller3.png)

4. K restartování nebo vypnutí kontroleru se vytvoří úloha a zobrazí se vztahuje upozornění, pokud existuje. K monitorování, restartování nebo vypnutí, přejděte na **služby > protokoly aktivit** a vyfiltrujte parametry specifické pro vaši službu. Pokud byl vypnutý kontroleru, je potřeba zapnout kontroler ho chcete zapnout tlačítko napájení.

#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>K restartování nebo vypnutí řadiče v prostředí Windows PowerShell pro StorSimple
Proveďte následující kroky a vypnete nebo restartujete jednoho řadiče na zařízení StorSimple z prostředí Windows PowerShell pro StorSimple.

1. Přístup k zařízení prostřednictvím konzoly sériového portu nebo relace Telnetu ze vzdáleného počítače. Pro připojení k řadiči 0 nebo 1 řadič, postupujte podle kroků v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**.
3. Ve zprávě, poznamenejte si řadič jste připojeni k (řadič 0 a řadič 1) a zda je aktivní nebo pasivní kontroler (pohotovostní).
   
   * Vypnutí jednoho kontroleru, do příkazového řádku zadejte:
     
       `Stop-HcsController`
     
       Vypne kontroler, který jste připojeni. Chcete-li zrušit aktivní kontroler, pak zařízení převezme služby při selhání na pasivním kontroleru.

   * Restartovat kontroler, na příkazovém řádku zadejte:
     
       `Restart-HcsController`
     
       Tím se znovu spustí kontroler, který jste připojeni. Pokud restartujete aktivní kontroler, ji převezme služby při selhání do pasivního kontroleru před restartováním počítače.

## <a name="shut-down-a-storsimple-device"></a>Vypněte zařízení StorSimple

Tato část vysvětluje, jak vypnout průběžného nebo selhání zařízení StorSimple ze vzdáleného počítače. Zařízení je vypnuto po oba kontrolery zařízení jsou vypnutí. Vypnutí zařízení se provádí při zařízení fyzicky přesune nebo je vyřazen ze služby.

> [!IMPORTANT]
> Před vypnutím zařízení zkontrolujte stav součásti zařízení. Přejděte do svého zařízení a pak klikněte na tlačítko **Nastavení > Stav hardwaru**. V **zdravotní stav a hardware** okno, ověřte, zda je Indikátor stavu všech součástí zeleně. Zelený stav má pouze v dobrém stavu zařízení. Pokud vaše zařízení je vypínán dolů k nahrazení nefunkční komponenty, zobrazí se selhání (červená) nebo degradovaném stavu (žlutý) pro příslušné komponenty.


#### <a name="to-shut-down-a-storsimple-device"></a>Pro vypnutí zařízení StorSimple

1. Použití [restartování nebo vypnutí kontroleru](#restart-or-shut-down-a-single-controller) postup k identifikaci a vypnout pasivní kontroler na vašem zařízení. Tuto operaci můžete provést na webu Azure Portal nebo v prostředí Windows PowerShell pro StorSimple.
2. Opakujte předchozí krok vypnout aktivní kontroler.
3. Nyní musíte se podívejte na rovině back zařízení. Po dvou řadiče jsou zcela vypnout, by měl stav LED na obou řadičích blikat červené. Potřebujete vypnout nastavení zařízení zcela v tuto chvíli překlopit vypínač na napájení a chlazení moduly (PCMs) na jinou pozici. To by měl vypnout zařízení.

## <a name="reset-the-device-to-factory-default-settings"></a>Resetování zařízení do výchozího továrního nastavení

> [!IMPORTANT]
> Pokud je potřeba obnovit v zařízení výchozí tovární nastavení, obraťte se na Microsoft Support. Níže popsaný postup byste měli použít pouze ve spojení s Microsoft Support.

Tento postup popisuje, jak obnovit v Microsoft Azure StorSimple zařízení výchozí tovární nastavení pomocí Windows Powershellu pro StorSimple.
Když v zařízení odeberete všechna data a nastavení z celý cluster ve výchozím nastavení.

Proveďte následující kroky a obnovíte výchozí tovární nastavení zařízení Microsoft Azure StorSimple:

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Chcete-li obnovit zařízení do výchozího nastavení v prostředí Windows PowerShell pro StorSimple
1. Přístup k zařízení prostřednictvím konzoly sériového portu. Zkontrolujte zprávě k zajištění, že jste připojeni k **aktivní** kontroleru.
2. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlášení pomocí úplný přístup**.
3. Do příkazového řádku zadejte následující příkaz k resetování celý cluster, odebrání všech nastavení kontroleru, metadat a dat:
   
    `Reset-HcsFactoryDefault`
   
    Chcete-li obnovit místo jediného kontroleru, použijte [resetování HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) rutinu s `-scope` parametru.)
   
    Systém se restartuje více než jednou. Při obnovení byla úspěšně dokončena, budete upozorněni. V závislosti na modelu systému může trvat 45 – 60 minut pro zařízení s 8100 a 8600 k dokončení tohoto procesu 60 – 90 minut.
   
## <a name="questions-and-answers-about-managing-device-controllers"></a>Otázky a odpovědi týkající se správy řadiče zařízení
V této části jsme vytvořili souhrn některé nejčastější dotazy týkající se správy řadiče zařízení StorSimple.

**Otázka:** Co se stane, když oba řadiče na zařízení jsou v pořádku a nastavená na a jsem restartovat nebo vypnout aktivní kontroler?

**Odpověď:** Pokud jsou oba řadiče na zařízení v pořádku a vypnutá, zobrazí se výzva k potvrzení. Můžete se rozhodnout pro:

* **Restartovat aktivní kontroler** – budete upozorněni, že restartovat aktivní kontroler způsobila zařízení převzít služby při selhání na pasivním kontroleru. Restartování kontroleru.
* **Vypnout aktivní kontroler** – budete upozorněni, že vypíná aktivní kontroler výsledkem výpadek. Potřebujete také push na tlačítku napájení v zařízení zapnout kontroleru.

**Otázka:** Co se stane, pokud pasivní kontroler na zařízení je vypnutá nebo není k dispozici sleva a jsem restartovat nebo vypnout aktivní kontroler?

**Odpověď:** Pokud je pasivní kontroler na vašem zařízení není k dispozici nebo vypnut vypnuté a budete chtít:

* **Restartovat aktivní kontroler** – budete upozorněni, že pokračování operace bude mít za následek dočasné přerušení služby, a zobrazí se výzva k potvrzení.
* **Vypnout aktivní kontroler** – budete upozorněni, že budete pokračovat operaci výsledky nebude znamenat výpadek. Potřebujete také vložit na tlačítku napájení v jedné nebo obou řadičích k zapnutí nastavení v zařízení. Zobrazí se výzva k potvrzení.

**Otázka:** Když se řadič restartování nebo vypnutí nepodaří průběh?

**Odpověď:** Restartování nebo vypnutí kontroleru může selhat, pokud:

* Probíhá aktualizace zařízení.
* Již probíhá restartování řadiče.
* Vypnout kontroler již probíhá.

**Otázka:** Jak lze zjistit, pokud kontroler byl restartován nebo vypnut?

**Odpověď:** Můžete zkontrolovat stav řadiče v okně Kontroleru. Stav řadiče označí, zda kontroler Probíhá restartování nebo vypnutí. Kromě toho **výstrahy** okno obsahovat informační výstrahy, pokud kontroler je restartován nebo vypnut. Operace restartování a vypínání řadiče jsou také zaznamenány v protokolech aktivit. Další informace o protokolech aktivit, přejděte na [zobrazení protokolů aktivit](storsimple-8000-service-dashboard.md#view-the-activity-logs).

**Otázka:** Je k dispozici žádný vliv na vstupy/výstupy v důsledku kontroler převzetí služeb při selhání?

**Odpověď:** Připojení TCP mezi iniciátory a aktivní kontroler se resetují v důsledku kontroler převzetí služeb při selhání, ale bude navázán při operaci předpokládá pasivní kontroler. V průběhu této operace může být pause dočasných (méně než 30 sekund) v aktivitě vstupně-výstupní operace mezi iniciátory a zařízení.

**Otázka:** Jakým způsobem se vrátit Můj kontroler pro službu, jakmile se vypne a odebrat?

**Odpověď:** Kontroleru vrátit do služby, je třeba jej vložit do skříň. jak je popsáno v [nahradit modul řadiče na zařízení StorSimple](storsimple-8000-controller-replacement.md).

## <a name="next-steps"></a>Další postup
* Pokud narazíte na případné problémy s řadičů zařízení StorSimple, které nelze vyřešit pomocí postupů uvedených v tomto kurzu [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* Další informace o použití služby Správce zařízení StorSimple, přejděte na [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

