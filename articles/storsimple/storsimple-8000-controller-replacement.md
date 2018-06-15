---
title: Nahraďte řadič zařízení řady StorSimple 8000 | Microsoft Docs
description: Vysvětluje, jak odeberete a nahradíte jeden nebo oba řadiče modulů ve vašem zařízení řady StorSimple 8000.
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
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 849eccff114c2fd6d952e44d095d0cc89a238675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875386"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Nahraďte modul řadiče zařízení StorSimple
## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak odeberete a nahradíte jeden nebo oba řadiče modulů v zařízení StorSimple. Popisuje také základní logiku pro scénáře nahrazení jednoduchá a Duální řadiče.

> [!NOTE]
> Před provedením nahrazení řadiče, doporučujeme vždy aktualizujte firmware řadiče na nejnovější verzi.
> 
> Chcete-li zabránit poškození do zařízení StorSimple, není vysunutí řadičem dokud indikátory LED zobrazují jako jednu z následujících:
> 
> * Všechny indikátory jsou OFF.
> * Indikátor 3, ![zelená ikona zaškrtnutí](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), a ![ikonu Red mezi](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) jsou přerušované, a jsou DIODU 0 a 7 DIODU **ON**.


Následující tabulka uvádí scénáře nahrazení podporované řadiče.

| Případ | Scénář nahrazení | Příslušný postup popsaný |
|:--- |:--- |:--- |
| 1 |Jeden řadič je ve stavu selhání, jiný řadič je v pořádku a aktivní. |[Jednotné nahrazení řadiče](#replace-a-single-controller), který popisuje [logiku za náhradní jediného kontroleru](#single-controller-replacement-logic), a taky [kroky nahrazení](#single-controller-replacement-steps). |
| 2 |Oběma řadičům selhaly a vyžadovat nahrazení. Skříň, disky a skříně disku jsou v pořádku. |[Dva řadiče nahrazení](#replace-both-controllers), který popisuje [logiku za náhradní dva řadiče](#dual-controller-replacement-logic), společně s [kroky nahrazení](#dual-controller-replacement-steps). |
| 3 |Řadiče ze stejné zařízení nebo z různých zařízení jsou vzájemně zaměněny. Skříň, disky a skříně disku jsou v pořádku. |Zobrazí se zpráva upozornění neshoda slot. |
| 4 |Chybí jeden řadič a jiný řadič se nezdaří. |[Dva řadiče nahrazení](#replace-both-controllers), který popisuje [logiku za náhradní dva řadiče](#dual-controller-replacement-logic), společně s [kroky nahrazení](#dual-controller-replacement-steps). |
| 5 |Jeden nebo oba řadiče se nezdařilo. Zařízení nelze přistupovat prostřednictvím konzoly sériového portu nebo vzdálenou komunikaci prostředí Windows PowerShell. |[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) postup nahrazení ruční řadiče. |
| 6 |V řadičích mít verzi různých sestavení, který může být z důvodu:<ul><li>Mají řadiče verze jiný software.</li><li>Verze firmwaru různých mají řadiče.</li></ul> |Pokud verze softwaru řadiče liší, logiku nahrazení rozpozná, že a aktualizací softwaru verze na nahrazení řadiče.<br><br>Pokud se liší verze firmwaru řadiče a je starší verze firmwaru **není** automaticky rozšiřitelný, se zobrazí zpráva s upozorněním na portálu Azure. Doporučujeme vyhledávání aktualizací a nainstalovat aktualizace firmwaru.</br></br>Pokud verze firmwaru řadiče se liší a je automaticky rozšiřitelný starší verze firmwaru, logice kontroleru nahrazení to zjistí a po spuštění kontroleru, firmwaru, bude automaticky aktualizovat. |

Je třeba odebrat modul řadiče, pokud se nezdařila. Jeden nebo oba řadiče moduly může selhat, což může vést ke jediného kontroleru náhrada nebo duální řadiče nahrazení. Náhradní postupy a logiku za je naleznete v následujících tématech:

* [Výměna jednoho kontroleru](#replace-a-single-controller)
* [Nahraďte oba řadiče](#replace-both-controllers)
* [Odebrání zařízení](#remove-a-controller)
* [Vložit řadič](#insert-a-controller)
* [Identifikovat řadič active na zařízení](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Před odebráním a nahrazení řadiče, informace zabezpečení v [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Nahraďte jediného kontroleru
Pokud jeden ze dvou řadičů v Microsoft Azure StorSimple zařízení se nezdařila, pracuje správně nebo chybí, je třeba nahradit jediného kontroleru.

### <a name="single-controller-replacement-logic"></a>Nahrazení logiku jediného kontroleru
V náhradní jediného kontroleru byste měli nejdřív odebrat řadičem se nezdařilo. (Zbývající řadič v zařízení je aktivní.) Když vložíte nahrazení řadiče, provedou se tyto akce:

1. Nahrazení řadiče okamžitě spustí komunikaci se zařízením StorSimple.
2. Snímek virtuálního pevného disku (VHD) pro aktivní řadič se zkopíruje na nahrazení řadiče.
3. Snímek se upravují tak, aby při nahrazení řadiče spuštění z tohoto virtuálního pevného disku, bude rozpoznán jako pohotovostní řadiče.
4. Po dokončení změny nahrazení řadiče se spustí jako pohotovostní řadič.
5. Pokud oběma řadičům spuštěné, cluster přepne do režimu online.

### <a name="single-controller-replacement-steps"></a>Kroky nahrazení jediného kontroleru
Pokud dojde k selhání jednoho z řadičů v zařízení s Microsoft Azure StorSimple, proveďte následující kroky. (Další řadič musí být aktivní a spuštěná. Pokud oba řadiče selhat nebo nebude fungovat správně, přejděte na [kroky nahrazení duální řadiče](#dual-controller-replacement-steps).)

> [!NOTE]
> Může trvat 30 – 45 minut pro řadič restartovat a zcela obnovit z procedury nahrazení jediného kontroleru. Celkový čas pro celý postup, včetně připojení kabely, je přibližně 2 hodiny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Chcete-li odebrat modul jeden řadič se nezdařilo
1. Na portálu Azure přejděte do služby Správce zařízení StorSimple, klikněte na tlačítko **zařízení**a potom klikněte na název zařízení, které chcete monitorovat.
2. Přejděte na **sledování > Stav hardwaru**. Stav řadič 0 a řadič 1 musí být red, což naznačuje selhání.
   
   > [!NOTE]
   > Selhání řadiče v náhradní jediného kontroleru je vždy řadič pohotovostní.
   
3. Pomocí obrázku 1 a v následující tabulce vyhledejte modul řadič se nezdařilo.
   
    ![Propojovací rozhraní systému modulů skříň primární zařízení](./media/storsimple-controller-replacement/IC740994.png)
   
    **Obrázek 1** zařízení StorSimple of zpět
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Řadič 0 |
   | 4 |Řadič 1 |
4. Odeberte všechny připojené síťové kabely z portů, data na řadičem se nezdařilo. Pokud používáte 8600 model, také odeberte SAS kabely, které se připojují k řadiči EBOD řadičem.
5. Postupujte podle kroků v [odebrání řadiče](#remove-a-controller) odebrat řadič se nezdařilo.
6. Nainstalujte nahrazení objektu pro vytváření stejný slot, ze kterého byl odebrán řadičem se nezdařilo. Tím se spustí logiku nahrazení jediného kontroleru. Další informace najdete v tématu [jednotné logiku nahrazení řadiče](#single-controller-replacement-logic).
7. Při nahrazení logiku jediného kontroleru hodnoty na pozadí, znovu se připojte kabely. Vezměte v potaz připojení všech kabelů přesně stejným způsobem, aby byly připojené před nahrazení.
8. Po restartování kontroleru, zkontrolujte **stav řadiče** a **clusteru stav** na portálu Azure k ověření, že je řadič je zpět do stavu v pořádku a v pohotovostním režimu.

> [!NOTE]
> Pokud sledujete zařízení prostřednictvím konzoly sériového portu, může se zobrazit více restartování při kontroleru je obnovení v postupu nahrazení. Když se v nabídce konzoly sériového portu, pak víte, že nahrazení je dokončen. Pokud nabídka se nezobrazí v rámci dvou hodin od nahrazení řadiče, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Spuštění aktualizace 4, můžete také použít rutinu `Get-HCSControllerReplacementStatus` v rozhraní Windows PowerShell zařízení k monitorování stavu procesu nahrazení řadiče.
> 

## <a name="replace-both-controllers"></a>Nahraďte oba řadiče
Pokud oba řadiče v zařízení Microsoft Azure StorSimple se nezdařilo, jsou nepracuje správně nebo chybí, je třeba nahradit oba řadiče. 

### <a name="dual-controller-replacement-logic"></a>Dva řadiče nahrazení logiky
V náhradní duální řadiče nejprve odeberte oba selhání řadiče a potom vložte náhrady. Při dvou nahrazení řadiče jsou vloženy, provedou následující akce:

1. Nahrazení řadiče do přihrádky 0 zkontroluje následující:
   
   1. Ji používá aktuální verze firmwaru a softwaru?
   2. Je součástí clusteru?
   3. Je řadič sdílené s a je v clusteru?
      
      Pokud žádná z těchto podmínek jsou splněny, řadičem hledá nejnovější denní zálohování (umístěný ve **nonDOMstorage** na jednotce S). Řadičem zkopíruje nejnovější snímek virtuálního pevného disku ze zálohy.
2. Řadič do přihrádky 0 používá snímku se image sám sebe.
3. Mezitím řadičem do přihrádky 1 čeká řadič 0 pro dokončení vytvoření bitové kopie a spustit.
4. Po spuštění řadič 0, zjistí řadič 1 clusteru vytvořené řadič 0, který aktivuje logiku nahrazení jednoho řadiče. Další informace najdete v tématu [jednotné logiku nahrazení řadiče](#single-controller-replacement-logic).
5. Pak se bude používat oba řadiče a clusteru přejde do režimu online.

> [!IMPORTANT]
> Následující dva řadiče náhradní po dokončení konfigurace zařízení StorSimple je nutné podniknout ruční zálohování zařízení. Denní zálohy konfigurace zařízení nejsou spustí až po uplynutí 24 hodin. Práce s [Microsoft Support](storsimple-8000-contact-microsoft-support.md) vytvářet ruční zálohu vašeho zařízení.


### <a name="dual-controller-replacement-steps"></a>Dva řadiče kroky nahrazení
Tento pracovní postup je potřeba při obě řadičů v zařízení Microsoft Azure StorSimple se nezdařilo. Toto může nastat v datacentru, ve kterém chladicí systém přestane fungovat, a v důsledku toho oběma řadičům selhání během krátké doby. Jinou sadu kroků je zapotřebí, v závislosti na tom, jestli zařízení StorSimple je vypnutý nebo v, a jestli používáte 8600 nebo 8100 model.

> [!IMPORTANT]
> 45 minut může trvat 1 hodinu řadiče pro restartování a zcela obnovit z procedury nahrazení duální řadiče. Celkový čas pro celý postup, včetně připojení kabely, je přibližně 2,5 hodin.

#### <a name="to-replace-both-controller-modules"></a>Chcete-li nahradit oba řadiče moduly
1. Pokud zařízení je vypnutý, tento krok přeskočit a přejít k dalšímu kroku. Pokud je zařízení zapnuté, vypněte zařízení.
   
   1. Pokud používáte 8600 model, nejprve vypnout primární skříň a vypněte EBOD skříň.
   2. Počkejte, dokud se úplně vypnout zařízení. Všechny LED zadní zařízení bude vypnuto.
2. Odeberte všechny síťové kabely, které jsou připojené k portům data. Pokud používáte 8600 model, také odeberte SAS kabely, které se připojují ke skříni EBOD primární skříň.
3. Odebrání oba řadiče zařízení StorSimple. Další informace najdete v tématu [odebrání řadiče](#remove-a-controller).
4. Nejprve vložte nahrazení objektu pro vytváření pro řadič 0 a potom vložte řadič 1. Další informace najdete v tématu [vložit řadič](#insert-a-controller). Tím se spustí logiku nahrazení duální řadiče. Další informace najdete v tématu [duální řadiče nahrazení logiku](#dual-controller-replacement-logic).
5. Při nahrazení logice kontroleru hodnoty na pozadí, znovu se připojte kabely. Vezměte v potaz připojení všech kabelů přesně stejným způsobem, aby byly připojené před nahrazení. V části Podrobné pokyny pro váš model v kabel vaše zařízení s [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Zapněte zařízení StorSimple. Pokud používáte 8600 model:
   
   1. Ujistěte se, že skříň EBOD zapnutá první.
   2. Počkejte, dokud skříni EBOD běží.
   3. Zapněte primární skříň.
   4. Po první řadič restartuje a je ve stavu v pořádku, bude spuštěna v systému.
      
      > [!NOTE]
      > Pokud sledujete zařízení prostřednictvím konzoly sériového portu, může se zobrazit více restartování při kontroleru je obnovení v postupu nahrazení. Jakmile se zobrazí v nabídce konzoly sériového portu, pak víte, pokud chcete nahrazení je dokončena. Pokud nabídka se nezobrazí v rámci 2,5 hodin od nahrazení řadiče, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Odebrání zařízení
Použijte následující postup k odebrání modul vadný řadiče zařízení StorSimple.

> [!NOTE]
> Následující ilustrace jsou pro řadič 0. Pro řadič 1 ty by vrátit zpět.


#### <a name="to-remove-a-controller-module"></a>Chcete-li odebrat modul řadiče
1. Pochopit západky modulu mezi Flash a ukazováčkem.
2. Jemně vměstnat Flash a ukazováčkem společně k uvolnění západky řadiče.
   
    ![Uvolnění západky řadiče](./media/storsimple-controller-replacement/IC741047.png)
   
    **Obrázek 2** uvolnění řadiče západky
3. Vysuňte řadičem mimo skříň pomocí zámek jako popisovač.
   
    ![Klouzavé řadiče mimo skříň](./media/storsimple-controller-replacement/IC741048.png)
   
    **Obrázek 3** klouzavé řadičem mimo skříň.

## <a name="insert-a-controller"></a>Vložit řadič
Použijte následující postup k instalaci modulu zadaný objekt factory kontroleru po odebrání modul vadný z zařízení StorSimple.

#### <a name="to-install-a-controller-module"></a>Chcete-li nainstalovat modul řadiče
1. Zkontrolujte, jestli jsou všechna poškození rozhraní konektory. Všechny kódy PIN konektor poškození nebo ohnuty není nainstalovaný modul.
2. Vysuňte modul řadiče do skříň. Když zámek plně vydání.
   
    ![Klouzavé řadiče do skříně](./media/storsimple-controller-replacement/IC741053.png)
   
    **Obrázek 4** posuvné řadiče do skříň.
3. S modulem řadiče vložit začněte zavření západky přitom dál tak, aby nabízel modul řadiče do skříň. Zámek se vymění Průvodce řadičem do místní.
   
    ![Zavření západky řadiče](./media/storsimple-controller-replacement/IC741054.png)
   
    **Obrázek 5** zavření západky řadiče
4. Jste hotovi, když přichytí zámek. **OK** DIODU by teď měly být na.
   
   > [!NOTE]
   > To může trvat až 5 minut pro kontroler a DIODU aktivovat.
  
5. Ověřte, že nahrazení úspěšná, na portálu Azure, přejděte na zařízení a pak přejděte do **monitorování** > **stavu hardwaru**a ujistěte se, že jsou v pořádku řadič 0 a řadič 1 (stav je zelená).

## <a name="identify-the-active-controller-on-your-device"></a>Identifikovat řadič active na zařízení
Je v mnoha situacích, jako je registrace nebo řadič nahrazení prvního zařízení, které vyžadují, abyste na zařízení StorSimple najít řadič služby active. Řadič active zpracovává všechny disku firmwaru a síťové operace. K identifikaci řadič active můžete použít některou z následujících metod:

* [Pomocí portálu Azure k identifikaci řadič active](#use-the-azure-portal-to-identify-the-active-controller)
* [Pomocí Windows Powershellu pro StorSimple k identifikaci řadič active](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Zkontrolujte fyzického zařízení k identifikaci řadič active](#check-the-physical-device-to-identify-the-active-controller)

Každý z těchto postupů je popsána dále.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Pomocí portálu Azure k identifikaci řadič active
Na portálu Azure přejděte do zařízení a pak na **monitorování** > **stavu hardwaru**a přejděte k položce **řadiče** části. Zde můžete ověřit, řadič, který je aktivní.

![Vyhledejte active řadič v portálu Azure](./media/storsimple-controller-replacement/IC752072.png)

**Obrázek 6** portálu Azure znázorňující řadič active

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Pomocí Windows Powershellu pro StorSimple k identifikaci řadič active
Při přístupu k zařízení prostřednictvím konzoly sériového portu, se zobrazí zpráva hlavičky. Zpráva hlavičky obsahuje základní informace, jako je model, název, verze nainstalovaného softwaru a stavu na zařízení, které se připojujete. Následující obrázek ukazuje příklad zpráva hlavičky:

![Zpráva sériové hlavičky](./media/storsimple-controller-replacement/IC741098.png)

**Obrázek 7** Banner zpráva zobrazující řadič 0 jako aktivní

Zpráva hlavičky můžete určit, zda je na zařízení, které jste připojeni k aktivní nebo pasivní.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Zkontrolujte fyzického zařízení k identifikaci řadič active
K identifikaci řadič active na vašem zařízení, vyhledejte blue DIODU výše DATA 5 port na zadní straně primární skříň.

Pokud tento indikátor LED bliká, kontroleru je aktivní a jiný řadič je v pohotovostním režimu. Použijte následující obrázek a tabulka jako pomocný.

![Propojovací rozhraní systému primární skříň zařízení s datové porty](./media/storsimple-controller-replacement/IC741055.png)

**Obrázek 8** zadní primární skříň s porty dat a monitorování LED

| Štítek | Popis |
|:--- |:--- |
| 1-6 |DATA 0 – 5 síťové porty |
| 7 |Modré DIODU |

## <a name="next-steps"></a>Další kroky
Další informace o [StorSimple hardwarové součásti nahrazení](storsimple-8000-hardware-component-replacement.md).

