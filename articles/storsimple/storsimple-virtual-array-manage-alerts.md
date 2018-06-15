---
title: Zobrazovat a spravovat Microsoft Azure StorSimple virtuální pole výstrahy | Microsoft Docs
description: Popisuje výstrahy podmínky pole virtuální zařízení StorSimple a závažnost a jak používat službu StorSimple Manager ke správě výstrah.
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
ms.openlocfilehash: 7d4d680e3460fbeff73c2f334c6461da7967374d
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2018
ms.locfileid: "27786403"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple Spravovat výstrahy pro toto pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Výstrahy funkce ve službě StorSimple Manager zařízení poskytuje způsob, jak ke kontrole a vymazat výstrahy související s pole virtuální zařízení StorSimple na základě v reálném čase. Můžete výstrahy na **souhrn služby** okna centrálně monitorovat stav problémy vaše pole virtuální zařízení StorSimple a celkového řešení Microsoft Azure StorSimple.

Tento kurz popisuje, jak nakonfigurovat oznámení o výstrahách, obecné výstrahy podmínky, úrovně závažnosti výstrah a postup zobrazení a sledovat výstrahy. Kromě toho zahrnuje výstrahy Stručná referenční tabulky, které vám umožní rychle vyhledat konkrétní výstrahu a reagují odpovídajícím způsobem.

![stránka výstrah](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete zvolit, zda chcete oznámení e-mailové výstrahy podmínky pro všechny vaše pole virtuální zařízení StorSimple. Kromě toho můžete identifikovat ostatní příjemci oznámení výstrah zadáním jejich e-mailové adresy ve **příjemců další e-mailu** pole, oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailové adresy na virtuální pole.


Po povolení e-mailové oznámení pro virtuální pole členy v seznamu oznámení e-mailovou zprávu, dojde k kritickou výstrahu pokaždé, když obdrží. Odešle zprávy ze  *storsimple-alerts-noreply@mail.windowsazure.com*  a popíše podmínka upozornění. Můžete kliknout na příjemci **Unsubscribe** sami odebrat ze seznamu adresátů oznámení e-mailu.

#### <a name="to-enable-email-notification-for-alerts"></a>Chcete-li povolit e-mailové oznámení pro výstrahy

1. Přejděte na služby StorSimple Manager zařízení a **správy** , vyberte a klikněte na tlačítko **zařízení**. Seznam zařízení, na které se zobrazí vyberte a klikněte na zařízení.
   
    ![nastavení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Otevře **nastavení** okno. V **nastavení zařízení** vyberte **Obecné**. Otevře **obecné nastavení** okno.
   
    ![Konfigurace oznámení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. V **obecné nastavení** okno, přejděte na **výstrahy nastavení** tématu a nastavte následující:
   
   1. V **povolit e-mailové oznámení** pole, vyberte **Ano**.
   2. V **e-mailem správci služeb** pole, vyberte **Ano** Pokud chcete mít správce služeb a spolusprávci všechny přijímat oznámení výstrah.
   3. V **příjemců další e-mailu** pole, zadejte e-mailové adresy všech příjemců, kterým mají být doručena oznámení výstrah. Zadejte názvy ve formátu  *someone@somewhere.com* . K oddělení e-mailové adresy použijte středníky. Můžete nakonfigurovat maximálně 20 e-mailové adresy na virtuální zařízení.
      
       ![Konfigurace oznámení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Pokud chcete poslat testovací e-mailová oznámení, klikněte na tlačítko **odeslat zkušební e-mail**. Služby StorSimple Manager zařízení se zobrazí stavové zprávy, jak předává testovací oznámení.
      
       ![Výstrahy testování odeslaných e-mailové oznámení](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Pokud nelze odeslat testovací zprávu oznámení, služby StorSimple Manager zařízení se zobrazí příslušná zpráva. Klikněte na tlačítko **OK**, počkejte několik minut a potom se pokusíte odeslat testovací zprávu oznámení znovu.
      > 
      > 
   5. V dolní části stránky klikněte na tlačítko **Uložit** uložte konfiguraci. Po zobrazení výzvy k potvrzení klikněte na **Ano**.
      
      ![Výstrahy testování odeslaných e-mailové oznámení](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Obecné podmínky výstrah

Pole virtuální zařízení StorSimple generuje výstrahy v reakci na celou řadu podmínek. Následují nejčastější typy výstrah podmínek:

* **Problémy s připojením k** – tyto výstrahy dojít, když se potíže při přenosu dat. Problémy s komunikací může dojít během přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku připojení mezi virtuální zařízení a služby StorSimple Manager zařízení. Problémy s komunikací jsou některé nejtěžší opravit, protože nejsou k dispozici mnoho body selhání. Měli byste vždy nejprve ověřit, že než budete pokračovat k více pokročilá řešení problémů jsou k dispozici síťové připojení a přístup k Internetu. Informace o portech a nastavení brány firewall, přejděte na [požadavky na systém pole virtuální zařízení StorSimple](storsimple-ova-system-requirements.md). Nápovědu k řešení potíží, přejděte na [Poradce při potížích s rutinu Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou nastat, když systém nepracuje optimálně, například když je v případě velkého zatížení.

Kromě toho se může zobrazit upozornění týkající se zabezpečení, aktualizací nebo selhání úlohy.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrah

Výstrahy mají různé úrovně závažnosti, v závislosti na dopadu, který bude mít výstrahy situaci a není nutné pro reakci na výstrahy. Jsou úrovně závažnosti:

* **Kritické** – Tato výstraha je v reakci na podmínku, která ovlivňuje úspěšné výkon systému. Je potřeba zajistit, aby StorSimple služba není přerušena.
* **Upozornění** – tento stav může být důležité, pokud není vyřešené. Měli prozkoumat situaci a proveďte akce požadované zrušte problém.
* **Informace o** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správa systému.

## <a name="view-and-track-alerts"></a>Zobrazení a sledovat výstrahy

V okně Souhrn služby StorSimple Manager zařízení vám poskytne rychlý přehled na počet výstrah na virtuální zařízení uspořádané podle úrovně závažnosti.

![Řídicí panel výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klepnutím na tlačítko úroveň závažnosti **výstrahy** okno. Výsledky budou zahrnovat pouze upozornění, která odpovídají této úrovně závažnosti.

![Sestava výstrahy omezená na typ výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klikněte na výstrahu v seznamu zobrazíte další podrobnosti výstrahy, včetně doby poslední výstrahy byla nahlášena, počet výskytů výstrahy na zařízení a doporučené akce k vyřešení výstrahy.

![Seznam výstrah a podrobnosti](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Podrobnosti výstrahy můžete zkopírovat do textového souboru, pokud je potřeba poslat informace o Microsoft Support. Poté, co jste postupovali podle doporučení a vyřešit podmínka pro upozornění na místě, byste měli Vymazat výstrahu ze seznamu. Vyberte příslušnou výstrahu ze seznamu a pak klikněte na tlačítko **zrušte**. Zrušte více výstrah, vyberte jednotlivé výstrahy, klikněte na možnost žádný sloupec s výjimkou **výstraha** sloupec a pak klikněte na tlačítko **vymazat** po výběru všechny výstrahy vymazat.

Když kliknete na tlačítko **zrušte**, máte možnost zadat komentář výstraha a kroky, které jste si k vyřešení problému. 

![výstrahy komentáře](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Některé události budou vymazána v systému, pokud jiná událost se aktivuje se novými informacemi. 

## <a name="sort-and-review-alerts"></a>Řazení a zkontrolujte výstrahy

**Výstrahy** okně může zobrazit až 250 výstrahy. Pokud byl překročen počet výstrah, zobrazí se ve výchozím zobrazení ne všechny výstrahy. Zkombinováním k přizpůsobení, které výstrahy se zobrazují následující pole:

* **Stav** – můžete zobrazit buď **Active** nebo **nezaškrtnuto** výstrahy. Aktivní výstrahy se stále aktivují v systému, když byly buď ručně vymazat správcem nebo prostřednictvím kódu programu vymazat, protože systém aktualizovat podmínka upozornění s informací o novém nezaškrtnuté výstrahy.
* **Závažnost** – můžete zobrazit výstrahy všechny úrovně závažnosti (kritická, upozornění, informace o) nebo pouze určité závažnosti, například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit výstrahy na ty, které pocházejí z služba nebo jednoho nebo všech virtuální zařízení.
* **Čas rozsah** – zadáním **z** a **k** kalendářních dat a časových razítek, můžete se podívat na výstrahy během časové období, které vás zajímají.

## <a name="alerts-quick-reference"></a>Stručná referenční příručka výstrahy

Následující tabulka uvádí některé z StorSimple výstrahy, které se můžete setkat, a také další informace a doporučení tam, kde je k dispozici. Pole virtuální zařízení StorSimple výstrahy spadat do jednoho z následujících kategorií:

* [Výstrahy připojení cloudu](#cloud-connectivity-alerts)
* [Konfigurace výstrahy](#configuration-alerts)
* [Upozornění na selhání úlohy](#job-failure-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení cloudu

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Zařízení  *<device name>*  není připojen do cloudu. |Pojmenované zařízení se nemůže připojit ke cloudu. |Nelze se připojit ke cloudu. To může být způsobeno jedním z těchto:<ul><li>Je možné, problémy s nastavení sítě na vašem zařízení.</li><li>Pravděpodobně došlo k problému s údaje k účtu úložiště.</li></ul>Další informace o řešení potíží s připojením, přejděte na [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) zařízení. |

### <a name="configuration-alerts"></a>Konfigurace výstrahy

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Nepodporovaná konfigurace virtuálního zařízení na místě. |Nízký výkon. |Aktuální konfigurace může vést ke snížení výkonu. Zkontrolujte, zda server splňuje minimální požadavky na konfiguraci. Další informace, přejděte na [pole požadavky virtuální zařízení StorSimple](storsimple-ova-system-requirements.md). |
| Spustíte nedostatek místa na disku zřízené v <*název zařízení*>. |Upozornění místa na disku. |Jsou dostatek místa na disku zřízené. Pro uvolnění místa, zvažte přesunutí úloh na jiný svazek nebo sdílenou složku nebo odstraňovat data. |

### <a name="job-failure-alerts"></a>Upozornění na selhání úlohy

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Zálohování <*název zařízení*> nebylo možné dokončit. |Selhání úlohy zálohování. |Nelze vytvořit zálohu. Zvažte jednu z těchto možností:<ul><li>Problémy s připojením k může bránit úspěšně dokončení operace zálohování. Ujistěte se, že neexistují žádné problémy s připojením. Další informace o řešení potíží s připojením, přejděte na [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) pro virtuální zařízení.</li><li>Dosáhli jste limitu úložiště k dispozici. Pro uvolnění místa, zvažte odstranění všechny zálohy, které už nejsou potřeba.</li></ul> Vyřešte problémy, vymažte výstrahy a operaci opakujte. |
| Klonovat z <*název zařízení*> nebylo možné dokončit. |Klonování úloha se nezdařila. |Nelze vytvořit klon počítače. Zvažte jednu z těchto možností:<ul><li>Zálohování seznamu nemusí být platný. Aktualizujte seznam a ověří, zda že je stále platný.</li><li>Problémy s připojením k může bránit úspěšné dokončení operace klonování. Ujistěte se, že neexistují žádné problémy s připojením.</li><li>Dosáhli jste limitu úložiště k dispozici. Pro uvolnění místa, zvažte odstranění všechny zálohy, které už nejsou potřeba.</li></ul>Vyřešte problémy, vymažte výstrahy a operaci opakujte. |

### <a name="networking-alerts"></a>Výstrahy sítě
| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |Adresu URL, která se používá k ověření není dostupný. Ujistěte se, že vašich pravidlech brány firewall zahrnují vzory adresa URL zadaná pro zařízení StorSimple. Další informace o vzorů adresy URL na portálu Azure, přejděte na [pole virtuální zařízení StorSimple sítě požadavky](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Neočekávané zpoždění při přenosu dat se setkáváte s. |Přenos dat pomalé. |Dochází k omezení chybám, když překročíte cíle škálovatelnosti služby úložiště. Služba úložiště nemá to zajistit, že žádné jednoho klienta nebo klienta můžete používat služby za cenu ostatní. Další informace o řešení potíží s vaším účtem úložiště Azure, přejděte na [monitorování, Diagnostika a řešení Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Máte málo místní rezervace místa na disku na <*název zařízení*>. |Doba pomalé odezvy. |10 % celkové velikosti zřízené pro <*název zařízení*> je vyhrazen na místním zařízení a vy teď docházejí vyhrazené místo. Úlohy na <*název zařízení*> generuje a vyšší míra změn, nebo může mít nedávno migrace velké množství dat. Výsledkem může být snížený výkon. Jeden z následujících akcí to vyřešit vezměte v úvahu:<ul><li>Zvětšete šířku pásma cloudu tohoto zařízení.</li><li>Snižte nebo přesouvat úlohy na jiný svazek nebo sdílenou složku.</li></ul> |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Textu výstrahy | Událost | Další informace / doporučené akce |
|:--- |:--- |:--- |
| Heslo pro <*název zařízení*> do vypršení platnosti <*číslo*> dnů. |Heslo upozornění. |Heslo vyprší za < číslo < dnů. Zvažte změnu hesla. Další informace, přejděte na [změnit heslo správce zařízení StorSimple virtuální pole](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Další postup

* [Další informace o pole virtuální zařízení StorSimple](storsimple-ova-overview.md).

