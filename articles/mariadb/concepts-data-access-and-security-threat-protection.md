---
title: Pokročilá ochrana před hrozbami – databáze Azure pro MariaDB
description: Pokročilá ochrana před internetovými zprávami detekuje neobvyklé databázové aktivity označující potenciální ohrožení zabezpečení databáze.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8485907eba75f80c8f0ed4fd0cc7368c6147b9fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532174"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Advanced Threat Protection

Pokročilá ochrana před internetovými hrozbami pro Azure Database for MariaDB detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

> [!IMPORTANT]
> Rozšířená ochrana před hrozbami je ve verzi Public Preview.

Advanced Threat Protection je součástí nabídky Advanced Data Security, což je jednotný balíček pro pokročilé možnosti zabezpečení. Pokročilá ochrana před internetovými zprávami je přístupná a spravovaná prostřednictvím [portálu Azure](https://portal.azure.com). Tato funkce je k dispozici pro servery optimalizované pro obecné účely a paměť.

> [!NOTE]
> Funkce Advanced Threat Protection **není** dostupná v následujících oblastech vlády Azure a suverénního cloudu: US Gov Texas, US Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Obecné informace o dostupnosti produktů naleznete [na produktech dostupných podle regionu.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="what-is-advanced-threat-protection"></a>Co je pokročilá ochrana před hrozbami?

Pokročilá ochrana před internetovými hrozbami pro Azure Database for MariaDB poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje zjišťovat potenciální hrozby a reagovat na ně tím, že poskytuje výstrahy zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění na podezřelé databázové aktivity a potenciální chyby zabezpečení, stejně jako neobvyklé přístup k databázi a dotazy vzory. Pokročilá ochrana před internetovými technologiemi pro Azure Database for MariaDB integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak prozkoumat a zmírnit hrozbu. Pokročilá ochrana před hrozbami pro Azure Database for MariaDB usnadňuje řešení potenciálních hrozeb pro databázi bez nutnosti být odborníkem na zabezpečení nebo spravovat pokročilé systémy monitorování zabezpečení. 

![Koncept pokročilé ochrany před hrozbami](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Pokročilá upozornění na ochranu před hrozbami 
Pokročilá ochrana před internetovými hrozbami pro Azure Database for MariaDB detekuje neobvyklé aktivity označující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k databázi Azure pro server MariaDB, kde se někdo přihlásil k databázi Azure pro server MariaDB z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k databázi Azure pro server MariaDB, kde se někdo přihlásil k serveru z neobvyklého datového centra Azure, které se na tomto serveru vidělo během nedávného období. V některých případech výstraha zjistí legitimní akci (nová aplikace v Azure, Power BI). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého jistiny**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k databázi Azure pro server MariaDB, kde se někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (Azure Database for MariaDB user). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Hrubá síla Azure Database pro mariadb přihlašovací údaje**: Tato výstraha se aktivuje, když je abnormální vysoký počet neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete na [stránce Azure Database for MariaDB Pricing .](https://azure.microsoft.com/pricing/details/mariadb/) 
* Konfigurace [azure databáze pro MariaDB Advanced Threat Protection](howto-database-threat-protection-portal.md) pomocí portálu Azure  
