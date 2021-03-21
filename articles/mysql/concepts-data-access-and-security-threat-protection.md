---
title: Rozšířená ochrana před internetovými útoky – Azure Database for MySQL
description: Přečtěte si o konceptech rozšířené ochrany před internetovými útoky, které detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542571"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL Rozšířená ochrana před internetovými útoky

Služba Advanced Threat Protection pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

> [!NOTE]
> Rozšířená ochrana před internetovými útoky je ve verzi Public Preview.

Rozšířená ochrana před internetovými útoky je součástí rozšířené nabídky zabezpečení dat, což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com) nebo pomocí [REST API](/rest/api/mysql). Tato funkce je k dispozici pro Pro obecné účely a paměťově optimalizované servery.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .


## <a name="what-is-advanced-threat-protection"></a>Co je rozšířená ochrana před internetovými útoky?

Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelé dostanou upozornění na podezřelé databázové aktivity a potenciální ohrožení zabezpečení a také neobvyklé přístup k databázi a vzor dotazů. Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL usnadňuje řešení potenciálních hrozeb pro databázi, aniž by musel být odborníkem na zabezpečení nebo spravovali pokročilé systémy monitorování zabezpečení. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Koncept rozšířené ochrany před internetovými útoky":::

## <a name="advanced-threat-protection-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky 
Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MySQL, kde se někdo přihlásil k serveru Azure Database for MySQL z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MySQL, kde se někdo přihlásil k serveru z neobvyklého datového centra Azure, které bylo na tomto serveru vidět během posledního období. V některých případech výstraha detekuje legitimní akci (novou aplikaci v Azure, Power BI Azure Database for MySQL editoru dotazů). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for MySQL, kde se někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (Azure Database for MySQL uživatel). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Azure Database for MySQL přihlašovací údaje hrubou silou**: Tato výstraha se aktivuje, když dojde k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](../security-center/security-center-introduction.md)
* Další informace o cenách najdete na stránce s [cenami Azure Database for MySQL](https://azure.microsoft.com/pricing/details/mysql/) . 
* Konfigurace [Azure Database for MySQL rozšířené ochrany před internetovými útoky](howto-database-threat-protection-portal.md) pomocí Azure Portal