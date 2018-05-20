---
title: Soubor Integrity sledování v Azure Security Center (Preview) | Microsoft Docs
description: " Informace o povolení sledování Integrity souborů v Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: terrylan
ms.openlocfilehash: 722a4fd11f35f04ed22d73638f07d15c49ea3c26
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="file-integrity-monitoring-in-azure-security-center-preview"></a>Soubor Integrity sledování v Azure Security Center (Preview)
Zjistěte, jak nakonfigurovat monitorování integritu souboru (FIM) v Azure Security Center pomocí tohoto průvodce.

## <a name="what-is-fim-in-security-center"></a>Co je FIM ve službě Security Center?
Soubor Integrity monitorování (FIM), také známé jako sledování změny prověří soubory a registry operačního systému, aplikací softwaru a jiné změny, které může signalizovat útok. Metoda porovnání se používá k určení, pokud aktuální stav souboru se liší od poslední kontroly souboru. Můžete využít k určení, pokud vaše soubory byly provedeny úpravy platné nebo podezřelé Toto porovnání.

Security Center soubor Integrity monitorování ověřuje integritu souborů systému Windows, registru systému Windows a Linux souborů. Vyberete soubory, které chcete monitorovaných povolením FIM. Security Center monitoruje soubory s FIM povolené aktivity, jako:

- Vytvoření souboru a registru a odebrání
- Úpravy souborů (změny velikosti souboru, seznamy řízení přístupu a hodnotu hash obsahu)
- Úpravy registru (změny velikosti, seznamy ovládací přístupu, typ a obsah)

Security Center doporučí entity monitorování, které můžete snadno povolit FIM na. Můžete také definovat vlastní zásady FIM nebo entity k monitorování. Tento návod ukazuje, jak.

> [!NOTE]
> Funkce monitorování integritu souboru (FIM) se dá použít pro Windows a Linux počítačů a virtuálních počítačů a je k dispozici ve standardní vrstvě služby Security Center. Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
FIM ukládání dat do pracovního prostoru analýzy protokolů. Data poplatky, podle množství dat, které odešlete. V tématu [analýzy protokolů ceny](https://azure.microsoft.com/pricing/details/log-analytics/) Další informace.
>
>

> [!NOTE]
> FIM používá řešení Azure sledování změn ke sledování a identifikovat změny ve vašem prostředí. Pokud je povoleno sledování Integrity souborů, máte **sledování změn** prostředek typu řešení. Pokud odeberete **sledování změn** prostředků, zakážete Integrity souborů monitorování funkce v Centru zabezpečení.
>
>

## <a name="which-files-should-i-monitor"></a>Soubory, které je třeba sledovat?
By si myslíte o soubory, které jsou důležité pro systém a aplikace při výběru souborů, které chcete monitorovat. Vezměte v úvahu výběr souborů, které nepředpokládáte změnit bez plánování. Výběrem možnosti soubory, které se často mění aplikace nebo operačního systému (například soubory protokolu a textové soubory) vytvořit mnoho šumu, který činit obtížné identifikovat útok.

Doporučuje Security Center, které souborů, které jste měli sledovat jako výchozí, podle schémat známé útoky, které zahrnují změny souboru a registru.

## <a name="using-file-integrity-monitoring"></a>Pomocí souboru Integrity monitorování
1. Otevřete řídicí panel **Security Center**.
2. V levém podokně v části **Advanced obrany cloudu**, vyberte **sledování Integrity souborů**.
![Řídicí panel Security Center][1]

**Soubor Integrity monitorování** otevře.
  ![Řídicí panel Security Center][2]

Pro každý pracovní prostor se poskytuje následující informace:

- Celkový počet změn, které nastaly během posledního týdne (může se zobrazit pomlčkou "-" Pokud FIM není povolena v pracovním prostoru)
- Celkový počet počítačů a virtuálních počítačů do pracovního prostoru generování sestav
- Geografické umístění pracovního prostoru
- Předplatné Azure, pracovní prostor je pod

Následující tlačítka se může zobrazit také pro pracovního prostoru:

- ![Povolit ikonu][3] Označuje, že FIM není povolen pro pracovní prostor. Když vyberete pracovním prostoru můžete povolit FIM na všechny počítače v pracovním prostoru.
- ![Ikona plán upgradu][4] označuje, že pracovního prostoru nebo předplatné není spuštěn v Centru zabezpečení na úrovni Standard. Pokud chcete používat funkci FIM, musí být vaše předplatné spuštěna Standard.  Výběr pracovním prostoru můžete upgradovat na Standard. Další informace o vrstvě Standard a postup upgradu najdete v tématu [upgradovat na plán úrovně Standard Centrum zabezpečení pro zvýšení zabezpečení](security-center-pricing.md).
- Prázdné (není tlačítko) znamená, že je FIM již povolena v pracovním prostoru.

V části **sledování Integrity souborů**, můžete vybrat prostoru povolit FIM pracovního prostoru, zobrazení řídicího panelu monitorování Integrity souborů pracovního prostoru, nebo [upgrade](security-center-pricing.md) v pracovním prostoru Standard.

## <a name="enable-fim"></a>Povolit FIM
Chcete-li povolit FIM v pracovním prostoru:

1. V části **sledování Integrity souborů**, vyberte pracovní prostor s **povolit** tlačítko.
2. **Povolit sledování integrity souborů** spustí zobrazí počet počítačů s Windows a Linux v pracovním prostoru.

   ![Povolit sledování integrity souborů.][5]

   Doporučená nastavení pro Windows a Linux jsou také uvedeny.  Rozbalte položku **soubory Windows**, **registru**, a **Linux soubory** chcete zobrazit úplný seznam Doporučené položky.

3. Zrušte zaškrtnutí políčka všechny doporučené entity, které nechcete použít produktu FIM.
4. Vyberte **použít sledování integrity souborů** povolit FIM.

> [!NOTE]
> Kdykoli můžete změnit nastavení. V tématu [úpravy monitorované entity](security-center-file-integrity-monitoring.md#edit-monitored-items) -li se dozvědět více.
>
>

## <a name="view-the-fim-dashboard"></a>Zobrazení řídicího panelu FIM
**Sledování integrity souborů** řídicí panel zobrazuje pro pracovní prostory, kde je povoleno FIM. Otevře řídicí panel FIM povolíte FIM v pracovním prostoru nebo po výběru pracovního prostoru v **sledování Integrity souborů** okno, které již FIM povolena.

![Řídicí panel monitorování Integrity souborů][6]

Řídicí panel FIM pro pracovní zobrazí následující informace:

- Celkový počet počítačů, které jsou připojené do pracovního prostoru
- Celkový počet změn, které nastaly během daného časového období
- Rozdělení typ změny (soubory, registr)
- Rozdělení změnit kategorii (upravit, přidat, odebrat)

Když vyberete filtru v horní části řídicího panelu můžete použít dobu, kterou chcete zobrazit změny.

![Období filtr času][7]

**Počítače** (viz výše) obsahuje seznam všech počítačů do tohoto pracovního prostoru generování sestav. Pro každý počítač řídicí panel uvádí:

- Celkový počet změn, ke kterým došlo za vybrané období času
- Rozdělení celkový změny jako soubor změny nebo změny v registru

**Přihlaste se vyhledávání** otevře při zadání názvu počítače do vyhledávání pole nebo vyberte počítač uvedené na kartě počítače. Protokol hledání se zobrazí všechny změny provedené v průběhu zvolené časové období pro počítač. Můžete rozbalit změnu Další informace.

![Prohledávání protokolů][8]

**Změny** (viz následující obrázek) obsahuje seznam všech změn pro pracovní prostor během daného časového období. U každé entity, která byla změněna, seznamy řídicího panelu:

- Počítač, který změnu došlo k chybě na
- Typ změny (registru nebo soubor)
- Kategorie změn (upravit, přidat, odebrat)
- Datum a čas změny

![Změny pro pracovní prostor][9]

**Změňte podrobnosti** otevře při zadání změnu do vyhledávání pole nebo vyberte entity uvedený v seznamu **změny** kartě.

![Změňte podrobnosti][10]

## <a name="edit-monitored-entities"></a>Upravit monitorované entity

1. Vraťte se na **řídicího panelu monitorování integritu souboru** a vyberte **nastavení**.

  ![Nastavení][11]

  **Konfigurace pracovního prostoru** otevře zobrazení tři karty: **registru systému Windows**, **soubory Windows**, a **Linux soubory**. Každé kartě jsou uvedeny entitami, které můžete upravit do této kategorie spadají. U každé entity uvedené Security Center označuje, jestli je FIM povoleno (true) nebo není povolen (false).  Úpravy entity umožňuje povolit nebo zakázat FIM.

  ![Konfigurace pracovního prostoru][12]

2. Vyberte identityprotection. V tomto příkladu jsme vybrali položku v registru systému Windows. **Upravit pro sledování změn** otevře.

  ![Upravit nebo sledování změn][13]

V části **upravit pro sledování změn** můžete:

- Povolte (True) nebo zakažte monitorování integritu souboru (False)
- Zadejte nebo změňte název entity
- Zadejte nebo změňte hodnotu nebo cesta
- Odstranit entitu, zahodit změny nebo ukládání změny

## <a name="add-a-new-entity-to-monitor"></a>Přidat nové entity k monitorování
1. Vraťte se do **souboru integirty řídicí panel monitorování** a vyberte **nastavení** v horní části. **Konfigurace pracovního prostoru** otevře.
2. V části **prostoru konfigurace**, vyberte kartu pro typ entity, který chcete přidat: registru systému Windows, soubory systému Windows nebo Linux soubory. V tomto příkladu jsme vybrali **Linux soubory**.

  ![Přidat novou položku k monitorování][14]

3. Vyberte **Přidat**. **Přidání pro sledování změn** otevře.

  ![Zadejte požadované informace][15]

4. Na **přidat** , zadejte požadované informace a vyberte **Uložit**.

## <a name="disable-monitored-entities"></a>Zakázat monitorované entity
1. Vraťte se na **sledování Integrity souborů** řídicího panelu.
2. Vyberte pracovní prostor, kde je aktuálně povoleno FIM. Pracovní prostor je povolený pro FIM, pokud chybí tlačítko Povolit nebo upgradovat plán.

  ![Vyberte pracovní prostor, kde je povoleno FIM][16]

3. V části Sledování Integrity souborů vyberte **nastavení**.

  ![Vyberte nastavení][17]

4. V části **prostoru konfigurace**, vyberte skupinu kde **povoleno** je nastaven na hodnotu true.

  ![Konfigurace pracovního prostoru][18]

5. V části **upravit pro sledování změn** okno sady **povoleno** na hodnotu False.

  ![Sada povoleno na hodnotu false][19]

6. Vyberte **Uložit**.

## <a name="disable-fim"></a>Zakázat FIM
Můžete zakázat FIM. FIM používá řešení Azure sledování změn ke sledování a identifikovat změny ve vašem prostředí. Zakázáním FIM, odeberete z vybraný pracovní prostor řešení sledování změn.

1. Chcete-li zakázat FIM, vraťte se k **sledování Integrity souborů** řídicího panelu.
2. Vyberte pracovní prostor.
3. V části **sledování Integrity souborů**, vyberte **zakázat**.

  ![Zakázat FIM][20]

4. Vyberte **odebrat** zakázat.

## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli používat monitorování integritu souboru (FIM) ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení](security-center-policies.md) – zjistěte, jak nakonfigurovat zásady zabezpečení pro skupiny prostředků a předplatná Azure.
* [Správa doporučení zabezpečení](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení](security-center-monitoring.md)– Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md)– zjistěte, jak spravovat a reakce na výstrahy zabezpečení.
* [Sledování partnerských řešení](security-center-partner-solutions.md) – zjistěte, jak sledovat stav vašich partnerských řešení.
* [Security Center – nejčastější dotazy](security-center-faq.md)– přečtěte si nejčastější dotazy o použití této služby.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější informace o zabezpečení Azure.

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
