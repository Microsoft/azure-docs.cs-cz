---
title: Zobrazovat a spravovat výstrahy pro zařízení řady StorSimple 8000 | Microsoft Docs
description: Popisuje výstrahy podmínek StorSimple a závažnost, jak nakonfigurovat oznámení výstrah a jak výstrahy můžete spravovat pomocí služby StorSimple Manager zařízení.
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
ms.workload: NA
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: e86b6af562208e51e36b4679fd088ea399ce70b8
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27745774"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Pomocí služby StorSimple Manager zařízení můžete zobrazit a spravovat výstrahy StorSimple

## <a name="overview"></a>Přehled

**Výstrahy** okno ve službě StorSimple Manager zařízení poskytuje způsob, jak ke kontrole a vymazat výstrahy související s zařízení StorSimple na základě v reálném čase. V tomto okně můžete centrálně monitorovat problémy stavu zařízení StorSimple a celkového řešení Microsoft Azure StorSimple.

Tento kurz popisuje běžné výstrahy podmínek, úrovně závažnosti výstrah a postup konfigurace oznámení výstrah. Kromě toho zahrnuje výstrahy Stručná referenční tabulky, které vám umožní rychle vyhledat konkrétní výstrahu a reagují odpovídajícím způsobem.

![stránka výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Obecné podmínky výstrah

Zařízení StorSimple generuje výstrahy v reakci na celou řadu podmínek. Následují nejčastější typy výstrah podmínek:

* **Problémy s hardwarem** – tyto výstrahy vás informovat o stav hardwaru. Umožňují vám vědět, pokud jsou potřeba upgrady firmwaru, pokud rozhraní sítě má problémy nebo pokud došlo k potížím s jedním z datových jednotkách.
* **Problémy s připojením k** – tyto výstrahy dojít, když se potíže při přenosu dat. Problémy s komunikací může dojít během přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku možností připojení mezi zařízeními a služby StorSimple Manager zařízení. Problémy s komunikací jsou některé nejtěžší opravit, protože nejsou k dispozici mnoho body selhání. Měli byste vždy nejprve ověřit, že než budete pokračovat k více pokročilá řešení problémů jsou k dispozici síťové připojení a přístup k Internetu. Nápovědu k řešení potíží, přejděte na [Poradce při potížích s rutinu Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou nastat, když systém nepracuje optimálně, například když je v případě velkého zatížení.

Kromě toho se může zobrazit upozornění týkající se zabezpečení, aktualizací nebo selhání úlohy.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrah

Výstrahy mají různé úrovně závažnosti, v závislosti na dopadu, který bude mít výstrahy situaci a není nutné pro reakci na výstrahy. Jsou úrovně závažnosti:

* **Kritické** – Tato výstraha je v reakci na podmínku, která ovlivňuje úspěšné výkon systému. Je potřeba zajistit, aby StorSimple služba není přerušena.
* **Upozornění** – tento stav může být důležité, pokud není vyřešené. Měli prozkoumat situaci a proveďte akce požadované zrušte problém.
* **Informace o** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správa systému.

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete zvolit, zda chcete oznámení e-mailové výstrahy podmínek pro každý z vašich zařízení StorSimple. Kromě toho můžete identifikovat ostatní příjemci oznámení výstrah zadáním jejich e-mailové adresy ve **ostatní příjemci e-mailu** pole, oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailové adresy na jedno zařízení.

Jakmile povolíte e-mailové oznámení pro zařízení, členové seznamu adresátů oznámení e-mailovou zprávu, dojde k kritickou výstrahu pokaždé, když obdrží. Odešle zprávy ze  *storsimple-alerts-noreply@mail.windowsazure.com*  a popíše podmínka upozornění. Můžete kliknout na příjemci **Unsubscribe** sami odebrat ze seznamu adresátů oznámení e-mailu.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Chcete-li povolit e-mailové oznámení výstrah pro zařízení.
1. Přejděte do služby Správce zařízení StorSimple. Seznam zařízení vyberte a klikněte na zařízení, které chcete nakonfigurovat.
2. Přejděte na **nastavení** > **Obecné** pro zařízení.

   ![Okno výstrahy](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. V **obecné nastavení** okno, přejděte na **výstrahy nastavení** a nastavte následující:
   
   1. V **odesílání e-mailové oznámení** pole, vyberte **Ano**.
   2. V **e-mailem správci služeb** pole, vyberte **Ano** do mají Správce služby a všechny spolusprávci přijímat oznámení výstrah.
   3. V **ostatní příjemci e-mailu** pole, zadejte e-mailové adresy všech příjemců, kterým mají být doručena oznámení výstrah. Zadejte názvy ve formátu  *someone@somewhere.com* . K oddělení e-mailové adresy použijte středníky. Můžete nakonfigurovat maximálně 20 e-mailové adresy na jedno zařízení. 
      
3. Pokud chcete poslat testovací e-mailová oznámení, klikněte na tlačítko **odeslat zkušební e-mail**. Služby StorSimple Manager zařízení se zobrazí stavové zprávy, jak předává testovací oznámení.

    ![nastavení výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Při odesílání e-mailu, test se zobrazí upozornění. 
   
    ![Výstrahy testování odeslaných e-mailové oznámení](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Pokud nelze odeslat testovací zprávu oznámení, služby StorSimple Manager zařízení se zobrazí příslušná chybová zpráva. Počkejte několik minut a potom se pokusíte odeslat testovací zprávu oznámení znovu. 

5. Jakmile dokončíte konfiguraci, klikněte na tlačítko **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Výstrahy testování odeslaných e-mailové oznámení](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Zobrazení a sledovat výstrahy

V okně Souhrn služby StorSimple Manager zařízení vám poskytne rychlý přehled na počet výstrah v zařízeních, uspořádané podle úrovně závažnosti.

![Řídicí panel výstrahy](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknutím na úroveň závažnosti otevře **výstrahy** okno. Výsledky budou zahrnovat pouze upozornění, která odpovídají této úrovně závažnosti.

Kliknutím na výstrahu v seznamu vám poskytne další podrobnosti k výstraze, včetně doby poslední výstrahy ohlásil, počet výskytů výstrahy na zařízení a doporučené akce k vyřešení výstrahy. Pokud je výstraha hardwaru, bude rovněž určit hardwarová součást.

![Příklad oznámení hardwaru](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Podrobnosti výstrahy můžete zkopírovat do textového souboru, pokud je potřeba poslat informace o Microsoft Support. Poté, co jste postupovali podle doporučení a vyřešit podmínka pro upozornění na místě, byste měli Vymazat výstrahu ze zařízení, tak, že vyberete výstrahy v **výstrahy** okno a kliknutím na **vymazat**. Zrušte více výstrah, vyberte jednotlivé výstrahy, klikněte na možnost žádný sloupec s výjimkou **výstraha** sloupec a pak klikněte na tlačítko **vymazat** po výběru všechny výstrahy vymazat. Všimněte si, že jsou některé výstrahy automaticky vymazány po vyřešení problému nebo když systém se novými informacemi aktualizují výstrahy.

Když kliknete na tlačítko **zrušte**, máte možnost zadat komentář výstraha a kroky, které jste si k vyřešení problému. Některé události budou vymazána v systému, pokud jiná událost se aktivuje se novými informacemi. V takovém případě se zobrazí následující zprávu.

![Vymazat výstrahu](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Řazení a zkontrolujte výstrahy

Možná bude efektivnější spustit sestavy na výstrahy, takže můžete zkontrolovat a poté je smažte ve skupinách. Kromě toho **výstrahy** okně může zobrazit až 250 výstrahy. Pokud byl překročen počet výstrah, zobrazí se ve výchozím zobrazení ne všechny výstrahy. Zkombinováním k přizpůsobení, které výstrahy se zobrazují následující pole:

* **Stav** – můžete zobrazit buď **Active** nebo **nezaškrtnuto** výstrahy. Aktivní výstrahy se stále aktivují v systému, když byly buď ručně vymazat správcem nebo prostřednictvím kódu programu vymazat, protože systém aktualizovat podmínka upozornění s informací o novém nezaškrtnuté výstrahy.
* **Závažnost** – můžete zobrazit výstrahy všechny úrovně závažnosti (kritická, upozornění, informace o) nebo pouze určité závažnosti, například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí z službu nebo jeden nebo všechna zařízení.
* **Čas rozsah** – zadáním **z** a **k** kalendářních dat a časových razítek, můžete se podívat na výstrahy během časové období, které vás zajímají.

![Seznam výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Stručná referenční příručka výstrahy

V následujících tabulkách jsou uvedeny některé z Microsoft Azure StorSimple výstrahy, které se můžete setkat, a také další informace a doporučení tam, kde je k dispozici. Výstrahy zařízení StorSimple spadat do jednoho z následujících kategorií:

* [Výstrahy připojení cloudu](#cloud-connectivity-alerts)
* [Výstrahy clusteru](#cluster-alerts)
* [Výstrahy pro zotavení po havárii](#disaster-recovery-alerts)
* [Výstrahy hardwaru](#hardware-alerts)
* [Upozornění na selhání úlohy](#job-failure-alerts)
* [Místně vázaný svazek výstrahy](#locally-pinned-volume-alerts)
* [Výstrahy sítě](#networking-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)
* [Podpora balíček výstrah](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení cloudu

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Připojení k <*název přihlašovacího údaje cloudu*> nelze navázat. |Nelze se připojit k účtu úložiště. |Zdá se, může být problém s připojením s vaším zařízením. Spusťte `Test-HcsmConnection` rutiny z rozhraní Windows Powershellu pro StorSimple na vašem zařízení a identifikovat a opravit potíže. Pokud jsou nastavení správná, může být problém s přihlašovacími údaji účtu úložiště, pro který byla výstraha vyvolána. V takovém případě použijte `Test-HcsStorageAccountCredential` rutiny určí, jestli jsou problémy, které lze vyřešit.<ul><li>Zkontrolujte nastavení sítě.</li><li>Zkontrolujte své přihlašovací údaje účtu úložiště.</li></ul> |
| Jsme neobdrželi prezenční signál ze zařízení pro poslední <*číslo*> minut. |Nelze se připojit k zařízení. |Zdá se, nastane problém s připojením s vaším zařízením. Použijte prosím `Test-HcsmConnection` rutiny z rozhraní Windows Powershellu pro StorSimple na vašem zařízení a identifikovat a opravit problém nebo se obraťte na správce sítě. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple chování při selhání připojení cloudu

Co se stane, pokud se nezdaří cloudu připojení zařízení StorSimple spuštění v produkčním prostředí?

Pokud cloudové připojení selže na produkční zařízení StorSimple, pak v závislosti na stavu zařízení, následující se může objevit:

* **Pro místní data na zařízení**: po určitou dobu, bude bez přerušení a čtení bude pokračovat ke zpracování. Však jako počet nezpracovaných vstupně-výstupních zvyšuje, překračuje limit, může spustit čtení selhání.

    V závislosti na množství dat na zařízení zápisů bude také dochází k výskytu první několik hodin po přerušení připojení k cloudu. Zápisů bude potom zpomalit a nakonec spusťte nezdaří, pokud připojení cloudu dojde k narušení po několik hodin. (Není dočasné úložiště na zařízení pro data, která se vloží do cloudu. Při odesílání dat vyprázdní této oblasti. V případě selhání připojení dat v této oblasti úložiště nebude vloží do cloudu, a vstupně-výstupní operace se nezdaří.)
* **Pro data v cloudu**: pro většinu cloudu k chybám připojení, je vrácena chyba. Po obnovení připojení jsou obnovit IOs, aniž by uživatel musel obnovit svazku online. Ve výjimečných případech může být potřeba navrácení svazek online z portálu Azure zásahu uživatele.
* **Pro cloudové snímky v průběhu**: operace je opakována několikrát v rámci 4 až 5 hodin a připojení není obnovena, se nezdaří cloudových snímků.

### <a name="cluster-alerts"></a>Výstrahy clusteru

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Zařízení se nepodařilo přes <*název zařízení*>. |Zařízení je v režimu údržby. |Zařízení se nezdařila. důvod zadáním nebo ukončení režimu údržby. To je normální a není vyžadována žádná akce. Po této výstrahy mít potvrzené, zrušte na stránce výstrahy. |
| Zařízení se nepodařilo přes <*název zařízení*>. |Právě bylo aktualizováno firmwaru zařízení nebo softwaru. |Došlo clusteru převzetí služeb při selhání kvůli aktualizaci. To je normální a není vyžadována žádná akce. Po této výstrahy mít potvrzené, zrušte na stránce výstrahy. |
| Zařízení se nepodařilo přes <*název zařízení*>. |Řadič vypnout nebo restartovat. |Zařízení při selhání, protože řadič active byl vypnutý nebo restartovaný správcem. Není vyžadována žádná akce. Po této výstrahy mít potvrzené, zrušte na stránce výstrahy. |
| Zařízení se nepodařilo přes <*název zařízení*>. |Plánované převzetí služeb při selhání. |Ověřte, že bylo plánované převzetí služeb při selhání. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| Zařízení se nepodařilo přes <*název zařízení*>. |Neplánované převzetí služeb při selhání. |StorSimple je integrována se automaticky zotavit neplánované převzetí služeb při selhání. Pokud se zobrazí velký počet tyto výstrahy, obraťte se na Microsoft Support. |
| Zařízení se nepodařilo přes <*název zařízení*>. |Jiné nebo neznámé příčina. |Pokud se zobrazí velký počet tyto výstrahy, obraťte se na Microsoft Support. Jakmile je problém vyřešen, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| Služba kritické zařízení hlásí stav jako neúspěšná. |DataPath selhání služby. |Požádejte o pomoc Microsoft Support. |
| Virtuální IP adresy pro síťové rozhraní <*DATA #*> oznámení stavu jako neúspěšná. |Jiné nebo neznámé příčina. |Někdy dočasné stavy může způsobit, že tyto výstrahy. Pokud je to tento případ, pak tato výstraha bude automaticky vymazán po určité době. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. |
| Virtuální IP adresy pro síťové rozhraní <*DATA #*> oznámení stavu jako neúspěšná. |Název rozhraní: <*DATA #*> IP adresu <IP address> nelze do online režimu, protože byla zjištěna duplicitní IP adresu v síti. |Ujistěte se, že duplicitní IP adresa je odebrat ze sítě nebo překonfigurujte rozhraní s jinou IP adresu. |

### <a name="disaster-recovery-alerts"></a>Výstrahy pro zotavení po havárii

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Operace obnovení nelze obnovit všechna nastavení pro tuto službu. Data konfigurace zařízení je v nekonzistentním stavu pro některá zařízení. |Data byla zjištěna nekonzistence po zotavení po havárii. |Šifrovaná data ve službě není synchronizován s, v zařízení. Autorizace zařízení <*název zařízení*> ve Správci zařízení zahájíte proces synchronizace StorSimple. Použít rozhraní Windows Powershellu pro StorSimple ke spuštění `Restore-HcsmEncryptedServiceData` na zařízení <*název zařízení*> rutiny poskytování staré heslo jako vstup do této rutiny obnovit profil zabezpečení. Spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutiny aktualizovat šifrovacího klíče dat služby. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |

### <a name="hardware-alerts"></a>Výstrahy hardwaru

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Hardwarová součást <*ID součásti*> oznámení stavu jako <*stav*>. | |Někdy dočasné stavy může způsobit, že tyto výstrahy. Pokud ano, tato výstraha bude automaticky vymazán po určité době. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. |
| Chybně fungující pasivní řadiče. |Pasivní (sekundární) řadič nefunguje. |Zařízení je funkční, ale jeden z vašich řadičů nepracuje správně. Zkuste restartovat kontroleru. Pokud se problém nevyřeší, obraťte se na Microsoft Support. |

### <a name="job-failure-alerts"></a>Upozornění na selhání úlohy

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Zálohování <*ID skupiny svazku zdroje*> se nezdařilo. |Úloha zálohování se nezdařila. |Problémy s připojením k může bránit úspěšně dokončení operace zálohování. Pokud nejsou žádné problémy s připojením, může bylo dosaženo maximálního počtu záloh. Odstraňte všechny zálohy, které již nejsou potřebné a operaci opakujte. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| Klonovat z <*zdroje ID zálohování prvků*> pro <*cílový svazek sériová čísla*> se nezdařilo. |Úloha klonování se nezdařila. |Aktualizace seznamu zálohování a ověřte, zda zálohování je stále platný. Pokud záloha není platná, je možné, že úspěšně dokončení operace klonování brání problémy s připojením k cloudu. Pokud nejsou žádné problémy s připojením, pravděpodobně bylo dosaženo limitu úložiště. Odstraňte všechny zálohy, které již nejsou potřebné a operaci opakujte. Po přijetí příslušné akce k vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| Obnovení z <*zdroje ID zálohování prvků*> se nezdařilo. |Obnovení úlohy se nezdařilo. |Aktualizace seznamu zálohování a ověřte, zda zálohování je stále platný. Pokud záloha není platná, je možné, že potíže s připojením k cloudu brání úspěšně dokončení operace obnovení. Pokud nejsou žádné problémy s připojením, pravděpodobně bylo dosaženo limitu úložiště. Odstraňte všechny zálohy, které již nejsou potřebné a operaci opakujte. Po přijetí příslušné akce k vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |

### <a name="locally-pinned-volume-alerts"></a>Místně vázaný svazek výstrahy

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Vytvoření místního svazku <*název svazku*> se nezdařilo. |Úloha vytvoření svazku se nezdařilo. <*Chybová zpráva odpovídající pomocí kódu chyby se nezdařila*>. |Problémy s připojením k může bránit úspěšné dokončení operace vytváření místa. Místně vázaných svazků jsou tlustě zřízený a proces vytváření místo zahrnuje přesahu vrstvené svazky do cloudu. Pokud nejsou žádné problémy s připojením, může pravděpodobně vyčerpala volné místo v zařízení. Určení, zda místo existuje v zařízení před opakovaným pokusem o tuto operaci. |
| Rozšíření místní svazek <*název svazku*> se nezdařilo. |Úloha změny svazku se nezdařila z důvodu <*chybová zpráva odpovídající pomocí kódu chyby se nezdařila*>. |Problémy s připojením k může bránit úspěšné dokončení operace rozšíření svazku. Místně vázaných svazků jsou tlustě zřízený a proces rozšíření stávající prostor zahrnuje přesahu vrstvené svazky do cloudu. Pokud nejsou žádné problémy s připojením, může pravděpodobně vyčerpala volné místo v zařízení. Určení, zda místo existuje v zařízení před opakovaným pokusem o tuto operaci. |
| Převod svazku <*název svazku*> se nezdařilo. |Úloha převedení svazku převod na typ svazku z místně připnut k vrstvené se nezdařilo. |Převod svazku typu místně vázaný k vrstvené nebylo možné dokončit. Ujistěte se, že neexistují žádné problémy s připojením k zabránění úspěšně dokončení operace. Pro řešení potíží s připojením problémy, přejděte na [Poradce při potížích s rutinu Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní místně vázaný svazek nyní byl označen jako vrstvený svazek vzhledem k tomu, že některá data z místně vázaný svazek má uniknout do cloudu při převodu. Výsledná vrstvený svazek je stále zabírá volné místo na zařízení, které nelze znovu použít pro budoucí místní svazky.<br>Vyřešte problémy s připojením, vymažte výstrahy a tento svazek převést zpět na původní typ místně vázaný svazek k zajištění všech dat je k dispozici místně znovu. |
| Převod svazku <*název svazku*> se nezdařilo. |Úloha převedení svazku pro převod z typu svazku vrstvené pro místně vázaný se nezdařilo. |Převod svazku typu vrstvené pro místně vázaný nelze dokončit. Ujistěte se, že neexistují žádné problémy s připojením k zabránění úspěšně dokončení operace. Pro řešení potíží s připojením problémy, přejděte na [Poradce při potížích s rutinu Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní vrstvený svazek je nyní označena jako místně vázaný svazek, jako součást procesu převodu nadále data uložená v cloudu, zatímco se už nedá uvolnit místo na tlustě zřízený v zařízení pro tento svazek pro budoucí místní svazky.<br>Vyřešte problémy s připojením, vymažte výstrahy a převést zpátky na původní typ vrstvený svazek k zajištění, že místní místo tlustě zřízený v zařízení můžete znovu použít tento svazek. |
| Blíží energie volné místo pro místní snímky <*název skupiny svazku*> |Místní snímky zásady zálohování může být brzy k dispozici dostatek místa a byla zneplatněna k vyloučení chyb při zápisu hostitele. |Časté místní snímky spolu s vysokou data změn ve svazcích, spojených s touto skupinou zásady zálohování způsobují volné místo na zařízení využívat rychle. Odstraňte všechny místní snímky, které už nejsou potřeba. Také aktualizujte vaše místní snímek plány pro tyto zásady zálohování k pořízení snímků méně častá místní a zajistit, aby se pravidelně přijata cloudových snímků. Pokud tyto akce se nezavedou, volné místo pro tyto snímky brzy byl vyčerpán a systém se automaticky odstraní, abyste zajistili, že zápisy hostitele dále úspěšně zpracovat. |
| Místní snímky pro <*název skupiny svazku*> se zrušila platnost. |Místní snímky pro <*název skupiny svazku*> byla zrušena a poté odstranit, protože jejich byly překročení volné místo v zařízení. |Aby to není v budoucnu opakovat, zkontrolujte místní snímek plány pro tyto zásady zálohování a odstraňte všechny místní snímky, které už nejsou potřeba. Časté místní snímky spolu s vysokou data změn ve svazcích, spojených s touto skupinou zásady zálohování může způsobit volné místo na zařízení využívat rychle. |
| Obnovení z <*zdroje ID zálohování prvků*> se nezdařilo. |Úlohu obnovení se nezdařilo. |Pokud máte místně vázaný nebo směs místně vázaný a vrstvené svazky v zásady zálohování, obnovení seznamu zálohování a ověřte, že zálohování je stále platný. Pokud záloha není platná, je možné, že potíže s připojením k cloudu brání úspěšně dokončení operace obnovení. Místně vázaných svazků obnovených v rámci této skupiny snímku všechna svá data do zařízení stahovat nemají, a pokud máte směs vrstvené a místně vázaných svazků v této skupině snímku, nebudou synchronizovány mezi sebou. Chcete-li úspěšně dokončit operaci obnovení, trvat svazky v této skupině do offline režimu na hostiteli a opakujte operaci obnovení. Všimněte si, že veškeré úpravy data na svazku, které byly provedeny během procesu obnovení budou ztraceny. |

### <a name="networking-alerts"></a>Výstrahy sítě

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |
| Pro 'Data0' zjištěna duplicitní IP adresu. | |Systém zjistil konflikt pro IP adresu, 10.0.0.1'. Síťovému prostředku 'Data0' na zařízení  *<device1>*  je offline. Ujistěte se, že tato IP adresa není používán ostatní entity v této síti. K odstraňování problémů se sítí, přejděte na [Poradce při potížích s rutinu Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Obraťte se na správce sítě pro pomoc při řešení tohoto problému. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |
| Adresa IPv4 (nebo IPv6) pro 'Data0' je offline. | |Síťovému prostředku "Data0" s IP adresou, 10.0.0.1." a délka, 22, na zařízení předpony  *<device1>*  je offline. Zajistěte, aby byly provozní porty přepínače, ke kterým je připojen toto rozhraní. K odstraňování problémů se sítí, přejděte na [Poradce při potížích s rutinu Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |URLthat se používá k ověření není dostupný. Ujistěte se, že vašich pravidlech brány firewall zahrnují vzory adresa URL zadaná pro zařízení StorSimple. Další informace o vzorů adresy URL na portálu Azure přejděte na https://aka.ms/ss-8000-network-reqs. Pokud používáte Azure Cloud vlády, přejděte na adresu URL vzory v https://aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Byla překročena zatížení zařízení <*prahová hodnota*>. |Nižší než očekávané doby odezvy. |Vaše zařízení sestavy využití v případě velkého zatížení vstupu a výstupu. To může způsobit, že vaše zařízení nebude fungovat stejně jako by měl. Zkontrolujte zatížení připojit do zařízení a určí, zda jsou k dispozici, může přesunout na jiné zařízení nebo které již nejsou potřebné.| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |a aktuální stav, přejděte na [použít službu StorSimple Manager zařízení k monitorování zařízení](storsimple-8000-monitor-device.md) |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Zahájení relace Microsoft Support. |Relace používaná podporu jiných výrobců. |Potvrďte, že je tento přístup oprávnění. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| Heslo pro <*element*> do vypršení platnosti <*dobu*>. |Se blíží vypršení platnosti hesla. |Změňte si heslo dřív, než vyprší. |
| Informace o konfiguraci zabezpečení pro <*ID elementu*>. | |Svazky přidružené k tomuto kontejneru svazků nelze použít k replikaci konfiguraci zařízení StorSimple. Aby se zajistilo, že data bezpečně uložena, doporučujeme odstranění kontejneru svazků a svazky přidružené kontejneru svazků. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |
| <*číslo*> pokusů o přihlášení se nezdařilo pro <*ID elementu*>. |Více neúspěšné pokusy o přihlášení. |Vaše zařízení může být terčem útoku nebo se autorizovaný uživatel snaží připojit pomocí nesprávného hesla.<ul><li>Obraťte se na oprávněným uživatelům a ověřte, že tyto pokusy byly ze skutečného zdroje. Pokud budete pokračovat v tématu velkého počtu neúspěšných pokusů o přihlášení, zvažte zakázání vzdálené správy a kontaktovat správce sítě. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy.</li><li>Zkontrolujte, zda vaše instance Snapshot Manager jsou nakonfigurovány s správné heslo. Po přijetí příslušné akce, zrušte zaškrtnutí této výstrahy na stránce výstrahy.</li></ul>Další informace, přejděte na [změnit heslo k zařízení s vypršenou platností](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Jedno nebo více selhání došlo k chybě při změně šifrovacího klíče dat služby. | |Došlo k při změně šifrovacího klíče dat služby došlo k chybám. Po mít řešit chybové stavy, spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutiny z rozhraní Windows Powershellu pro StorSimple na zařízení k aktualizaci služby. Pokud potíže potrvají, kontaktujte podporu Microsoftu. Po vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |

### <a name="support-package-alerts"></a>Podpora balíček výstrah

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Vytvoření balíčku pro podporu se nezdařilo. |Balíček nelze generovat StorSimple. |Tuto operaci opakujte. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. Po vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrahy. |

## <a name="next-steps"></a>Další postup

Další informace o [StorSimple chyb a potíží s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md).

