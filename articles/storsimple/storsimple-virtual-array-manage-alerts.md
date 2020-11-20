---
title: Zobrazení a správa výstrah pro virtuální pole StorSimple
description: Popisuje upozornění na virtuální pole StorSimple a závažnost a způsob používání služby StorSimple Manager ke správě výstrah.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 12fcc9996697f3bbba35826d79bec238bfb0f8b3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956207"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Použití Device Manager StorSimple ke správě výstrah pro virtuální pole StorSimple

## <a name="overview"></a>Přehled

Funkce výstrahy ve službě StorSimple Device Manager poskytuje způsob, jak zkontrolovat a vymazat výstrahy související s virtuálními poli StorSimple na základě v reálném čase. Pomocí upozornění v okně **souhrnu služby** můžete centrálně monitorovat problémy se stavem StorSimple virtuálních polí a celkového řešení Microsoft Azure StorSimple.

V tomto kurzu se dozvíte, jak nakonfigurovat upozornění na výstrahy, běžné podmínky upozornění, úrovně závažnosti výstrahy a jak zobrazovat a sledovat výstrahy. Kromě toho zahrnuje rychlé referenční tabulky výstrah, které umožňují rychle vyhledat konkrétní výstrahu a vhodně reagovat.

![Stránka s výstrahami](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurace nastavení upozornění

Můžete zvolit, zda chcete být upozorňováni e-mailem s podmínkami upozornění pro každé z vašich virtuálních polí StorSimple. Kromě toho můžete určit další příjemce oznámení výstrah zadáním jejich e-mailových adres do pole **Další příjemci e-mailu** , které jsou odděleny středníky.

> [!NOTE]
> Pro každé virtuální pole můžete zadat maximálně 20 e-mailových adres.

Po povolení e-mailových oznámení pro virtuální pole budou členové seznamu oznámení při každém výskytu kritické výstrahy dostávat e-mailové zprávy. Zprávy budou odeslány z *StorSimple-Alerts- \@ mail.windowsazure.com odpovědi* a popíší se podmínky upozornění. Příjemci můžou kliknout na **zrušit odběr** a odebrat si ho ze seznamu e-mailových oznámení.

#### <a name="to-enable-email-notification-for-alerts"></a>Povolení e-mailových oznámení pro výstrahy

1. Přejděte do služby StorSimple Device Manager a v části **Správa** vyberte a klikněte na **zařízení**. V zobrazeném seznamu zařízení vyberte a klikněte na své zařízení.
   
    ![nastavení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Otevře se okno **Nastavení** . V části **nastavení zařízení** vyberte **Obecné**. Otevře se okno **Obecné nastavení** .
   
    ![Snímek obrazovky se zobrazí v podokně nastavení zařízení s vyvolanou oblastí nastavení výstrahy.](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. V okně **Obecné nastavení** , přejít na **Nastavení výstrahy** a nastavte následující:
   
   1. V poli **Povolit e-mailová oznámení** vyberte **Ano**.
   2. V poli **Správci e-mailové služby** vyberte možnost **Ano** , pokud chcete, aby správce služby a všichni spolusprávci dostávali oznámení o výstrahách.
   3. Do pole **Další příjemci e-mailu** zadejte e-mailové adresy všech dalších příjemců, kteří by měli dostávat oznámení o výstrahách. Zadejte jména ve formátu *někdo \@ Somewhere.com*. Oddělte e-mailové adresy pomocí středníků. Na jedno virtuální zařízení můžete nakonfigurovat maximálně 20 e-mailových adres.
      
       ![Snímek obrazovky s nastavením popsaným v tomto kroku zobrazuje podrobnosti nastavení výstrah.](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Pokud chcete poslat testovací e-mailové oznámení, klikněte na **Odeslat zkušební e-mail**. Služba StorSimple Device Manager zobrazí stavové zprávy, když přepošle oznámení o testu.
      
       ![Snímek obrazovky s ověřováním testovacího e-mailu se zobrazí v dialogovém okně.](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Pokud zprávu o testovacím oznámení nelze odeslat, Služba StorSimple Device Manager zobrazí příslušnou zprávu. Klikněte na **OK**, počkejte pár minut a pak zkuste poslat zprávu testovacího oznámení znovu.
      >
      >
   5. V dolní části stránky klikněte na **Uložit** a uložte konfiguraci. Po zobrazení výzvy k potvrzení klikněte na **Ano**.
      
      ![Snímek obrazovky se zobrazí v podokně nastavení s vybraným tlačítkem Uložit.](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Běžné podmínky upozornění

Vaše virtuální pole StorSimple generuje výstrahy v reakci na nejrůznější podmínky. Níže jsou uvedené nejběžnější typy podmínek upozornění:

* **Problémy s připojením** – tyto výstrahy se objevují v případě problémů s přenosem dat. Problémy s komunikací mohou nastat během přenosu dat do účtu služby Azure Storage a z důvodu nedostatku připojení mezi virtuálními zařízeními a službou StorSimple Device Manager. Problémy s komunikací jsou závažné, aby je bylo možné opravit, protože došlo k mnoha bodům selhání. Předtím, než budete pokračovat na pokročilejší řešení potíží, byste měli nejdřív ověřit, že je k dispozici připojení k síti a přístup k Internetu. Informace o portech a nastaveních brány firewall najdete v [požadavcích na systém StorSimple Virtual Array](storsimple-ova-system-requirements.md). Pomoc s řešením potíží najdete v tématu [řešení potíží pomocí rutiny Test-Connection](./storsimple-8000-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny tím, že systém nepracuje optimálně, například když se nachází v případě vysoké zátěže.

Kromě toho se můžou zobrazovat výstrahy související se zabezpečením, aktualizacemi nebo selháními úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrahy

Výstrahy mají různé úrovně závažnosti v závislosti na tom, jaký vliv bude mít situace v případě výstrahy, a nutnost reakce na výstrahu. Úrovně závažnosti jsou následující:

* **Kritická** – Tato výstraha je v reakci na podmínku, která má vliv na úspěšný výkon systému. Pro zajištění, že služba StorSimple není přerušena, je vyžadována akce.
* **Upozornění** – tento stav může být kritický, pokud není vyřešen. Měli byste prošetřit situaci a provést jakoukoli akci nutnou k odstranění problému.
* **Informace** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správě systému.

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okno s přehledem služby StorSimple Device Manager poskytuje rychlý přehled o počtu upozornění na virtuálních zařízeních uspořádaných podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Kliknutím na úroveň závažnosti otevřete okno **výstrahy** . Výsledky obsahují jenom výstrahy, které odpovídají této úrovni závažnosti.

![Sestava výstrah vymezená na typ výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknutím na výstrahu v seznamu získáte další podrobnosti o výstraze, včetně času, kdy byla výstraha zaznamenána, počtu výskytů výstrahy na zařízení a doporučené akci pro vyřešení výstrahy.

![Seznam výstrah a podrobnosti](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Podrobnosti o výstraze můžete zkopírovat do textového souboru, pokud potřebujete informace odeslat podpora Microsoftu. Po doporučení a vyřešení podmínky upozornění v místním prostředí byste měli upozornění vymazat ze seznamu. Vyberte výstrahu ze seznamu a klikněte na tlačítko **Vymazat**. Chcete-li vymazat více výstrah, vyberte jednotlivá upozornění, klikněte na kterýkoli sloupec kromě sloupce **Výstraha** a po výběru všech výstrah, které mají být vymazány, klikněte na tlačítko **Vymazat** .

Když kliknete na **Vymazat**, budete mít možnost zadat komentáře k výstraze a kroky, které jste provedli k vyřešení tohoto problému.

![Komentáře výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Pokud se v systému spustí jiná událost s novými informacemi, vymaže se některé události.

## <a name="sort-and-review-alerts"></a>Řazení a Kontrola výstrah

Okno **výstrahy** může zobrazit až 250 upozornění. Pokud jste tento počet výstrah překročili, nezobrazí se ve výchozím zobrazení všechny výstrahy. Můžete zkombinovat následující pole a přizpůsobit tak, která upozornění se zobrazí:

* **Stav** – můžete zobrazit **aktivní** nebo **vymazané** výstrahy. Aktivní výstrahy se ve vašem systému pořád spouštějí, zatímco vymazané výstrahy odstranil správce nebo je program vymazal ručně, protože systém aktualizoval podmínku upozornění novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všech úrovní závažnosti (kritická, upozornění, informace) nebo jenom určité závažnosti, jako třeba jenom kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí ze služby nebo jednoho nebo všech virtuálních zařízení.
* **Časový rozsah** – zadáním hodnot **z** a **na** data a časová razítka si můžete prohlédnout výstrahy během časového období, které vás zajímá.

## <a name="alerts-quick-reference"></a>Rychlé referenční informace o výstrahách

V následujících tabulkách jsou uvedeny některé StorSimple výstrahy, se kterými se můžete setkat, a také další informace a doporučení, kde jsou k dispozici. Výstrahy virtuálního pole StorSimple spadají do jedné z následujících kategorií:

* [Výstrahy cloudového připojení](#cloud-connectivity-alerts)
* [Výstrahy konfigurace](#configuration-alerts)
* [Výstrahy selhání úlohy](#job-failure-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy cloudového připojení

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Zařízení <*název zařízení*> není připojené ke cloudu. |Pojmenované zařízení se nemůže připojit ke cloudu. |Nepovedlo se připojit ke cloudu. Může to být způsobené jedním z následujících důvodů:<ul><li>Může se jednat o problém s nastavením sítě v zařízení.</li><li>Může se jednat o problém s přihlašovacími údaji k účtu úložiště.</li></ul>Další informace o řešení problémů s připojením najdete v [místním webovém uživatelském rozhraní](storsimple-ova-web-ui-admin.md) zařízení. |

### <a name="configuration-alerts"></a>Výstrahy konfigurace

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Místní konfigurace virtuálních zařízení není podporována. |Pomalý výkon. |Aktuální konfigurace může způsobit snížení výkonu. Ujistěte se, že server splňuje minimální požadavky na konfiguraci. Další informace najdete v požadavcích na [virtuální pole StorSimple](storsimple-ova-system-requirements.md). |
| Na <*název zařízení* dochází místo zřízeného místa na disku \> . |Upozornění na místo na disku. |Máte dostatek volného místa na disku. Pokud chcete uvolnit místo, zvažte přesunutí úloh na jiný svazek nebo sdílení nebo odstranění dat. |

### <a name="job-failure-alerts"></a>Výstrahy selhání úlohy

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nepovedlo se dokončit zálohování *názvu <zařízení* \> . |Úloha zálohování se nezdařila. |Nebylo možné vytvořit zálohu. Vezměte v úvahu jednu z následujících možností:<ul><li>Operaci zálohování nelze úspěšně dokončit, protože může dojít k problémům s připojením. Ujistěte se, že nedochází k žádným problémům s připojením. Další informace o řešení problémů s připojením najdete v [místním webovém uživatelském rozhraní](storsimple-ova-web-ui-admin.md) pro vaše virtuální zařízení.</li><li>Dosáhli jste limitu dostupného úložiště. Pokud chcete uvolnit místo, zvažte odstranění všech záloh, které už nepotřebujete.</li></ul> Vyřešte problémy, vymažte výstrahu a zkuste operaci zopakovat. |
| Klonování *názvu zařízení* <\> se nepovedlo dokončit. |Klonování úlohy se nezdařilo. |Nelze vytvořit klon. Vezměte v úvahu jednu z následujících možností:<ul><li>Seznam zálohování možná není platný. Aktualizujte seznam a ověřte, zda je stále platný.</li><li>Operaci klonování se nepodařilo úspěšně dokončit kvůli problémům s připojením. Ujistěte se, že nedochází k žádným problémům s připojením.</li><li>Dosáhli jste limitu dostupného úložiště. Pokud chcete uvolnit místo, zvažte odstranění všech záloh, které už nepotřebujete.</li></ul>Vyřešte problémy, vymažte výstrahu a zkuste operaci zopakovat. |

### <a name="networking-alerts"></a>Výstrahy sítě

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |Adresa URL, která se používá k ověření, není dostupná. Ujistěte se, že pravidla brány firewall obsahují vzory adres URL určené pro zařízení StorSimple. Další informace o vzorech adres URL v Azure Portal najdete v článku [požadavky na síť virtuálních polí StorSimple](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Dochází k neočekávaným zpožděním při přenosu dat. |Pomalý přenos dat. |K chybám omezování dochází, když překročíte cíle škálovatelnosti služby úložiště. Služba úložiště to dělá tak, aby se zajistilo, že žádný jednotlivý klient ani tenant nemůže službu používat na náklady ostatních. Další informace o řešení potíží s účtem služby Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Na <*název zařízení* se spouští místní rezervované místo na disku \> . |Nízká doba odezvy. |10% celkové zřízené velikosti pro <*název zařízení* \> je rezervované na místním zařízení a na rezervované místo teď dochází. Zatížení <ho *zařízení* \> vygeneruje vyšší míru četnosti změn nebo jste mohli nedávno migrovat velké množství dat. To může vést ke snížení výkonu. K vyřešení tohoto problému zvažte jednu z následujících akcí:<ul><li>Zvyšte šířku pásma cloudu na toto zařízení.</li><li>Snižte nebo přesuňte úlohy na jiný svazek nebo sdílenou složku.</li></ul> |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text výstrahy | Událost | Další informace/doporučené akce |
|:--- |:--- |:--- |
| Heslo pro <*název zařízení* \> vyprší za <*počet* \> dnů. |Upozornění na heslo |Platnost vašeho hesla vyprší za <*počet* \> dnů. Zvažte změnu hesla. Další informace najdete [v části Změna hesla správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Další kroky

* [Přečtěte si informace o virtuálním poli StorSimple](storsimple-ova-overview.md).