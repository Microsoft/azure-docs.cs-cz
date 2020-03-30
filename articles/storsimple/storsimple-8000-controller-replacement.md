---
title: Vyměňte řadič zařízení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Vysvětluje, jak odebrat a nahradit jeden nebo oba moduly řadiče na zařízení řady StorSimple 8000.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267921"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Výměna modulu ovladače na zařízení StorSimple
## <a name="overview"></a>Přehled
Tento kurz vysvětluje, jak odebrat a nahradit jeden nebo oba moduly řadiče v zařízení StorSimple. Také popisuje základní logiku pro scénáře nahrazení jednoho a duálního řadiče.

> [!NOTE]
> Před provedením výměny řadiče doporučujeme vždy aktualizovat firmware ovladače na nejnovější verzi.
> 
> Abyste zabránili poškození zařízení StorSimple, nevysuňte ovladač, dokud se led diody nezobrazí jako jedna z následujících:
> 
> * Všechna světla jsou vypnutá.
> * LED ![3, zelená](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)ikona ![kontroly](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) a ikona červeného kříže blikají a LED 0 a LED 7 jsou **zapnuté**.


V následující tabulce jsou uvedeny podporované scénáře nahrazení řadiče.

| Obchodní případ | Scénář nahrazení | Použitelný postup |
|:--- |:--- |:--- |
| 1 |Jeden řadič je ve stavu selhání, druhý řadič je v pořádku a aktivní. |[Výměna jednoho řadiče](#replace-a-single-controller), která popisuje [logiku za nahrazením jednoho řadiče](#single-controller-replacement-logic), stejně jako [kroky nahrazení](#single-controller-replacement-steps). |
| 2 |Oba řadiče selhaly a vyžadují výměnu. Šasi, disky a kryt disku jsou v pořádku. |[Výměna duálního řadiče](#replace-both-controllers), která popisuje [logiku výměny duálního řadiče](#dual-controller-replacement-logic), stejně jako [kroky výměny](#dual-controller-replacement-steps). |
| 3 |Ovladače ze stejného zařízení nebo z různých zařízení jsou vyměněny. Šasi, disky a skříně disků jsou v pořádku. |Zobrazí se výstražná zpráva o neshodě slotů. |
| 4 |Jeden řadič chybí a druhý řadič selže. |[Výměna duálního řadiče](#replace-both-controllers), která popisuje [logiku výměny duálního řadiče](#dual-controller-replacement-logic), stejně jako [kroky výměny](#dual-controller-replacement-steps). |
| 5 |Jeden nebo oba řadiče selhaly. K zařízení nelze přistupovat prostřednictvím sériové konzole nebo vzdálené komunikace prostředí Windows PowerShell. |[Obraťte se](storsimple-8000-contact-microsoft-support.md) na podporu společnosti Microsoft pro ruční výměnu řadiče postup. |
| 6 |Řadiče mají jinou verzi sestavení, což může být způsobeno:<ul><li>Ovladače mají jinou verzi softwaru.</li><li>Ovladače mají jinou verzi firmwaru.</li></ul> |Pokud se verze softwaru řadiče liší, logika nahrazení to zjistí a aktualizuje verzi softwaru na náhradním řadiči.<br><br>Pokud se verze firmwaru řadiče liší a stará verze firmwaru **není** automaticky upgradovatelná, zobrazí se na webu Azure Portal výstražná zpráva. Měli byste vyhledat aktualizace a nainstalovat aktualizace firmwaru.</br></br>Pokud se verze firmwaru řadiče liší a stará verze firmwaru je automaticky rozšiřitelná, logika nahrazení řadiče to zjistí a po spuštění řadiče bude firmware automaticky aktualizován. |

Pokud se nezdařilo, je třeba odebrat modul řadiče. Jeden nebo oba moduly řadiče mohou selhat, což může mít za následek výměnu jednoho řadiče nebo výměnu duálního řadiče. Náhradní postupy a logiku za nimi naleznete v následujících tématech:

* [Výměna jednoho kontroleru](#replace-a-single-controller)
* [Výměna obou ovladačů](#replace-both-controllers)
* [Odebrání ovladače](#remove-a-controller)
* [Vložení ovladače](#insert-a-controller)
* [Identifikace aktivního ovladače v zařízení](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Před odebráním a výměnou ovladače zkontrolujte bezpečnostní informace v [části Výměna hardwarové součásti StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Výměna jednoho kontroleru
Pokud jeden ze dvou řadičů na zařízení Microsoft Azure StorSimple selhal, nefunguje správně nebo chybí, je třeba vyměnit jeden řadič.

### <a name="single-controller-replacement-logic"></a>Logika nahrazení jednoho řadiče
Při nahrazení jedním řadičem byste měli nejprve odebrat vadný řadič. (Zbývající ovladač v zařízení je aktivní ovladač.) Při vložení náhradního řadiče dojde k následujícím akcím:

1. Náhradní řadič okamžitě začne komunikovat se zařízením StorSimple.
2. Na náhradní řadič se zkopíruje snímek virtuálního pevného disku (VHD) pro aktivní řadič.
3. Snímek je upraven tak, aby při spuštění náhradního řadiče z tohoto virtuálního pevného disku byl rozpoznán jako pohotovostní řadič.
4. Po dokončení úprav se jako pohotovostní řadič spustí náhradní ovladač.
5. Když jsou spuštěny oba řadiče, cluster je online.

### <a name="single-controller-replacement-steps"></a>Kroky výměny jednoho ovladače
Pokud jeden z řadičů ve vašem zařízení Microsoft Azure StorSimple selže, proveďte následující kroky. (Druhý řadič musí být aktivní a spuštěný. Pokud oba regulátory selžou nebo selhávají, přejděte k [krokům výměny duálního řadiče](#dual-controller-replacement-steps).)

> [!NOTE]
> Může trvat 30 – 45 minut, než se ovladač restartuje a zcela se zotaví z postupu výměny jediného ovladače. Celková doba celého postupu, včetně připojení kabelů, je přibližně 2 hodiny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Odebrání jednoho modulu řadiče se nezdařilo
1. Na webu Azure Portal přejděte na službu Správce zařízení StorSimple, klikněte na **zařízení**a potom klikněte na název zařízení, které chcete monitorovat.
2. Přejděte na **informace o stavu > hardwaru**. Stav řadiče 0 nebo řadiče 1 by měl být červený, což znamená selhání.
   
   > [!NOTE]
   > Vadný řadič v jedné výměně řadiče je vždy pohotovostní řadič.
   
3. Pomocí obrázku 1 a následující tabulky vyhledejte modul řadiče, který selhal.
   
    ![Zadní rovina primárních modulů skříně zařízení](./media/storsimple-controller-replacement/IC740994.png)
   
    **Obrázek 1** Zadní část zařízení StorSimple
   
   | Popisek | Popis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na vadném řadiči odeberte všechny připojené síťové kabely z datových portů. Pokud používáte model 8600, odstraňte také kabely SAS, které připojují řadič k řadiči EBOD.
5. Podle pokynů v [části Odebrání řadiče](#remove-a-controller) odeberte řadič, který selhal.
6. Nainstalujte tovární výměnu do stejného slotu, ze kterého byl odebrán vadný řadič. Tím se aktivuje logika nahrazení jednoho řadiče. Další informace naleznete v [tématu logika nahrazení jednoho řadiče](#single-controller-replacement-logic).
7. Zatímco logika nahrazení jednoho řadiče postupuje na pozadí, znovu připojte kabely. Dbát na připojení všech kabelů přesně stejným způsobem, že byly připojeny před výměnou.
8. Po restartování řadiče zkontrolujte **stav řadiče** a **stav clusteru** na portálu Azure, abyste ověřili, že je řadič zpět ve stavu V pořádku a je v pohotovostním režimu.

> [!NOTE]
> Pokud sledujete zařízení prostřednictvím sériové konzole, může se zobrazit více restartování, zatímco řadič se zotavuje z postupu nahrazení. Když je zobrazena nabídka sériové konzole, pak víte, že výměna je dokončena. Pokud se nabídka nezobrazí do dvou hodin od spuštění výměny řadiče, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
>
> Spuštění majesamozřejminy 4 můžete `Get-HCSControllerReplacementStatus` také použít rutinu v rozhraní prostředí Windows PowerShell zařízení ke sledování stavu procesu nahrazení řadiče.
> 

## <a name="replace-both-controllers"></a>Výměna obou ovladačů
Pokud oba řadiče na zařízení Microsoft Azure StorSimple selhaly, jsou nefunkční nebo chybí, je třeba nahradit oba řadiče. 

### <a name="dual-controller-replacement-logic"></a>Logika nahrazení duálního řadiče
Při výměně duálního řadiče nejprve odeberete oba řadiče, které selhaly, a potom vložíte náhradní. Po vložení dvou náhradních řadičů dojde k následujícím akcím:

1. Náhradní regulátor ve slotu 0 kontroluje následující:
   
   1. Používá aktuální verze firmwaru a softwaru?
   2. Je to součást clusteru?
   3. Je peer controller spuštěn a je seskupený?
      
      Pokud žádná z těchto podmínek není splněna, řadič hledá nejnovější denní zálohování (umístěné v **úložišti bez DOMstorage** na jednotce S). Řadič zkopíruje nejnovější snímek virtuálního pevného disku ze zálohy.
2. Řadič v patice 0 používá snímek k samotnému obrazu.
3. Mezitím ovladač v patici 1 čeká na dokončení zobrazení řadiče 0 a spuštění.
4. Po spuštění řadiče 0 řadič 1 detekuje cluster vytvořený řadičem 0, který aktivuje logiku nahrazení jednoho řadiče. Další informace naleznete v [tématu logika nahrazení jednoho řadiče](#single-controller-replacement-logic).
5. Poté budou spuštěny oba řadiče a cluster se přepne do režimu online.

> [!IMPORTANT]
> Po nahrazení duálního řadiče je po konfiguraci zařízení StorSimple nezbytné provést ruční zálohování zařízení. Denní zálohy konfigurace zařízení se spustí až po uplynutí 24 hodin. Spolupracujte s [podporou microsoftu](storsimple-8000-contact-microsoft-support.md) a proveďte ruční zálohování vašeho zařízení.


### <a name="dual-controller-replacement-steps"></a>Kroky výměny duálního ovladače
Tento pracovní postup je povinný, pokud se nezdařily oba řadiče ve vašem zařízení Microsoft Azure StorSimple. K tomu může dojít v datovém centru, ve kterém chladicí systém přestane fungovat, a v důsledku toho oba řadiče selžou v krátkém časovém období. V závislosti na tom, zda je zařízení StorSimple vypnuto nebo zapnuto a zda používáte model 8600 nebo 8100, je vyžadována jiná sada kroků.

> [!IMPORTANT]
> Může trvat 45 minut až 1 hodinu, než se ovladač restartuje a zcela se zotaví z postupu výměny duálního řadiče. Celková doba celého postupu, včetně připojení kabelů, je přibližně 2,5 hodiny.

#### <a name="to-replace-both-controller-modules"></a>Výměna obou modulů regulátoru
1. Pokud je zařízení vypnuté, tento krok přeskočte a pokračujte dalším krokem. Pokud je zařízení zapnuté, vypněte ho.
   
   1. Pokud používáte model 8600, nejprve vypněte primární skříň a potom vypněte skříň EBOD.
   2. Počkejte, až se zařízení úplně vypne. Všechny LED diody na zadní straně zařízení budou vypnuty.
2. Odeberte všechny síťové kabely, které jsou připojeny k datovým portům. Pokud používáte model 8600, odstraňte také kabely SAS, které připojují primární skříň ke skříni EBOD.
3. Odeberte oba řadiče ze zařízení StorSimple. Další informace naleznete [v tématu odebrání ovladače](#remove-a-controller).
4. Nejprve vložte tovární náhradu řadiče 0 a poté vložte ovladač 1. Další informace naleznete [v tématu vložení ovladače](#insert-a-controller). Tím se aktivuje logika nahrazení duálního řadiče. Další informace naleznete v [tématu logika nahrazení duálního řadiče](#dual-controller-replacement-logic).
5. Zatímco logika nahrazení řadiče postupuje na pozadí, znovu připojte kabely. Dbát na připojení všech kabelů přesně stejným způsobem, že byly připojeny před výměnou. Podrobné pokyny pro váš model naleznete v části Kabel zařízení [na instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Zapněte zařízení StorSimple. Pokud používáte model 8600:
   
   1. Ujistěte se, že je nejprve zapnutá skříň EBOD.
   2. Počkejte, až bude spuštěna skříň EBOD.
   3. Zapněte primární kryt.
   4. Po restartování prvního řadiče a je v pořádku stavu, systém bude spuštěn.
      
      > [!NOTE]
      > Pokud sledujete zařízení prostřednictvím sériové konzole, může se zobrazit více restartování, zatímco řadič se zotavuje z postupu nahrazení. Když se zobrazí nabídka sériové konzole, pak víte, že nahrazení je dokončeno. Pokud se nabídka nezobrazí do 2,5 hodiny od zahájení výměny řadiče, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Odebrání ovladače
Pomocí následujícího postupu odeberte vadný modul řadiče ze zařízení StorSimple.

> [!NOTE]
> Následující ilustrace jsou pro řadič 0. U řadiče 1 by byly obráceny.


#### <a name="to-remove-a-controller-module"></a>Odebrání modulu řadiče
1. Uchopte západku modulu mezi palcem a ukazováčkem.
2. Jemně stlačte palec a ukazováček k sobě, abyste uvolnili západku ovladače.
   
    ![Uvolňující západka regulátoru](./media/storsimple-controller-replacement/IC741047.png)
   
    **Obrázek 2** Uvolňující západka regulátoru
3. Pomocí zámku jako rukojeti vysuňte ovladač z podvozku.
   
    ![Posuvný regulátor z podvozku](./media/storsimple-controller-replacement/IC741048.png)
   
    **Obrázek 3** Vysunutí regulátoru z podvozku

## <a name="insert-a-controller"></a>Vložení ovladače
Po odebrání vadného modulu ze zařízení StorSimple použijte následující postup.

#### <a name="to-install-a-controller-module"></a>Instalace modulu řadiče
1. Zkontrolujte, zda nedošlo k poškození konektorů rozhraní. Neinstalujte modul, pokud je některý z kolíků konektoru poškozený nebo ohnutý.
2. Posuňte modul regulátoru do šasi, dokud je západka plně uvolněna.
   
    ![Posuvný regulátor do podvozku](./media/storsimple-controller-replacement/IC741053.png)
   
    **Obrázek 4** Posuvný regulátor do podvozku
3. S vloženým modulem regulátoru začněte zavírat západku a pokračujte v tlačení modulu regulátoru do šasi. Západka se zapíná, aby regulátor naváděla na místo.
   
    ![Uzavírací západka regulátoru](./media/storsimple-controller-replacement/IC741054.png)
   
    **Obrázek 5** Zavření západky regulátoru
4. Jste hotovi, když západka zapadne na místo. Ok **OK** LED by nyní měla být zapnutá.
   
   > [!NOTE]
   > Aktivace ovladače a LED diody může trvat až 5 minut.
  
5. Chcete-li ověřit, zda je nahrazení úspěšné, přejděte na webu Azure Portal do zařízení a přejděte na **stav hardwaru monitoru** > **Hardware health**a ujistěte se, že řadič 0 i řadič 1 jsou v pořádku (stav je zelený).

## <a name="identify-the-active-controller-on-your-device"></a>Identifikace aktivního ovladače v zařízení
Existuje mnoho situací, jako je například registrace poprvé zařízení nebo nahrazení řadiče, které vyžadují, abyste lokalizovali aktivní řadič na zařízení StorSimple. Aktivní řadič zpracovává veškerý firmware disku a síťové operace. K identifikaci aktivního řadiče můžete použít některou z následujících metod:

* [Použití portálu Azure k identifikaci aktivního řadiče](#use-the-azure-portal-to-identify-the-active-controller)
* [K identifikaci aktivního řadiče použijte prostředí Windows PowerShell pro StorSimple.](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Zkontrolujte fyzické zařízení k identifikaci aktivního ovladače](#check-the-physical-device-to-identify-the-active-controller)

Každý z těchto postupů je popsán dále.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Použití portálu Azure k identifikaci aktivního řadiče
Na webu Azure Portal přejděte na své zařízení a potom na **Sledování** > **stavu hardwaru**a přejděte do části **Řadiče.** Zde můžete ověřit, který řadič je aktivní.

![Identifikace aktivního řadiče na webu Azure Portal](./media/storsimple-controller-replacement/IC752072.png)

**Obrázek 6** Portál Azure zobrazující aktivní řadič

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>K identifikaci aktivního řadiče použijte prostředí Windows PowerShell pro StorSimple.
Při přístupu k zařízení prostřednictvím sériové konzole se zobrazí bannerová zpráva. Zpráva banneru obsahuje základní informace o zařízení, jako je model, název, nainstalovaná verze softwaru a stav řadiče, ke který přistupujete. Následující obrázek znázorňuje příklad bannerové zprávy:

![Sériová bannerová zpráva](./media/storsimple-controller-replacement/IC741098.png)

**Obrázek 7** Bannerová zpráva zobrazující řadič 0 jako aktivní

Pomocí bannerové zprávy můžete určit, zda je řadič, ke který jste připojeni, aktivní nebo pasivní.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Zkontrolujte fyzické zařízení k identifikaci aktivního ovladače
Chcete-li identifikovat aktivní ovladač v zařízení, vyhledejte modrou LED diodu nad portem DATA 5 na zadní straně primárního krytu.

Pokud tato LED dioda bliká, ovladač je aktivní a druhý ovladač je v pohotovostním režimu. Jako pomůcku použijte následující diagram a tabulku.

![Propojovací rozhraní primárního krytu zařízení s datovými porty](./media/storsimple-controller-replacement/IC741055.png)

**Obrázek 8** Zadní část primárního krytu s datovými porty a monitorovacími LED diodami

| Popisek | Popis |
|:--- |:--- |
| 1-6 |DATA 0 – 5 síťových portů |
| 7 |Modrá LED dioda |

## <a name="next-steps"></a>Další kroky
Další informace o [výměně hardwarových komponent StorSimple](storsimple-8000-hardware-component-replacement.md).

