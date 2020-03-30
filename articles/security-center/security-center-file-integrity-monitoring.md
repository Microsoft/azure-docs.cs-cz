---
title: Monitorování integrity souborů v Centru zabezpečení Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí tohoto návodu nakonfigurovat monitorování integrity souborů (FIM) v Centru zabezpečení Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2019
ms.author: memildin
ms.openlocfilehash: 4d65ca8d97e1cca81886259d4f15cc880e45be9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604290"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitorování integrity souborů ve službě Azure Security Center
Zjistěte, jak pomocí tohoto návodu nakonfigurovat monitorování integrity souborů (FIM) v Centru zabezpečení Azure.

## <a name="what-is-fim-in-security-center"></a>Co je FIM v centru zabezpečení?
Monitorování integrity souborů (FIM), známé také jako sledování změn, zkoumá soubory a registry operačního systému, aplikačního softwaru a dalších změn, které by mohly naznačovat útok. Metoda porovnání se používá k určení, zda aktuální stav souboru se liší od posledního prohledávání souboru. Toto porovnání můžete využít k určení, zda byly provedeny platné nebo podezřelé změny souborů.

Monitorování integrity souborů centra zabezpečení ověřuje integritu souborů systému Windows, registru systému Windows a souborů Linuxu. Povolíte příkazem FIM soubory, které chcete sledovat. Security Center monitoruje soubory s povolenou funkcí FIM pro aktivitu, jako jsou:

- Vytvoření a odebrání souboru a registru
- Úpravy souborů (změny velikosti souboru, seznamy řízení přístupu a hash obsahu)
- Změny registru (změny velikosti, seznamy řízení přístupu, typ a obsah)

Security Center doporučuje entity ke sledování, které můžete snadno povolit FIM. Můžete také definovat vlastní zásady FIM nebo entity ke sledování. Tento návod ukazuje, jak na to.

> [!NOTE]
> Funkce Monitorování integrity souborů (FIM) funguje pro počítače se systémem Windows a Linux a virtuální počítače a je k dispozici na úrovni Standard security center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md). FIM nahrává data do pracovního prostoru Log Analytics. Poplatky za data se účtují na základě množství nahraných dat. Další informace najdete v [tématu Ceny analýzy protokolů.](https://azure.microsoft.com/pricing/details/log-analytics/)

FIM používá řešení Azure Change Tracking ke sledování a identifikaci změn ve vašem prostředí. Pokud je povoleno monitorování integrity souborů, máte prostředek **sledování změn** typu **Řešení**. Podrobnosti o četnosti shromažďování dat najdete v [tématu Podrobnosti o shromažďování dat sledování změn](https://docs.microsoft.com/azure/automation/automation-change-tracking#change-tracking-data-collection-details) pro azure sledování změn.

> [!NOTE]
> Pokud odeberete prostředek **sledování změn,** zakážete také funkci Sledování integrity souborů v Centru zabezpečení.

## <a name="which-files-should-i-monitor"></a>Které soubory mám sledovat?
Měli byste přemýšlet o souborech, které jsou důležité pro váš systém a aplikace při výběru souborů, které chcete sledovat. Zvažte výběr souborů, které neočekáváte, že změníte bez plánování. Výběr souborů, které jsou často měněny aplikacemi nebo operačním systémem (například soubory protokolu a textové soubory), vytváří velké množství šumu, což ztěžuje identifikaci útoku.

Security Center doporučuje, které soubory byste měli sledovat jako výchozí podle známých vzorů útoku, které zahrnují změny souborů a registru.

## <a name="using-file-integrity-monitoring"></a>Použití monitorování integrity souborů
1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **Rozšířená ochrana cloudu**vyberte **možnost Sledování integrity souborů**.
![Řídicí panel Security Center][1]

**Otevře se monitorování integrity souborů.**
  ![Řídicí panel Security Center][2]

Pro každý pracovní prostor jsou uvedeny následující informace:

- Celkový počet změn, ke kterým došlo v posledním týdnu (může se zobrazit pomlčka "-", pokud FIM není povolena v pracovním prostoru)
- Celkový počet počítačů a virtuálních počítačů vykazování do pracovního prostoru
- Geografická poloha pracovního prostoru
- Předplatné Azure, pod kterým je pracovní prostor

Pro pracovní plochu mohou být zobrazena také následující tlačítka:

- ![Ikona Povolit][3] Označuje, že FIM není povolena pro pracovní prostor. Výběr pracovního prostoru umožňuje povolit FIM na všech počítačích v pracovním prostoru.
- ![Ikona][4] plánu upgradu Označuje, že pracovní prostor nebo předplatné není spuštěno pod úrovní Standard Centra zabezpečení. Chcete-li používat funkci FIM, vaše předplatné musí být spuštěno Standard.  Výběr pracovního prostoru umožňuje upgradovat na standardní. Další informace o úrovni Standard a o tom, jak upgradovat, najdete [v tématu Upgrade na úroveň Standard centra zabezpečení, kde najdete lepší zabezpečení](security-center-pricing.md).
- Prázdné (není žádné tlačítko) znamená, že FIM je již povolena v pracovním prostoru.

V části **Sledování integrity souborů**můžete vybrat pracovní prostor, který povolí fim pro tento pracovní prostor, zobrazí řídicí panel Sledování integrity souborů pro tento pracovní prostor nebo [inovuje](security-center-pricing.md) pracovní prostor na standardní.

## <a name="enable-fim"></a>Povolit FIM
Povolení fim v pracovním prostoru:

1. V části **Sledování integrity souborů**vyberte pracovní prostor pomocí tlačítka **Povolit.**
2. **Povolit monitorování integrity souborů** otevře zobrazení počtu počítačů se systémem Windows a Linux v pracovním prostoru.

   ![Povolení monitorování integrity souborů][5]

   Doporučená nastavení pro Windows a Linux jsou také uvedeny.  Rozbalte **soubory systému Windows**, **Registr**a **Linux** a zobcujete úplný seznam doporučených položek.

3. Odškrtejte všechny doporučené entity, na které nechcete použít FIM.
4. Chcete-li povolit funkci FIM, vyberte **použít monitorování integrity souboru.**

> [!NOTE]
> Nastavení můžete kdykoli změnit. Další informace najdete v tématu Úpravy sledovaných entit níže.
>
>

## <a name="view-the-fim-dashboard"></a>Zobrazení řídicího panelu FIM
Řídicí panel **monitorování integrity souborů** se zobrazí pro pracovní prostory, kde je povolena FIM. Řídicí panel FIM se otevře po povolení FIM v pracovním prostoru nebo po výběru pracovního prostoru v okně **Sledování integrity integrity souborů,** který již má povolenou funkci FIM.

![Řídicí panel monitorování integrity souborů][6]

Řídicí panel FIM pro pracovní prostor zobrazuje následující podrobnosti:

- Celkový počet počítačů připojených k pracovnímu prostoru
- Celkový počet změn, ke kterým došlo během vybraného časového období
- Rozpis typu změny (soubory, registr)
- Rozdělení kategorie změn (změněno, přidáno, odebráno)

Výběrfiltru v horní části řídicího panelu umožňuje použít časové období, pro které chcete zobrazit změny.

![Filtr časového období][7]

Karta **Počítače** (viz výše) obsahuje seznam všech počítačů, které se hlásí do tohoto pracovního prostoru. Pro každý stroj je na řídicím panelu uvedeno:

- Celkové změny, ke kterým došlo během vybraného časového období
- Rozpis celkových změn při změnách souborů nebo změn registru

**Hledání protokolu** se otevře, když do vyhledávacího pole zadáte název počítače nebo vyberete počítač uvedený na kartě Počítače. Změnu můžete rozbalit pro další informace.

![Prohledávání protokolů][8]

Na kartě **Změny** (viz níže) jsou uvedeny všechny změny pracovního prostoru během vybraného časového období. Pro každou entitu, která byla změněna, řídicí panel uvádí:

- Počítač, na kterých došlo ke změně
- Typ změny (registr nebo soubor)
- Kategorie změn (změněná, přidaná, odstraněná)
- Datum a čas změny

![Změny pracovního prostoru][9]

**Změna podrobností** se otevře, když zadáte změnu ve vyhledávacím poli nebo vyberete entitu uvedenou na kartě **Změny.**

![Změna podrobností][10]

## <a name="edit-monitored-entities"></a>Úprava sledovaných entit

1. Vraťte se na **řídicí panel Sledování integrity souborů** a vyberte **Nastavení**.

   ![Nastavení][11]

   Otevře se **konfigurace pracovního prostoru** se třemi kartami: **Registr systému Windows**, Soubory **systému Windows**a Soubory **Linuxu**. Na každé kartě jsou uvedeny entity, které můžete v dané kategorii upravovat. Pro každou uvedenou entitu Centrum zabezpečení identifikuje, zda je FIM povoleno (true) nebo není povoleno (false).  Úprava entity umožňuje povolit nebo zakázat FIM.

   ![Konfigurace pracovního prostoru][12]

2. Vyberte ochranu identity. V tomto příkladu jsme vybrali položku v registru systému Windows. **Otevře se možnost Upravit pro sledování změn.**

   ![Úprava nebo změna sledování][13]

V části **Upravit pro sledování změn** můžete:

- Povolit (True) nebo zakázat (False) sledování integrity souborů
- Poskytněte nebo změňte název entity
- Poskytněte nebo změňte hodnotu nebo cestu
- Odstranit entitu, zahodit změnu nebo uložit změnu

## <a name="add-a-new-entity-to-monitor"></a>Přidání nové entity ke sledování
1. Vraťte se na **řídicí panel monitorování integrity souborů** a nahoře vyberte **Nastavení.** **Otevře se konfigurace pracovního prostoru.**
2. V části **Konfigurace pracovního prostoru**vyberte kartu pro typ entity, kterou chcete přidat: Registr systému Windows, Soubory systému Windows nebo Soubory Linuxu. V tomto příkladu jsme vybrali **Linux Files**.

   ![Přidání nové položky ke sledování][14]

3. Vyberte **Přidat**. **Přidat pro sledování změn** otevře.

   ![Zadání požadovaných informací][15]

4. Na stránce **Přidat** zadejte požadované informace a vyberte **Uložit**.

## <a name="disable-monitored-entities"></a>Zakázání sledovaných entit
1. Vraťte se na řídicí panel **Sledování integrity souborů.**
2. Vyberte pracovní prostor, ve kterém je aktuálně povolena funkce FIM. Pracovní prostor je povolen pro FIM, pokud chybí tlačítko Povolit nebo Plán upgradu.

   ![Výběr pracovního prostoru, ve kterém je povolen fim][16]

3. V části Sledování integrity souborů vyberte **Nastavení**.

   ![Výběr nastavení][17]

4. V části **Konfigurace pracovního prostoru**vyberte skupinu, ve které je **povoleno** nastaveno na hodnotu true.

   ![Konfigurace pracovního prostoru][18]

5. V části **Upravit pro sledování změn** nastavte okno **Povoleno** na Hodnotu Nepravda.

   ![Nastavit povoleno na hodnotu false][19]

6. Vyberte **Uložit**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Sledování složek a cest pomocí zástupných znaků

Pomocí zástupných znaků můžete zjednodušit sledování v adresářích. Následující pravidla platí při konfiguraci monitorování složek pomocí zástupných znaků:
-   Pro sledování více souborů jsou vyžadovány zástupné znaky.
-   Zástupné znaky lze použít pouze v posledním segmentu cesty, například C:\folder\file nebo /etc/*.conf
-   Pokud proměnná prostředí obsahuje cestu, která není platná, ověření bude úspěšné, ale cesta se nezdaří při spuštění zásob.
-   Při nastavování cesty se vyhněte\*obecným cestám, například c: .*, což bude mít za následek příliš mnoho složek, které procházejí.

## <a name="disable-fim"></a>Zakázat FIM
Fim můžete zakázat. FIM používá řešení Azure Change Tracking ke sledování a identifikaci změn ve vašem prostředí. Zakázáním funkce FIM odeberete řešení sledování změn z vybraného pracovního prostoru.

1. Chcete-li zakázat funkci FIM, vraťte se na řídicí panel **Sledování integrity souborů.**
2. Vyberte pracovní prostor.
3. V části **Sledování integrity souborů**vyberte **Zakázat**.

   ![Zakázat FIM][20]

4. Chcete-li zakázat, vyberte **odebrat.**

## <a name="next-steps"></a>Další kroky
V tomto článku jste se naučili používat monitorování integrity souborů (FIM) v Centru zabezpečení. Další informace o Centru zabezpečení najdete na následujících stránkách:

* [Nastavení zásad zabezpečení](tutorial-security-policy.md) – Naučte se, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Monitorování stavu zabezpečení](security-center-monitoring.md)--Naučte se sledovat stav prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md)-- Přečtěte si, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Monitorování partnerských řešení](security-center-partner-solutions.md) – Zjistěte, jak sledovat stav partnerských řešení.
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
