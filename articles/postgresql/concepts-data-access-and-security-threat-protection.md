---
title: Rozšířená ochrana před internetovými útoky – Azure Database for PostgreSQL – jeden server
description: Přečtěte si, jak používat rozšířenou ochranu před internetovými útoky k detekci neobvyklé databázových aktivit, které označují potenciální bezpečnostní hrozby databáze
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fa136a15e4fa034307e16ef50c2b16060c5af0d2
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490199"
---
# <a name="advanced-threat-protection-in-azure-database-for-postgresql---single-server"></a>Rozšířená ochrana před internetovými útoky v Azure Database for PostgreSQL – jeden server

Advanced Threat Protection pro službu Azure Database for PostgreSQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

> [!NOTE]
> Rozšířená ochrana před internetovými útoky je ve verzi Public Preview.

Ochrana před hrozbami je součástí nabídky rozšířené ochrany před internetovými útoky (ATP), což je jednotný balíček pro pokročilé funkce zabezpečení. Rozšířená ochrana před internetovými útoky je dostupná a spravovaná prostřednictvím [Azure Portal](https://portal.azure.com) nebo pomocí [REST API](/rest/api/postgresql). Tato funkce je k dispozici pro Pro obecné účely a paměťově optimalizované servery.

> [!NOTE]
> Funkce rozšířené ochrany před internetovými útoky **není dostupná v** těchto oblastech cloudu Azure a v svrchovaném cloudu: US Gov – Texas, US Gov – Arizona, US gov – Iowa, US, gov) – virginia, US DoD – východ, US DoD – střed, Německo Central, Německo – sever, Čína – východ, Čína – východ 2. Pokud chcete získat obecnou dostupnost produktu, navštivte prosím [produkty dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) .

## <a name="what-is-advanced-threat-protection"></a>Co je rozšířená ochrana před internetovými útoky?

Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje rozpoznávat a reagovat na potenciální hrozby při jejich výskytu tím, že poskytuje výstrahy zabezpečení pro aktivity neobvyklé. Uživatelé dostanou upozornění na podezřelé databázové aktivity a potenciální ohrožení zabezpečení a také neobvyklé přístup k databázi a vzor dotazů. Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL integruje výstrahy s [Azure Security Center](https://azure.microsoft.com/services/security-center/), což zahrnuje podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit. Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL usnadňuje řešení potenciálních hrozeb pro databázi, aniž by musel být odborníkem na zabezpečení nebo spravovali pokročilé systémy monitorování zabezpečení. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Koncept rozšířené ochrany před internetovými útoky":::

## <a name="advanced-threat-protection-alerts"></a>Výstrahy rozšířené ochrany před internetovými útoky 
Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL detekuje aktivity neobvyklé indikující neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití a může aktivovat následující výstrahy:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for PostgreSQL, kde se někdo přihlásil k serveru Azure Database for PostgreSQL z neobvyklého zeměpisného umístění. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for PostgreSQL, kde se někdo přihlásil k serveru z neobvyklého datového centra Azure, které bylo na tomto serveru vidět během posledního období. V některých případech výstraha detekuje legitimní akci (novou aplikaci v Azure, Power BI Azure Database for PostgreSQL editoru dotazů). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k serveru Azure Database for PostgreSQL, kde se někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (Azure Database for PostgreSQL uživatel). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Azure Database for PostgreSQL přihlašovací údaje hrubou silou**: Tato výstraha se aktivuje, když dojde k neobvyklému vysokému počtu neúspěšných přihlášení s různými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Security Center](../security-center/security-center-introduction.md)
* Další informace o cenách najdete na stránce s [cenami Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/) . 
* Konfigurace [Azure Database for PostgreSQL rozšířené ochrany před internetovými útoky](howto-database-threat-protection-portal.md) pomocí Azure Portal