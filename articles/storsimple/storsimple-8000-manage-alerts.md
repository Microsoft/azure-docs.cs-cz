---
title: Zobrazení a správa upozornění pro zařízení řady StorSimple 8000
description: Popisuje StorSimple výstražné podmínky a závažnost, jak nakonfigurovat oznámení výstrah a jak používat službu StorSimple Device Manager ke správě výstrah.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: ff50836e1438b8d35f26ddfdf165084406f52faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267817"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Zobrazení a správa výstrah StorSimple pomocí služby StorSimple Device Manager

## <a name="overview"></a>Přehled

Okno **Výstrahy** ve službě StorSimple Device Manager poskytuje způsob, jak zkontrolovat a vymazat výstrahy související se zařízením StorSimple v reálném čase. Z tohoto okna můžete centrálně sledovat problémy se stavem vašich zařízení StorSimple a celkové řešení Microsoft Azure StorSimple.

Tento kurz popisuje běžné podmínky výstrahy, úrovně závažnosti výstrah a konfiguraci oznámení výstrah. Kromě toho obsahuje tabulky rychlých odkazů výstrah, které umožňují rychle vyhledat konkrétní výstrahu a odpovídajícím způsobem reagovat.

![Stránka Upozornění](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Běžné výstražné podmínky

Vaše zařízení StorSimple generuje výstrahy v reakci na různé podmínky. Nejběžnější typy výstražných podmínek jsou následující:

* **Problémy s hardwarem** – tyto výstrahy informují o stavu hardwaru. Dají vám vědět, zda jsou potřeba upgrady firmwaru, pokud má síťové rozhraní problémy nebo pokud dojde k potížím s jednou z vašich datových jednotek.
* **Problémy s připojením** – K těmto výstrahám dochází, pokud je při potížích s přenosem dat. Problémy s komunikací může dojít při přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku připojení mezi zařízeními a službou StorSimple Device Manager. Problémy s komunikací jsou některé z nejtěžších opravit, protože existuje tolik bodů selhání. Před pokračováním v pokročilejším řešení potíží byste měli vždy nejprve ověřit, zda je k dispozici připojení k síti a přístup k Internetu. Nápovědu k řešení potíží naleznete [v části Poradce při potížích s rutinou Testovací připojení](storsimple-8000-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny, když váš systém nepracuje optimálně, například když je pod velkým zatížením.

Kromě toho se mohou zobrazit výstrahy týkající se zabezpečení, aktualizací nebo selhání úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrahy

Výstrahy mají různé úrovně závažnosti v závislosti na dopadu, který bude mít situace výstrahy, a na potřebě odpovědi na výstrahu. Úrovně závažnosti jsou následující:

* **Kritické** – Tato výstraha je v reakci na podmínku, která ovlivňuje úspěšný výkon systému. Akce je nutné zajistit, že služba StorSimple není přerušena.
* **Upozornění** – Tento stav může být kritický, pokud není vyřešen. Měli byste prozkoumat situaci a přijmout veškerá opatření potřebná k odstranění problému.
* **Informace** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správě systému.

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete si vybrat, zda chcete být upozorněni e-mailem na podmínky výstrahy pro každé z vašich zařízení StorSimple. Kromě toho můžete identifikovat další příjemce oznámení upozornění zadáním jejich e-mailové adresy do pole **Ostatní příjemci e-mailu,** oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailových adres na zařízení.

Po povolení e-mailového oznámení pro zařízení obdrží členové seznamu oznámení e-mailovou zprávu pokaždé, když dojde k kritické výstraze. Zprávy budou odeslány z *storsimple-alerts-noreply\@mail.windowsazure.com* a bude popisovat stav výstrahy. Příjemci se můžou klepnutím na **odhlásit odebrat** ze seznamu e-mailových oznámení.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Povolení e-mailových upozornění na upozornění pro zařízení
1. Přejděte do služby Správce zařízení StorSimple. Ze seznamu zařízení vyberte a klikněte na zařízení, které chcete konfigurovat.
2. Přejděte k **obecnému nastavení** > **General** zařízení.

   ![Okno Upozornění](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. V okně **Obecné nastavení** přejděte na **Nastavení výstrah** a nastavte následující:
   
   1. V poli **Odeslat e-mailové oznámení** vyberte **ANO**.
   2. V poli **Správci e-mailových služeb** vyberte **ANO,** aby správce služby a všichni spolusprávci obdrželi upozornění.
   3. Do pole **Ostatní příjemci e-mailu** zadejte e-mailové adresy všech ostatních příjemců, kteří by měli dostávat upozornění. Zadejte jména ve *formátu, který někdo\@somewhere.com*. K oddělení e-mailových adres použijte středníky. Můžete nakonfigurovat maximálně 20 e-mailových adres na zařízení. 
      
3. Chcete-li odeslat oznámení o testovacím e-mailu, klepněte na tlačítko **Odeslat testovací e-mail**. Služba StorSimple Device Manager zobrazí stavové zprávy, když předá oznámení testu.

    ![Nastavení výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Při odeslání testovacího e-mailu se zobrazí oznámení. 
   
    ![E-mail s upozorněními testuje oznámení odeslané](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Pokud testovací oznámení nelze odeslat, služba Správce zařízení StorSimple zobrazí příslušnou chybovou zprávu. Počkejte několik minut a zkuste znovu odeslat zprávu o testovacím oznámení. 

5. Po dokončení konfigurace klepněte na tlačítko **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![E-mail s upozorněními testuje oznámení odeslané](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okno souhrnu služby StorSimple Device Manager poskytuje rychlý pohled na počet výstrah na vašich zařízeních, uspořádaných podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknutím na úroveň závažnosti se otevře okno **Výstrahy.** Výsledky zahrnují pouze výstrahy, které odpovídají této úrovni závažnosti.

Kliknutím na výstrahu v seznamu získáte další podrobnosti o výstraze, včetně posledního oznámení, počtu výskytů výstrahy v zařízení a doporučené akce k vyřešení výstrahy. Pokud se jedná o hardwarovou výstrahu, identifikuje také hardwarovou součást.

![Příklad výstrahy hardwaru](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Podrobnosti výstrahy můžete zkopírovat do textového souboru, pokud potřebujete odeslat informace na podporu společnosti Microsoft. Poté, co jste postupovali podle doporučení a vyřešili stav výstrahy v místním prostředí, měli byste vymazat výstrahu ze zařízení tak, že vyberete výstrahu v okně **Výstrahy** a klepnete na tlačítko **Vymazat**. Chcete-li vymazat více výstrah, vyberte jednotlivé výstrahy, klikněte na libovolný sloupec kromě sloupce **Výstraha** a poté, co jste vybrali všechna upozornění, která mají být vymazána, klepněte na tlačítko **Vymazat.** Všimněte si, že některé výstrahy jsou automaticky vymazány při vyřešení problému nebo při aktualizaci výstrahy novými informacemi.

Po klepnutí na tlačítko **Vymazat**budete mít možnost poskytnout komentáře k upozornění a krokům, které jste provedli k vyřešení problému. Některé události budou vymazány systémem, pokud je spuštěna jiná událost s novými informacemi. V takovém případě se zobrazí následující zpráva.

![Vymazat výstražnou zprávu](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Řazení a kontrola výstrah

Může být efektivnější spouštět sestavy s výstrahami, abyste je mohli zkontrolovat a vymazat ve skupinách. Kromě toho okno **Výstrahy** můžete zobrazit až 250 výstrah. Pokud jste tento počet výstrah překročili, ne všechny výstrahy se zobrazí ve výchozím zobrazení. Můžete kombinovat následující pole a přizpůsobit tak, které výstrahy se zobrazí:

* **Stav** – Můžete zobrazit **aktivní** nebo **vymazáná** upozornění. Aktivní výstrahy jsou stále spuštěny ve vašem systému, zatímco vymazány výstrahy byly buď ručně vymazány správcem nebo programově vymazány, protože systém aktualizoval stav výstrahy novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všech úrovní závažnosti (kritické, upozornění, informace) nebo pouze určité závažnosti, například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí ze služby nebo jednoho nebo všech zařízení.
* **Časový rozsah** – Zadáním dat **od** a **do** a časových razítek se můžete podívat na výstrahy během časového období, které vás zajímá.

![Seznam upozornění](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Stručný přehled výstrah

V následujících tabulkách jsou uvedeny některé výstrahy Microsoft Azure StorSimple, se kterými se můžete setkat, a další informace a doporučení, pokud jsou k dispozici. Výstrahy zařízení StorSimple spadají do jedné z následujících kategorií:

* [Výstrahy připojení ke cloudu](#cloud-connectivity-alerts)
* [Výstrahy clusteru](#cluster-alerts)
* [Výstrahy zotavení po havárii](#disaster-recovery-alerts)
* [Hardwarová upozornění](#hardware-alerts)
* [Upozornění na selhání úlohy](#job-failure-alerts)
* [Místně připnutá upozornění na svazek](#locally-pinned-volume-alerts)
* [Síťové výstrahy](#networking-alerts)
* [Upozornění na výkon](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)
* [Výstrahy balíčků podpory](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení ke cloudu

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Připojení k <*názvu pověření cloudu*> nelze navázat. |Nelze se připojit k účtu úložiště. |Vypadá to, že by mohlo dojít k problému s připojením k vašemu zařízení. Chcete-li `Test-HcsmConnection` identifikovat a opravit problém, spusťte rutinu z rozhraní Windows PowerShell pro systém StorSimple v zařízení. Pokud jsou nastavení správná, problém může být s pověřeními účtu úložiště, pro který byla vyvolána výstraha. V takovém případě `Test-HcsStorageAccountCredential` pomocí rutiny určete, zda existují problémy, které můžete vyřešit.<ul><li>Zkontrolujte nastavení sítě.</li><li>Zkontrolujte přihlašovací údaje účtu úložiště.</li></ul> |
| Za posledních <*číslo*> minut jsme z vašeho zařízení neobdrželi prezenční signál. |Nelze se připojit k zařízení. |Vypadá to, že je problém s připojením s vaším zařízením. Pomocí `Test-HcsmConnection` rutiny z rozhraní Windows PowerShell pro službu StorSimple v zařízení můžete identifikovat a opravit problém nebo se obraťte na správce sítě. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple chování při selhání připojení ke cloudu

Co se stane, když se připojení ke cloudu nezdaří pro moje zařízení StorSimple spuštěné v produkčním prostředí?

Pokud připojení ke cloudu selže na vašem produkčním zařízení StorSimple, pak v závislosti na stavu vašeho zařízení může dojít následující:

* **Pro místní data v zařízení**: Po určitou dobu nedojde k žádnému narušení a čtení bude nadále obsluhováno. Však jako počet nevyřízených vstupně-up zvyšuje a překračuje limit, čtení může začít selhat.

    V závislosti na množství dat v zařízení bude zápisy také nadále dochází v prvních několika hodinách po přerušení připojení ke cloudu. Zápisy se pak zpomalí a nakonec začnou selhat, pokud je připojení ke cloudu přerušeno na několik hodin. (V zařízení je dočasné úložiště pro data, která mají být posunuta do cloudu. Tato oblast je vyprázdněna při odeslání dat. Pokud připojení selže, data v této oblasti úložiště nebudou posunuta do cloudu a vi se nezdaří.)
* **Pro data v cloudu**: Pro většinu chyb připojení cloudu je vrácena chyba. Po obnovení připojení jsou vifikovány vi bez nutnosti převést svazek do režimu online. Ve výjimečných případech může být vyžadován zásah uživatele k vrácení svazku online z portálu Azure.
* **Pro probíhající snímky cloudu**: Operace se několikrát zopakována během 4-5 hodin a pokud připojení není obnoveno, snímky cloudu se nezdaří.

### <a name="cluster-alerts"></a>Výstrahy clusteru

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Zařízení se nezdařilo <*> názvu zařízení.* |Zařízení je v režimu údržby. |Zařízení selhalo z důvodu vstupu nebo ukončení režimu údržby. To je normální a není nutná žádná akce. Po potvrzování této výstrahy ji vymažte na stránce výstrah. |
| Zařízení se nezdařilo <*> názvu zařízení.* |Firmware nebo software zařízení byl právě aktualizován. |Došlo k převzetí služeb při selhání clusteru z důvodu aktualizace. To je normální a není nutná žádná akce. Po potvrzování této výstrahy ji vymažte na stránce výstrah. |
| Zařízení se nezdařilo <*> názvu zařízení.* |Ovladač byl vypnut nebo restartován. |Převzetí služby při selhání zařízení, protože aktivní řadič byl vypnut nebo restartován správcem. Není nutná žádná akce. Po potvrzování této výstrahy ji vymažte na stránce výstrah. |
| Zařízení se nezdařilo <*> názvu zařízení.* |Plánované převzetí služeb při selhání. |Ověřte, zda se jednalo o plánované převzetí služeb při selhání. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah. |
| Zařízení se nezdařilo <*> názvu zařízení.* |Neplánované převzetí služeb při selhání. |StorSimple je vytvořen tak, aby se automaticky zotavil z neplánovaných převzetí služeb při selhání. Pokud se zobrazí velký počet těchto výstrah, obraťte se na podporu společnosti Microsoft. |
| Zařízení se nezdařilo <*> názvu zařízení.* |Jiná/neznámá příčina. |Pokud se zobrazí velký počet těchto výstrah, obraťte se na podporu společnosti Microsoft. Po vyřešení problému vymažte tuto výstrahu na stránce výstrah. |
| Služba kritického zařízení hlásí stav jako neúspěšný. |Selhání služby Datapath. |Požádejte o pomoc podporu společnosti Microsoft. |
| Virtuální IP adresa pro síťové rozhraní <*DATA #*> stavu hlásí jako neúspěšné. |Jiná/neznámá příčina. |Někdy dočasné podmínky mohou způsobit tyto výstrahy. Pokud se jedná o tento případ, bude tato výstraha po určité době automaticky vymazána. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |
| Virtuální IP adresa pro síťové rozhraní <*DATA #*> stavu hlásí jako neúspěšné. |Název rozhraní: <*DATA* `<IP address>` #> IP adresu nelze přepnout do režimu online, protože v síti byla zjištěna duplicitní adresa IP. |Ujistěte se, že duplicitní adresa IP je odebrána ze sítě, nebo překonfigurujte rozhraní s jinou adresou IP. |

### <a name="disaster-recovery-alerts"></a>Výstrahy zotavení po havárii

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Operace obnovení nemohly obnovit všechna nastavení této služby. Konfigurační data zařízení jsou pro některá zařízení v nekonzistentním stavu. |Nekonzistence dat zjištěná po zotavení po havárii. |Šifrovaná data ve službě nejsou synchronizována s daty v zařízení. Autorizujte *>>> zařízení* <zařízení ze Správce zařízení StorSimple ke spuštění procesu synchronizace. Pomocí rozhraní Windows PowerShell pro StorSimple spusťte `Restore-HcsmEncryptedServiceData` na zařízení <název *zařízení*> rutina, poskytuje staré heslo jako vstup do této rutiny obnovit profil zabezpečení. Potom spusťte rutinu `Invoke-HcsmServiceDataEncryptionKeyChange` a aktualizujte šifrovací klíč dat služby. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah. |

### <a name="hardware-alerts"></a>Hardwarová upozornění

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| *ID <součásti hardwarové součásti*> hlásí stav *stavu* <stavu>. | |Někdy dočasné podmínky mohou způsobit tyto výstrahy. Pokud ano, bude tato výstraha po určité době automaticky vymazána. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |
| Pasivní regulátor nefunguje správně. |Pasivní (sekundární) řadič nefunguje. |Vaše zařízení je funkční, ale jeden z vašich ovladačů nefunguje správně. Zkuste restartovat tento ovladač. Pokud problém není vyřešen, obraťte se na podporu společnosti Microsoft. |

### <a name="job-failure-alerts"></a>Upozornění na selhání úlohy

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Zálohování *ID skupiny zdrojového svazku* <> nezdařilo. |Úloha zálohování se nezdařila. |Problémy s připojením mohou bránit úspěšnému dokončení operace zálohování. Pokud nejsou žádné problémy s připojením, pravděpodobně jste dosáhli maximálního počtu záloh. Odstraňte všechny zálohy, které již nejsou potřeba, a opakujte operaci. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah. |
| Klon <ování *ID zdrojových prvků*> k <*sériových čísel cílového svazku*> se nezdařilo. |Klonování se nezdařilo. |Aktualizujte seznam záloh a ověřte, zda je záloha stále platná. Pokud je záloha platná, je možné, že problémy s připojením ke cloudu brání úspěšnému dokončení operace klonování. Pokud nejsou žádné problémy s připojením, pravděpodobně jste dosáhli limitu úložiště. Odstraňte všechny zálohy, které již nejsou potřeba, a opakujte operaci. Poté, co jste provedli příslušnou akci k vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrah. |
| Obnovení *ID <zdrojového záložního prvku*> nezdařilo. |Úloha obnovení se nezdařila. |Aktualizujte seznam záloh a ověřte, zda je záloha stále platná. Pokud je záloha platná, je možné, že problémy s připojením ke cloudu brání úspěšnému dokončení operace obnovení. Pokud nejsou žádné problémy s připojením, pravděpodobně jste dosáhli limitu úložiště. Odstraňte všechny zálohy, které již nejsou potřeba, a opakujte operaci. Poté, co jste provedli příslušnou akci k vyřešení problému, zrušte zaškrtnutí této výstrahy na stránce výstrah. |

### <a name="locally-pinned-volume-alerts"></a>Místně připnutá upozornění na svazek

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Vytvoření názvu místního svazku <*svazek*> se nezdařilo. |Úloha vytvoření svazku se nezdařila. <*Chybová zpráva odpovídající chybové chybě>.* |Problémy s připojením mohou bránit úspěšnému dokončení operace vytváření prostoru. Místně vázaných svazků jsou hustě zřízené a proces vytváření prostoru zahrnuje rozlití vrstvené svazky do cloudu. Pokud nejsou žádné problémy s připojením, je možné, že jste vyčerpali místní místo v zařízení. Zjistěte, zda v zařízení existuje místo před opakováním této operace. |
| Rozšíření místního svazku <*název svazku*> se nezdařilo. |Úloha změny svazku se nezdařila z důvodu chybové zprávy <*odpovídající neúspěšnému kódu chyby*>. |Problémy s připojením mohou bránit úspěšnému dokončení operace rozšíření svazku. Místně vázaných svazků jsou hustě zřízené a proces rozšíření existujícího prostoru zahrnuje rozlití vrstvené svazky do cloudu. Pokud nejsou žádné problémy s připojením, je možné, že jste vyčerpali místní místo v zařízení. Zjistěte, zda v zařízení existuje místo před opakováním této operace. |
| Převod názvu svazku <*svazku*> se nezdařil. |Úloha převodu svazku pro převod typu svazku z místně připnutého na vrstvené se nezdařila. |Převod svazku z typu místně vázaný na vrstvené nelze dokončit. Ujistěte se, že neexistují žádné problémy s připojením, které by bránily úspěšnému dokončení operace. Problémy s připojením naleznete v [tématu Poradce při potížích s rutinou Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní místně vázaný svazek byl nyní označen jako vrstvený svazek, protože některá data z místně vázaný svazek se rozlildo u cloudu během převodu. Výsledný vrstvený svazek stále zabírá místní místo v zařízení, které nelze uvolnit pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, vymažte výstrahu a převeďte tento svazek zpět na původní místně vázaný typ svazku, abyste zajistili, že všechna data budou znovu k dispozici místně. |
| Převod názvu svazku <*svazku*> se nezdařil. |Úloha převodu svazku pro převod typu svazku z vrstveného na místně připnutý se nezdařila. |Převod svazku z typu vrstvené na místně připnutý nelze dokončit. Ujistěte se, že neexistují žádné problémy s připojením, které by bránily úspěšnému dokončení operace. Problémy s připojením naleznete v [tématu Poradce při potížích s rutinou Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní vrstvený svazek, který je nyní označen jako místně vázaný svazek jako součást procesu převodu, má i nadále data v cloudu, zatímco hustě zřízené místo v zařízení pro tento svazek již nelze uvolnit pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, vymažte výstrahu a převeďte tento svazek zpět na původní typ vrstveného svazku, abyste zajistili, že místní prostor silně zřízený v zařízení může být znovu vyzvednut. |
| Blížící se spotřeba místního místa pro místní snímky <*název skupiny svazků*> |Místní snímky pro zásady zálohování může brzy nedostatek místa a zneplatněny, aby se zabránilo selhání zápisu hostitele. |Časté místní snímky spolu s vysokou změnou dat ve svazcích přidružených k této skupině zásad zálohování způsobují rychlé využití místního místa v zařízení. Odstraňte všechny místní snímky, které již nejsou potřeba. Aktualizujte také místní snímky plány pro tuto zásadu zálohování pořizovat méně časté místní snímky a ujistěte se, že snímky cloudu jsou pořizovat pravidelně. Pokud tyto akce nejsou prováděny, místní prostor pro tyto snímky může být brzy vyčerpány a systém automaticky odstraní, aby bylo zajištěno, že hostitelské zápisy budou i nadále úspěšně zpracovány. |
| Místní snímky pro *název skupiny* <svazek> byly zrušeny. |Místní snímky pro <*název skupiny svazků*> byly zrušeny a poté odstraněny, protože byly vyšší než místní místo v zařízení. |Chcete-li zajistit, aby se to v budoucnu neopakovalo, zkontrolujte místní plány snímků pro tuto zásadu zálohování a odstraňte všechny místní snímky, které již nejsou potřeba. Časté místní snímky spolu s vysokou změnou dat ve svazcích přidružených k této skupině zásad zálohování může způsobit rychlé využití místního místa v zařízení. |
| Obnovení *ID <zdrojového záložního prvku*> nezdařilo. |Úloha obnovení se nezdařila. |Pokud jste v této zásadě zálohování připnuli místně nebo kombinaci místně vázaných a vrstvených svazků, aktualizujte seznam záloh a ověřte, zda je záloha stále platná. Pokud je záloha platná, je možné, že problémy s připojením ke cloudu brání úspěšnému dokončení operace obnovení. Místně vázaných svazků, které byly obnoveny jako součást této skupiny snímek nemají všechna jejich data stažena do zařízení a pokud máte kombinaci vrstvené a místně vázaných svazků v této skupině snímků, nebudou synchronizovány mezi sebou. Chcete-li úspěšně dokončit operaci obnovení, převeďte svazky v této skupině do režimu offline na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny změny dat svazku, které byly provedeny během procesu obnovení budou ztraceny. |

### <a name="networking-alerts"></a>Síťové výstrahy

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Nelze spustit služby StorSimple. |Chyba datové cesty |Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |
| Byla zjištěna duplicitní adresa IP pro data 0. | |Systém zjistil konflikt pro IP adresu '10.0.0.1'. Síťový prostředek Data0 na * \<zařízení zařízení1>* je offline. Ujistěte se, že tuto adresu IP nepoužívá žádná jiná entita v této síti. Chcete-li vyřešit problémy se sítí, přejděte na [článek Poradce při potížích s rutinou Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Obraťte se na správce sítě, který vám pomůže tento problém vyřešit. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |
| Adresa IPv4 (nebo IPv6) pro data0 je offline. | |Síťový prostředek Data0 s IP adresou 10.0.0.1. a délka předpony "22" na * \<zařízení1>* je offline. Ujistěte se, že porty přepínače, ke kterým je toto rozhraní připojeno, jsou funkční. Chcete-li vyřešit problémy se sítí, přejděte na [článek Poradce při potížích s rutinou Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nelze se připojit ke službě ověřování. |Chyba datové cesty |Adresy URL, které se používají k ověření není k zastižení. Ujistěte se, že pravidla brány firewall obsahují vzory adres URL určené pro zařízení StorSimple. Další informace o vzorcích adres URL na\/webu Azure Portal najdete na https: /aka.ms/ss-8000-network-reqs. Pokud používáte Azure Government Cloud, přejděte\/na vzory adres URL v https: /aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Upozornění na výkon

| Text výstrahy | Událost | Více informací / doporučená opatření | |
|:--- |:--- |:--- | --- |
| Zatížení zařízení překročilo *> <prahovou hodnotu.* |Pomalejší než očekávané doby odezvy. |Vaše zařízení hlásí využití pod velkým vstupním a výstupním zatížením. To by mohlo způsobit, že vaše zařízení nebude fungovat tak, jak by mělo. Zkontrolujte úlohy, které jste připojili k zařízení, a zjistěte, zda existují nějaké, které by mohly být přesunuty do jiného zařízení nebo které již nejsou nutné.|
| Nelze spustit služby StorSimple. |Chyba datové cesty |Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Byla zahájena relace podpory společnosti Microsoft. |Relace podpory přístupná třetím i třetím stranám. |Potvrďte, že tento přístup je autorizován. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah. |
| Heslo pro> *<prvku* vyprší za <*dobu*>. |Blíží se vypršení platnosti hesla. |Před vypršením platnosti změňte heslo. |
| Pro> *ID prvku* <chybí informace o konfiguraci zabezpečení. | |Svazky přidružené k tomuto kontejneru svazků nelze použít k replikaci konfigurace StorSimple. Chcete-li zajistit, aby byla data bezpečně uložena, doporučujeme odstranit kontejner svazku a všechny svazky přidružené ke kontejneru svazků. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah. |
| <*pokusy* o přihlášení> čísel se nezdařily pro> *ID prvku* <. |Několik neúspěšných pokusů o přihlášení. |Vaše zařízení může být napadeno nebo se oprávněný uživatel pokouší připojit k nesprávnému heslu.<ul><li>Obraťte se na oprávněné uživatele a ověřte, zda tyto pokusy pocházejí z legitimního zdroje. Pokud se nadále zobrazí velký počet neúspěšných pokusů o přihlášení, zvažte zakázání vzdálené správy a kontaktování správce sítě. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah.</li><li>Zkontrolujte, zda jsou instance Správce snímků nakonfigurovány se správným heslem. Po přijetí příslušné akce vymažte tuto výstrahu na stránce výstrah.</li></ul>Další informace naleznete v části [Změna hesla zařízení, jehož platnost vypršela.](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password) |
| Při změně šifrovacího klíče dat služby došlo k jedné nebo více chybám. | |Při změně šifrovacího klíče dat služby došlo k chybám. Po vyřešení chybových stavů spusťte rutinu `Invoke-HcsmServiceDataEncryptionKeyChange` z rozhraní Windows PowerShell pro StorSimple v zařízení a aktualizujte službu. Pokud tento problém přetrvává, obraťte se na podporu společnosti Microsoft. Po vyřešení problému vymažte tuto výstrahu na stránce výstrah. |

### <a name="support-package-alerts"></a>Výstrahy balíčků podpory

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Vytvoření balíčku podpory se nezdařilo. |StorSimple nemohl vygenerovat balíček. |Opakujte tuto operaci. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. Po vyřešení problému vymažte tuto výstrahu na stránce výstrah. |

## <a name="next-steps"></a>Další kroky

Další informace o [chybách StorSimple a řešení potíží s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md).

