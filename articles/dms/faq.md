---
title: Nejčastější dotazy o použití služby Azure databáze migrace | Microsoft Docs
description: Přečtěte si nejčastější dotazy o použití služby Azure databáze migrace k provedení migrace databáze.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: 85052e1363ebbfe21cd7d6d5b3720f79cec7c417
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="faq-about-using-the-azure-database-migration-service"></a>Nejčastější dotazy o použití služby Azure databáze migrace
V tomto článku jsou uvedené nejčastější dotazy týkající se používání služby Azure databáze migrace společně s související odpovědi.

### <a name="q-what-is-azure-database-migration-service"></a>OTÁZKY. Co je služba migrace databáze Azure?
Služba Azure databáze migrace je plně spravovaná služba navržených k povolení bezproblémové migrace z více zdrojů databáze do platformy Azure dat s minimálními výpadky. Služba je ve verzi Public Preview, se zaměřuje na úsilí vývoj aktuálně:
- Spolehlivost a výkon.
- Iterační přidání dvojic cílového zdroje.
- Trvalá investice do bez třecí migrace.

### <a name="q-what-source-target-pairs-does-the-azure-database-migration-service-currently-support"></a>OTÁZKY. Jaké zdroje cíl páry služba migrace databáze Azure aktuálně podporuje?
Služba ve verzi Public Preview aktuálně podporuje migrace ze systému SQL Server do Azure SQL Database a můžete přejít na portálu Azure nyní chcete začít používat službu Azure databáze migrace pro tento scénář. Další zdroje cíl páry, jako je například SQL Server na instanci spravované databáze Azure SQL Server a Oracle do Azure SQL Database jsou dostupné prostřednictvím omezené privátní Preview verzi. Pro možnost zapojit se do omezené privátní Preview verzi z těchto scénářů, zaregistrujte si [zde](https://sqldatabase-migrationpreview.azurewebsites.net/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-other-microsoft-database-migration-tools-such-as-the-database-migration-assistant-dma-or-sql-server-migration-assistant-ssma"></a>OTÁZKY. Jak služba Azure databáze migrace porovnat jiných nástrojů migrace databáze Microsoft, například databáze migrace Assistant (DMA) nebo SQL asistent migrace Server (SSMA)?
Služba Azure databáze migrace je upřednostňovanou metodou pro migrace databáze do služby Microsoft Azure ve velkém měřítku. Další podrobnosti o tom, jak službu Azure databáze migrace porovnává dalšího softwaru společnosti Microsoft databáze nástroje pro migraci a doporučení o používání služby pro různé scénáře, najdete v blogu příspěvků [rozlišení Microsoft databáze Nástroje pro migraci a služby](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

### <a name="q-how-does-the-azure-database-migration-service-compare-to-the-azure-migrate-offering"></a>OTÁZKY. Jak služba Azure databáze migrace porovnat nabídku Azure migrovat?
Migrace služby Azure pomáhá s migrací místní virtuální počítače Azure IaaS. Služba vyhodnocuje vhodnosti migrace a výkonu na základě velikosti a poskytuje odhadované náklady na spuštění místní virtuální počítače v Azure. Azure migrací jsou užitečné pro navýšení a shift migrace místních virtuálních počítačů na základě úloh virtuálních počítačů Azure IaaS. Ale na rozdíl od migrace službu Azure databáze Azure migrace není služby migrace specializované databáze nabídky pro platformy Azure PaaS relační databáze jako je například Azure SQL Database nebo SQL Azure nebo Azure SQL Database spravované Instance.

### <a name="q-what-is-a-summary-of-the-steps-required-to-use-the-azure-database-migration-service-to-perform-a-database-migration"></a>OTÁZKY. Co je uveden seznam kroků potřebných k provedení migrace databáze používat službu Azure databáze migrace?
Během migrace typické, je databáze můžete:
1.  Vytvoření cílové databáze.
2.  Migrace schéma databáze pomocí [databáze migrace pomocníka](https://www.microsoft.com/en-us/download/details.aspx?id=53595).
3.  Vytvoření instance služby migrace databáze Azure.
4.  Vytvoření projektu migrace zdrojové databáze, cílové databáze a tabulky, které chcete migrovat.
5.  Spustit úplné načtení.
6.  Vyberte následné ověřování.
7.  Proveďte ruční přepnutí vašeho produkčního prostředí k nové databázi založené na cloudu. 

### <a name="q-what-are-the-prerequisites-for-using-the-azure-database-migration-service"></a>OTÁZKY. Jaké jsou požadavky pro používání služby Azure databáze migrace?
Existuje několik předpokladů, vyžaduje se pro zajištění, že služba migrace databáze Azure běží bez problémů při provádění migrace databáze. Některé požadované součásti aplikaci ve všech scénářích (cílový Zdroj páry) podporována službou, zatímco jiné požadavky, které jsou jedinečné pro konkrétní scénář.
Požadavky Azure služba migrace databáze, které jsou společné pro všechny podporované scénáře migrace patří potřeba:
- Vytvoření virtuální sítě pro službu Azure databáze migrace pomocí modelu nasazení Azure Resource Manager, které poskytuje připojení site-to-site k vaší místní zdrojové servery pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Ujistěte se, že vaše Azure virtuální síť (VNET) skupinu zabezpečení sítě pravidla proveďte bloku následující komunikace porty 443, 53, 9354, 445, 12000. Další podrobnosti o filtrování provozu NSG virtuální síť Azure, najdete v článku [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Pokud používáte zařízení brány firewall před vaší zdrojové databáze, musíte přidat pravidla firewallu povolující službu Azure databáze migrace pro přístup k databází zdroje pro migraci.
 
Seznam všech požadovaných součástí pro pokouší konkrétní migrace scénáře využívající službu Azure databáze migrace, naleznete v části Související kurzy ve službě Azure databáze migrace [dokumentace](https://docs.microsoft.com/azure/dms/dms-overview) na docs.microsoft.com.

### <a name="q-how-do-i-find-the-ip-address-for-the-azure-database-migration-service-so-that-i-can-create-an-allow-list-for-the-firewall-rules-used-to-access-my-source-database-for-migration"></a>OTÁZKY. Jak najdu IP adresu pro službu Azure databáze migrace tak, aby seznam povolených pro pravidla brány firewall pro přístup k databázi mé zdroje pro migraci můžete vytvořit?
Musíte přidat pravidla brány firewall umožňuje službě Azure Database migrace k přístupu k vaší zdrojové databáze pro migraci. IP adresa pro službu je dynamické, ale pokud používáte Express Route, je tato adresa soukromě přiřazená ve vaší podnikové síti. Nejjednodušší způsob, jak identifikovat odpovídající IP adresy, které se má hledat ve stejné skupině prostředků jako zřízené prostředku Azure databáze migrace služby najít přidružené síťové rozhraní. Název prostředku síťové rozhraní obvykle začíná předponu síťový adaptér a následovaný jedinečný znak a číslo posloupnosti, příklad jj6tnztnmarpsskr82rbndyp síťový adaptér. Pokud vyberete tento prostředek rozhraní sítě, se zobrazí IP adresu, která musí být součástí v seznamu povolených na Přehled prostředků Azure stránky portálu.

Můžete také zahrnovat port zdroj, který SQL Server naslouchá na seznamu povolených. Ve výchozím nastavení je port 1433, ale zdroje systému SQL Server může být nakonfigurována tak, aby naslouchala na i další porty. V takovém případě musíte zahrnout tyto porty na seznamu povolených. Můžete určit port, který SQL Server naslouchá na pomocí dotazu na zobrazení dynamické správy:

```sql
    SELECT DISTINCT 
        local_tcp_port 
    FROM sys.dm_exec_connections 
    WHERE local_tcp_port IS NOT NULL
```
Můžete také určit port, který je SQL Server přijímá pomocí dotazu v protokolu chyb systému SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on' 
    GO
```

### <a name="q-are-there-any-recommendations-for-optimizing-the-performance-of-the-azure-database-migration-service"></a>OTÁZKY. Existují žádná doporučení pro optimalizaci výkonu databáze migrace služby Azure?
Můžete provést několik akcí pro urychlení migrace databáze pomocí služby:
- Když vytvoříte instanci služby lze umožnit službě chcete využít výhod více Vcpu paralelizace a rychlejší přenos dat, použijte více procesoru obecné účely cenová úroveň.
- Dočasně rozšiřování škálování využívajících vaše cílová instance Azure SQL Database na vrstvě | Premium SKU během operace migrace dat. Chcete-li minimalizovat Azure SQL Database omezení, která může mít vliv na činnosti přenosu dat při použití SKU nižší úrovně.

### <a name="q-how-do-i-set-up-an-azure-virtual-network"></a>OTÁZKY. Jak mám nastavit virtuální síť Azure?
Při více Microsoft návodů, které může vás provede procesem nastavení o virtuální síť Azure, se zobrazí v oficiální dokumentaci v článku [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="q-why-is-my-azure-database-migration-service-unavailable-or-stopped"></a>OTÁZKY. Není k dispozici nebo se zastavil, proč je můj služba migrace databáze Azure?
Pokud uživatel explicitně zastaví službu migrace databáze Azure (DMS) nebo pokud je služba neaktivní po dobu 24 hodin, služba bude v zastaven nebo automaticky pozastaveném stavu. V každém případě bude služba není k dispozici a je v zastaveném stavu.  Chcete-li obnovit active migrace, restartujte službu.

### <a name="q-where-can-i-leave-feedback-about-the-azure-database-migration-service"></a>OTÁZKY. Kde je můžete nechat zpětnou vazbu o službě Azure databáze migrace?
Chceme slyšet od vás. Pošlete prosím všechny zpětnou vazbu a / nápady máte o službě Azure databáze migrace prostřednictvím User Voice [zde](https://feedback.azure.com/forums/906100-azure-database-migration-service).

## <a name="next-steps"></a>Další postup
Přehled služby Azure databáze migrace a místní dostupnosti během verzi Public Preview, najdete v článku [co je Azure databáze migrace služby Preview](dms-overview.md). 
