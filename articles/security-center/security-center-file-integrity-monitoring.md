---
title: Ve službě Azure Security Center monitorování Integrity souborů | Dokumentace Microsoftu
description: " Zjistěte, jak povolit monitorování Integrity souborů ve službě Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: b7e2a075df7d0d7f6bb68163414fab525aad25a8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55093440"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Ve službě Azure Security Center monitorování Integrity souborů
Zjistěte, jak nakonfigurovat monitorování Integrity souborů (FIM) ve službě Azure Security Center pomocí tohoto názorného postupu.

## <a name="what-is-fim-in-security-center"></a>Co je FIM ve službě Security Center?
Soubor Integrity monitorování (FIM), také známé jako sledování změny prozkoumá souborů a registrů operačního systému, aplikačního softwaru a jiné změny, které můžou signalizovat útok. Metoda porovnání slouží k určení, zda je aktuální stav souboru liší od posledního skenování souboru. Můžete využít Toto porovnání k určení, pokud platné, nebo škodlivé změny byly provedeny na souborech.

Monitorování Integrity souborů Security Center ověřuje integritu souborů Windows, Windows registru a soubory Linuxu. Můžete vybrat soubory, které chcete monitorované povolením FIM. Security Center monitoruje soubory s FIM povolena pro aktivity, jako:

- Vytvoření souboru a registru a odstranění
- Úpravy souborů (změny velikosti souboru, seznamy řízení přístupu a hodnotu hash obsahu)
- Registr (změny velikosti, seznamy řízení přístupu, typ a obsah)

Security Center doporučuje pro monitorování, entit, které lze snadno povolit FIM. Můžete také definovat vlastní zásady FIM nebo entity k monitorování. Tento návod ukazuje, jak.

> [!NOTE]
> Funkce monitorování Integrity souborů (FIM) se dá použít pro virtuální počítače a počítače s Windows a Linuxem a je k dispozici na na úrovni Standard služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
FIM nahrává data do pracovního prostoru Log Analytics. Poplatky za data použít, na základě objemu dat, které nahrajete. Zobrazit [přehledu cen Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) Další informace.
>
>

> [!NOTE]
> FIM pomocí řešení Azure Change Tracking sleduje a identifikuje změny ve vašem prostředí. Pokud je zapnuto monitorování Integrity souborů, máte **řešení Change Tracking** prostředek typu řešení. Pokud odeberete **řešení Change Tracking** prostředků, zakažte monitorování funkcí ve službě Security Center Integrity souborů.
>
>

## <a name="which-files-should-i-monitor"></a>Sledování souborů
Byste uvažovat o soubory, které jsou důležité pro váš systém a aplikace, při výběru souborů, které chcete monitorovat. Zvažte volbu soubory, které jste Neočekáváme, že chcete-li změnit bez plánování. Když zvolíte soubory, které jsou často změněny aplikace nebo operačního systému (například soubory protokolů a textové soubory) vytvořte spoustu šumu, které se obtížně identifikovat útok.

Security Center doporučuje, které soubory, které jste třeba sledovat jako výchozí podle známým vzorcům útoku, které zahrnují změny souboru a registru.

## <a name="using-file-integrity-monitoring"></a>Pomocí monitorování Integrity souborů
1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **Pokročilá obrana cloudu**vyberte **monitorování Integrity souborů**.
![Řídicí panel Security Center][1]

**Monitorování Integrity souborů** otevře.
  ![Řídicí panel Security Center][2]

Za každý pracovní prostor je k dispozici následující informace:

- Celkový počet změn, které nastaly během posledního týdne (uvidíte pomlčku "-" Pokud FIM není povolené v pracovním prostoru)
- Celkový počet počítačů a virtuálních počítačů odesílajících sestavy do pracovního prostoru
- Geografické umístění pracovního prostoru
- Předplatné Azure, který pracovní prostor je v části

Pro pracovní prostor může také zobrazit následující tlačítka:

- ![Stisknout ikonu][3] Označuje, že FIM není povolen pro pracovní prostor. Výběr pracovního prostoru umožňuje povolit FIM na všech počítačích v části pracovní prostor.
- ![Upgradujte plán. ikona][4] označuje, že pracovní prostor nebo předplatné není spuštěn v Security Center úrovně Standard. Jak používat funkci FIM, vaše předplatné musí běžet Standard.  Vyberte pracovní prostor můžete upgradovat na Standard. Další informace o na úrovni Standard a způsobech upgradu najdete v tématu [upgradovat na Security Center úrovně Standard pro zvýšení zabezpečení](security-center-pricing.md).
- Prázdnou hodnotu (není tlačítko) znamená, že v pracovním prostoru je už povolená FIM.

V části **monitorování Integrity souborů**, můžete vybrat pracovní prostor k povolení FIM pro tento pracovní prostor, zobrazte si řídicí panel monitorování Integrity souborů pro tento pracovní prostor, nebo [upgradovat](security-center-pricing.md) pracovní prostor na úroveň Standard.

## <a name="enable-fim"></a>Povolit FIM
Pokud chcete povolit FIM na pracovní prostor:

1. V části **monitorování Integrity souborů**, vyberte pracovní prostor se **povolit** tlačítko.
2. **Povolit monitorování integrity souborů** otevře zobrazení počtu počítače s Windows a Linuxem v pracovním prostoru.

   ![Povolit monitorování integrity souborů][5]

   Doporučené nastavení pro Windows a Linux jsou také uvedeny.  Rozbalte **soubory Windows**, **registru**, a **soubory Linuxu** zobrazíte úplný seznam doporučených položek.

3. Zrušte zaškrtnutí políčka žádné doporučené entity, které nechcete použít produktu FIM.
4. Vyberte **použít monitorování integrity souborů** umožňující FIM.

> [!NOTE]
> Nastavení můžete změnit kdykoli. Zobrazit [úpravy monitorované entity](security-center-file-integrity-monitoring.md#edit-monitored-items) a zjistěte více.
>
>

## <a name="view-the-fim-dashboard"></a>Zobrazení řídicího panelu FIM
**Monitorování integrity souborů** řídicí panel zobrazuje pracovní prostory, kde je povolena FIM. Po povolení FIM na pracovní prostor nebo vyberte pracovní prostor v, otevře se řídicí panel FIM **monitorování Integrity souborů** okno, které už má FIM povolena.

![Řídicí panel monitorování Integrity souborů][6]

Řídicí panel FIM pro pracovní prostor zobrazí následující informace:

- Celkový počet počítačů připojený k pracovnímu prostoru
- Celkový počet změn, ke kterým došlo během vybraného časového období
- Přehled změnit typ (soubory, registr)
- Přehled změnit kategorii (upravit, přidat, odebrat)

Výběr filtru v horní části řídicího panelu, vám umožní použít časový interval, který chcete zobrazit změny provedené u.

![Období filtr času][7]

**Počítače** karta (popsaný výš) obsahuje seznam všech počítačů odesílajících sestavy do tohoto pracovního prostoru. Pro každý počítač jsou uvedeny řídicí panel:

- Celkový počet změn, ke kterým došlo během vybraného časového období
- Rozpis celkové změny jako soubor změny nebo změny v registru

**Prohledávání protokolů** otevře při zadání názvu počítače do vyhledávacího pole nebo vyberte počítač, který je uvedený na kartě počítače. Hledání v protokolu se zobrazí všechny změny provedené během vybraného časového období pro počítač. Můžete rozbalit změnu pro další informace.

![Prohledávání protokolů][8]

**Změny** karta (viz následující obrázek) obsahuje všechny změny pracovního prostoru během vybraného časového období. Pro každou entitu, která byla změněna, seznamy řídicích panelů:

- Počítač, který kdy ke změně došlo v
- Typ změny (registru a souboru)
- Kategorie změn (upravit, přidat, odebrat)
- Datum a čas změny

![Změny pracovního prostoru][9]

**Podrobnosti o změně** otevře při zadání změnu do vyhledávacího pole nebo vyberte entitu v části **změny** kartu.

![Podrobnosti o změně][10]

## <a name="edit-monitored-entities"></a>Upravit monitorované entity

1. Vraťte se **řídicí panel monitorování Integrity souborů** a vyberte **nastavení**.

  ![Nastavení][11]

  **Konfigurace pracovního prostoru** otevře, zobrazuje tři karty: **Registr Windows**, **soubory Windows**, a **soubory Linuxu**. Každá karta obsahuje entity, které můžete upravit v dané kategorii. Pro každou entitu uvedené, Security Center identifikuje FIM, je-li povolena (pravda) nebo nejsou povolené (false).  Úpravy entit umožňuje povolit nebo zakázat FIM.

  ![Konfigurace pracovního prostoru][12]

2. Vyberte identityprotection. V tomto příkladu jsme vybrali položku v registru Windows. **Upravit pro řešení Change Tracking** otevře.

  ![Upravit nebo sledování změn][13]

V části **upravit pro řešení Change Tracking** můžete:

- Povolit (True) nebo zakázat monitorování integrity souborů (False)
- Zadejte nebo změňte název entity
- Zadejte nebo změňte hodnotu nebo cesta
- Odstranit entitu, zahodit změny nebo uložit změny

## <a name="add-a-new-entity-to-monitor"></a>Přidat novou entitu k monitorování
1. Vraťte se **řídicí panel monitorování integrity souborů** a vyberte **nastavení** v horní části. **Konfigurace pracovního prostoru** otevře.
2. V části **konfigurace pracovního prostoru**, vyberte na kartě pro typ entity, které chcete přidat: Registru Windows, Windows soubory nebo soubory Linuxu. V tomto příkladu jsme vybrali **soubory Linuxu**.

  ![Přidat novou položku k monitorování][14]

3. Vyberte **Přidat**. **Přidat pro řešení Change Tracking** otevře.

  ![Zadejte požadované informace][15]

4. Na **přidat** stránky, zadejte požadované informace a vyberte **Uložit**.

## <a name="disable-monitored-entities"></a>Zakázat monitorované entity
1. Vraťte se **monitorování Integrity souborů** řídicího panelu.
2. Vyberte pracovní prostor, ve kterém je aktuálně povoleno FIM. Pracovní prostor je povolený pro FIM, pokud chybí tlačítko Povolit nebo upgradujte plán.

  ![Vyberte pracovní prostor, kde je povolena FIM][16]

3. V části monitorování Integrity souborů vyberte **nastavení**.

  ![Vyberte nastavení][17]

4. V části **konfigurace pracovního prostoru**, vyberte skupinu kde **povoleno** je nastavena na hodnotu true.

  ![Konfigurace pracovního prostoru][18]

5. V části **upravit pro řešení Change Tracking** okno sady **povoleno** na hodnotu False.

  ![Sada povoleno na hodnotu false][19]

6. Vyberte **Uložit**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Složky a cesta k monitorování použití zástupných znaků

Použijte zástupné znaky zjednodušit sledování mezi adresáře. Při konfiguraci monitorování složky pomocí zástupných znaků, platí následující pravidla:
-   Zástupné znaky jsou požadovány pro sledování více souborů.
-   Zástupné znaky lze použít pouze v posledním segmentu cesty, jako je například C:\folder\file nebo /etc/*.conf
-   Pokud proměnná prostředí obsahuje cestu, která není platná, ověření proběhne úspěšně, ale cesta se nezdaří spuštění inventáře.
-   Při nastavování cestu, vyhněte se obecné cesty, jako je například c:\*. * se budou účtovat příliš mnoho složek se procházet.

## <a name="disable-fim"></a>Zakázat FIM
Můžete zakázat FIM. FIM pomocí řešení Azure Change Tracking sleduje a identifikuje změny ve vašem prostředí. Tím, že zakážete FIM, odeberete řešení Change Tracking z vybraného pracovního prostoru.

1. Chcete-li zakázat FIM, vraťte se na **monitorování Integrity souborů** řídicího panelu.
2. Vyberte pracovní prostor.
3. V části **monitorování Integrity souborů**vyberte **zakázat**.

  ![Zakázat FIM][20]

4. Vyberte **odebrat** zakázat.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, chcete-li používat monitorování Integrity souborů (FIM) ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení](tutorial-security-policy.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení](security-center-monitoring.md)– zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md)– zjistěte, jak spravovat a reagovat na výstrahy zabezpečení.
* [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
* [Security Center – nejčastější dotazy](security-center-faq.md)– přečtěte si nejčastější dotazy k používání této služby.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[2]: ./media/security-center-file-integrity-monitoring/file-integrity-monitoring.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[6]: ./media/security-center-file-integrity-monitoring/fim-dashboard.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
