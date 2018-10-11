---
title: Rozšířená ochrana před internetovými útoky – Azure Database for MySQL | Dokumentace Microsoftu
description: Rozšířená ochrana před internetovými útoky detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze.
services: mysql
author: bolzmj
manager: kfile
ms.service: mysql
ms.topic: article
ms.date: 09/20/2018
ms.author: mbolz
ms.openlocfilehash: e123acd09e4cbfdbaf0531db72598dbd478fa1c9
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069382"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL – Rozšířená ochrana před internetovými útoky

Služba Advanced Threat Protection pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití.

Rozšířená ochrana před internetovými útoky je součástí nabídky Rozšířené zabezpečení dat, která je jednotný balíček pro pokročilé možnosti zabezpečení. Rozšířená ochrana před internetovými útoky jde přistupovat a spravovat přes [webu Azure portal](https://portal.azure.com) a je aktuálně ve verzi preview.

> [!NOTE]
> Funkce Advanced Threat Protection je **není** dostupná v oblastech suverénních cloudů a následující Azure government: US Gov Texas, USA Arizona, USA (gov) (gov) – Iowa, USA, (gov) – Virginia, US DoD – východ, US DoD – střed, Německo – střed, Německo Severní Čína – východ, Čína – východ 2. Navštivte prosím [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/) pro obecnou dostupnost.
> 

## <a name="what-is-advanced-threat-protection"></a>Co je Advanced Threat Protection?

Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL poskytuje novou vrstvu zabezpečení, která zákazníkům umožňuje detekovat a reagovat na potenciální hrozby, jak se objeví díky poskytování upozornění zabezpečení na neobvyklé aktivity. Uživatelé obdrží upozornění při podezřelých databázových aktivitách, potenciálních ohroženích zabezpečení, i neobvyklé databázové vzory přístupu a dotazy. Rozšířená ochrana před internetovými útoky pro databázi Azure pro MySQL integruje výstrahy se službou [Azure Security Center](https://azure.microsoft.com/services/security-center/), který obsahuje podrobnosti o podezřelé aktivitě a doporučuje akce na tom, jak zkoumat a zmírnit hrozby. Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL usnadňuje řešení potenciálních ohrožení databáze, aniž byste museli být odborné zabezpečení nebo spravovat pokročilé zabezpečení systémy pro monitorování. 

![Koncept Advanced Threat Protection](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Upozornění rozšířené ochrany před internetovými útoky 
Rozšířená ochrana před internetovými útoky pro Azure Database for MySQL detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a ji můžete spustit následující upozornění:
- **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for MySQL serveru, když někdo přihlásil k Azure Database for MySQL serveru z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for MySQL serveru, když někdo přihlásil k serveru z neobvyklého datového centra Azure, která už na tomto serveru v nedávné době. V některých případech výstraha detekuje legitimní akci (nová aplikace v Azure, Power BI, Azure Database v editoru dotazů MySQL). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
- **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k Azure Database for MySQL serveru, když někdo přihlásil k serveru pomocí neobvyklého objektu zabezpečení (– Azure Database for MySQL uživatele). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
- **Přístup z potenciálně škodlivé aplikace:** Tato výstraha se aktivuje, pokud je pro přístup k databázi použita potenciálně škodlivá aplikace. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
- **Hrubou silou Azure databázi pro přihlašovací údaje MySQL**: Tato výstraha se aktivuje po neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="next-steps"></a>Další postup

* Další informace o [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Další informace o cenách najdete v tématu [– Azure Database for MySQL ceník](https://azure.microsoft.com/pricing/details/mysql/) 
* Konfigurace [– Azure Database for MySQL Advanced Threat Protection](howto-database-threat-protection-portal.md) pomocí webu Azure portal  
