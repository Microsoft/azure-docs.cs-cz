---
title: Rozšířené zabezpečení dat pro IaaS v Azure Security Center | Dokumenty společnosti Microsoft
description: " Zjistěte, jak povolit pokročilé zabezpečení dat pro IaaS v Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2019
ms.author: memildin
ms.openlocfilehash: a2970ea3f5ad360deaedd7efc82154cd3bc50337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282728"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Pokročilé zabezpečení dat pro SQL servery ve virtuálních počítačích Azure (Preview)
Pokročilé zabezpečení dat pro SQL servery na virtuálních počítačích Azure je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Tato funkce náhledu zahrnuje funkce pro identifikaci a zmírnění potenciálních chyb zabezpečení databáze a detekci neobvyklých aktivit, které by mohly naznačovat ohrožení databáze. 

Tato nabídka zabezpečení pro servery SQL virtuálních počítače Azure je založená na stejné základní technologii, která se používá v [balíčku Azure SQL Database Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Přehled

Rozšířené zabezpečení dat poskytuje sadu pokročilých funkcí zabezpečení SQL, které se skládají z posouzení ohrožení zabezpečení a pokročilé ochrany před hrozbami.

* [Posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je snadno konfigurovatelná služba, která dokáže zjistit, sledovat a pomoci vám napravit potenciální chyby zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje kroky k řešení problémů se zabezpečením a vylepšení opevnění databáze.
* [Pokročilá ochrana před internetovými zprávami](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k serveru SQL nebo jejich zneužití. Neustále monitoruje databázi podezřelých aktivit a poskytuje výstrahy zabezpečení orientované na akce na neobvyklé vzory přístupu k databázi. Tyto výstrahy poskytují podrobnosti o podezřelé aktivitě a doporučené akce k prošetření a zmírnění hrozby.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Začínáme s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure

Následující kroky vám pomohou začít s pokročilým zabezpečením dat pro SQL ve verzi Azure VM Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Nastavení rozšířeného zabezpečení dat pro SQL na virtuálních počítačích Azure

Povolit rozšířené zabezpečení dat pro servery SQL na virtuálních počítačích na úrovni předplatného/pracovního prostoru:
 
1. Na postranním panelu Centra zabezpečení otevřete stránku **Nastavení & cen.**

1. Vyberte předplatné nebo pracovní prostor, pro který chcete povolit rozšířené zabezpečení dat pro SQL na virtuálních počítačích Azure.

1. Přepněte možnost **pro sql servery na virtuálním počítači (Preview)** na povoleno. 

    (Kliknutím na snímek obrazovky rozbalte)

    [![Doporučení a výstrahy Centra zabezpečení, jak je vidět v Centru pro správu Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Rozšířené zabezpečení dat pro servery SQL bude povoleno na všech serverech SQL připojených k vybranému pracovnímu prostoru nebo ve výchozím pracovním prostoru vybraného předplatného.

    >[!NOTE]
    > Řešení bude plně aktivní po prvním restartování serveru SQL Server. 

Chcete-li vytvořit nový pracovní prostor, postupujte podle pokynů v [části Vytvoření pracovního prostoru Analýzy protokolů](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Chcete-li připojit hostitele serveru SQL Server k pracovnímu prostoru, postupujte podle pokynů v části [Připojení počítačů se systémem Windows ke službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Nastavení e-mailového upozornění na výstrahy zabezpečení 

Seznam příjemců můžete nastavit tak, aby při generování výstrah Centra zabezpečení dostávali e-mailové oznámení. E-mail obsahuje přímý odkaz na výstrahu v Centru zabezpečení Azure se všemi relevantními podrobnostmi. 

1. Přejděte na**nastavení Cenové &** Centra >  **zabezpečení**a klikněte na příslušné předplatné.

    ![Nastavení předplatného](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. V nabídce Nastavení klikněte na **E-mailová oznámení**. 
1. Do textového pole **E-mailová adresa** zadejte e-mailové adresy pro příjem oznámení. Můžete zadat více než jednu e-mailovou adresu oddělením e-mailové adresy čárkou (,).  Například admin1@mycompany.comadmin2@mycompany.com,admin3@mycompany.com

    ![Nastavení e-mailu](./media/security-center-advanced-iaas-data/email-settings.png)

1. V nastavení **e-mailových oznámení** nastavte následující možnosti:
  
    * **Odeslat e-mailové oznámení pro upozornění vysoké závažnosti:** Namísto odesílání e-mailů pro všechna upozornění odesílejte pouze pro výstrahy s vysokou závažností.
    * **Také posílat e-mailová oznámení vlastníkům předplatného**: Odeslat oznámení vlastníkům odběrů příliš.

1. V horní části obrazovky **E-mailová oznámení** klikněte na **Uložit**.

  > [!NOTE]
  > Nezapomeňte před zavřením okna kliknout na **Uložit,** jinak se nové nastavení **e-mailových oznámení** neuloží.

## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumejte zprávy o posouzení zranitelnosti

Řídicí panel hodnocení ohrožení zabezpečení poskytuje přehled výsledků hodnocení ve všech databázích. Můžete zobrazit distribuci databází podle verze serveru SQL Server, spolu se souhrnem selhání versus předávání databází a celkový souhrn selhání kontroly podle rozložení rizika.

Výsledky hodnocení ohrožení zabezpečení můžete zobrazit přímo z centra zabezpečení.

1. V postranním panelu Centra zabezpečení vyberte v části HYGIENA ZABEZPEČENÍ ZDROJŮ **položku Data & Storage**.

1. Vyberte doporučení **Chyby zabezpečení v databázích SQL ve virtuálních počítačích by měly být opraveny (Náhled).** Další informace naleznete v [tématu Doporučení Centra zabezpečení](security-center-recommendations.md). 

    [![**Chyby zabezpečení v databázích SQL ve virtuálních počítačích by měly být opraveny (preview)** doporučení](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Zobrazí se podrobné zobrazení tohoto doporučení.

    [![Podrobné zobrazení **Chyb zabezpečení v databázích SQL ve virtuálních počítačích by mělo být opraveno (preview)** doporučení](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Chcete-li přejít k podrobnostem pro více informací:

    * Přehled naskenovaných prostředků (databází) a seznam testovaných bezpečnostních kontrol zobrazíte klepnutím na server, který je zajímá.
    [![Chyby zabezpečení seskupené podle serveru SQL](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Chcete-li získat přehled o chybách zabezpečení seskupených podle konkrétní databáze SQL, klepněte na databázi, která je zajímavá.
    [![Chyby zabezpečení seskupené podle serveru SQL](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    V každém zobrazení jsou kontroly zabezpečení seřazeny podle **závažnosti**. Klepnutím na konkrétní kontrolu zabezpečení zobrazíte podokno podrobností s **popisem**, jak jej **napravit** a další související informace, jako je **Impact** nebo **Benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Pokročilá ochrana před hrozbami pro sql servery ve výstrahách virtuálních počítačích Azure
Výstrahy jsou generovány neobvyklé a potenciálně škodlivé pokusy o přístup nebo zneužití serverů SQL. Tyto události mohou aktivovat následující výstrahy:

### <a name="anomalous-access-pattern-alerts-preview"></a>Upozornění na anomální přístupový vzor (náhled)

* **Přístup z neobvyklého místa:** Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru SQL, kde se někdo přihlásil k serveru SQL z neobvyklého zeměpisného umístění. Možné příčiny:
    * Útočník nebo bývalý škodlivý zaměstnanec získal přístup k serveru SQL Server.
    * Legitimní uživatel má přístup k serveru SQL Server z nového umístění.
* **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. Možné příčiny:
    * Útočník, který se pokouší proniknout do sql pomocí běžných nástrojů pro útok.
    * Legitimní penetrační testování v akci.
* **Přístup z neznámého objektu zabezpečení:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil s použitím neobvyklého objektu zabezpečení (uživatel SQL). Možné příčiny:
    * Útočník nebo bývalý škodlivý zaměstnanec získal přístup k serveru SQL Server. 
    * Legitimní uživatel má přístup k serveru SQL Server z s novým objektem zabezpečení.
* **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. Možné příčiny:
    * Útočník, který se pokouší proniknout do vašeho SQL hrubou silou.
    * Legitimní penetrační testování v akci.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenciální útoky injektáží SQL (podporováno v SQL Serveru 2019)

* **Chyba zabezpečení vůči vkládání SQL**: Tato výstraha se aktivuje, když aplikace generuje vadný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Možné příčiny:
    * Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
    * Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
* **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Nebezpečný příkaz (podporováno v SQL Serveru 2019)

* **Potenciálně nebezpečná akce**: Tato výstraha se aktivuje při spuštění vysoce privilegovaného a potenciálně nebezpečného příkazu. Možné příčiny:
    * Je povolen příkaz, který doporučuje zakázat pro lepší stav zabezpečení.
    * Útočník, který se pokouší zneužít oprávnění SQL nebo eskalovat.   


## <a name="explore-and-investigate-security-alerts"></a>Prozkoumání a prozkoumání výstrah zabezpečení

Výstrahy zabezpečení dat jsou k dispozici na stránce výstrah Centra zabezpečení, na kartě zabezpečení prostředku nebo prostřednictvím přímého odkazu v e-mailech s výstrahami.

1. Zobrazení výstrah:

    * V Centru zabezpečení – klikněte na **výstrahy zabezpečení** z postranního panelu a vyberte výstrahu.
    * V oboru prostředků - Otevřete příslušnou stránku prostředku a na postranním panelu klepněte na tlačítko **Zabezpečení**. 

1. Výstrahy jsou navrženy tak, aby byly samostatné, s podrobnými nápravnými kroky a informacemi o vyšetřování v každém z nich. Můžete prozkoumat další pomocí jiných Azure Security Center a Azure Sentinel možnosti pro širší zobrazení:

    * Povolte funkci auditování serveru SQL Server pro další šetření. Pokud jste uživatel Azure Sentinelu, můžete nahrát protokoly auditování SQL z událostí protokolu zabezpečení systému Windows do Sentinelu a využívat bohaté prostředí vyšetřování.
    * Chcete-li zlepšit stav zabezpečení, použijte doporučení Centra zabezpečení pro hostitelský počítač uvedená v každé výstraze. Tím se sníží riziko budoucích útoků. 


## <a name="next-steps"></a>Další kroky

Související materiály naleznete v následujícím článku:

- [Jak napravit doporučení](security-center-remediate-recommendations.md)