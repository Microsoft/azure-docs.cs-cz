---
title: Rozšířené zabezpečení dat pro IaaS v Azure Security Center | Dokumentace Microsoftu
description: " Zjistěte, jak povolit pokročilé data zabezpečení pro IaaS v Azure Security Center. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: monhaber
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: ed94b92a34e2989c9f2226c344ac4d34a279eeac
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551845"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Pokročilé data zabezpečení pro servery SQL Server na virtuálních počítačích Azure (Public Preview)
Pokročilé data zabezpečení pro servery SQL v Azure Virtual Machines je jednotný balíček pro pokročilé funkce zabezpečení SQL. Aktuálně (ve verzi Public Preview) obsahuje funkce pro zpřístupnění a zmírnění potenciální ohrožení zabezpečení databáze a detekuje neobvyklé aktivity, které může znamenat hrozbu pro vaše databáze. 

Toto zabezpečení nabízí pro virtuální počítače SQL serverech Azure je založeno na stejné základní technologii použitou v [rozšířené zabezpečení dat Azure SQL Database balíčku](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Přehled

Zabezpečení dat pokročilé poskytuje sadu pokročilé funkce zabezpečení SQL, který se skládá z posouzení ohrožení zabezpečení a Rozšířená ochrana před internetovými útoky.

* [Posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je snadno konfigurovat službu, můžete zjistit, sledovat a umožňují náprava potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu vašeho zabezpečení a obsahuje postup, jak vyřešit problémy se zabezpečením a Vylepšete vaše fortifications databáze.
* [Rozšířená ochrana před internetovými útoky](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup nebo zneužití vašeho SQL serveru. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje výstrahy zabezpečení orientovaných na akci na vzorech přístupu neobvyklé databázové. Tyto výstrahy poskytují podrobnosti o podezřelé aktivitě a doporučených akcích pro šetření a zmírnění hrozby.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Začínáme s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure

Následující kroky vám pomůžou začít s pokročilým zabezpečením dat SQL v Azure virtuální počítače veřejné verzi Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Nastavení rozšířené zabezpečení dat pro SQL na virtuálních počítačích Azure

**Než začnete**: Potřebujete pracovní prostor Log Analytics k ukládání protokolů zabezpečení analyzován. Pokud nemáte jednu, pak můžete vytvořit jednu snadno, jak je vysvětleno v [vytvořit pracovní prostor Log Analytics na portálu Azure portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Připojte virtuální počítač, který je hostitelem SQL serveru do pracovního prostoru Log Analytics. Pokyny najdete v tématu [počítače připojit Windows do Azure monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Z webu Azure Marketplace, přejděte [řešení SQL rozšířené zabezpečení dat](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Najdete ho pomocí možnosti vyhledávání marketplace jako naleznete na následujícím obrázku.) **SQL rozšířené zabezpečení dat** otevře se stránka.

    ![Pokročilé Data zabezpečení pro IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klikněte na možnost **Vytvořit**. Zobrazí se na pracovištích.

    ![Vytváření pokročilých Data zabezpečení](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Vyberte pracovní prostor používat, a klikněte na tlačítko **vytvořit**.

   ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Restartujte [Virtuálního počítače SQL serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Prozkoumejte a prošetřování výstrah zabezpečení

Můžete zobrazit a spravovat své aktuální výstrahy zabezpečení.

1. Klikněte na tlačítko **Security Center** > **výstrahy zabezpečení**a klikněte na výstrahu.

    ![Najít upozornění](./media/security-center-advanced-iaas-data/find-alert.png)

1. Z **napadených prostředků** sloupce, klikněte na prostředek, který byl napaden.

1. Chcete-li zobrazit podrobnosti výstrahy a akce pro zkoumání aktuální hrozby a adresování budoucími hrozbami, posuňte se dolů **obecné informace** stránky a **kroky k nápravě** části, klikněte na  **KROKY šetření** odkaz.

    ![Kroky pro odstranění problému](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Pokud chcete zobrazit protokoly, které jsou spojeny s aktivuje výstrahu, přejděte na **pracovní prostory Log analytics** a proveďte následující kroky:

     > [!NOTE]
     > Pokud **pracovní prostory Log analytics** nebude zobrazovat v nabídce vlevo, klikněte na tlačítko **všechny služby**a vyhledejte **pracovní prostory Log analytics**.

    1. Ujistěte se, sloupce, které se zobrazuje **cenová úroveň** a **ID pracovního prostoru** sloupce. (**Pracovní prostory log analytics** > **upravit sloupce**, přidejte **cenová úroveň** a **ID pracovního prostoru**.)

     ![Upravit sloupce](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klikněte na pracovním prostoru, který se má upozornění protokolů.

    1. V části **Obecné** nabídky, klikněte na tlačítko **protokoly**

    1. Klikněte na tlačítko oka vedle **SQLAdvancedThreatProtection** tabulky. Protokoly jsou uvedeny.

     ![Zobrazit protokoly](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Nastavení e-mailové oznámení pro výstrahy ochrany ATP v programu 

Můžete nastavit seznam příjemců pro příjem e-mailové oznámení, když ASC výstrahy jsou generovány. E-mailu obsahuje přímý odkaz na upozornění ve službě Azure Security Center s všechny relevantní informace. 

1. Přejděte na **Security Center** > **ceny na & stavení** a klikněte na příslušné předplatné

    ![Nastavení předplatného](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. Z **nastavení** klikněte na nabídku **e-mailová oznámení**. 
1. V **e-mailová adresa** textové pole, zadejte e-mailových adres pro příjem oznámení. Je-li zadat více než jednu e-mailovou adresu, e-mailové adresy oddělte čárkou (,).  Například admin1@mycompany.com,admin2@mycompany.com,admin3@mycompany.com

      ![Nastavení e-mailu](./media/security-center-advanced-iaas-data/email-settings.png)

1. V **e-mailová oznámení** nastavte následující možnosti:
  
    * **Odeslání e-mailové oznámení pro výstrahy s vysokou závažností**: Místo odesílání e-mailů pro všechny výstrahy, odeslat pouze výstrahy s vysokou závažností.
    * **E-mailová oznámení také odeslat vlastníkům předplatného**:  Příliš odesílat oznámení vlastníkům předplatného.

1. V horní části **e-mailová oznámení** obrazovce, klikněte na tlačítko **Uložit**.

  > [!NOTE]
  > Nezapomeňte kliknout na **Uložit** před zavřením okna nebo do nového **e-mailová oznámení** nastavení nebude uloženo.

## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumání sestav posouzení ohrožení zabezpečení

Řídicí panel posouzení ohrožení zabezpečení poskytuje přehled výsledků posouzení napříč všemi databázemi. Můžete zobrazit distribuční databáze podle verze SQL serveru, spolu se shrnutím selhání a předáním databází a celkový přehled kontrol podle rizika distribuce, které selhaly.

Zobrazíte výsledky posouzení ohrožení zabezpečení a sestavy přímo ze služby Log Analytics.

1. Přejděte do pracovního prostoru Log Analytics s pokročilým zabezpečením dat řešení.
1. Přejděte do **řešení** a vyberte **posouzení ohrožení zabezpečení SQL** řešení.
1. V **Souhrn** podokně klikněte na tlačítko **zobrazit souhrn** a vyberte váš **sestava posouzení ohrožení zabezpečení SQL**.

    ![Sestavu posouzení SQL](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Načte řídicí panel sestavu. Ujistěte se, že časový interval je nastavena alespoň na **posledních 7 dnů** od spuštění kontrol posouzení ohrožení zabezpečení v databázích podle pevného plánu jednou za 7 dní.

    ![Nastavte posledních 7 dní](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Chcete přejít k podrobnostem další podrobnosti, klikněte na některý z prvků řídicího panelu. Příklad:

   1. Klikněte na kontrolu ohrožení zabezpečení v **neúspěšné kontroly Souhrn** části zobrazení tabulky Log Analytics s výsledky pro tuto kontrolu napříč všemi databázemi. Ty, které se mají výsledky se zobrazují jako první.

   1. Potom klikněte na prostřednictvím chcete-li zobrazit podrobnosti pro jednotlivé chyby zabezpečení, včetně popisu ohrožení zabezpečení a dopad, stav, spojené riziko a skutečné výsledky v této databázi. Zobrazí se také samotný dotaz, který byl spuštěn k provedení této kontroly a informace o nápravných opatřeních k vyřešení této chyby.

    ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Všechny dotazy Log Analytics můžete spustit na datech výsledky posouzení ohrožení zabezpečení, k vyfiltrování a rozčlenění dat podle vašich potřeb.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Rozšířená ochrana před internetovými útoky pro servery SQL Server na virtuálních počítačích Azure výstrahy
Výstrahy jsou generovány a potenciálně nebezpečné pokusy o přístup nebo zneužití servery SQL. Tyto události můžete spustit následující upozornění:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Neobvyklé přístup vzor výstrahy (podporované ve verzi Public Preview)

* **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklé geografické lokality. Možné příčiny:
     * Útočník nebo Bývalé zlými úmysly využívat přístup k serveru SQL Server.
     * Legitimní uživatel se připojil k serveru SQL z nového místa.
* **Přístup z potenciálně škodlivé aplikace**: jeho výstraha se aktivuje v případě potenciálně škodlivé aplikace se používá pro přístup k databázi. Možné příčiny:
     * Útočník při pokusu o porušení zabezpečení SQL pomocí běžných nástrojů útoku.
     * Legitimní probíhající test průniku.
* **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru pomocí neobvyklého objektu zabezpečení (uživatel SQL). Možné příčiny:
     * Útočník nebo Bývalé zlými úmysly využívat přístup k serveru SQL Server. 
     * Oprávněný uživatel použil SQL serveru z pomocí nového objektu.
* **Přihlašovací údaje SQL útok hrubou silou**: Tato výstraha se aktivuje po neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. Možné příčiny:
     * Útočník při pokusu o porušení zabezpečení vašich SQL s použitím útok hrubou silou.
     * Legitimní probíhající test průniku.

### <a name="potential-sql-injection-attacks-coming"></a>Potenciální útok prostřednictvím injektáže SQL, útoky (vstupu)

* **Zranitelnost vůči útoku prostřednictvím injektáže SQL**: Tato výstraha se aktivuje, pokud aplikace vygeneruje Chybný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Možné příčiny:
     * Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
     * Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
* **Potenciální útok prostřednictvím injektáže SQL**: Tato výstraha se aktivuje při výskytu aktivního zneužití zranitelnosti identifikované aplikace zranitelnost vůči útoku prostřednictvím injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
