---
title: Advanced Threat Protection – Azure Database for PostgreSQL
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: b01238b0ebfe07b605824a75301165e477bd05e9
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54910708"
---
# <a name="azure-database-for-postgresql-advanced-threat-protection"></a>Azure Database for postgresql – Rozšířená ochrana před internetovými útoky

Služba Advanced Threat Protection pro Azure Database for PostgreSQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Ochrana před internetovými útoky je součástí nabídky rozšířené ochrany před internetovými útoky (ATP), která je jednotný balíček pro pokročilé možnosti zabezpečení. Rozšířená ochrana před internetovými útoky jde přistupovat a spravovat přes [webu Azure portal](https://portal.azure.com) a je aktuálně ve verzi preview.

> [!NOTE]
> Funkce Advanced Threat Protection je **není** dostupná v oblastech suverénních cloudů a Azure government pro následující: (Gov) – Iowa, USA, Arizona, USA (gov) US Gov Texas, USA (gov) – Virginia, US DoD – východ, US DoD – střed, Německo – střed, Německo – sever, Čína – východ, Čína – východ 2. Navštivte prosím [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) pro obecnou dostupnost.
>

> [!NOTE]
> Tato funkce je dostupná ve všech oblastech Azure, ve kterém je nasazená – Azure Database for PostgreSQL pro servery pro obecné účely a optimalizovaný pro paměť.

## <a name="what-is-advanced-threat-protection"></a>Co je Advanced Threat Protection?

Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování upozornění zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění při podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, i neobvyklé databázové vzory přístupu a dotazy. Rozšířená ochrana před internetovými útoky pro databázi Azure pro PostgreSQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivitě a doporučuje akce na tom, jak zkoumat a zmírnit hrozby. Rozšířená ochrana před internetovými útoky pro Azure Database for PostgreSQL usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé zabezpečení systémy pro monitorování. 

![Koncept Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Upozornění rozšířené ochrany před internetovými útoky 
Rozšířená ochrana před internetovými útoky pro Azure Database for postgresql – detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a ji můžete spustit následující upozornění:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for PostgreSQL server, když někdo přihlásil k Azure Database for PostgreSQL server z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for PostgreSQL server, když někdo přihlásil k serveru z neobvyklého datového centra Azure, která už na tomto serveru se v poslední době. V některých případech výstraha detekuje legitimní akci (nová aplikace v Azure, Power BI, Azure Database v editoru dotazů PostgreSQL). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for PostgreSQL server, když někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (– Azure Database for PostgreSQL uživatele). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace**: Tato výstraha se aktivuje, když se potenciálně škodlivé aplikace používá pro přístup k databázi. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Hrubou vynutit – Azure Database for postgresql – přihlašovací údaje**: Tato výstraha se aktivuje po neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [– Azure Database for PostgreSQL ceník](https://azure.microsoft.com/pricing/details/postgresql/) 
* Konfigurace [– Azure Database for PostgreSQL Advanced Threat Protection](howto-database-threat-protection-portal.md) pomocí webu Azure portal  
