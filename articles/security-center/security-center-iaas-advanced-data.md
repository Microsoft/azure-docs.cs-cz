---
title: Rozšířené zabezpečení dat v počítačích s SQL (Preview) Azure Security Center
description: Naučte se, jak povolit pokročilé zabezpečení dat pro počítače s SQL v Azure Security Center
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
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: de5346387336acb5a4b13457d3a163f529c51b89
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438683"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Pokročilé zabezpečení dat pro počítače SQL (Preview)

Pokročilé zabezpečení dat v počítačích s SQL je Azure Security Center chrání SQL servery hostované v Azure, v jiných cloudových prostředích i v místních počítačích. Tím se rozšiřuje ochrana vašich serverů SQL Azure Native tak, aby plně podporovala hybridní prostředí.

Tato funkce Preview zahrnuje funkce pro identifikaci a zmírnění potenciálních chyb zabezpečení databáze a zjišťování neobvyklé aktivit, které by mohly označovat hrozby pro vaši databázi: 

* **Posouzení ohrožení zabezpečení** – služba prohledávání, která zjišťuje, sleduje a usnadňuje napravení potenciálních ohrožení zabezpečení databáze. Kontroly Hodnocení poskytují přehled o stavu zabezpečení počítačů s SQL a podrobnosti o jakýchkoli zjištěních zabezpečení.

* [Rozšířená ochrana před internetovými útoky](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) – služba zjišťování, která nepřetržitě monitoruje vaše servery SQL na hrozbách, jako jsou například injektáže SQL, útoky hrubou silou a zneužití oprávnění. Tato služba poskytuje výstrahy zabezpečení zaměřené na akce v Azure Security Center s podrobnostmi o podezřelé aktivitě, pokyny ke zmírnění hrozeb a možnosti pro pokračování v šetření pomocí ověřování Azure Sentinel.

>[!TIP]
> Rozšířené zabezpečení dat pro počítače s SQL je rozšíření [balíčku pokročilého zabezpečení dat](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)Azure Security Center dostupného pro Azure SQL Database, Azure synapse a SQL Managed instance.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Nastavení pokročilého zabezpečení dat pro počítače s SQL 

Nastavením pokročilých zabezpečení dat Azure Security Center pro počítače SQL se skládá ze dvou kroků:

* Zřídí agenta Log Analytics na hostiteli SQL serveru. To umožňuje připojení k Azure.

* Povolte na stránce Security Center ceny a nastavení volitelné sady prostředků.

Obě tyto parametry jsou popsány níže.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Krok 1. Zřízení agenta Log Analytics na hostiteli SQL serveru:

- **SQL Server na virtuálním počítači Azure** – Pokud je váš počítač SQL hostovaný na virtuálním počítači Azure, můžete [automaticky zřídit agenta Log Analytics](security-center-enable-data-collection.md#workspace-configuration). Alternativně můžete postupovat podle ručního postupu pro [Přidání virtuálního počítače Azure](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server v Azure ARC** – Pokud je vaše SQL Server hostovaná v počítači [ARC Azure](https://docs.microsoft.com/azure/azure-arc/) , můžete agenta Log Analytics nasadit pomocí Security Center doporučení Log Analytics agenta, který se má nainstalovat na počítače se systémem Windows Azure ARC (Preview). Alternativně můžete postupovat podle ručního postupu v [dokumentaci k Azure ARC](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server Prem** – Pokud je váš SQL Server hostovaný na místním počítači s Windows bez ARC Azure, máte dvě možnosti, jak ho připojit k Azure:
    
    - **Nasazení ARC Azure** – k Security Center můžete připojit libovolný počítač s Windows. Azure ARC ale poskytuje hlubší integraci ve *všech* prostředích Azure. Pokud nastavíte Azure ARC, na portálu se zobrazí stránka **SQL Server – ARC Azure** a výstrahy zabezpečení se zobrazí na vyhrazené kartě **zabezpečení** na této stránce. První a doporučenou možností je [nastavit na hostiteli Azure ARC](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) a postupovat podle pokynů **SQL Server v Arc Azure ARC**výše.
        
    - **Připojit počítač s Windows bez ARC Azure** – Pokud se rozhodnete připojit SQL Server běžící na počítači s Windows bez použití ARC Azure, postupujte podle pokynů v tématu [připojení počítačů s Windows k Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Krok 2. Povolte na stránce Security Center ceny a nastavení volitelné sady prostředků:

1. Na bočním panelu Security Center otevřete stránku **Nastavení cenové &** .

    - Pokud používáte **výchozí pracovní prostor Azure Security Center** (s názvem "defaultworkspace-[vaše předplatné ID]-[oblast]"), vyberte příslušné **předplatné**.

    - Pokud používáte **jiný pracovní prostor než výchozí**, vyberte příslušný **pracovní prostor** (v případě potřeby zadejte název pracovního prostoru ve filtru):

        ![title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Přepněte možnost pro **SQL servery na počítačích (Preview)** na povoleno. 

    [![Stránka s cenami Security Center s volitelnými sadami](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Rozšířené zabezpečení dat pro servery SQL na počítačích se povolí na všech serverech SQL připojených k vybranému pracovnímu prostoru. Ochrana bude plně aktivní po prvním restartování instance SQL Server.

    >[!TIP] 
    > Pokud chcete vytvořit nový pracovní prostor, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Volitelně můžete nakonfigurovat e-mailová oznámení pro výstrahy zabezpečení. 
    Můžete nastavit seznam příjemců, na které se dostanete e-mailové oznámení, když se generují Security Center výstrahy. E-mail obsahuje přímý odkaz na výstrahu v Azure Security Center se všemi souvisejícími podrobnostmi. Další informace najdete v tématu [Nastavení e-mailových oznámení pro výstrahy zabezpečení](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumejte sestavy posouzení ohrožení zabezpečení

Služba posouzení ohrožení zabezpečení prohledává vaše databáze jednou týdně. Kontroly jsou spouštěny ve stejném dni v týdnu, kdy jste službu povolili.

Řídicí panel posouzení ohrožení zabezpečení poskytuje přehled výsledků hodnocení napříč všemi vašimi databázemi a souhrnem zdravých a poškozených databází a celkový souhrn neúspěšných kontrol v závislosti na rozdělení rizika.

Výsledky posouzení ohrožení zabezpečení můžete zobrazit přímo z Security Center.

1. Z bočního panelu Security Center otevřete stránku **doporučení** a vyberte **ohrožení zabezpečení na serverech SQL na počítačích, které by měly být opraveny (Preview)**. Další informace najdete v tématu [Security Center doporučení](security-center-recommendations.md). 


    [![* * Na počítačích s SQL serverem by se měla opravit Chyba zabezpečení (Preview) * * doporučení](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Zobrazí se podrobné zobrazení tohoto doporučení.

    [![Podrobné zobrazení pro chyby zabezpečení * * na počítačích SQL na počítačích by mělo být opravené (Preview) * * doporučení](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Další podrobnosti najdete v podrobnostech:

    * Chcete-li zobrazit přehled naskenovaných prostředků (databáze) a seznam kontrol kontroly zabezpečení, které byly testovány, vyberte server, který vás zajímá.

    * Přehled chyb, které jsou seskupeny podle konkrétní databáze SQL, získáte tak, že vyberete databázi, která je zajímavá.

    V každém zobrazení jsou kontroly zabezpečení seřazené podle **závažnosti**. Kliknutím na konkrétní kontrolu zabezpečení zobrazíte podokno podrobností s **popisem**, postupem jeho **opravte** a dalšími souvisejícími informacemi, jako je třeba **dopad** nebo **Srovnávací test**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky pro servery SQL na počítačích
Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k počítačům SQL nebo jejich zneužití. Tyto události mohou aktivovat výstrahy zobrazené v [části výstrahy pro SQL Database a Azure synapse Analytics (dříve SQL Data Warehouse) na stránce referenčních informací o výstrahách](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Prozkoumat a prozkoumat výstrahy zabezpečení

Výstrahy zabezpečení jsou k dispozici na stránce výstrahy Security Center, na kartě zabezpečení prostředku nebo prostřednictvím přímého odkazu v e-mailech s výstrahami.

1. Pokud chcete zobrazit výstrahy, vyberte výstrahy **zabezpečení** z bočního panelu Security Center a vyberte výstrahu.

1. Výstrahy jsou navržené tak, aby byly samostatné, s podrobnými kroky pro nápravu a informace o šetření v každé z nich. Můžete prozkoumat další možnosti pomocí dalších možností Azure Security Center a Azure Sentinel pro širší zobrazení:

    * Povolit funkci auditování SQL Server pro další šetření. Pokud jste uživatelem Sentinel Azure, můžete nahrát protokoly auditování SQL z událostí protokolu zabezpečení Windows do Sentinel a využít bohatých prostředí pro šetření. [Přečtěte si další informace o SQL Server auditování](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Chcete-li zlepšit stav zabezpečení, použijte doporučení Security Center pro hostitelský počítač uvedený v každé výstraze. Tím se sníží rizika budoucích útoků. 

    [Přečtěte si další informace o správě a reakci na výstrahy](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Další kroky

Související materiály najdete v následujícím článku:

- [Výstrahy zabezpečení pro SQL Database a Azure synapse Analytics (dřív SQL Data Warehouse)](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Nastavení e-mailových oznámení pro výstrahy zabezpečení](security-center-provide-security-contact-details.md)
- [Další informace o Sentinel Azure](https://docs.microsoft.com/azure/sentinel/)
- [Balíček pokročilého zabezpečení dat Azure Security Center](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)