---
title: Pokročilá ochrana před hrozbami – databáze Azure pro PostgreSQL – jeden server
description: Další informace o použití rozšířené ochrany před hrozbami ke zjištění neobvyklých databázových aktivit označujících potenciální ohrožení zabezpečení databáze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 3d86c76472580567c95d285924761e1714465d6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768737"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Pokročilá ochrana před hrozbami v databázi Azure pro PostgreSQL – jeden server

Advanced Threat Protection pro službu Azure Database for PostgreSQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

> [!NOTE]
> Rozšířená ochrana před hrozbami je ve verzi Public Preview.

Ochrana před hrozbami je součástí nabídky Advanced Threat Protection (ATP), což je jednotný balíček pro pokročilé možnosti zabezpečení. K rozšířené ochraně před internetovými hrozbami lze přistupovat a spravovat ji prostřednictvím [portálu Azure nebo](https://portal.azure.com) pomocí [rozhraní REST API](/rest/api/postgresql/serversecurityalertpolicies). Tato funkce je k dispozici pro servery optimalizované pro obecné účely a paměť.

> [!NOTE]
> Funkce Advanced Threat Protection **není** dostupná v následujících oblastech vlády Azure a suverénního cloudu: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Obecné informace o dostupnosti produktů naleznete [na produktech dostupných podle regionu.](https://azure.microsoft.com/global-infrastructure/services/)

## <a name="what-is-advanced-threat-protection"></a>Co je pokročilá ochrana před hrozbami?

Pokročilá ochrana před hrozbami pro Azure Database for PostgreSQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat potenciální hrozby a reagovat na ně tak, že poskytují výstrahy zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění na podezřelé databázové aktivity a potenciální chyby zabezpečení, stejně jako neobvyklé přístup k databázi a dotazy vzory. Pokročilá ochrana před internetovými hrozbami pro Azure Database for PostgreSQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak prozkoumat a zmírnit hrozbu. Pokročilá ochrana před hrozbami pro Azure Database for PostgreSQL usnadňuje řešení potenciálních hrozeb pro databázi bez nutnosti být odborníkem na zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení. 

![Koncept pokročilé ochrany před hrozbami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Pokročilá upozornění na ochranu před hrozbami 
Pokročilá ochrana před internetovými hrozbami pro databázi Azure pro PostgreSQL detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k databázi Azure pro postgreSQL server, kde se někdo přihlásil k azure databázi pro postgreSQL server z neobvyklého geografického umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for PostgreSQL, kde se někdo přihlásil k serveru z neobvyklého datového centra Azure, které se na tomto serveru vidělo během nedávného období. V některých případech výstraha zjistí legitimní akci (nová aplikace v Azure, Power BI, Azure Database for PostgreSQL Query Editor). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého jistiny**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k azure databázi pro postgreSQL server, kde se někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (Azure Database for PostgreSQL user). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Hrubá síla Azure Database pro přihlašovací údaje PostgreSQL**: Tato výstraha se aktivuje, když je abnormální vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete na [stránce Azure Database for PostgreSQL Pricing .](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurace [azure databáze pro pokročilou ochranu před internetovými hrozbami PostgreSQL](howto-database-threat-protection-portal.md) pomocí portálu Azure  
