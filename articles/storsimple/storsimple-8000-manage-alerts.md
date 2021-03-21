---
title: Zobrazení a správa výstrah pro zařízení řady StorSimple 8000
description: Popisuje podmínky upozornění StorSimple a závažnost, způsob konfigurace oznámení o výstrahách a způsob používání služby StorSimple Správce zařízení pro správu výstrah.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 36ed87a75d590a8647e5347b3e90e6f5159dc6b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016841"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-storsimple-alerts"></a>Použití služby StorSimple Správce zařízení k zobrazení a správě upozornění StorSimple

## <a name="overview"></a>Přehled

Okno **výstrahy** ve službě StorSimple Správce zařízení poskytuje způsob, jak zkontrolovat a vymazat výstrahy týkající se zařízení StorSimple na základě v reálném čase. Z tohoto okna můžete centrálně monitorovat problémy se stavem zařízení StorSimple a celkovému řešení Microsoft Azure StorSimple.

Tento kurz popisuje běžné podmínky upozornění, úrovně závažnosti výstrah a postup konfigurace oznámení výstrah. Kromě toho zahrnuje rychlé referenční tabulky výstrah, které umožňují rychle vyhledat konkrétní výstrahu a vhodně reagovat.

![Stránka s výstrahami](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="common-alert-conditions"></a>Běžné podmínky upozornění

Vaše zařízení StorSimple generuje výstrahy v reakci na nejrůznější podmínky. Níže jsou uvedené nejběžnější typy podmínek upozornění:

* **Problémy s hardwarem** – tyto výstrahy vás informují o stavu hardwaru. Poskytují informace o tom, jestli jsou potřeba upgrady firmwaru, pokud má síťové rozhraní nějaké problémy, nebo pokud dojde k potížím s jednou z vašich datových jednotek.
* **Problémy s připojením** – tyto výstrahy se objevují v případě problémů s přenosem dat. Problémy s komunikací mohou nastat během přenosu dat do účtu služby Azure Storage a z důvodu nedostatku připojení mezi zařízeními a službou StorSimple Správce zařízení. Problémy s komunikací jsou závažné, aby je bylo možné opravit, protože došlo k mnoha bodům selhání. Předtím, než budete pokračovat na pokročilejší řešení potíží, byste měli nejdřív ověřit, že je k dispozici připojení k síti a přístup k Internetu. Pomoc s řešením potíží najdete v tématu [řešení potíží pomocí rutiny Test-Connection](storsimple-8000-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny tím, že systém nepracuje optimálně, například když se nachází v případě vysoké zátěže.

Kromě toho se můžou zobrazovat výstrahy související se zabezpečením, aktualizacemi nebo selháními úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrahy

Výstrahy mají různé úrovně závažnosti v závislosti na tom, jaký vliv bude mít situace v případě výstrahy, a nutnost reakce na výstrahu. Úrovně závažnosti jsou následující:

* **Kritická** – Tato výstraha je v reakci na podmínku, která má vliv na úspěšný výkon systému. Pro zajištění, že služba StorSimple není přerušena, je vyžadována akce.
* **Upozornění** – tento stav může být kritický, pokud není vyřešen. Měli byste prošetřit situaci a provést jakoukoli akci nutnou k odstranění problému.
* **Informace** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správě systému.

## <a name="configure-alert-settings"></a>Konfigurace nastavení upozornění

Můžete zvolit, jestli chcete být upozorňováni e-mailem podmínek upozornění pro každé zařízení StorSimple. Kromě toho můžete určit další příjemce oznámení výstrah zadáním jejich e-mailových adres v poli **ostatní příjemci e-mailu** , které jsou odděleny středníky.

> [!NOTE]
> Pro každé zařízení můžete zadat maximálně 20 e-mailových adres.

Po povolení e-mailových oznámení pro zařízení budou členové seznamu oznámení dostávat e-mailové zprávy pokaždé, když dojde k kritickému upozornění. Zprávy budou odeslány z *StorSimple-Alerts- \@ mail.windowsazure.com odpovědi* a popíší se podmínky upozornění. Příjemci můžou kliknout na **zrušit odběr** a odebrat si ho ze seznamu e-mailových oznámení.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Povolení e-mailových oznámení o výstrahách pro zařízení
1. Přejděte do služby Správce zařízení StorSimple. V seznamu zařízení vyberte a klikněte na zařízení, které chcete nakonfigurovat.
2. Pro zařízení přejít na **Nastavení**  >  **Obecné** .

   ![Okno výstrahy](./media/storsimple-8000-manage-alerts/configure-alerts-email2.png)
   
2. V okně **Obecné nastavení** otevřete **Nastavení výstrahy** a nastavte následující:
   
   1. V poli **Odeslat oznámení e-mailem** vyberte **Ano**.
   2. V poli **Správci e-mailové služby** vyberte **Ano** , pokud chcete, aby správce služby a všichni spolusprávci dostávali oznámení o výstrahách.
   3. V poli **ostatní příjemci e-mailu** zadejte e-mailové adresy všech dalších příjemců, kteří by měli dostávat oznámení o výstrahách. Zadejte jména ve formátu *někdo \@ Somewhere.com*. Oddělte e-mailové adresy pomocí středníků. Pro každé zařízení můžete nakonfigurovat maximálně 20 e-mailových adres. 
      
3. Pokud chcete poslat testovací e-mailové oznámení, klikněte na **Odeslat zkušební e-mail**. Služba StorSimple Správce zařízení zobrazí stavové zprávy, když přepošle oznámení o testu.

    ![Nastavení výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email3.png)

4. Po odeslání testovacího e-mailu se zobrazí oznámení. 
   
    ![E-mail s testovacím oznámením výstrah odeslán](./media/storsimple-8000-manage-alerts/configure-alerts-email4.png)
   
   > [!NOTE]
   > Pokud zprávu o testovacím oznámení nelze odeslat, Služba StorSimple Správce zařízení zobrazí příslušnou chybovou zprávu. Počkejte pár minut a pak zkuste poslat zprávu testovacího oznámení znovu. 

5. Po dokončení konfigurace klikněte na **Uložit**. Po zobrazení výzvy k potvrzení klikněte na **Ano**.

     ![E-mail s oznámením o testování výstrah odeslán 2](./media/storsimple-8000-manage-alerts/configure-alerts-email5.png)

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okno StorSimple Správce zařízení Service Summary vám poskytne rychlý přehled o počtu upozornění na vašich zařízeních uspořádaných podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-8000-manage-alerts/device-summary4.png)

Kliknutím na úroveň závažnosti se otevře okno **výstrahy** . Výsledky obsahují jenom výstrahy, které odpovídají této úrovni závažnosti.

Kliknutím na výstrahu v seznamu získáte další podrobnosti o výstraze, včetně času, kdy byla výstraha zaznamenána, počtu výskytů výstrahy na zařízení a doporučené akci pro vyřešení výstrahy. Pokud se jedná o hardwarovou výstrahu, bude také identifikovat hardwarovou součást.

![Příklad hardwarové výstrahy](./media/storsimple-8000-manage-alerts/configure-alerts-email14.png)

Podrobnosti o výstraze můžete zkopírovat do textového souboru, pokud potřebujete informace odeslat podpora Microsoftu. Po doporučení a vyřešení podmínky upozornění v místním prostředí byste měli výstrahu ze zařízení vymazat výběrem výstrahy v okně **výstrahy** a kliknutím na tlačítko **Vymazat**. Chcete-li vymazat více výstrah, vyberte jednotlivá upozornění, klikněte na kterýkoli sloupec kromě sloupce **Výstraha** a po výběru všech výstrah, které mají být vymazány, klikněte na tlačítko **Vymazat** . Všimněte si, že některé výstrahy se automaticky vymažou při vyřešení problému nebo když systém aktualizuje upozornění o nové informace.

Když kliknete na **Vymazat**, budete mít možnost zadat komentáře k výstraze a kroky, které jste provedli k vyřešení tohoto problému. Pokud se v systému spustí jiná událost s novými informacemi, vymaže se některé události. V takovém případě se zobrazí následující zpráva.

![Vymazat zprávu výstrahy](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Řazení a Kontrola výstrah

Může to být efektivnější pro spouštění sestav na základě výstrah, abyste je mohli zkontrolovat a vymazat ve skupinách. Kromě toho může okno **výstrahy** zobrazit až 250 upozornění. Pokud jste tento počet výstrah překročili, nezobrazí se ve výchozím zobrazení všechny výstrahy. Můžete zkombinovat následující pole a přizpůsobit tak, která upozornění se zobrazí:

* **Stav** – můžete zobrazit **aktivní** nebo **vymazané** výstrahy. Aktivní výstrahy se ve vašem systému pořád spouštějí, zatímco vymazané výstrahy odstranil správce nebo je program vymazal ručně, protože systém aktualizoval podmínku upozornění novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všech úrovní závažnosti (kritická, upozornění, informace) nebo jenom určité závažnosti, jako třeba jenom kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí ze služby nebo jednoho nebo všech zařízení.
* **Časový rozsah** – zadáním hodnot **z** a **na** data a časová razítka si můžete prohlédnout výstrahy během časového období, které vás zajímá.

![Seznam výstrah](./media/storsimple-8000-manage-alerts/configure-alerts-email11.png)

## <a name="alerts-quick-reference"></a>Rychlé referenční informace o výstrahách

V následujících tabulkách jsou uvedeny některé výstrahy Microsoft Azure StorSimple, se kterými se můžete setkat, a také další informace a doporučení, kde jsou k dispozici. Výstrahy zařízení StorSimple spadají do jedné z následujících kategorií:

* [Výstrahy cloudového připojení](#cloud-connectivity-alerts)
* [Výstrahy clusteru](#cluster-alerts)
* [Výstrahy zotavení po havárii](#disaster-recovery-alerts)
* [Hardwarové výstrahy](#hardware-alerts)
* [Výstrahy selhání úlohy](#job-failure-alerts)
* [Výstrahy místně připojeného svazku](#locally-pinned-volume-alerts)
* [Výstrahy sítě](#networking-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)
* [Podpora výstrah balíčku](#support-package-alerts)
* [Výstrahy prostředí skříně](#enclosure-environment-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy cloudového připojení

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nelze navázat připojení k <*název přihlašovacích údajů cloudu*>. |Nejde se připojit k účtu úložiště. |Vypadá to, že může dojít k potížím s připojením k vašemu zařízení. Pokud `Test-HcsmConnection` Chcete tento problém identifikovat a vyřešit, spusťte prosím rutinu z rozhraní Windows PowerShellu pro StorSimple na vašem zařízení. Pokud je nastavení správné, problém může být s přihlašovacími údaji účtu úložiště, pro který byla výstraha vyvolána. V takovém případě pomocí `Test-HcsStorageAccountCredential` rutiny určete, jestli jsou problémy, které můžete vyřešit.<ul><li>Ověřte nastavení sítě.</li><li>Ověřte přihlašovací údaje účtu úložiště.</li></ul> |
| Z vašeho zařízení jsme nedostali prezenční signál pro poslední <*číslo*> minut. |Nelze se připojit k zařízení. |Vypadá to, že máte problém s připojením k vašemu zařízení. Použijte prosím `Test-HcsmConnection` rutinu z rozhraní Windows PowerShellu pro StorSimple na vašem zařízení k identifikaci a vyřešení problému nebo kontaktujte správce sítě. |

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>StorSimple chování při neúspěšném připojení ke cloudu

Co se stane, když se pro zařízení StorSimple běžící v produkčním prostředí nepovede cloudové připojení?

Pokud v produkčním zařízení StorSimple dojde k chybě cloudového připojení, může v závislosti na stavu zařízení dojít k následujícím akcím:

* **Pro místní data na zařízení**: v některých případech nedojde k žádnému přerušení a čtení se bude dál obsluhovat. Protože se ale počet nezpracovaných IOs zvyšuje a překračuje limit, může čtení začít selhat.

    V závislosti na množství dat ve vašem zařízení budou zápisy taky pořád po přerušení připojení cloudu i po výpadku. Zápisy se pak zpomalí a nakonec začnou selhat, pokud dojde k narušení cloudového připojení několik hodin. (Na zařízení je dočasné úložiště pro data, která se mají vložit do cloudu. Tato oblast se po odeslání dat vyprázdní. Pokud se připojení nezdaří, data v této oblasti úložiště nebudou vložena do cloudu a vstupně-výstupní operace selže.)
* **Pro data v cloudu**: pro většinu chyb cloudového připojení se vrátí chyba. Po obnovení připojení se IOs obnoví, aniž by uživatel musel převést svazek do online režimu. Ve výjimečných případech může být nutný zásah uživatele, aby se vrátil zpátky do online režimu z Azure Portal.
* **Pro cloudové snímky** probíhá opakování operace několikrát během 4-5 hodin, a pokud se připojení neobnoví, snímky v cloudu se nezdaří.

### <a name="cluster-alerts"></a>Výstrahy clusteru

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Zařízení je v režimu údržby. |Došlo k převzetí služeb zařízení, protože došlo k přechodu do režimu údržby nebo jeho opuštění. To je normální a není nutné provádět žádnou akci. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Firmware nebo software zařízení byl právě aktualizován. |Kvůli aktualizaci došlo k převzetí služeb při selhání clusteru. To je normální a není nutné provádět žádnou akci. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Kontroler se vypnul nebo restartoval. |Došlo k převzetí služeb zařízení, protože správce ukončil nebo restartoval aktivní kontroler. Není nutné provádět žádnou akci. Až toto upozornění potvrdíte, vymažte ho ze stránky s upozorněními. |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Plánované převzetí služeb při selhání. |Ověřte, že se jednalo o plánované převzetí služeb při selhání. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními. |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Neplánované převzetí služeb při selhání. |StorSimple je sestaven tak, aby se automaticky obnovil z neplánovaných převzetí služeb při selhání. Pokud se vám zobrazí velký počet výstrah, obraťte se na podpora Microsoftu. |
| Při převzetí služeb při selhání zařízení <*název zařízení*>. |Jiná/neznámá příčina. |Pokud se vám zobrazí velký počet výstrah, obraťte se na podpora Microsoftu. Po vyřešení problému vymažte toto upozornění ze stránky s upozorněními. |
| Kritická služba zařízení hlásí stav jako neúspěšné. |Chyba služby DataPath. |Požádejte o pomoc podpora Microsoftu. |
| Virtuální IP adresa pro síťové rozhraní <*data #*> hlásí stav jako neúspěšný. |Jiná/neznámá příčina. |Někdy můžou tyto výstrahy způsobit dočasné podmínky. V takovém případě se tato výstraha po nějaké době automaticky vymaže. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| Virtuální IP adresa pro síťové rozhraní <*data #*> hlásí stav jako neúspěšný. |Název rozhraní: <*data #*> IP adresu `<IP address>` nelze uvést do režimu online, protože v síti byla zjištěna duplicitní IP adresa. |Zajistěte, aby se duplicitní IP adresa odebrala ze sítě, nebo překonfigurovali rozhraní s jinou IP adresou. |

### <a name="disaster-recovery-alerts"></a>Výstrahy zotavení po havárii

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Operace obnovení nemohly obnovit všechna nastavení této služby. Data konfigurace zařízení jsou pro některá zařízení v nekonzistentním stavu. |Po zotavení po havárii se zjistila nekonzistence dat. |Šifrovaná data ve službě nejsou synchronizovaná s tímto zařízením na zařízení. Pokud chcete spustit proces synchronizace, autorizujte zařízení <*název zařízení*> z StorSimple Správce zařízení. Použijte rozhraní Windows PowerShell pro StorSimple ke spuštění `Restore-HcsmEncryptedServiceData` rutiny device <*název zařízení*> a zadáním starého hesla jako vstupu do této rutiny obnovíte profil zabezpečení. Pak spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutinu, která aktualizuje šifrovací klíč dat služby. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními. |

### <a name="hardware-alerts"></a>Hardwarové výstrahy

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Hardwarová součást <*ID komponenty*> stav sestav jako *stav <>* . | |Někdy můžou tyto výstrahy způsobit dočasné podmínky. V takovém případě se tato výstraha po nějaké době automaticky vymaže. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |
| Pasivní kontroler nefunguje. |Pasivní (sekundární) kontroler nefunguje. |Vaše zařízení je funkční, ale jeden z vašich řadičů nefunguje správně. Zkuste restartovat kontroler. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. |

### <a name="job-failure-alerts"></a>Výstrahy selhání úlohy

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se vytvořit zálohu *ID skupiny zdrojového svazku* <>. |Úloha zálohování se nezdařila. |Operaci zálohování nelze úspěšně dokončit, protože může dojít k problémům s připojením. Pokud nedochází k žádným problémům s připojením, možná jste dosáhli maximálního počtu záloh. Odstraňte všechny zálohy, které už nepotřebujete, a zkuste operaci zopakovat. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními. |
| Klonování *ID elementu zálohování zdroje* <> na <*sériová čísla cílových svazků*> se nezdařilo. |Úloha klonování se nezdařila. |Aktualizujte seznam zálohování a ověřte, zda je záloha stále platná. Pokud je záloha platná, je možné, že problémy s cloudovým připojením brání úspěšnému dokončení operace klonování. Pokud nedochází k žádným problémům s připojením, možná jste dosáhli limitu úložiště. Odstraňte všechny zálohy, které už nepotřebujete, a zkuste operaci zopakovat. Až provedete příslušnou akci k vyřešení problému, vymažte tuto výstrahu ze stránky s upozorněními. |
| Obnovení *ID elementu zálohování zdroje* <> se nezdařilo. |Úloha obnovení se nezdařila. |Aktualizujte seznam zálohování a ověřte, zda je záloha stále platná. Pokud je záloha platná, je možné, že potíže s cloudovým připojením brání úspěšnému dokončení operace obnovení. Pokud nedochází k žádným problémům s připojením, možná jste dosáhli limitu úložiště. Odstraňte všechny zálohy, které už nepotřebujete, a zkuste operaci zopakovat. Až provedete příslušnou akci k vyřešení problému, vymažte tuto výstrahu ze stránky s upozorněními. |

### <a name="locally-pinned-volume-alerts"></a>Výstrahy místně připojeného svazku

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Vytvoření místního svazku <*název svazku*> se nezdařilo. |Úloha vytvoření svazku se nezdařila. <*Chybová zpráva odpovídající neúspěšnému kódu chyby*>. |Kvůli problémům s připojením se nepovedlo úspěšně dokončit operaci vytváření prostoru. Místně připnuté svazky jsou silným zřizováním a proces vytváření prostoru zahrnuje přelití vrstvených svazků do cloudu. Pokud nedochází k žádným problémům s připojením, možná jste vyčerpali místní prostor na zařízení. Než zkusíte operaci zopakovat, určete, jestli na zařízení existuje prostor. |
| Nepovedlo se rozbalení *názvu svazku* <místního svazku>. |Úloha změny svazku se nezdařila z důvodu <*chybové zprávy odpovídající neúspěšnému kódu chyby*>. |Kvůli problémům s připojením se nepovedlo úspěšně dokončit operaci rozšíření svazku. Místně připnuté svazky jsou silným zajišťovány a proces rozšíření stávajícího prostoru zahrnuje přelití vrstvených svazků do cloudu. Pokud nedochází k žádným problémům s připojením, možná jste vyčerpali místní prostor na zařízení. Než zkusíte operaci zopakovat, určete, jestli na zařízení existuje prostor. |
| Převod> *názvu* svazku <svazku se nezdařil. |Úloha převodu svazků pro převod typu svazku z místně připnutého na vrstvený typ se nezdařila. |Převod svazku z typu místně připnutého na vrstvený nelze dokončit. Zajistěte, aby nedocházelo k úspěšnému dokončení operace, protože nedochází k problémům se sítí. Potíže s připojením najdete v tématu [řešení potíží pomocí rutiny Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní místně připojený svazek je teď označený jako vrstvený svazek, protože některá z dat z místně připnutého svazku se během převodu převedla do cloudu. Výsledný vrstvený svazek stále používá místní prostor na zařízení, které se nedá uvolnit pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, zrušte výstrahu a převeďte tento svazek zpátky na původní typ připojeného svazku, abyste zajistili, že budou všechna data místně k dispozici. |
| Převod> *názvu* svazku <svazku se nezdařil. |Úloha převodu svazků pro převod typu svazku z vrstveného na místně připnuté se nezdařila. |Převod svazku z typu vrstveného na místně připnutý nešlo dokončit. Zajistěte, aby nedocházelo k úspěšnému dokončení operace, protože nedochází k problémům se sítí. Potíže s připojením najdete v tématu [řešení potíží pomocí rutiny Test-HcsmConnection](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>Původní vrstvený svazek teď označený jako součást procesu převodu, který je místně připnutého svazku, má nadále data v cloudu, ale silné zřízené místo na zařízení pro tento svazek se už nedá uvolnit pro budoucí místní svazky.<br>Vyřešte všechny problémy s připojením, vymažte výstrahu a převeďte tento svazek zpátky na původní typ svazku s vrstvenými vrstvami, aby bylo zajištěno, že se místní prostor na zařízení dá znovu zřídit. |
| Blíží se místní spotřebě místa pro místní snímky <*název skupiny svazků* .> |Místním snímkům pro zásady zálohování může dojít k brzkému vyzkoušení místa a zrušení platnosti, aby se předešlo chybám při zápisu hostitele. |Časté místní snímky spolu s vysokou četností změn dat ve svazcích přidružených k této skupině zásad zálohování způsobují, že místní prostor na zařízení bude možné rychle spotřebovat. Odstraňte všechny místní snímky, které už nepotřebujete. Aktualizujte také místní plány snímků pro tyto zásady zálohování tak, aby se zohlednily méně časté místní snímky, a zajistěte, aby se snímky cloudu pravidelně provedly. Pokud tyto akce nejsou podniknuty, může dojít k brzkému vyčerpání místního prostoru pro tyto snímky a systém je automaticky odstraní, aby bylo zajištěno, že budou zápisy hostitele nadále zpracovávány úspěšně. |
| Došlo k zrušení platnosti místních snímků pro <*název skupiny svazků*>. |Místní snímky pro <*název skupiny svazků*> se zrušily a pak se odstranily, protože překročily místní prostor na zařízení. |Abyste se ujistili, že se to v budoucnu neopakuje, Projděte si plány místních snímků pro tyto zásady zálohování a odstraňte všechny místní snímky, které už nepotřebujete. Časté místní snímky spolu s vysokou četností změn dat ve svazcích přidružených k této skupině zásad zálohování můžou způsobit, že se místní prostor na zařízení bude rychle spotřebovat. |
| Obnovení *ID elementu zálohování zdroje* <> se nezdařilo. |Úloha obnovení se nezdařila. |Pokud jste v této zásadě zálohování připnuli místně nebo kombinaci místně připnutých a vrstvených svazků, aktualizujte seznam zálohování a ověřte, jestli je záloha pořád platná. Pokud je záloha platná, je možné, že potíže s cloudovým připojením brání úspěšnému dokončení operace obnovení. Místně připojené svazky, které se obnovily jako součást této skupiny snímků, neobsahují všechna data stažená do zařízení, a pokud máte v této skupině snímků kombinaci vrstvených a místně připnuté svazků, nebudou se vzájemně synchronizovat. Chcete-li úspěšně dokončit operaci obnovení, převeďte svazky v této skupině do stavu offline na hostiteli a opakujte operaci obnovení. Všimněte si, že všechny změny dat svazků, které byly provedeny během procesu obnovení, budou ztraceny. |

### <a name="networking-alerts"></a>Výstrahy sítě

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud potíže potrvají, obraťte se na podpora Microsoftu. |
| Byla zjištěna duplicitní IP adresa pro ' DATA0 '. | |Systém zjistil konflikt pro IP adresu 10.0.0.1. Síťový prostředek ' DATA0 ' na zařízení *\<device1>* je offline. Ujistěte se, že se tato IP adresa nepoužívá v žádné jiné entitě v této síti. Pokud chcete řešit problémy se sítí, přečtěte si [řešení potíží pomocí rutiny Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Pokud chcete tento problém vyřešit, obraťte se na správce sítě. Pokud potíže potrvají, obraťte se na podpora Microsoftu. |
| Adresa IPv4 (nebo IPv6) pro ' DATA0 ' je offline. | |Síťový prostředek "DATA0" s IP adresou "10.0.0.1" a délka předpony 22 na zařízení *\<device1>* je offline. Zajistěte, aby byly porty přepínače, ke kterým je toto rozhraní připojené, v provozu. Pokud chcete řešit problémy se sítí, přečtěte si [řešení potíží pomocí rutiny Get-NetAdapter](storsimple-8000-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |URLthat se používá k ověření, není dosažitelný. Ujistěte se, že pravidla brány firewall obsahují vzory adres URL určené pro zařízení StorSimple. Další informace o vzorech adresy URL v Azure Portal najdete v protokolu https: \/ /aka.MS/SS-8000-Network-reqs. Pokud používáte Azure Government Cloud, pokračujte na vzory adres URL v protokolu https: \/ /aka.MS/ss8000-gov-Network-reqs.|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Zatížení zařízení překročilo> *prahové hodnoty* <. |Pomalejší než očekávaná doba odezvy. |Vaše zařízení hlásí využití v případě velkého vstupně-výstupního zatížení. To může způsobit, že vaše zařízení nefunguje, a mělo by to být. Zkontrolujte úlohy, které jste připojili k zařízení, a zjistěte, jestli existují nějaké, které by se daly přesunout na jiné zařízení nebo které už nejsou potřebné.|
| Nepovedlo se spustit služby StorSimple. |Chyba DataPath |Pokud potíže potrvají, obraťte se na podpora Microsoftu. |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Relace podpora Microsoftu začala. |Relace podpory, která je k dispozici třetí strany. |Potvrďte prosím, že je tento přístup autorizovaný. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními. |
| Heslo pro <*element*> vyprší v <*délce*>. |Blíží se vypršení platnosti hesla. |Změňte heslo, než vyprší jeho platnost. |
| Chybí informace o konfiguraci zabezpečení pro <*identifikátor elementu*>. | |Svazky přidružené k tomuto kontejneru svazků nelze použít k replikaci konfigurace StorSimple. Abyste měli jistotu, že jsou vaše data bezpečně uložená, doporučujeme odstranit kontejner svazků a všechny svazky, které jsou přidružené k kontejneru svazků. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními. |
| <*počet*> pokusů o přihlášení pro *ID elementu* <>. |Několik neúspěšných pokusů o přihlášení |Vaše zařízení může být útoku nebo se autorizovaný uživatel snaží připojit pomocí nesprávného hesla.<ul><li>Obraťte se na autorizované uživatele a ověřte, že tyto pokusy byly z legitimního zdroje. Pokud budete dál zobrazovat velké počty neúspěšných pokusů o přihlášení, zvažte zakázání vzdálené správy a kontaktování správce sítě. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními.</li><li>Ověřte, že vaše instance Snapshot Manager jsou nakonfigurované se správným heslem. Až provedete příslušnou akci, vymažte tuto výstrahu ze stránky s upozorněními.</li></ul>Další informace najdete v [pozměně hesla zařízení s prošlou platností](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password). |
| Při změně šifrovacího klíče dat služby došlo k jedné nebo více chybám. | |Při změně šifrovacího klíče dat služby došlo k chybám. Po vyřešení chybových podmínek spusťte `Invoke-HcsmServiceDataEncryptionKeyChange` rutinu z rozhraní Windows PowerShellu pro StorSimple na zařízení a aktualizujte službu. Pokud s tím budou dál problémy, obraťte se na podporu Microsoftu. Po vyřešení problému vymažte toto upozornění ze stránky s upozorněními. |

### <a name="support-package-alerts"></a>Podpora výstrah balíčku

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se vytvořit balíček pro podporu. |StorSimple nemohl vytvořit balíček. |Zkuste tuto operaci znovu. Pokud se problém nevyřeší, obraťte se na podpora Microsoftu. Po vyřešení problému vymažte toto upozornění ze stránky s upozorněními. |

### <a name="enclosure-environment-alerts"></a>Výstrahy prostředí skříně

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Senzor teploty okolí hardwarových součástí hlásí stav jako neúspěšný.  | Typ skříně: hlavní skříň | Tato výstraha se aktivuje, když okolí vnější teploty kolem StorSimple překračuje přijatelný rozsah. Prohlédněte si vnější teplotu mimo AC otvor v datacentru. Když se teplota vrátí na normální, výstraha se po uplynutí určité doby automaticky vymaže. Pokud potíže trvají, obraťte se na podporu Microsoftu.   |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [chybách StorSimple a řešení problémů s nasazením zařízení](storsimple-8000-troubleshoot-deployment.md).
