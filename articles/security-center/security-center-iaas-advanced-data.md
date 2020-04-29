---
title: Pokročilé zabezpečení dat pro IaaS v Azure Security Center | Microsoft Docs
description: " Naučte se, jak povolit pokročilé zabezpečení dat pro IaaS v Azure Security Center. "
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282728"
---
# <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Pokročilé zabezpečení dat pro SQL servery na Azure Virtual Machines (Preview)
Rozšířené zabezpečení dat pro SQL Server v Azure Virtual Machines je jednotný balíček pro pokročilé funkce zabezpečení SQL. Tato funkce Preview zahrnuje funkce pro identifikaci a zmírnění potenciálních chyb zabezpečení databáze a zjišťování neobvyklé aktivit, které by mohly označovat hrozby pro vaši databázi. 

Tato nabídka zabezpečení pro virtuální počítače Azure SQL Server je založená na stejné základní technologii, kterou používá [Azure SQL Database balíčku pro pokročilou zabezpečení dat](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).


## <a name="overview"></a>Přehled

Rozšířené zabezpečení dat poskytuje sadu pokročilých funkcí zabezpečení SQL, které se skládají z posouzení ohrožení zabezpečení a rozšířené ochrany před internetovými útoky.

* [Posouzení ohrožení zabezpečení](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) je jednoduchá konfigurace služby, která může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje kroky pro řešení problémů se zabezpečením a vylepšení fortifications databáze.
* [Rozšířená ochrana před internetovými útoky](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k SQL serveru nebo jeho zneužití. Nepřetržitě monitoruje vaši databázi pro podezřelé aktivity a poskytuje výstrahy zabezpečení zaměřené na akce na vzorech přístupu k databázi neobvyklé. Tyto výstrahy obsahují podrobnosti o podezřelé aktivitě a doporučené akce pro šetření a zmírnění hrozby.

## <a name="get-started-with-advanced-data-security-for-sql-on-azure-vms"></a>Začínáme s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure

Následující kroky vám pomohou začít s pokročilým zabezpečením dat pro SQL na virtuálních počítačích Azure Public Preview.

### <a name="set-up-advanced-data-security-for-sql-on-azure-vms"></a>Nastavení pokročilého zabezpečení dat pro SQL na virtuálních počítačích Azure

Povolit pokročilé zabezpečení dat pro SQL servery na Virtual Machines na úrovni předplatného nebo pracovního prostoru:
 
1. Na bočním panelu Security Center otevřete stránku **Nastavení cenové &** .

1. Vyberte předplatné nebo pracovní prostor, pro který chcete povolit pokročilé zabezpečení dat pro SQL na virtuálních počítačích Azure.

1. Přepněte možnost pro **SQL servery na virtuálním počítači (Preview)** na povoleno. 

    (Rozbalte kliknutím na snímek obrazovky)

    [![Security Center doporučení a výstrah, jak se zobrazuje v centru pro správu Windows](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Pokročilá zabezpečení dat pro SQL Server se povolí na všech serverech SQL připojených k vybranému pracovnímu prostoru nebo k výchozímu pracovnímu prostoru vybraného předplatného.

    >[!NOTE]
    > Po prvním restartování SQL Server řešení bude zcela aktivní. 

Pokud chcete vytvořit nový pracovní prostor, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

Pokud chcete připojit hostitele SQL Server k pracovnímu prostoru, postupujte podle pokynů v tématu [připojení počítačů se systémem Windows k Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


## <a name="set-up-email-notification-for-security-alerts"></a>Nastavení e-mailových oznámení pro výstrahy zabezpečení 

Můžete nastavit seznam příjemců, na které se dostanete e-mailové oznámení, když se generují Security Center výstrahy. E-mail obsahuje přímý odkaz na výstrahu v Azure Security Center se všemi souvisejícími podrobnostmi. 

1. Přejděte na **Security Center** > **cenové & nastavení** a klikněte na příslušné předplatné.

    ![Nastavení předplatného](./media/security-center-advanced-iaas-data/subscription-settings.png)

1. V nabídce nastavení klikněte na **e-mailová oznámení**. 
1. Do textového pole **e-mailová adresa** zadejte e-mailové adresy pro příjem oznámení. Můžete zadat více než jednu e-mailovou adresu tak, že e-mailové adresy oddělíte čárkou (,).  Například admin1@mycompany.comadmin2@mycompany.com,,admin3@mycompany.com

    ![Nastavení e-mailu](./media/security-center-advanced-iaas-data/email-settings.png)

1. V nastavení **e-mailových oznámení** nastavte následující možnosti:
  
    * **Odesílat e-mailová oznámení pro upozornění s vysokou závažností**: místo odesílání e-mailů pro všechny výstrahy stačí odeslat jenom výstrahy s vysokou závažností.
    * **Také posílat e-mailová oznámení vlastníkům předplatného**: odesílat oznámení i vlastníkům předplatného.

1. V horní části obrazovky **e-mailová oznámení** klikněte na **Uložit**.

  > [!NOTE]
  > Nezapomeňte před zavřením okna kliknout na **Uložit** , jinak se nastavení nového **e-mailového oznámení** neuloží.

## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumejte sestavy posouzení ohrožení zabezpečení

Řídicí panel posouzení ohrožení zabezpečení poskytuje přehled výsledků hodnocení napříč všemi vašimi databázemi. Distribuci databází můžete zobrazit podle SQL Server verze, spolu se shrnutím selhání v porovnání s předáváním databází a celkovým shrnutím neúspěšných kontrol v závislosti na rozdělení rizika.

Výsledky posouzení ohrožení zabezpečení můžete zobrazit přímo z Security Center.

1. Z postranního panelu Security Center v části HYGIENa zabezpečení prostředků vyberte **Data & úložiště**.

1. Vyberte, jak se **mají opravit ohrožení zabezpečení vašich databází SQL na virtuálních počítačích (Preview)**. Další informace najdete v tématu [Security Center doporučení](security-center-recommendations.md). 

    [![* * Na vašich databázích SQL na virtuálních počítačích by se měla opravit ohrožení zabezpečení (Preview) * * doporučení](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Zobrazí se podrobné zobrazení tohoto doporučení.

    [![Podrobné zobrazení pro chyby zabezpečení * * na vašich databázích SQL na virtuálních počítačích by mělo být opraveno (Preview) * * doporučení](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Další podrobnosti najdete v podrobnostech:

    * Chcete-li zobrazit přehled naskenovaných prostředků (databáze) a seznam kontrol zabezpečení, které byly testovány, klikněte na server, který vás zajímá.
    [![Ohrožení zabezpečení seskupená podle SQL serveru](media/security-center-advanced-iaas-data/single-server-view.png)](media/security-center-advanced-iaas-data/single-server-view.png#lightbox)

    * Přehled chyb, které jsou seskupeny podle konkrétní databáze SQL, získáte kliknutím na databázi, která je zajímavá.
    [![Ohrožení zabezpečení seskupená podle SQL serveru](media/security-center-advanced-iaas-data/single-database-view.png)](media/security-center-advanced-iaas-data/single-database-view.png#lightbox)

    V každém zobrazení jsou kontroly zabezpečení seřazené podle **závažnosti**. Kliknutím na konkrétní kontrolu zabezpečení zobrazíte podokno podrobností s **popisem**, postupem jeho **opravte** a dalšími souvisejícími informacemi, jako je třeba **dopad** nebo **Srovnávací test**.

## <a name="advanced-threat-protection-for-sql-servers-on-azure-vms-alerts"></a>Rozšířená ochrana před internetovými útoky pro servery SQL na virtuálních počítačích Azure – výstrahy
Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k serverům SQL nebo jejich zneužití. Tyto události mohou aktivovat následující výstrahy:

### <a name="anomalous-access-pattern-alerts-preview"></a>Výstrahy vzorového přístupu neobvyklé (Preview)

* **Přístup z neobvyklého umístění:** Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru SQL, když se někdo přihlásil k SQL serveru z neobvyklého geografického umístění. Možné příčiny:
    * K vašemu SQL Server získala útočník nebo dříve škodlivý uživatel.
    * Legitimnímu uživateli se přistupoval k vašemu SQL Server z nového umístění.
* **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. Možné příčiny:
    * Útočník, který se pokouší o porušení SQL pomocí běžných nástrojů pro útok.
    * Legitimní testování průniku v akci.
* **Přístup z neznámého objektu zabezpečení:** Tato výstraha se aktivuje, pokud dojde ke změně vzoru přístupu k serveru SQL, když se někdo k serveru SQL přihlásil s použitím neobvyklého objektu zabezpečení (uživatel SQL). Možné příčiny:
    * K vašemu SQL Server získala útočník nebo dříve škodlivý uživatel. 
    * Legitimní uživatel se k vašemu SQL Server přistupoval z pomocí nového objektu zabezpečení.
* **Útok hrubou silou na přihlašovací údaje SQL:** Tato výstraha se aktivuje, pokud byly zaznamenán neobvykle vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. Možné příčiny:
    * Útočník, který se pokouší o porušení SQL pomocí hrubou silou
    * Legitimní testování průniku v akci.

### <a name="potential-sql-injection-attacks-supported-in-sql-server-2019"></a>Potenciální útoky prostřednictvím injektáže SQL (podporované v SQL Server 2019)

* **Zranitelnost vůči INJEKTÁŽE SQL**: Tato výstraha se aktivuje, když aplikace vygeneruje chybný příkaz SQL v databázi. Tato výstraha může značit možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Možné příčiny:
    * Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
    * Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
* **Potenciální útok prostřednictvím injektáže SQL:** Tato výstraha se aktivuje v případě výskytu aktivního zneužití zranitelnosti identifikované aplikace v důsledku injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.


### <a name="unsafe-command-supported-in-sql-server-2019"></a>Nezabezpečený příkaz (podporuje se v SQL Server 2019)

* **Potenciálně nebezpečná akce**: Tato výstraha se aktivuje, když se spustí vysoce privilegovaný a potenciálně nebezpečný příkaz. Možné příčiny:
    * Příkaz, který se doporučuje zakázat pro lepší zabezpečení stav, je povolený.
    * Útočník, který se snaží zneužít přístup k SQL nebo zvýšení oprávnění.   


## <a name="explore-and-investigate-security-alerts"></a>Prozkoumat a prozkoumat výstrahy zabezpečení

Výstrahy zabezpečení dat jsou k dispozici na stránce výstrahy Security Center, na kartě zabezpečení prostředku nebo prostřednictvím přímého odkazu v e-mailech s výstrahami.

1. Zobrazení výstrah:

    * V Security Center klikněte na tlačítko **výstrahy zabezpečení** na bočním panelu a vyberte výstrahu.
    * V oboru prostředků otevřete relevantní stránku prostředku a z postranního panelu klikněte na **zabezpečení**. 

1. Výstrahy jsou navržené tak, aby byly samostatné, s podrobnými kroky pro nápravu a informace o šetření v každé z nich. Můžete prozkoumat další možnosti pomocí dalších možností Azure Security Center a Azure Sentinel pro širší zobrazení:

    * Povolit funkci auditování SQL Server pro další šetření. Pokud jste uživatelem Sentinel Azure, můžete nahrát protokoly auditování SQL z událostí protokolu zabezpečení Windows do Sentinel a využít bohatých prostředí pro šetření.
    * Chcete-li zlepšit stav zabezpečení, použijte doporučení Security Center pro hostitelský počítač uvedený v každé výstraze. Tím se sníží rizika budoucích útoků. 


## <a name="next-steps"></a>Další kroky

Související materiály najdete v následujícím článku:

- [Jak opravit doporučení](security-center-remediate-recommendations.md)