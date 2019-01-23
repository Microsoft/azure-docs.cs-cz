---
title: Umožňuje zobrazit a spravovat Microsoft Azure StorSimple Virtual Array upozornění | Dokumentace Microsoftu
description: Popisuje podmínky upozornění StorSimple Virtual Array a závažnost a jak spravovat výstrahy pomocí služby StorSimple Manager.
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
ms.openlocfilehash: 0cd3f506205a3ee4f4435b16fd2185eeb20aef3c
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447220"
---
# <a name="use-storsimple-device-manager-to-manage-alerts-for-the-storsimple-virtual-array"></a>Pomocí Správce zařízení StorSimple ke správě výstrah pro StorSimple Virtual Array

## <a name="overview"></a>Přehled

Funkce výstrah ve službě Správce zařízení StorSimple poskytuje způsob, jak ke kontrole a zrušte upozornění týkající se virtuálních polí StorSimple na základě v reálném čase. Výstrahy můžete použít na **souhrn služby** okno centrálně monitorovat stav problémy vaše virtuální pole StorSimple a celkového řešení Microsoft Azure StorSimple.

Tento kurz popisuje, jak nakonfigurovat oznámení o výstrahách, běžné podmínky upozornění, úrovně závažnosti výstrah a jak zobrazit a sledovat výstrahy. Kromě toho zahrnuje upozornění Stručná referenční příručka tabulek, které vám umožní rychle najít konkrétní výstrahu a reagují odpovídajícím způsobem.

![Stránky s upozorněními](./media/storsimple-virtual-array-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Konfigurace nastavení výstrah

Můžete zvolit, jestli chcete dostat e-mailové výstrahy podmínek pro každý z vašich virtuálních polí StorSimple. Kromě toho můžete identifikovat dalších příjemců oznámení výstrah tak, že zadáte své e-mailové adresy **další e-mailové příjemce** pole, oddělené středníky.

> [!NOTE]
> Můžete zadat maximálně 20 e-mailových adres na jedno virtuální pole.

Po povolení e-mailové oznámení pro virtuální pole se v seznamu oznámení obdrží e-mailovou zprávu, vyvolá se pokaždé, když kritickou výstrahu. Pošle zprávy z *storsimple-alerts-noreply@mail.windowsazure.com* a popíše se vyskytl výstražný stav. Můžete kliknout na příjemce **Unsubscribe** sami odebrat ze seznamu e-mailové oznámení.

#### <a name="to-enable-email-notification-for-alerts"></a>Chcete-li povolit e-mailové oznámení pro výstrahy

1. Přejděte do služby Správce zařízení StorSimple a **správu** části, vyberte a klikněte na tlačítko **zařízení**. Ze seznamu zařízení zobrazí vyberte a klikněte na vašem zařízení.
   
    ![nastavení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts2.png)
2. Otevře **nastavení** okno. V **nastavení zařízení** vyberte **Obecné**. Otevře **obecné nastavení** okno.
   
    ![Konfigurace oznámení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts4.png)
3. V **obecné nastavení** okno, přejděte na **nastavení výstrah** tématu a nastavte následující:
   
   1. V **povolit e-mailové oznámení** pole, vyberte **Ano**.
   2. V **e-mailem správci služeb** pole, vyberte **Ano** Pokud chcete mít správce služeb a všichni spolusprávci dostávali oznámení výstrah.
   3. V **další e-mailové příjemce** zadejte e-mailové adresy všech příjemců, kteří mají dostávat oznámení výstrah. Zadejte názvy ve formátu *someone@somewhere.com*. K oddělení e-mailových adres použijte středníky. Nakonfigurovat můžete nanejvýš 20 e-mailových adres na jedno virtuální zařízení.
      
       ![Konfigurace oznámení výstrah](./media/storsimple-virtual-array-manage-alerts/alerts6.png)
   4. Pokud chcete poslat testovací e-mailové oznámení, klikněte na tlačítko **odeslat zkušební email**. Služba Správce zařízení StorSimple se zobrazí stavové zprávy, jak předává testovací oznámení.
      
       ![Upozornění testovací odeslání e-mailové oznámení](./media/storsimple-virtual-array-manage-alerts/alerts7.png)
      
      > [!NOTE]
      > Pokud nelze odeslat testovací oznámení, ve službě Správce zařízení StorSimple se zobrazí odpovídající zprávu. Klikněte na tlačítko **OK**, počkejte několik minut a pak zkuste znovu odeslat zprávu oznámení vašeho testu.
      >
      >
   5. V dolní části stránky klikněte na tlačítko **Uložit** uložte konfiguraci. Po zobrazení výzvy k potvrzení klikněte na **Ano**.
      
      ![Upozornění testovací odeslání e-mailové oznámení](./media/storsimple-virtual-array-manage-alerts/alerts10.png)

## <a name="common-alert-conditions"></a>Běžné podmínky upozornění

StorSimple Virtual Array generuje výstrahy v reakci na různé podmínky. Následují nejčastější typy podmínky upozornění:

* **Problémy s připojením** – tyto výstrahy zobrazují tehdy, když se potíže při přenášení dat. Problémy s komunikací může dojít při přenosu dat do a z účtu úložiště Azure nebo z důvodu nedostatku možností připojení mezi virtuální zařízení a služby Správce zařízení StorSimple. Problémy s komunikací jsou některé z těch nejtěžších opravit, protože tolik bodů selhání. Vždy nejdříve je nutné ověřit, že před pokračováním pokročilejší řešení potíží jsou k dispozici připojení k síti a přístup k Internetu. Informace o portech a nastavení brány firewall, přejděte na [požadavky systému virtuálních polí StorSimple](storsimple-ova-system-requirements.md). Pomoc při řešení potíží, přejděte na [řešení potíží pomocí rutiny Test-Connection](storsimple-troubleshoot-deployment.md).
* **Problémy s výkonem** – tyto výstrahy jsou způsobeny, když váš systém nepracuje optimálně, například pokud je v případě velkého zatížení.

Kromě toho se může zobrazit upozornění týkající se zabezpečení, aktualizace nebo selhání úloh.

## <a name="alert-severity-levels"></a>Úrovně závažnosti výstrah

Výstrahy mají různé úrovně závažnosti, v závislosti na dopadu, který bude mít výstrah situace a není nutné pro reakci na upozornění. Existují tyto úrovně závažnosti:

* **Kritické** – Tato výstraha se v reakci na určitou podmínku, která ovlivňuje úspěšné výkon vašeho systému. Akce je potřeba zajistit, aby StorSimple služba není přerušena.
* **Upozornění** – tento stav může být důležité, pokud nebyl vyřešen. By měl prozkoumat situace a provádět žádnou akci potřeba vymazat problém.
* **Informace o** – Tato výstraha obsahuje informace, které mohou být užitečné při sledování a správa systému.

## <a name="view-and-track-alerts"></a>Zobrazení a sledování výstrah

Okně s přehledem služby Správce zařízení StorSimple poskytuje rychlý přehled počet oznámení na vaše virtuální zařízení, uspořádané podle úrovně závažnosti.

![Řídicí panel výstrah](./media/storsimple-virtual-array-manage-alerts/alerts14.png)

Klepnutím na tlačítko úroveň závažnosti **výstrahy** okno. Budou výsledky obsahovat pouze výstrahy, které odpovídají této úrovni závažnosti.

![Sestava výstrah omezená na typ výstrahy](./media/storsimple-virtual-array-manage-alerts/alerts15.png)

Klikněte na výstrahu v seznamu zobrazíte další podrobnosti k výstraze, včetně posledního upozornění byla nahlášena, počet výskytů prvku oznámení na zařízení a doporučenou akci pro tuto výstrahu vyřešíte tak.

![Seznam výstrah a podrobností](./media/storsimple-virtual-array-manage-alerts/alerts16.png)

Do textového souboru můžete zkopírovat podrobnosti výstrahy, pokud je potřeba poslat informace o Microsoft Support. Až budete mít postupovali podle doporučení a vyskytl výstražný stav v místním, byste měli vymazat výstrahy ze seznamu. Vyberte příslušnou výstrahu ze seznamu a potom klikněte na tlačítko **vymazat**. Pokud chcete vymazat více výstrah, vyberte jednotlivé výstrahy, klikněte na všechny sloupce s výjimkou **výstraha** sloupec a pak klikněte na tlačítko **vymazat** po výběru všech výstrah vymazání.

Po kliknutí na **vymazat**, budete mít příležitost k poskytování poznámky o upozornění a kroky, které jste provedli v zájmu vyřešení problému.

![oznámení komentářů](./media/storsimple-virtual-array-manage-alerts/alerts17.png)

Některé události bude vymazáno systém, pokud jiné událost se aktivuje pomocí nové informace.

## <a name="sort-and-review-alerts"></a>Řazení a kontrola upozornění

**Výstrahy** okně můžete zobrazit až 250 výstrahy. Pokud byl překročen počet výstrah, ne všechny výstrahy, zobrazí se ve výchozím zobrazení. Můžete kombinovat přizpůsobení, které výstrahy se zobrazují následující pole:

* **Stav** – můžete zobrazit buď **aktivní** nebo **nezaškrtnuto** výstrahy. Aktivní výstrahy se stále aktivují ve vašem systému, zatímco nezaškrtnuté výstrahy byla buď ručně zrušena správcem nebo programově vymazat, protože systém se vyskytl výstražný stav aktualizuje novými informacemi.
* **Závažnost** – můžete zobrazit výstrahy všechny úrovně závažnosti (kritická, upozornění, informace o), nebo jenom určité závažnosti, jako je například pouze kritické výstrahy.
* **Zdroj** – můžete zobrazit výstrahy ze všech zdrojů nebo omezit oznámení na ty, které pocházejí z jednoho nebo všech virtuálních zařízení nebo službu.
* **Časový rozsah** – zadáním **z** a **k** data a časová razítka, můžete se podívat na výstrahy během časového období, které vás zajímají.

## <a name="alerts-quick-reference"></a>Stručná referenční příručka výstrahy

V následujících tabulkách jsou uvedeny některé výstrahy StorSimple, které může dojít, a také další informace a doporučení tam, kde je k dispozici. StorSimple Virtual Array výstrahy spadají do jedné z následujících kategorií:

* [Výstrahy připojení cloudu](#cloud-connectivity-alerts)
* [Konfigurace výstrah](#configuration-alerts)
* [Výstrahy na selhání úloh](#job-failure-alerts)
* [Výstrahy výkonu](#performance-alerts)
* [Výstrahy zabezpečení](#security-alerts)

### <a name="cloud-connectivity-alerts"></a>Výstrahy připojení cloudu

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Zařízení <*název zařízení*> není připojená ke cloudu. |Pojmenované zařízení lze připojit ke cloudu. |Nepovedlo se připojit ke cloudu. Může to být způsobené jedním z následujících důvodů:<ul><li>Může se jednat problém pomocí nastavení sítě na vašem zařízení.</li><li>Může se jednat problém s přihlašovacími údaji účtu úložiště.</li></ul>Další informace o řešení problémů s připojením, přejděte [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) zařízení. |

### <a name="configuration-alerts"></a>Konfigurace výstrah

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Nepodporovaná konfigurace virtuálního zařízení místní. |Nízký výkon. |Aktuální konfigurace může způsobit snížení výkonu. Zajistěte, aby server splňoval minimální konfigurační požadavky. Další informace najdete v části [StorSimple virtuální pole – požadavky](storsimple-ova-system-requirements.md). |
| Vám dochází zřízené místo na disku <*název zařízení*\>. |Upozornění místa na disku. |Vám dochází zřízené místo na disku. Uvolněte místo tak, zvažte přesun úloh do jiného svazku nebo sdílené složky nebo odstraňovat data. |

### <a name="job-failure-alerts"></a>Výstrahy na selhání úloh

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Zálohování <*název zařízení* \> nebylo možné dokončit. |Selhání úlohy zálohování. |Nejde vytvořit zálohu. Zvažte jednu z následujících akcí:<ul><li>Problémy s připojením může bránit operaci zálohování nejde úspěšně dokončit. Ujistěte se, že neexistují žádné problémy s připojením. Další informace o řešení problémů s připojením, přejděte [místního webového uživatelského rozhraní](storsimple-ova-web-ui-admin.md) pro vaše virtuální zařízení.</li><li>Jste dosáhli limitu úložiště k dispozici. Uvolněte místo tak, zvažte odstranění všechny zálohy, které už nejsou potřeba.</li></ul> Vyřešte problémy, vymažte upozornění a zkuste operaci zopakovat. |
| Klon <*název zařízení* \> nebylo možné dokončit. |Naklonujte nezdaření úlohy. |Nepovedlo se vytvořit klon. Zvažte jednu z následujících akcí:<ul><li>Zálohování seznamu nemusí být platný. Aktualizujte seznam k ověření, že je stále platný.</li><li>Problémy s připojením může bránit úspěšné dokončení operace klonování. Ujistěte se, že neexistují žádné problémy s připojením.</li><li>Jste dosáhli limitu úložiště k dispozici. Uvolněte místo tak, zvažte odstranění všechny zálohy, které už nejsou potřeba.</li></ul>Vyřešte problémy, vymažte upozornění a zkuste operaci zopakovat. |

### <a name="networking-alerts"></a>Výstrahy sítě
| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Nelze se připojit k ověřovací službě. |Chyba DataPath |Adresa URL, která se používá k ověření není dostupný. Ujistěte se, že pravidla brány firewall zahrnout vzory adres URL zadaná pro zařízení StorSimple. Další informace o vzorech adres URL na webu Azure portal, přejděte na [StorSimple Virtual Array požadavky na síť](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).|

### <a name="performance-alerts"></a>Výstrahy výkonu

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Dochází k neočekávaným zpožděním při přenosu dat. |Přenos dat pomalé. |Po překročení cíle škálovatelnosti služby úložiště, dojde k chybám omezování. Služba storage to dělá proto ujistěte se, že žádné jednoho klienta nebo tenanta použít služby za cenu ostatní. Další informace o řešení potíží s svůj účet úložiště Azure, přejděte na [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md). |
| Dochází vám místní rezervace místa na disku ve <*název zařízení*\>. |Pomalá odezva čas. |10 % celkové zřízené velikosti pro <*název zařízení* \> je vyhrazen na místním zařízení a vy teď docházejí vyhrazené místo. Úlohy na <*název zařízení* \> generuje a vyšší míra četností změn dat nebo vám může mít nedávno provedla migraci velkých objemů dat. To může mít za následek snížený výkon. Zvažte jednu z následujících akcí, chcete-li tento problém vyřešit:<ul><li>Zvětšete šířku pásma cloudu do tohoto zařízení.</li><li>Omezit nebo přesunout úlohy na jiný svazek nebo sdílenou složku.</li></ul> |

### <a name="security-alerts"></a>Výstrahy zabezpečení

| Text upozornění | Událost | Další informace o / doporučené akce |
|:--- |:--- |:--- |
| Heslo pro <*název zařízení* \> vyprší za <*číslo* \> dnů. |Upozornění heslo. |Vaše heslo vyprší za <*číslo* \> dnů. Zvažte možnost změnit své heslo. Další informace najdete v části [změnit heslo správce zařízení StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md). |

## <a name="next-steps"></a>Další postup

* [Další informace o StorSimple Virtual Array](storsimple-ova-overview.md).
