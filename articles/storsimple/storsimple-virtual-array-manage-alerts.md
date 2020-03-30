---
title: Zobrazení a správa výstrah pro virtuální pole StorSimple
description: Popisuje podmínky výstrahy a závažnost virtuálního pole StorSimple a způsob použití služby StorSimple Manager ke správě výstrah.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 97ee25a1-0ec3-4883-9a0a-54b722598462
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/12/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7c469be4a1d8aba23857b1ba52ee829c126a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267427"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Správa výstrah pro virtuální pole StorSimple pomocí Správce zařízení StorSimple

## <a name="overview"></a>Přehled

Funkce výstrah ve službě StorSimple Device Manager poskytuje způsob, jak zkontrolovat a vymazat výstrahy související s virtuálními poli StorSimple v reálném čase. Výstrahy v okně **souhrnu služby** můžete centrálně sledovat problémy se stavem vašich virtuálních polí StorSimple a celkového řešení Microsoft Azure StorSimple.

Tento kurz popisuje, jak konfigurovat upozornění, běžné podmínky výstrah, úrovně závažnosti výstrah a jak zobrazit a sledovat výstrahy. Kromě toho obsahuje tabulky rychlých odkazů výstrah, které umožňují rychle vyhledat konkrétní výstrahu a odpovídajícím způsobem reagovat.

![Stránka Upozornění](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete si vybrat, zda chcete být informováni e-mailem o podmínkách výstrahy pro každý z vašich virtuálních polí StorSimple. Kromě toho můžete identifikovat další příjemce oznámení upozornění zadáním jejich e-mailové adresy do pole **Další příjemci e-mailu,** oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailových adres na virtuální pole.

Po povolení e-mailové oznámení pro virtuální pole, členové seznamu oznámení obdrží e-mailovou zprávu pokaždé, když dojde k kritické výstrahy. Zprávy budou odeslány z *storsimple-alerts-noreply\@mail.windowsazure.com* a bude popisovat stav výstrahy. Příjemci se můžou klepnutím na **odhlásit odebrat** ze seznamu e-mailových oznámení.

#### <a name="to-enable-email-notification-for-alerts"></a>Povolení e-mailových oznámení pro výstrahy

1. Přejděte na službu Správce zařízení StorSimple a v části **Správa** vyberte a klepněte na **položku Zařízení**. Ze zobrazeného seznamu zařízení vyberte a klikněte na zařízení.
   
    ![nastavení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Tím se otevře okno **Nastavení.** V části **Nastavení zařízení** vyberte **Obecné**. Tím se otevře okno **Obecné nastavení.**
   
    ![konfigurace upozornění](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. V okně **Obecné nastavení** přejděte do části **Nastavení výstrah** a nastavte následující:
   
   1. V poli **Povolit e-mailové oznámení** vyberte **ANO**.
   2. V poli **Správci e-mailových služeb** vyberte **ANO,** pokud chcete, aby správce služby a všichni spolusprávci obdrželi upozornění.
   3. Do pole **Další příjemci e-mailu** zadejte e-mailové adresy všech ostatních příjemců, kteří by měli dostávat upozornění. Zadejte jména ve *formátu, který někdo\@somewhere.com*. K oddělení e-mailových adres použijte středníky. Na virtuální zařízení můžete nakonfigurovat maximálně 20 e-mailových adres.
      
       ![konfigurace upozornění](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Chcete-li odeslat oznámení o testovacím e-mailu, klepněte na tlačítko **Odeslat testovací e-mail**. Služba StorSimple Device Manager zobrazí stavové zprávy, když předá oznámení testu.
      
       ![E-mail s upozorněními testuje oznámení odeslané](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Pokud testovací oznámení nelze odeslat, služba Správce zařízení StorSimple zobrazí příslušnou zprávu. Klepněte na tlačítko **OK**, počkejte několik minut a zkuste znovu odeslat zprávu o testovacím oznámení.
      >
      >
   5. V dolní části stránky klikněte na **Uložit** a uložte konfiguraci. Po zobrazení výzvy k potvrzení klikněte na **Ano**.
      
      ![E-mail s upozorněními testuje oznámení odeslané](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Běžné výstražné podmínky

Virtuální pole StorSimple generuje výstrahy v reakci na různé podmínky. Nejběžnější typy výstražných podmínek jsou následující:

* **Problémy s připojením** – K těmto výstrahám dochází, pokud je při potížích s přenosem dat. Problémy s komunikací může dojít při přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku připojení mezi virtuálními zařízeními a službou StorSimple Device Manager. Problémy s komunikací jsou některé z nejtěžších opravit, protože existuje tolik bodů selhání. Před pokračováním v pokročilejším řešení potíží byste měli vždy nejprve ověřit, zda je k dispozici připojení k síti a přístup k Internetu. Informace o portech a nastavení brány firewall naleznete v [části Požadavky na systém StorSimple Virtual Array](storsimple-ova-system-requirements.md). Nápovědu k řešení potíží naleznete [v části Poradce při potížích s rutinou Testovací připojení](storsimple-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny, když váš systém nepracuje optimálně, například když je pod velkým zatížením.

Kromě toho se mohou zobrazit výstrahy týkající se zabezpečení, aktualizací nebo selhání úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrahy

Výstrahy mají různé úrovně závažnosti v závislosti na dopadu, který bude mít situace výstrahy, a na potřebě odpovědi na výstrahu. Úrovně závažnosti jsou následující:

* **Kritické** – Tato výstraha je v reakci na podmínku, která ovlivňuje úspěšný výkon systému. Akce je nutné zajistit, že služba StorSimple není přerušena.
* **Upozornění** – Tento stav může být kritický, pokud není vyřešen. Měli byste prozkoumat situaci a přijmout veškerá opatření potřebná k odstranění problému.
* **Informace** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správě systému.

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okno souhrnu služby StorSimple Device Manager poskytuje rychlý pohled na počet výstrah na virtuálních zařízeních, uspořádaných podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klepnutím na úroveň závažnosti otevřete okno **Výstrahy.** Výsledky zahrnují pouze výstrahy, které odpovídají této úrovni závažnosti.

![Výstrahy sestavy s rozsahem na typ výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Kliknutím na výstrahu v seznamu získáte další podrobnosti o výstraze, včetně posledního oznámení, počtu výskytů výstrahy v zařízení a doporučené akce k vyřešení výstrahy.

![Seznam upozornění a podrobnosti](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Podrobnosti výstrahy můžete zkopírovat do textového souboru, pokud potřebujete odeslat informace na podporu společnosti Microsoft. Poté, co jste postupovali podle doporučení a vyřešili podmínku výstrahy v místním prostředí, měli byste vymazat výstrahu ze seznamu. Vyberte výstrahu ze seznamu a klepněte na tlačítko **Vymazat**. Chcete-li vymazat více výstrah, vyberte jednotlivé výstrahy, klikněte na libovolný sloupec kromě sloupce **Výstraha** a poté, co jste vybrali všechna upozornění, která mají být vymazána, klepněte na tlačítko **Vymazat.**

Po klepnutí na tlačítko **Vymazat**budete mít možnost poskytnout komentáře k upozornění a krokům, které jste provedli k vyřešení problému.

![komentáře upozornění](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Některé události budou vymazány systémem, pokud je spuštěna jiná událost s novými informacemi.

## <a name="sort-and-review-alerts"></a>Řazení a kontrola výstrah

Okno **Upozornění** může zobrazit až 250 výstrah. Pokud jste tento počet výstrah překročili, ne všechny výstrahy se zobrazí ve výchozím zobrazení. Můžete kombinovat následující pole a přizpůsobit tak, které výstrahy se zobrazí:

* **Stav** – Můžete zobrazit **aktivní** nebo **vymazáná** upozornění. Aktivní výstrahy jsou stále spuštěny ve vašem systému, zatímco vymazány výstrahy byly buď ručně vymazány správcem nebo programově vymazány, protože systém aktualizoval stav výstrahy novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všech úrovní závažnosti (kritické, upozornění, informace) nebo pouze určité závažnosti, například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí ze služby nebo jednoho nebo všech virtuálních zařízení.
* **Časový rozsah** – Zadáním dat **od** a **do** a časových razítek se můžete podívat na výstrahy během časového období, které vás zajímá.

## <a name="alerts-quick-reference"></a>Stručný přehled výstrah

V následujících tabulkách jsou uvedeny některé výstrahy StorSimple, se kterými se můžete setkat, a další informace a doporučení, pokud jsou k dispozici. Výstrahy StorSimple Virtual Array spadají do jedné z následujících kategorií:

* [Výstrahy připojení ke cloudu](#cloud-connectivity-alerts)
* [Upozornění na konfiguraci](#configuration-alerts)
* [Upozornění na selhání úlohy](#job-failure-alerts)
* [Upozornění na výkon](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení ke cloudu

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| > *název zařízení* <zařízení není připojen ke cloudu. |Pojmenované zařízení se nemůže připojit ke cloudu. |Nelze se připojit ke cloudu. Může to být způsobené jedním z následujících důvodů:<ul><li>Pravděpodobně došlo k potížím s nastavením sítě v zařízení.</li><li>Pravděpodobně došlo k potížím s pověřeními účtu úložiště.</li></ul>Další informace o řešení potíží s připojením najdete v [místním webovém uživatelském rozhraní](storsimple-ova-web-ui-admin.md) zařízení. |

### <a name="configuration-alerts"></a>Upozornění na konfiguraci

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Konfigurace místního virtuálního zařízení není podporována. |Pomalý výkon. |Aktuální konfigurace může mít za následek snížení výkonu. Ujistěte se, že server splňuje minimální požadavky na konfiguraci. Další informace naleznete v poli [StorSimple Virtual Array Requirements](storsimple-ova-system-requirements.md). |
| Dochází vám zřízené místo na disku <*název*\>zařízení . |Upozornění na místo na disku. |Dochází vám zřízené místo na disku. Chcete-li uvolnit místo, zvažte přesunutí úloh na jiný svazek nebo sdílení nebo odstranění dat. |

### <a name="job-failure-alerts"></a>Upozornění na selhání úlohy

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Zálohování *<názvu* \> zařízení nelze dokončit. |Selhání úlohy zálohování. |Nelze vytvořit zálohu. Zvažte jednu z následujících možností:<ul><li>Problémy s připojením mohou bránit úspěšnému dokončení operace zálohování. Ujistěte se, že neexistují žádné problémy s připojením. Další informace o řešení potíží s připojením najdete v [místním webovém uživatelském rozhraní](storsimple-ova-web-ui-admin.md) virtuálního zařízení.</li><li>Dosáhli jste dostupného limitu úložiště. Chcete-li uvolnit místo, zvažte odstranění všech záloh, které již nejsou potřeba.</li></ul> Vyřešte problémy, vymažte výstrahu a opakujte operaci. |
| Klon <*název* \> zařízení nelze dokončit. |Selhání klonování práce. |Nelze vytvořit klon. Zvažte jednu z následujících možností:<ul><li>Záložní seznam pravděpodobně není platný. Aktualizujte seznam a ověřte, zda je stále platný.</li><li>Problémy s připojením mohou bránit úspěšnému dokončení operace klonování. Ujistěte se, že neexistují žádné problémy s připojením.</li><li>Dosáhli jste dostupného limitu úložiště. Chcete-li uvolnit místo, zvažte odstranění všech záloh, které již nejsou potřeba.</li></ul>Vyřešte problémy, vymažte výstrahu a opakujte operaci. |

### <a name="networking-alerts"></a>Síťové výstrahy

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Nelze se připojit ke službě ověřování. |Chyba datové cesty |Adresa URL, která se používá k ověření, není dostupná. Ujistěte se, že pravidla brány firewall obsahují vzory adres URL určené pro zařízení StorSimple. Další informace o vzorcích adres URL na webu Azure Portal najdete v [požadavcích na sítě StorSimple Virtual Array](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Upozornění na výkon

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Dochází k neočekávaným zpožděním při přenosu dat. |Pomalý přenos dat. |Omezení chybdochází při překročení cíle škálovatelnosti služby úložiště. Služba úložiště to k zajištění, že žádný jeden klient nebo klient můžete používat službu na úkor ostatních. Další informace o řešení potíží s účtem úložiště Azure najdete v centru [webu Sledování, diagnostika a řešení potíží s úložištěm Microsoft Azure](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Dochází vám místo na místním rezervačním disku v <*název*\>zařízení . |Pomalá doba odezvy. |10 % celkové zřízené velikosti pro <*název* \> zařízení je vyhrazena na místním zařízení a nyní dochází rezervované místo. Úloha na *<název* \> zařízení generuje vyšší míru změn nebo jste nedávno migrovali velké množství dat. To může mít za následek snížení výkonu. Zvažte jednu z následujících akcí k vyřešení tohoto problému:<ul><li>Zvyšte šířku pásma cloudu na toto zařízení.</li><li>Snižte nebo přesuňte úlohy na jiný svazek nebo sdílenou složku.</li></ul> |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text výstrahy | Událost | Více informací / doporučená opatření |
|:--- |:--- |:--- |
| Heslo pro <*název* \> zařízení vyprší za <*počet dní.* \> |Upozornění na heslo. |Platnost vašeho hesla vyprší v <*dnech.* \> Zvažte změnu hesla. Další informace naleznete v části [Změna hesla správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Další kroky

* [Další informace o storsimple virtuální pole](storsimple-ova-overview.md).
