---
title: Pokročilé zabezpečení dat pro IaaS v Azure Security Center | Microsoft Docs
description: " Naučte se, jak povolit pokročilé zabezpečení dat pro IaaS v Azure Security Center. "
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
ms.author: v-mohabe
ms.openlocfilehash: 0b83575baa2221f0b502abbf919654492c7ab6cf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295759"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-public-preview"></a>Pokročilé zabezpečení dat pro SQL servery na Azure Virtual Machines (Public Preview)
Rozšířené zabezpečení dat pro SQL Server v Azure Virtual Machines je jednotný balíček pro pokročilé funkce zabezpečení SQL. V současné době (v Public Preview) obsahuje funkce pro zpřístupnění a zmírnění potenciálních chyb zabezpečení databáze a zjišťování neobvyklé aktivit, které by mohly znamenat hrozbu pro vaši databázi. 

Tato nabídka zabezpečení pro virtuální počítače Azure SQL Server je založená na stejné základní technologii, kterou používá [Azure SQL Database balíčku pro pokročilou zabezpečení dat](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Přehled

Rozšířené zabezpečení dat poskytuje sadu pokročilých funkcí zabezpečení SQL, které se skládají z posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky.

* [Posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je jednoduchá konfigurace služby, která může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje kroky pro řešení problémů se zabezpečením a vylepšení fortifications databáze.
* [Rozšířená ochrana před internetovými útoky](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k SQL serveru nebo jeho zneužití. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje výstrahy zabezpečení zaměřené na akce na vzorech přístupu k databázi neobvyklé. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučené akce pro šetření a zmírnění hrozby.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Začínáme s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure

Následující kroky vám pomohou začít s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Nastavení pokročilého zabezpečení dat pro SQL na virtuálních počítačích Azure

**Než začnete**: K ukládání analyzovaných protokolů zabezpečení potřebujete Log Analytics pracovní prostor. Pokud ho nemáte, můžete ho vytvořit snadno, jak je vysvětleno v tématu [Vytvoření pracovního prostoru Log Analytics v Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

1. Připojte virtuální počítač, který hostuje SQL Server, do pracovního prostoru Log Analytics. Pokyny najdete v tématu [připojení počítačů s Windows k Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).

1. Z Azure Marketplace můžete přejít na [Řešení SQL Advanced Data Security](https://ms.portal.azure.com/#create/Microsoft.SQLAdvancedDataSecurity).
(Můžete ji najít pomocí možnosti hledání na webu Marketplace, jak je vidět na následujícím obrázku.) Otevře se stránka **zabezpečení rozšířených dat SQL** .

    ![Pokročilé zabezpečení dat pro IaaS](./media/security-center-advanced-iaas-data/sql-advanced-data-security.png)

1. Klikněte na možnost **Vytvořit**. Zobrazí se pracoviště.

    ![Vytvoření pokročilého zabezpečení dat](./media/security-center-advanced-iaas-data/sql-advanced-data-create.png)

1. Vyberte pracovní prostor, který chcete použít, a klikněte na **vytvořit**.

   ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/sql-workspace.png)

1. Restartujte [systém SQL Server virtuálního počítače](https://docs.microsoft.com/sql/database-engine/configure-windows/start-stop-pause-resume-restart-sql-server-services?view=sql-server-2017).


## <a name="explore-and-investigate-security-alerts"></a>Prozkoumat a prozkoumat výstrahy zabezpečení

Můžete zobrazit a spravovat aktuální výstrahy zabezpečení.

1. Klikněte na **Security Center** > **výstrahy zabezpečení**a klikněte na výstrahu.

    ![Najít upozornění](./media/security-center-advanced-iaas-data/find-alert.png)

1. Ve sloupci **napadené prostředky** klikněte na prostředek, který byl napadený.

1. Chcete-li zobrazit podrobnosti a akce týkající se zkoumání aktuální hrozby a vyřešení budoucích hrozeb, posuňte se dolů na stránku **Obecné informace** a v části **Postup odstranění problému** klikněte na odkaz **kroky pro šetření** .

    ![Postup odstranění problému](./media/security-center-advanced-iaas-data/remediation-steps.png)

1. Pokud chcete zobrazit protokoly, které jsou přidružené k triggeru výstrahy, použijte **pracovní prostory Log Analytics** a proveďte následující kroky:

     > [!NOTE]
     > Pokud se **pracovní prostory Log Analytics** nezobrazí v nabídce vlevo, klikněte na **všechny služby**a vyhledejte **pracovní prostory Log Analytics**.

    1. Ujistěte se, že sloupce zobrazují **cenovou úroveň** a **ID pracovního prostoru** sloupce.  > (**Pracovní prostory Log Analytics** **upravte sloupce**, přidejte **cenové úrovně** a **ID pracovního prostoru**.)

     ![Upravit sloupce](./media/security-center-advanced-iaas-data/edit-columns.png)

    1. Klikněte na pracovní prostor, který obsahuje protokoly výstrah.

    1. V nabídce **Obecné** klikněte na **protokoly** .

    1. Klikněte na oči vedle tabulky **SQLAdvancedThreatProtection** . V seznamu jsou uvedené protokoly.

     ![Zobrazit protokoly](./media/security-center-advanced-iaas-data/view-logs.png)

## <a name="set-up-email-notification-for-atp-alerts"></a>Nastavení e-mailových oznámení pro výstrahy ATP 

Můžete nastavit seznam příjemců, na které se dostanete e-mailové oznámení, když se generují výstrahy ASC. E-mail obsahuje přímý odkaz na výstrahu v Azure Security Center se všemi souvisejícími podrobnostmi. 

1. Přejděte na **Security Center** > **cenové & nastavení** a klikněte na příslušné předplatné.

    ![Nastavení předplatného](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. V nabídce **Nastavení** klikněte na **e-mailová oznámení**. 
1. Do textového pole **e-mailová adresa** zadejte e-mailové adresy pro příjem oznámení. Můžete zadat více než jednu e-mailovou adresu tak, že e-mailové adresy oddělíte čárkou (,).  admin1@mycompany.comNapříkladadmin2@mycompany.com,,admin3@mycompany.com

      ![Nastavení e-mailu](./media/security-center-advanced-iaas-data/email-settings.png)

1. V nastavení **e-mailových oznámení** nastavte následující možnosti:
  
    * **Odesílat e-mailová oznámení pro upozornění s vysokou závažností**: Místo posílání e-mailů pro všechny výstrahy se odesílají jenom výstrahy s vysokou závažností.
    * **Také posílat e-mailová oznámení vlastníkům**předplatného:  Odeslat oznámení vlastníkům předplatného.

1. V horní části obrazovky **e-mailová oznámení** klikněte na **Uložit**.

  > [!NOTE]
  > Nezapomeňte před zavřením okna kliknout na **Uložit** , jinak se nastavení nového **e-mailového oznámení** neuloží.

## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumejte sestavy posouzení ohrožení zabezpečení

Řídicí panel posouzení ohrožení zabezpečení poskytuje přehled výsledků hodnocení napříč všemi vašimi databázemi. Distribuci databází můžete zobrazit podle SQL Server verze, spolu se shrnutím selhání v porovnání s předáváním databází a celkovým shrnutím neúspěšných kontrol v závislosti na rozdělení rizika.

Výsledky posouzení ohrožení zabezpečení a sestavy můžete zobrazit přímo z Log Analytics.

1. Přejděte do svého pracovního prostoru Log Analytics s pokročilým řešením zabezpečení dat.
1. Přejděte na **řešení** a vyberte řešení **posouzení ohrožení zabezpečení SQL** .
1. V podokně **Souhrn** klikněte na **Zobrazit souhrn** a vyberte **sestavu posouzení ohrožení zabezpečení SQL**.

    ![Sestava SQL Assessment](./media/security-center-advanced-iaas-data/ads-sql-server-1.png)

    Řídicí panel sestavy se načte. Zajistěte, aby byl časový interval nastavený alespoň na **posledních 7 dnů** od doby, kdy se kontroly ohrožení zabezpečení spouští v databázích s pevným plánem na 7 dní.

    ![Nastaveno posledních 7 dní](./media/security-center-advanced-iaas-data/ads-sql-server-2.png)

1. Chcete-li přejít k podrobnostem a zobrazit další podrobnosti, klikněte na některý z prvků na řídicím panelu. Příklad:

   1. Pokud chcete zobrazit Log Analytics tabulku s výsledky pro tuto kontrolu napříč všemi databázemi, klikněte na kontrolu ohrožení zabezpečení v části **Souhrn neúspěšných kontrol** . Ty, které mají výsledky, jsou uvedeny jako první.

   1. Potom kliknutím na Procházet zobrazíte podrobnosti o každé chybě zabezpečení, včetně popisu ohrožení zabezpečení a dopadu, stavu, přidruženého rizika a skutečných výsledků v této databázi. Můžete si také prohlédnout vlastní dotaz, který byl spuštěn, aby provedl tuto kontrolu, a informace o nápravě pro řešení tohoto ohrožení zabezpečení.

    ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/ads-sql-server-3.png)

    ![Výběr pracovního prostoru](./media/security-center-advanced-iaas-data/ads-sql-server-4.png)

1. Můžete spustit jakékoli Log Analytics dotazy na data výsledků posouzení ohrožení zabezpečení, rozdělit a indexovat data podle vašich potřeb.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Rozšířená ochrana před internetovými útoky pro servery SQL na virtuálních počítačích Azure – výstrahy
Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k serverům SQL nebo jejich zneužití. Tyto události mohou aktivovat následující výstrahy:

### <a name="anomalous-access-pattern-alerts-supported-in-public-preview"></a>Výstrahy vzorového přístupu neobvyklé (podporované v Public Preview)

* **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru SQL, když se někdo přihlásil k SQL serveru z neobvyklého geografického umístění. Možné příčiny:
     * K vašemu SQL Server získala útočník nebo dříve škodlivý uživatel.
     * Legitimnímu uživateli se přistupoval k vašemu SQL Server z nového umístění.
* **Přístup z potenciálně škodlivé aplikace**: jeho výstraha se aktivuje, když se pro přístup k databázi používá potenciálně škodlivá aplikace. Možné příčiny:
     * Útočník, který se pokouší o porušení SQL pomocí běžných nástrojů pro útok.
     * Legitimní testování průniku v akci.
* **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru SQL, když se někdo přihlásil k serveru SQL pomocí neobvyklého objektu zabezpečení (uživatel SQL). Možné příčiny:
     * K vašemu SQL Server získala útočník nebo dříve škodlivý uživatel. 
     * Legitimní uživatel se k vašemu SQL Server přistupoval z pomocí nového objektu zabezpečení.
* **Pověření hrubou silou SQL**: Tato výstraha se aktivuje, když dojde k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. Možné příčiny:
     * Útočník, který se pokouší o porušení SQL pomocí hrubou silou
     * Legitimní testování průniku v akci.

### <a name="potential-sql-injection-attacks-coming"></a>Potenciální útoky prostřednictvím injektáže SQL (připravujeme)

* **Zranitelnost vůči INJEKTÁŽE SQL**: Tato výstraha se aktivuje, když aplikace vygeneruje chybný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Možné příčiny:
     * Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
     * Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
* **Potenciální INJEKTÁŽE SQL**: Tato výstraha se aktivuje, když dojde k aktivnímu zneužití zjištěné chyby zabezpečení identifikované aplikace při vkládání SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
