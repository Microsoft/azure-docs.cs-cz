---
title: Jak používat Azure Defender pro SQL
description: Naučte se používat volitelné plánování Azure Defenderu pro SQL Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: 205f92d4282f0cad00c1dd136636da319e107f3f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391064"
---
# <a name="azure-defender-for-sql-servers-on-machines"></a>Azure Defender pro servery SQL na počítačích 

Tento plán v programu Azure Defender detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

Výstrahy se zobrazí, když dojde k podezřelým databázovým aktivitám, potenciálním ohrožením zabezpečení nebo útokům prostřednictvím injektáže SQL a k neobvyklé přístupu k databázi a ke vzorům dotazů.

## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|**Azure Defender pro servery SQL na počítačích** se fakturuje, jak je znázorněno na [stránce s cenami](security-center-pricing.md) .|
|Chráněné verze SQL:|Azure SQL Server (všechny verze, na které se vztahuje podpora Microsoftu)|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Čína gov, jiné gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Nastavení Azure Defenderu pro servery SQL na počítačích

Postup povolení tohoto plánu:

* Zřídí agenta Log Analytics na hostiteli SQL serveru. To umožňuje připojení k Azure.

* Povolte na stránce Security Center ceny a nastavení volitelného plánu.

Obě tyto parametry jsou popsány níže.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Krok 1. Zřízení agenta Log Analytics na hostiteli SQL serveru:

- **SQL Server na virtuálním počítači Azure** – Pokud je váš počítač SQL hostovaný na virtuálním počítači Azure, můžete [Povolit automatické zřizování <a name="auto-provision-mma"></a> agenta Log Analytics](security-center-enable-data-collection.md#auto-provision-mma). Alternativně můžete postupovat podle ručního postupu připojení [Azure Stackch virtuálních počítačů](quickstart-onboard-machines.md#onboard-your-azure-stack-vms).
- **SQL Server v Azure ARC** – Pokud je váš SQL Server spravovaný servery s podporou [ARC Azure](../azure-arc/index.yml) , můžete agenta Log Analytics nasadit pomocí Security Center doporučení Log Analytics agenta, který se má nainstalovat na počítače se systémem Windows Azure ARC (Preview). Případně můžete postupovat podle metod instalace popsaných v [dokumentaci k Azure ARC](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server Prem** – Pokud je váš SQL Server hostovaný na místním počítači s Windows bez ARC Azure, máte dvě možnosti, jak ho připojit k Azure:
    
    - **Nasazení ARC Azure** – k Security Center můžete připojit libovolný počítač s Windows. Azure ARC ale poskytuje hlubší integraci ve *všech* prostředích Azure. Pokud nastavíte Azure ARC, na portálu se zobrazí stránka **SQL Server – ARC Azure** a výstrahy zabezpečení se zobrazí na vyhrazené kartě **zabezpečení** na této stránce. První a doporučenou možností je [nastavit na hostiteli Azure ARC](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) a postupovat podle pokynů **SQL Server v Arc Azure ARC** výše.
        
    - **Připojit počítač s Windows bez ARC Azure** – Pokud se rozhodnete připojit SQL Server běžící na počítači s Windows bez použití ARC Azure, postupujte podle pokynů v tématu [připojení počítačů s Windows k Azure monitor](../azure-monitor/platform/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Krok 2. Povolte na stránce Security Center ceny a nastavení volitelného plánu:

1. V nabídce Security Center otevřete stránku **Nastavení cenové &** .

    - Pokud používáte **výchozí pracovní prostor Azure Security Center** (s názvem "defaultworkspace-[vaše předplatné ID]-[oblast]"), vyberte příslušné **předplatné**.

    - Pokud používáte **jiný pracovní prostor než výchozí**, vyberte příslušný **pracovní prostor** (v případě potřeby zadejte název pracovního prostoru ve filtru):

        ![Hledání nevýchozího pracovního prostoru podle názvu](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Nastavte možnost pro **Azure Defender pro servery SQL na počítačích** v plánu na **zapnuto**. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Stránka s cenami Security Center s nepovinnými plány":::

    Plán bude povolen na všech serverech SQL připojených k vybranému pracovnímu prostoru. Ochrana bude plně aktivní po prvním restartování instance SQL Server.

    >[!TIP] 
    > Pokud chcete vytvořit nový pracovní prostor, postupujte podle pokynů v tématu [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/learn/quick-create-workspace.md).


1. Volitelně můžete nakonfigurovat e-mailová oznámení pro výstrahy zabezpečení. 
    Můžete nastavit seznam příjemců, na které se dostanete e-mailové oznámení, když se generují Security Center výstrahy. E-mail obsahuje přímý odkaz na výstrahu v Azure Security Center se všemi souvisejícími podrobnostmi. Další informace najdete v tématu [Nastavení e-mailových oznámení pro výstrahy zabezpečení](security-center-provide-security-contact-details.md).



## <a name="explore-vulnerability-assessment-reports"></a>Prozkoumejte sestavy posouzení ohrožení zabezpečení

Služba posouzení ohrožení zabezpečení prohledává vaše databáze jednou týdně. Kontroly jsou spouštěny ve stejném dni v týdnu, kdy jste službu povolili.

Řídicí panel posouzení ohrožení zabezpečení poskytuje přehled výsledků hodnocení napříč všemi vašimi databázemi a souhrnem zdravých a poškozených databází a celkový souhrn neúspěšných kontrol v závislosti na rozdělení rizika.

Výsledky posouzení ohrožení zabezpečení můžete zobrazit přímo z Security Center.

1. Z bočního panelu Security Center otevřete stránku **doporučení** a vyberte **ohrožení zabezpečení na serverech SQL na počítačích, které by měly být opraveny (Preview)**. Další informace najdete v tématu [Security Center doporučení](security-center-recommendations.md). 

    :::image type="content" source="./media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png" alt-text="Na počítačích s SQL serverem by se měly opravit výsledky posouzení ohrožení zabezpečení (Preview)":::

    Zobrazí se podrobné zobrazení tohoto doporučení.

    :::image type="content" source="./media/security-center-advanced-iaas-data/all-servers-view.png" alt-text="Podrobné zobrazení doporučení":::

1. Další podrobnosti najdete v podrobnostech:

    * Chcete-li zobrazit přehled naskenovaných prostředků (databáze) a seznam kontrol kontroly zabezpečení, které byly testovány, vyberte server, který vás zajímá.

    * Přehled chyb, které jsou seskupeny podle konkrétní databáze SQL, získáte tak, že vyberete databázi, která je zajímavá.

    V každém zobrazení jsou kontroly zabezpečení seřazené podle **závažnosti**. Kliknutím na konkrétní kontrolu zabezpečení zobrazíte podokno podrobností s **popisem**, postupem jeho **opravte** a dalšími souvisejícími informacemi, jako je třeba **dopad** nebo **Srovnávací test**.

## <a name="azure-defender-for-sql-alerts"></a>Výstrahy Azure Defenderu pro SQL
Výstrahy jsou generovány neobvyklými a potenciálně škodlivými pokusy o přístup k počítačům SQL nebo jejich zneužití. Tyto události mohou aktivovat výstrahy zobrazené na [stránce s odkazem výstrahy](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Prozkoumat a prozkoumat výstrahy zabezpečení

Výstrahy pro Azure Defender pro SQL jsou k dispozici na stránce výstrahy Security Center, na kartě zabezpečení prostředku, na [řídicím panelu Azure Defenderu](azure-defender-dashboard.md)nebo prostřednictvím přímého odkazu v e-mailech s výstrahami.

1. Chcete-li zobrazit výstrahy, vyberte možnost **výstrahy zabezpečení** z nabídky Security Center a vyberte výstrahu.

1. Výstrahy jsou navržené tak, aby byly samostatné, s podrobnými kroky pro nápravu a informace o šetření v každé z nich. Můžete prozkoumat další možnosti pomocí dalších možností Azure Security Center a Azure Sentinel pro širší zobrazení:

    * Povolit funkci auditování SQL Server pro další šetření. Pokud jste uživatelem Sentinel Azure, můžete nahrát protokoly auditování SQL z událostí protokolu zabezpečení Windows do Sentinel a využít bohatých prostředí pro šetření. [Přečtěte si další informace o SQL Server auditování](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Chcete-li zlepšit stav zabezpečení, použijte doporučení Security Center pro hostitelský počítač uvedený v každé výstraze. Tím se sníží rizika budoucích útoků. 

    [Přečtěte si další informace o správě a reakci na výstrahy](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Další kroky

Související materiály najdete v následujícím článku:

- [Výstrahy zabezpečení pro SQL Database a Azure synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Nastavení e-mailových oznámení pro upozornění zabezpečení](security-center-provide-security-contact-details.md)
- [Další informace o Sentinel Azure](../sentinel/index.yml)