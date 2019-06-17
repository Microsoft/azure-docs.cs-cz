---
title: Nahraďte kontroleru zařízení StorSimple 8000 series | Dokumentace Microsoftu
description: Vysvětluje, jak odstranit a nahradit jeden nebo oba moduly řadiče na zařízení řady StorSimple 8000.
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
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61482790"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Nahraďte modul řadiče na zařízení StorSimple
## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak odstranit a nahradit jeden nebo oba řadiče modulů v zařízení StorSimple. Popisuje také základní logiku pro scénáře nahrazení jednoduchá a duální kontroleru.

> [!NOTE]
> Před provedením nahrazení řadiče, doporučujeme vám vždy aktualizovat firmware řadiče na nejnovější verzi.
> 
> Aby nedošlo k poškození ke svému zařízení StorSimple, vysunout kontroleru dokud indikátorů LED se zobrazují jako jedna z následujících akcí:
> 
> * Všechny světla jsou OFF.
> * Indikátor 3 ![zelená ikona zaškrtnutí](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), a ![červený křížek ikonu](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) se začne blikat, a LED 0 a LED 7 **ON**.


Následující tabulka uvádí scénáře podporované řadiče nahrazení.

| případ | Scénáře s výměnou | Příslušný postup popsaný |
|:--- |:--- |:--- |
| 1 |Jeden kontroler je v chybovém stavu, druhý kontroler je v pořádku a aktivní. |[Jeden kontroler nahrazení](#replace-a-single-controller), která popisuje [logice nahrazení jedním řadičem](#single-controller-replacement-logic), stejně jako [kroky nahrazení](#single-controller-replacement-steps). |
| 2 |Oba kontrolery selhat a vyžadovat nahrazení. Skříň, disky a skříně disku jsou v pořádku. |[Nahrazení dvěma řadiči](#replace-both-controllers), vystihuje [logiku za dvěma řadiči nahrazení](#dual-controller-replacement-logic), stejně jako [kroky nahrazení](#dual-controller-replacement-steps). |
| 3 |Jsou přehozeny řadiče ze stejné zařízení nebo z různých zařízení. Skříň, disky a skříně disku jsou v pořádku. |Zobrazí se zpráva upozornění neshoda slot. |
| 4 |Chybí jeden kontroler a druhý řadič se nezdaří. |[Nahrazení dvěma řadiči](#replace-both-controllers), vystihuje [logiku za dvěma řadiči nahrazení](#dual-controller-replacement-logic), stejně jako [kroky nahrazení](#dual-controller-replacement-steps). |
| 5 |Jeden nebo oba kontrolery se nezdařilo. Zařízení nemůže přistupovat prostřednictvím konzoly sériového portu nebo vzdálenou komunikaci prostředí Windows PowerShell. |[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) postup nahrazení ruční kontroleru. |
| 6 |Kontrolery mají jiné sestavení verze, které mohou být z důvodu:<ul><li>Verze různých softwarových mají řadiče.</li><li>Verze firmwaru různých mají řadiče.</li></ul> |Pokud verze softwaru kontroleru se liší, náhradní logiku zjistí, že a aktualizuje verze softwaru na řadiči nahrazení.<br><br>Pokud verze firmwaru řadič se liší a je starší verze firmwaru **není** automaticky je možné upgradovat, se zobrazí zpráva s upozorněním na webu Azure Portal. By měla vyhledávat aktualizace a nainstalujte aktualizace firmwaru.</br></br>Pokud verze firmwaru řadič se liší a je automaticky je možné upgradovat staré verze firmwaru, nahrazení logice kontroleru zjistí a po spuštění kontroleru firmware se automaticky aktualizují. |

Budete muset odebrat řadič modulu, pokud se nezdařilo. Jeden nebo oba řadiče moduly může selhat, což může znamenat jeden řadič nahrazení nebo nahradit aktuální soubor dvěma řadiči. Náhradní postupy a logiku za nimi stojí naleznete v následujících tématech:

* [Výměna jednoho kontroleru](#replace-a-single-controller)
* [Nahraďte oba kontrolery](#replace-both-controllers)
* [Odebrání zařízení](#remove-a-controller)
* [Vložit kontroleru](#insert-a-controller)
* [Identifikace aktivního kontroleru na vašem zařízení](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Před odebírání a nahrazování kontroleru, přečtěte si informace bezpečnost v [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Výměna jednoho kontroleru
Pokud mezi dva řadiče na zařízení Microsoft Azure StorSimple se nezdařila, nefunguje nebo chybí, je nutné k výměna jednoho kontroleru.

### <a name="single-controller-replacement-logic"></a>Jedním řadičem náhradní logiku
V nahrazení jedním řadičem byste měli nejprve odebrat kontroleru, který selhal. (Zbývající řadiče na zařízení je aktivní kontroler.) Když vložíte nahrazení řadiče, provedou se tyto akce:

1. Nahrazení řadiče okamžitě spustí komunikaci se zařízením StorSimple.
2. Na řadiči nahrazení se zkopíruje snímek virtuálního pevného disku (VHD) pro aktivní kontroler.
3. Snímek se mění tak, aby při nahrazení řadiče se spouští z tohoto virtuálního pevného disku, bude rozpoznán jako kontroler v pohotovostním režimu.
4. Po dokončení změn se nahrazení řadiče se spustí jako kontroler v pohotovostním režimu.
5. Po obou řadičích spuštěna, obsahuje clusteru online.

### <a name="single-controller-replacement-steps"></a>Jedním řadičem nahrazení kroky
Pokud selže některý kontroler v zařízení Microsoft Azure StorSimple, proveďte následující kroky. (Druhý kontroler musí být aktivní a v chodu. Pokud oba kontrolery selhat nebo selhání, přejděte na [kroky nahrazení dvěma řadiči](#dual-controller-replacement-steps).)

> [!NOTE]
> Může trvat 30 – 45 minut kontroleru restartovat a zcela obnovit z procedury nahrazení jediného kontroleru. Celkový čas pro celý postup, včetně připojení kabely, je přibližně 2 hodiny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Pro odebrání modulu jednoho kontroleru, který selhal
1. Na webu Azure Portal, přejděte do služby Správce zařízení StorSimple, klikněte na tlačítko **zařízení**a pak klikněte na název zařízení, které chcete monitorovat.
2. Přejděte na **sledování > Stav hardwaru**. Stav řadič 0 a řadič 1 by měla být červená, což znamená selhání.
   
   > [!NOTE]
   > Selhal řadič v nahrazení jedním řadičem je vždy kontroler v pohotovostním režimu.
   
3. Obrázek 1 a v následující tabulce použijte k vyhledání modulu kontroleru, který selhal.
   
    ![Propojovací rozhraní modulů primární skříň zařízení](./media/storsimple-controller-replacement/IC740994.png)
   
    **Obrázek 1** zařízení StorSimple z zpět
   
   | Štítek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na řadiči selhalo odeberte všechny připojené síťové kabely z portů, data. Pokud používáte modelu 8600, také odeberte SAS kabely, které se připojují ke kontroleru EBOD kontroleru.
5. Postupujte podle kroků v [odebrat řadič](#remove-a-controller) odebrání kontroleru, který selhal.
6. Nainstalujte náhradní objekt pro vytváření stejný slot, ze kterého byla odebrána kontroleru, který selhal. Tím se aktivuje jedním řadičem náhradní logiku. Další informace najdete v tématu [jeden kontroler náhradní logiku](#single-controller-replacement-logic).
7. Při nahrazení logiky jedním řadičem přejde na pozadí, znovu připojte kabely. Pečlivě připojte všechny kabely přesně stejným způsobem, že byly připojené před nahrazení.
8. Po restartování kontroleru, zkontrolujte **řadič** a **clusteru stav** na webu Azure Portal a ověřte, že kontroler je zpět do stavu v pořádku a je v pohotovostním režimu.

> [!NOTE]
> Pokud monitorujete zařízení prostřednictvím konzoly sériového portu, může zobrazit vícenásobné restartování, zatímco kontroler se zotavuje ze postup nahrazení. Když se zobrazí v nabídce konzoly sériového portu, pak víte, že nahrazení je kompletní. Pokud se nezobrazí v nabídce v rámci dvou hodin od nahrazení řadiče [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Počínaje verzí Update 4, můžete taky použít rutinu `Get-HCSControllerReplacementStatus` v rozhraní Windows PowerShell zařízení k monitorování stavu procesu nahrazení řadiče.
> 

## <a name="replace-both-controllers"></a>Nahraďte oba kontrolery
Když oba kontrolery zařízení Microsoft Azure StorSimple selhaly, jsou nefunguje správně nebo chybí, je třeba nahradit oba kontrolery. 

### <a name="dual-controller-replacement-logic"></a>Duální kontroleru náhradní logiku
V nahrazení dvěma řadiči nejprve odeberte oba kontrolery se nezdařilo a vložte nahrazení. Po vložení dvou nahrazení řadiče, provedou se tyto akce:

1. Nahrazení řadiče ve slotu 0 zkontroluje následující:
   
   1. Používá aktuální verze firmwaru a softwaru?
   2. Je součástí clusteru?
   3. Je řadič peer spuštěna a je v clusteru?
      
      Pokud žádná z těchto podmínek jsou splněny, kontroler hledá nejnovější denní zálohování (umístěný ve **nonDOMstorage** na jednotku S). Kontroler zkopíruje nejnovější snímek virtuálního pevného disku ze zálohy.
2. Kontroler do přihrádky 0 použitím snímku do vlastní image.
3. Mezitím adaptéru ve slotu 1 čeká řadič 0 pro dokončení vytvoření bitové kopie a spustit.
4. Po spuštění řadič 0 zjistí řadiče 1 vytvořené řadič 0, clusteru, který aktivuje jedním řadičem náhradní logiku. Další informace najdete v tématu [jeden kontroler náhradní logiku](#single-controller-replacement-logic).
5. Později poběží oba kontrolery a cluster se převede do online režimu.

> [!IMPORTANT]
> Po nahrazení dvěma řadiči po dokončení konfigurace zařízení StorSimple je nezbytné, že provedete ruční zálohování zařízení. Denní zálohy konfigurace zařízení se aktivuje až po uplynutí 24 hodin. Práce s [Microsoft Support](storsimple-8000-contact-microsoft-support.md) provést ruční zálohování vašeho zařízení.


### <a name="dual-controller-replacement-steps"></a>Kroky nahrazení duální kontroleru
Tento pracovní postup je potřeba při oba řadiče v zařízení Microsoft Azure StorSimple, které selhaly. To může nastat v datacentru, ve kterém chladicí systém přestane fungovat, a v důsledku toho selžou oba kontrolery v krátké době. V závislosti na tom, jestli zařízení StorSimple je vypnutý, nebo na a určuje, zda používáte 8600 nebo 8100 model, různé sady kroků je povinný.

> [!IMPORTANT]
> Může trvat 45 minut na 1 hodinu pro kontroler, restartování a zcela obnovit z procedury nahrazení dvěma řadiči. Celkový čas pro celý postup, včetně připojení kabely, je přibližně 2,5 hodin.

#### <a name="to-replace-both-controller-modules"></a>Chcete-li nahradit oba moduly kontroleru
1. Pokud se zařízení vypne, tento krok přeskočit a pokračovat k dalšímu kroku. Pokud je zařízení zapnuté, vypněte zařízení.
   
   1. Pokud používáte modelu 8600, nejprve vypnout primární skříň a vypněte EBOD skříň.
   2. Počkejte, dokud zařízení se úplně vypnout. Indikátorů LED zadní zařízení bude vypnuto.
2. Odeberte všechny síťové kabely, které jsou připojené k portům data. Pokud používáte modelu 8600, také odeberte SAS kabely, které se připojují ke skříni EBOD primární skříň.
3. Odebrání oba kontrolery zařízení StorSimple. Další informace najdete v tématu [odebrat řadič](#remove-a-controller).
4. Nejprve vložit objekt pro vytváření nahrazení pro řadič 0 a vložte řadič 1. Další informace najdete v tématu [vložit kontroleru](#insert-a-controller). Aktivuje se dvěma řadiči náhradní logiku. Další informace najdete v tématu [dvěma řadiči náhradní logiku](#dual-controller-replacement-logic).
5. Při nahrazení logice kontroleru přejde na pozadí, znovu připojte kabely. Pečlivě připojte všechny kabely přesně stejným způsobem, že byly připojené před nahrazení. V části Podrobné pokyny pro váš model v kabel vaše zařízení s [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Zapnutí zařízení StorSimple. Pokud používáte 8600 model:
   
   1. Ujistěte se, jestli skříň EBOD je zapnutá první.
   2. Počkejte, dokud běží EBOD skříň.
   3. Zapněte primární skříň.
   4. Po prvním řadiči restartuje a je v pořádku, systém bude spuštěn.
      
      > [!NOTE]
      > Pokud monitorujete zařízení prostřednictvím konzoly sériového portu, může zobrazit vícenásobné restartování, zatímco kontroler se zotavuje ze postup nahrazení. Jakmile se zobrazí v nabídce konzoly sériového portu, pak víte, že nahrazení je kompletní. Pokud se nezobrazí v nabídce v rámci 2,5 hodin od nahrazení řadiče [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Odebrání zařízení
Pomocí následujícího postupu pro odebrání modulu vadným kontroleru z vašeho zařízení StorSimple.

> [!NOTE]
> Na následujících obrázcích jsou pro řadič 0. Pro řadič 1 ty by vrátit zpět.


#### <a name="to-remove-a-controller-module"></a>Pro odebrání modulu kontroleru
1. Pochopit její podstatu západku modulu mezi thumb a ukazováčkem.
2. Jemně vměstnat thumb a ukazováčkem dohromady a uvolnit zámek kontroleru.
   
    ![Uvolnění kontroleru západky](./media/storsimple-controller-replacement/IC741047.png)
   
    **Obrázek 2** uvolnění kontroleru západky
3. Použijte zámek jako popisovač snímku odjede stranou kontroleru mimo skříni.
   
    ![Klouzavé kontroleru mimo skříně](./media/storsimple-controller-replacement/IC741048.png)
   
    **Obrázek 3** klouzavé kontroleru mimo skříň.

## <a name="insert-a-controller"></a>Vložit kontroleru
Použijte následující postup k instalaci modulu zadaný objekt pro vytváření adaptéru vadným modulu odebrali z vašeho zařízení StorSimple.

#### <a name="to-install-a-controller-module"></a>Chcete-li nainstalovat řadič modulu
1. Zkontrolujte, jestli je škody s konektory rozhraní. Pokud některé konektor PIN kódy jsou poškozeny nebo ohnuty není nainstalovaný modul.
2. Ukázat modulu řadič ve skříni a plně se uvolní zámek.
   
    ![Klouzavé řadiče do skříně](./media/storsimple-controller-replacement/IC741053.png)
   
    **Obrázek 4** posuvné řadiče do skříň.
3. S modulem kontroleru vložen proces zavření západku přitom pro nasdílení změn do skříni modulu kontroleru. Zámek se spojí vodítko kontroleru na místě.
   
    ![Zavření kontroleru západky](./media/storsimple-controller-replacement/IC741054.png)
   
    **Obrázek 5** zavření západku kontroleru
4. Pak budete hotovi, když přichytí zámek. **OK** LED by měla nyní být na.
   
   > [!NOTE]
   > Může trvat až 5 minut pro kontroler a LED k aktivaci.
  
5. Pokud chcete ověřit, jestli nahrazení se úspěšně dokončila, na webu Azure Portal, přejděte do svého zařízení a potom přejděte na **monitorování** > **stav hardwaru**a ujistěte se, že řadič 0 a řadič 1 jsou v pořádku (stav je zelená).

## <a name="identify-the-active-controller-on-your-device"></a>Identifikace aktivního kontroleru na vašem zařízení
Existuje mnoho situací, jako je například zařízení první registraci nebo řadič nahrazení, které vyžadují, abyste na zařízení StorSimple k aktivním řadiči. Aktivní kontroler zpracovává všechny disku firmwaru a síťové operace. Můžete použít některý z následujících metod k identifikace aktivního kontroleru:

* [Identifikace aktivního kontroleru pomocí webu Azure portal](#use-the-azure-portal-to-identify-the-active-controller)
* [Identifikace aktivního kontroleru pomocí prostředí Windows PowerShell pro StorSimple](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Zkontrolujte fyzické zařízení identifikace aktivního kontroleru](#check-the-physical-device-to-identify-the-active-controller)

Každá z těchto postupů je popsána dále.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Identifikace aktivního kontroleru pomocí webu Azure portal
Na webu Azure Portal, přejděte do svého zařízení a potom do **monitorování** > **stav hardwaru**a přejděte k položce **řadiče** oddílu. Zde můžete ověřit, který řadič není aktivní.

![Identifikace aktivního kontroleru na webu Azure portal](./media/storsimple-controller-replacement/IC752072.png)

**Obrázek 6** webu Azure portal zobrazující aktivní kontroler

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Identifikace aktivního kontroleru pomocí prostředí Windows PowerShell pro StorSimple
Při přístupu k zařízení prostřednictvím konzoly sériového portu, se zobrazí zpráva hlavičky. Zpráva hlavičky obsahuje základní informace, jako je model, název, verze nainstalovaného softwaru a stav řadiče, ke kterému přistupujete. Následující obrázek ukazuje příklad zpráva hlavičky:

![Zpráva hlavičky sériového portu](./media/storsimple-controller-replacement/IC741098.png)

**Obrázek 7** Banner zpráva zobrazující řadiče 0 jako aktivní

Zprávě můžete zjistit, jestli je na zařízení, které jsou připojené k aktivní nebo pasivní.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Zkontrolujte fyzické zařízení identifikace aktivního kontroleru
Identifikace aktivního kontroleru na vašem zařízení, vyhledejte modrá VEDLA nad DATY 5 port na zadní straně primární skříň.

Pokud tento Indikátor je bliká vám kontrolka, kontroleru je aktivní a druhý kontroler je v pohotovostním režimu. Jako vodítko použijte následující diagram a tabulky.

![Propojovací rozhraní systému primární skříň zařízení s datové porty](./media/storsimple-controller-replacement/IC741055.png)

**Obrázek 8** zadní primární skříň s porty dat a monitorování indikátorů LED

| Štítek | Popis |
|:--- |:--- |
| 1-6 |DATA 0 – 5 síťové porty |
| 7 |Modrý indikátor LED |

## <a name="next-steps"></a>Další postup
Další informace o [StorSimple hardwarové komponenty nahrazení](storsimple-8000-hardware-component-replacement.md).

