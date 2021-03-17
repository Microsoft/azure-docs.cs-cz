---
title: Rozšířená ochrana před internetovými útoky – Azure Database for MariaDB
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 98eacad1b8a10be175846401eaa733c888f765e1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661615"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Rozšířená ochrana před internetovými útoky

Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB detekuje aktivity neobvyklé, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití.

> [!IMPORTANT]
> Rozšířená ochrana před internetovými útoky je ve verzi Public Preview.

Rozšířená ochrana před internetovými útoky je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com). Tato funkce je k dispozici pro Pro obecné účely a paměťově optimalizované servery.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .


## <a name="what-is-advanced-threat-protection"></a>Co je rozšířená ochrana před internetovými útoky?

Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelé dostanou upozornění na podezřelé databázové aktivity a potenciální ohrožení zabezpečení a také neobvyklé přístup k databázi a vzor dotazů. Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB usnadňuje řešení potenciálních hrozeb pro databázi, aniž by musel být odborníkem na zabezpečení nebo spravovali pokročilé systémy monitorování zabezpečení. 

![Koncept rozšířené ochrany před internetovými útoky](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky 
Rozšířená ochrana před internetovými útoky pro Azure Database for MariaDB detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MariaDB, kde se někdo přihlásil k serveru Azure Database for MariaDB z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MariaDB, kde se někdo přihlásil k serveru z neobvyklého datového centra Azure, které bylo na tomto serveru vidět během posledního období. V některých případech výstraha detekuje legitimní akci (novou aplikaci v Azure, Power BI). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MariaDB, kde se někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (Azure Database for MariaDB uživatel). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Azure Database for MariaDB přihlašovací údaje hrubou silou**: Tato výstraha se aktivuje, když dojde k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](../security-center/security-center-introduction.md)
* Další informace o cenách najdete na stránce s [cenami Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/) . 
* Konfigurace [Azure Database for MariaDB rozšířené ochrany před internetovými útoky](howto-database-threat-protection-portal.md) pomocí Azure Portal