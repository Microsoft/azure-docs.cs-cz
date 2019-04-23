---
title: Umožňuje zobrazit a spravovat výstrahy pro zařízení StorSimple řady 8000 | Dokumentace Microsoftu
description: Popisuje podmínky upozornění StorSimple a závažnost, jak konfigurovat výstražná oznámení a jak pomocí služby Správce zařízení StorSimple ke správě výstrah.
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
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: c3be0cdf2ef33c26dfa9d177e9b34f808b1b862a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320334"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Pomocí služby Správce zařízení StorSimple můžete zobrazit a spravovat výstrahy StorSimple

## <a name="overview"></a>Přehled

**Výstrahy** okno ve službě Správce zařízení StorSimple poskytuje způsob, jak ke kontrole a zrušte výstrahy související s zařízení StorSimple na základě v reálném čase. Z tohoto okna můžete centrálně monitorovat stav problémy zařízení StorSimple a celkového řešení Microsoft Azure StorSimple.

Tento kurz popisuje běžné podmínky upozornění úrovně závažnosti výstrah a jak nakonfigurovat oznámení výstrah. Kromě toho zahrnuje upozornění Stručná referenční příručka tabulek, které vám umožní rychle najít konkrétní výstrahu a reagují odpovídajícím způsobem.

![Stránky s upozorněními](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Běžné podmínky upozornění

Zařízení StorSimple generuje výstrahy v reakci na různé podmínky. Následují nejčastější typy podmínky upozornění:

* **Problémy s hardwarem** – tyto výstrahy je informovat o stavu hardwaru. Umožňují vědět, pokud jsou potřeba upgrady firmwaru, pokud je síťové rozhraní má problémy nebo pokud dojde k nějakému problému s jednou z vašich datových jednotek.
* **Problémy s připojením** – tyto výstrahy zobrazují tehdy, když se potíže při přenášení dat. Problémy s komunikací může dojít při přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku možností připojení mezi zařízeními a služby Správce zařízení StorSimple. Problémy s komunikací jsou některé z těch nejtěžších opravit, protože tolik bodů selhání. Vždy nejdříve je nutné ověřit, že před pokračováním pokročilejší řešení potíží jsou k dispozici připojení k síti a přístup k Internetu. Pomoc při řešení potíží, přejděte na [řešení potíží pomocí rutiny Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny, když váš systém nepracuje optimálně, například pokud je v případě velkého zatížení.

Kromě toho se může zobrazit upozornění týkající se zabezpečení, aktualizace nebo selhání úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrah

Výstrahy mají různé úrovně závažnosti, v závislosti na dopadu, který bude mít výstrah situace a není nutné pro reakci na upozornění. Existují tyto úrovně závažnosti:

* **Kritické** – Tato výstraha se v reakci na určitou podmínku, která ovlivňuje úspěšné výkon vašeho systému. Akce je potřeba zajistit, aby StorSimple služba není přerušena.
* **Upozornění** – tento stav může být důležité, pokud nebyl vyřešen. By měl prozkoumat situace a provádět žádnou akci potřeba vymazat problém.
* **Informace o** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správa systému.

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete zvolit, jestli chcete dostat e-mailové výstrahy podmínky pro každé z vašich zařízení StorSimple. Kromě toho můžete identifikovat dalších příjemců oznámení výstrah tak, že zadáte své e-mailové adresy **další příjemci e-mailu** pole, oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailových adres na jedno zařízení.

Po povolení e-mailové oznámení pro zařízení se v seznamu oznámení obdrží e-mailovou zprávu, vyvolá se pokaždé, když kritickou výstrahu. Pošle zprávy z *storsimple oznámení noreply\@mail.windowsazure.com* a popíše se vyskytl výstražný stav. Můžete kliknout na příjemce **Unsubscribe** sami odebrat ze seznamu e-mailové oznámení.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Chcete-li povolit e-mailové oznámení o výstrahách pro zařízení
1. Přejděte do služby Správce zařízení StorSimple. Ze seznamu zařízení vyberte a klikněte na zařízení, které chcete nakonfigurovat.
2. Přejděte na **nastavení** > **Obecné** pro zařízení.

   ![Okno výstrahy](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. V **obecné nastavení** okno, přejděte na **nastavení výstrah** a nastavte následující:
   
   1. V **odeslat e-mailové oznámení** pole, vyberte **Ano**.
   2. V **e-mailem správci služeb** pole, vyberte **Ano** chcete mít správce služeb a všichni spolusprávci dostávali oznámení výstrah.
   3. V **další příjemci e-mailu** zadejte e-mailové adresy všech příjemců, kteří mají dostávat oznámení výstrah. Zadejte názvy ve formátu *někdo\@somewhere.com*. K oddělení e-mailových adres použijte středníky. Nakonfigurovat můžete nanejvýš 20 e-mailových adres na jedno zařízení. 
      
3. Pokud chcete poslat testovací e-mailové oznámení, klikněte na tlačítko **odeslat zkušební email**. Služba Správce zařízení StorSimple se zobrazí stavové zprávy, jak předává testovací oznámení.

    ![Nastavení výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Při odeslání testovacího e-mailu se zobrazí oznámení. 
   
    ![Upozornění testovací odeslání e-mailové oznámení](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Pokud nelze odeslat testovací oznámení, se zobrazí ve službě Správce zařízení StorSimple příslušnou chybovou zprávu. Počkejte pár minut a pak zkuste znovu odeslat zprávu oznámení vašeho testu. 

5. Jakmile dokončíte konfiguraci, klikněte na tlačítko **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![Upozornění testovací odeslání e-mailové oznámení](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okně s přehledem služby Správce zařízení StorSimple poskytuje rychlý přehled na počet výstrah v zařízeních, uspořádané podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknutím na úroveň závažnosti se otevře **výstrahy** okno. Budou výsledky obsahovat pouze výstrahy, které odpovídají této úrovni závažnosti.

Kliknutím na výstrahu v seznamu vám poskytuje další podrobnosti k výstraze, včetně posledního upozornění byla nahlášena, počet výskytů prvku oznámení na zařízení a doporučenou akci pro tuto výstrahu vyřešíte tak. Pokud je výstraha hardwaru, budou rovněž určit hardwarová komponenta.

![Příklad výstrahy hardwaru](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Do textového souboru můžete zkopírovat podrobnosti výstrahy, pokud je potřeba poslat informace o Microsoft Support. Až budete mít postupovali podle doporučení a vyskytl výstražný stav v místním, byste měli Vymazat výstrahu v zařízení tak, že vyberete výstrahu v **výstrahy** okno a kliknutím na **vymazat**. Pokud chcete vymazat více výstrah, vyberte jednotlivé výstrahy, klikněte na všechny sloupce s výjimkou **výstraha** sloupec a pak klikněte na tlačítko **vymazat** po výběru všech výstrah vymazání. Všimněte si, že jsou některé výstrahy automaticky vymazány, když dojde k vyřešení problému nebo když systém aktualizuje novými informacemi výstrahu.

Po kliknutí na **vymazat**, budete mít příležitost k poskytování poznámky o upozornění a kroky, které jste provedli v zájmu vyřešení problému. Některé události bude vymazáno systém, pokud jiné událost se aktivuje pomocí nové informace. V takovém případě se zobrazí následující zpráva.

![Vymazat výstrahu](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Řazení a kontrola upozornění

Může být pro vás mnohem efektivnější ke spouštění sestav o výstrahách, takže můžete zkontrolovat a poté je smažte ve skupinách. Kromě toho **výstrahy** okně můžete zobrazit až 250 výstrahy. Pokud byl překročen počet výstrah, ne všechny výstrahy, zobrazí se ve výchozím zobrazení. Můžete kombinovat přizpůsobení, které výstrahy se zobrazují následující pole:

* **Stav** – můžete zobrazit buď **aktivní** nebo **nezaškrtnuto** výstrahy. Aktivní výstrahy se stále aktivují ve vašem systému, zatímco nezaškrtnuté výstrahy byla buď ručně zrušena správcem nebo programově vymazat, protože systém se vyskytl výstražný stav aktualizuje novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všechny úrovně závažnosti (kritická, upozornění, informace o), nebo jenom určité závažnosti, jako je například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit oznámení na ty, které pocházejí ze služby nebo jeden nebo všechna zařízení.
* **Časový rozsah** – zadáním **z** a **k** data a časová razítka, můžete se podívat na výstrahy během časového období, které vás zajímají.

![Seznam výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Stručná referenční příručka výstrahy

V následujících tabulkách jsou uvedeny některé výstrahy Microsoft Azure StorSimple, které může dojít, a také další informace a doporučení tam, kde je k dispozici. Upozornění zařízení StorSimple spadají do jedné z následujících kategorií:

* [Výstrahy připojení cloudu](#cloud-connectivity-alerts)
* [Upozornění clusteru](#cluster-alerts)
* [Výstrahy pro zotavení po havárii](#disaster-recovery-alerts)
* [Hardwarové výstrahy](#hardware-alerts)
* [Výstrahy na selhání úloh](#job-failure-alerts)
* [Místně vázaný svazek výstrahy](#locally-pinned-volume-alerts)
* [Výstrahy sítě](#networking-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)
* [Podpora balíčku upozornění](#support-package-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení cloudu

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Připojení k <*název přihlašovacího údaje cloudu*> nelze navázat. |Nelze se připojit k účtu úložiště. |Zdá se, může být problém s připojením s vaším zařízením. Spusťte prosím `Test-HcsmConnection` rutiny z rozhraní Windows PowerShell pro StorSimple na vašem zařízení a identifikovat a opravit tento problém. Pokud je nastavení správné, může být problém s přihlašovacími údaji účtu úložiště, pro který byla výstraha vyvolána. V takovém případě použijte `Test-HcsStorageAccountCredential` rutiny k určení, jestli jsou nějaké problémy, které lze vyřešit.<ul><li>Zkontrolujte nastavení sítě.</li><li>Zkontrolujte přihlašovací údaje účtu úložiště.</li></ul> |
| Jsme nedostali prezenční signál z vašeho zařízení za posledních <*číslo*> minut. |Nelze se připojit k zařízení. |Zdá se, nastane problém s připojením s vaším zařízením. Použijte prosím `Test-HcsmConnection` rutiny z rozhraní Windows PowerShell pro StorSimple na svém zařízení identifikovat a tento problém vyřešit nebo se obraťte na správce sítě. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple chování při selhání připojení ke cloudu

Co se stane, když selže připojení ke cloudu pro zařízení StorSimple běžících v produkčním prostředí?

Pokud připojení ke cloudu na zařízení StorSimple produkční selže, pak v závislosti na stavu zařízení, následující může dojít:

* **Pro místní data na vašem zařízení**: Nějakou dobu bude bez přerušení a čtení bude pokračovat ke zpracování. Ale počet nezpracovaných vstupně zvyšuje a překročí limit, může spustit čtení selhání.

    V závislosti na objemu dat na vašem zařízení také nadále zápisů dojde k první několik hodin po narušení připojení cloudu. Zápisy se pak zpomalovat a nakonec začnou mít problémy, pokud naruší se připojení ke cloudu pro několik hodin. (Není dočasného úložiště na zařízení pro data, které mají být vloženy do cloudu. Tato oblast vyprázdní data při odeslání. Pokud připojení selže, neodešle se data v této oblasti úložiště do cloudu, a vstupně-výstupní operace se nezdaří.)
* **Pro data v cloudu**: Většina chyb připojení cloudu je vrácena chyba. Po obnovení připojení jsou obnovit IOs, aniž by uživatel musel svazku online. Ve výjimečných případech může být nutný zásah uživatele požadované vrací do stavu online hlasitosti na webu Azure Portal.
* **Pro cloudové snímky v průběhu**: Operace je opakovat několikrát v rámci 4 až 5 hodin a pokud připojení se obnoví, cloudové snímky se nezdaří.

### <a name="cluster-alerts"></a>Upozornění clusteru

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Zařízení se pokusilo převzít <*název zařízení*>. |Zařízení je v režimu údržby. |Zařízení se nezdařila. důvod spustil nebo ukončil režim údržby. To je běžné a není potřeba žádná akce. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Zařízení se pokusilo převzít <*název zařízení*>. |Firmwaru zařízení nebo software byl právě aktualizován. |Došlo k selhání clusteru z důvodu aktualizace. To je běžné a není potřeba žádná akce. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Zařízení se pokusilo převzít <*název zařízení*>. |Kontroler se vypnul nebo restartoval. |Zařízení se nepodařilo přes, protože byla aktivní kontroler vypnul nebo restartoval microsoftem nebo správcem. Není nutné provádět žádnou akci. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Zařízení se pokusilo převzít <*název zařízení*>. |Plánované převzetí služeb při selhání. |Ověřte, že bylo plánované převzetí služeb při selhání. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními. |
| Zařízení se pokusilo převzít <*název zařízení*>. |Neplánované převzetí služeb při selhání. |StorSimple je navržená k automaticky zotavit po neplánovaném převzetí služeb při selhání. Pokud zjistíte velký počet těchto výstrahách, obraťte se na Microsoft Support. |
| Zařízení se pokusilo převzít <*název zařízení*>. |Ostatní/Neznámý důvod. |Pokud zjistíte velký počet těchto výstrahách, obraťte se na Microsoft Support. Jakmile je problém vyřešen, vymažte toto upozornění ze stránky s upozorněními. |
| Služba kritické zařízení hlásí stav jako neúspěšný. |DataPath selhání služby. |Požádejte o pomoc Microsoft Support. |
| Virtuální IP adresu pro síťové rozhraní <*dat #*> hlásí stav jako neúspěšný. |Ostatní/Neznámý důvod. |Někdy dočasné situace může způsobit, že tyto výstrahy. Pokud je to tento případ, pak tato výstraha bude automaticky vymazán po nějaké době. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. |
| Virtuální IP adresu pro síťové rozhraní <*dat #*> hlásí stav jako neúspěšný. |Název rozhraní: <*dat #*> IP adresa `<IP address>` nelze do online režimu, protože byla zjištěna duplicitní IP adresa v síti. |Ujistěte se, že duplicitní IP adresa se odebere ze sítě nebo změnit konfiguraci rozhraní s jinou IP adresu. |

### <a name="disaster-recovery-alerts"></a>Výstrahy pro zotavení po havárii

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Operace obnovení nemohly obnovit všechna nastavení pro tuto službu. Data konfigurace zařízení je v nekonzistentním stavu pro některá zařízení. |Data byla zjištěna nekonzistence po zotavení po havárii. |Šifrovaná data ve službě není synchronizován s, která na zařízení. Povolit zařízení <*název zařízení*> ze Správce StorSimple zařízení k zahájení procesu synchronizace. Použijte rozhraní Windows PowerShell pro StorSimple ke spuštění `Restore-HcsmEncryptedServiceData` na zařízení <*název zařízení*> rutina zadání starého hesla jako vstup do této rutiny obnovit profil zabezpečení. Spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutina pro aktualizaci šifrovací klíč dat služby. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními. |

### <a name="hardware-alerts"></a>Hardwarové výstrahy

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Hardwarová komponenta <*ID komponenty*> hlásí stav <*stav*>. | |Někdy dočasné situace může způsobit, že tyto výstrahy. Pokud ano, tato výstraha bude automaticky vymazán po nějaké době. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. |
| Pasivní kontroler nefunguje správně. |Pasivní kontroler (sekundární) nebude fungovat správně. |Vaše zařízení je funkční, ale jeden z řadičů nefunguje správně. Zkuste restartovat tento kontroler. Pokud se problém nevyřeší, obraťte se na Microsoft Support. |

### <a name="job-failure-alerts"></a>Výstrahy na selhání úloh

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Zálohování <*ID skupiny svazku zdrojového*> se nezdařilo. |Úloha zálohování se nezdařila. |Problémy s připojením může bránit operaci zálohování nejde úspěšně dokončit. Pokud nejsou žádné problémy s připojením, může dosáhli jste maximálního počtu záloh. Odstraňte všechny zálohy, které jsou už je nepotřebujete a zkuste operaci zopakovat. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními. |
| Klon <*zdroje ID elementu zálohování*> do <*cílový svazek sériová čísla*> se nezdařilo. |Úloha klonování se nezdařilo. |Aktualizace seznamu zálohování a ověřte, že je záloha stále platná. Pokud je záloha platná, je možné, že potíže s připojením cloudu brání operaci klonování úspěšně dokončit. Pokud nejsou žádné problémy s připojením, pravděpodobně bylo dosaženo limitu úložiště. Odstraňte všechny zálohy, které jsou už je nepotřebujete a zkuste operaci zopakovat. Poté, co jste udělali vhodná opatření, aby tento problém vyřešit, vymažte toto upozornění ze stránky s upozorněními. |
| Obnovení <*zdroje ID elementu zálohování*> se nezdařilo. |Obnovte zpracování úlohy se nezdařilo. |Aktualizace seznamu zálohování a ověřte, že je záloha stále platná. Pokud je záloha platná, je možné, že brání problémy s připojením cloud operace obnovení úspěšně dokončit. Pokud nejsou žádné problémy s připojením, pravděpodobně bylo dosaženo limitu úložiště. Odstraňte všechny zálohy, které jsou už je nepotřebujete a zkuste operaci zopakovat. Poté, co jste udělali vhodná opatření, aby tento problém vyřešit, vymažte toto upozornění ze stránky s upozorněními. |

### <a name="locally-pinned-volume-alerts"></a>Místně vázaný svazek výstrahy

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Vytvoření místní svazek <*název svazku*> se nezdařilo. |Úloha vytvoření svazku se nezdařilo. <*Odpovídající kód se chybová zpráva*>. |Problémy s připojením může bránit operaci vytvoření prostoru nejde úspěšně dokončit. Místně připojené svazky se zřídilo hodně a proces vytvoření prostoru je přesunutí vrstvených svazků do cloudu. Pokud nejsou žádné problémy s připojením, může mít vyčerpali místní prostor na zařízení. Zjistěte, zda prostor existuje na zařízení, než to zkusíte znovu. |
| Rozšíření místní svazek <*název svazku*> se nezdařilo. |Úloha změnu svazku se nezdařila z důvodu <*odpovídající kód se chybová zpráva*>. |Problémy s připojením může bránit operaci rozšíření svazku nejde úspěšně dokončit. Místně připnuté se zřídilo hodně svazků a součástí procesu rozšíření existujícího prostoru je přesunutí vrstvených svazků do cloudu. Pokud nejsou žádné problémy s připojením, může mít vyčerpali místní prostor na zařízení. Zjistěte, zda prostor existuje na zařízení, než to zkusíte znovu. |
| Převod svazku <*název svazku*> se nezdařilo. |Úloha převodu svazků pro převod typu svazku z místně připnuté na vrstvené se nezdařilo. |Převod typu svazku z typu místně připnuté na vrstvené nebylo možné dokončit. Ujistěte se, že neexistují žádné problémy s připojením operaci dokončit úspěšně. Řešení potíží s připojením problémy najdete [řešení potíží pomocí rutiny Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní místně vázaný svazek teď byl označen jako vrstvený svazek vzhledem k tomu, že některá data z místně vázaný svazek je při převodu přesahovat do cloudu. Výsledná vrstvený svazek je pořád využívá místní prostor zařízení, které nelze znovu použít pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, vymažte upozornění a tento svazek převést zpět na původní typ místně vázaný svazek k zajištění všech dat je k dispozici místně znovu. |
| Převod svazku <*název svazku*> se nezdařilo. |Úloha převodu svazků pro převod typu svazku z vrstveného na místně připnutý se nezdařilo. |Převod typu svazku z typu vrstveného na místně připnutý nebylo možné dokončit. Ujistěte se, že neexistují žádné problémy s připojením operaci dokončit úspěšně. Řešení potíží s připojením problémy najdete [řešení potíží pomocí rutiny Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní vrstvený svazek je nyní označena jako místně vázaný svazek, část procesu převodu se stále mít data uložená v cloudu, když se už nedá uvolnit tlustě zřízené místo na zařízení pro tento svazek pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, vymažte upozornění a tento svazek převést zpět na původní typ vrstvený svazek k zajištění, že se místní prostor tlustě zřízený v zařízení se nedá uvolnit. |
| Už brzy spotřebovávat místní prostor pro místní snímky <*název skupiny svazků*> |Místní snímky pro zásadu zálohování, která může brzy dostatek místa a lze zrušit platnost, aby se zabránilo chybám zápisu hostitele. |Časté pořizování vysoká četnost změn dat ve svazcích přidružených k této skupině zásad zálohování místních snímků způsobují v zařízení bude rychle spotřebovávat místní prostor. Odstraňte místní snímky, které už nejsou potřeba. Také aktualizujte vaše plány místních snímků pro tyto zásady zálohování využít méně častá místních snímků a ujistěte se, že cloudové snímky jsou pravidelně udělali. Pokud se provedou tyto akce, místní prostor pro tyto snímky mohou brzy dojít k vyčerpání a systém se automaticky odstraní je zajistit, že zápisy hostitele i nadále úspěšně zpracována. |
| Místní snímky pro <*název skupiny svazků*> byly zneplatněny. |Místní snímky pro <*název skupiny svazků*> byla zrušena a pak odstranit, protože jejich byly překročení místní prostor na zařízení. |K zajištění, že to není opakování v budoucnu, projděte si plány místních snímků pro tyto zásady zálohování a odstraňte místní snímky, které už nejsou potřeba. Časté pořizování vysoká četnost změn dat ve svazcích přidružených k této skupině zásad zálohování místních snímků může způsobit, že místní prostor na zařízení bude rychle spotřebovávat. |
| Obnovení <*zdroje ID elementu zálohování*> se nezdařilo. |Úloha obnovení se nezdařilo. |Pokud máte místně připnuté nebo kombinaci místně připnuté a vrstvené svazky v této zásadě zálohování, obnovení seznamu zálohování a ověřte, že je záloha stále platné. Pokud je záloha platná, je možné, že brání problémy s připojením cloud operace obnovení úspěšně dokončit. Místně vázaných svazků obnovených jako součást tohoto snímku skupiny nemají všechna svoje data do zařízení stahovat, a pokud máte v této skupině snímku kombinaci místně připnuté a vrstvené svazky, nebudou vzájemná synchronizace. K úspěšnému dokončení operace obnovení, uveďte svazky do této skupiny do offline režimu na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny úpravy dat svazku, které byly provedeny během procesu obnovení budou ztraceny. |

### <a name="networking-alerts"></a>Výstrahy sítě

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |
| Duplicitní IP adresa se nedetekovaly "Data0". | |Systém zjistil konflikt pro IP adresu '10.0.0.1'. Síťový prostředek "Data0" na zařízení  *\<zařízení1 >* je offline. Ujistěte se, že je tato IP adresa nepoužívá žádná jiná entita v této síti. Chcete-li vyřešit problémy se sítí, přejděte na [řešení potíží pomocí rutiny Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Obraťte se na správce sítě o pomoc s řešením tohoto problému. Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |
| Adresa IPv4 (nebo IPv6) pro 'Data0' je v režimu offline. | |Síťový prostředek "Data0" s IP adresou '10.0.0.1'. a délka "22" na zařízení předpony  *\<zařízení1 >* je offline. Ujistěte se, že jsou funkční porty přepínače, ke kterým je toto rozhraní připojené. Chcete-li vyřešit problémy se sítí, přejděte na [řešení potíží pomocí rutiny Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |URLthat slouží k ověřování není k dispozici. Ujistěte se, že pravidla brány firewall zahrnout vzory adres URL zadaná pro zařízení StorSimple. Další informace o vzorech adres URL na webu Azure portal, přejděte na protokol https:\//aka.ms/ss-8000-network-reqs. Pokud používáte Azure Government Cloud, přejděte na vzory adresy URL na https:\//aka.ms/ss8000-gov-network-reqs.|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Text upozornění | Událost | Další informace o / doporučené akce | |
|:--- |:--- |:--- | --- |
| Zatížení zařízení překročilo <*prahová hodnota*>. |Pomalejší než očekávané doby odezvy. |Vaše zařízení hlásí využití při velké zátěži uchovalo vstupu a výstupu. To může způsobit vaše zařízení nebude pracovat stejně jako by měl. Zkontrolujte úlohy připojili k zařízení a určí, zda jsou k dispozici, která by mohla přesunout na jiné zařízení nebo jestli nějaké už nejsou potřebná.|
| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud to problém nevyřeší, obraťte se na oddělení podpory Microsoftu. |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Byla zahájena relace Microsoft Support. |Relace používaná podporu třetí strany. |Zkontrolujte prosím, že tento přístup je autorizovaný. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními. |
| Heslo pro <*element*> vyprší za <*dobu*>. |Blíží se vypršení platnosti hesla. |Změňte si heslo dřív, než vyprší. |
| Chybí informace o konfiguraci zabezpečení <*ID elementu*>. | |Svazky přidružené k tomuto kontejneru svazků nejde používanou k replikaci konfigurace StorSimple. Aby bylo zajištěno, že vaše data jsou bezpečně uložená, doporučujeme odstranit kontejner svazků a svazky přidružené k kontejner svazků. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními. |
| <*číslo*> neúspěšných pokusů o přihlášení k <*ID elementu*>. |Několik neúspěšných pokusů o přihlášení. |Vaše zařízení může být terčem útoku nebo se autorizovaný uživatel snaží připojit pomocí nesprávného hesla.<ul><li>Kontaktujte autorizované uživatele a ověřte, že tyto pokusy pocházejí z legitimního zdroje. Pokud bude pořád zobrazovat velké množství neúspěšných pokusů o přihlášení, zvažte zakázání vzdálené správy a kontaktovat správce sítě. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními.</li><li>Zkontrolujte, že vaše instance Snapshot Manageru jsou nakonfigurovány s správné heslo. Poté, co jste udělali příslušnou akci, vymažte toto upozornění ze stránky s upozorněními.</li></ul>Další informace najdete v části [změnit heslo k zařízení s vypršenou platností](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Během změny šifrovacího klíče dat služby došlo k nejméně jednomu selhání. | |Došlo k během změny šifrovacího klíče dat služby došlo k chybám. Poté, co jste vyřešili chybové stavy, spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutiny z rozhraní Windows PowerShell pro StorSimple na vašem zařízení k aktualizaci služby. Pokud potíže potrvají, kontaktujte podporu Microsoftu. Po vyřešení problému, vymažte toto upozornění ze stránky s upozorněními. |

### <a name="support-package-alerts"></a>Podpora balíčku upozornění

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se vytvořit balíček pro podporu. |StorSimple nejde generovat balíček. |Tuto operaci opakujte. Pokud potíže potrvají, kontaktujte prosím podporu Microsoftu. Po vyřešení problému, vymažte toto upozornění ze stránky s upozorněními. |

## <a name="next-steps"></a>Další postup

Další informace o [StorSimple chyb a potíží s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md).

