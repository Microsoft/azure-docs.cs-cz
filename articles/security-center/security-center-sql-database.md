---
title: Služba Azure Security Center a Azure SQL Database | Microsoft Docs
description: V tomto článku se dozvíte, jak vám Security Center může pomáhat zabezpečit databáze v Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60704509"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Služba Azure Security Center a Azure SQL Database
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

V tomto článku se dozvíte, jak vám Security Center může pomáhat zabezpečit databáze v Azure SQL Database.

## <a name="why-use-security-center"></a>Proč používat Security Center?
Security Center pomáhá chránit data v SQL Database tím, že poskytuje přehled o zabezpečení všech serverů a databází. Pomocí Security Center můžete:

* Definujte zásady pro SQL Database šifrování a auditování.
* Monitorujte zabezpečení SQL Database prostředků napříč všemi vašimi předplatnými.
* Rychle identifikujte a napravte problémy se zabezpečením.
* Integrujte výstrahy z [Azure SQL Database detekce hrozeb](../sql-database/sql-database-threat-detection.md).

Kromě ochrany vašich SQL Databasech prostředků poskytuje Security Center také monitorování a správu zabezpečení pro virtuální počítače Azure, Cloud Services, App Services, virtuální sítě a další. Další informace o Security Center [najdete tady](security-center-intro.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. V rámci vašeho předplatného je povolená bezplatná úroveň Security Center. Další informace o úrovních Free a Standard Security Center najdete v tématu [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

Security Center podporuje přístup na základě rolí. Další informace o řízení přístupu na základě role (RBAC) v Azure najdete v tématu [Azure Active Directory Access Control na základě rolí](../role-based-access-control/role-assignments-portal.md). Security Center Nejčastější dotazy obsahují informace o [tom, jak jsou v Security Center zpracovávána oprávnění](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Přístup ke službě Security Center
Služba Security Center je přístupná prostřednictvím [portálu Azure](https://azure.microsoft.com/features/azure-portal/). [Přihlaste se k portálu](https://portal.azure.com/) a vyberte **možnost Security Center**.

![Možnost Security Center][1]

Otevře se okno **Security Center** .
![Okno Security Center][2]

## <a name="set-security-policy"></a>Nastavení zásad zabezpečení
Zásady zabezpečení definují sadu ovládacích prvků, které jsou doporučeny pro prostředky v rámci zadaného předplatného nebo skupiny prostředků. V Security Center definujete zásady pro vaše předplatná nebo skupiny prostředků podle potřeb zabezpečení vaší společnosti a typu aplikací nebo citlivosti dat v jednotlivých předplatných.

Můžete nastavit zásadu, která bude zobrazovat doporučení pro auditování SQL a šifrování transparentních dat SQL (TDE).

* Když zapnete **auditování SQL a detekci hrozeb**, Security Center doporučuje, aby bylo auditování přístupu do Azure Database povolené pro účely dodržování předpisů, rozšířeného zjišťování a vyšetřování.
* Když zapnete **transparentní šifrování dat SQL**, Security Center doporučuje, aby bylo šifrování v klidovém prostředí povolené pro vaše Azure SQL Database, přidružené zálohy a soubory protokolů transakcí.

Pokud chcete nastavit zásady zabezpečení, vyberte dlaždici **zásady** v okně Security Center. V okně **zásady zabezpečení** vyberte předplatné, pro které chcete zásady zabezpečení povolit. Vyberte **Zásady prevence** a zapněte doporučení zabezpečení, která chcete v tomto předplatném použít.
![Zásady zabezpečení][3]

Další informace najdete v tématu [nastavení zásad zabezpečení](tutorial-security-policy.md).

## <a name="manage-security-recommendation"></a>Spravovat doporučení zabezpečení
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

Po nastavení zásad zabezpečení Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikoval potenciální ohrožení zabezpečení. Doporučení se zobrazí ve formátu tabulky, kde každý řádek představuje jedno konkrétní doporučení. Následující tabulku použijte jako pomůcku, která vám pomůže porozumět dostupným doporučením pro Azure SQL Database a podle toho, co se týká jednotlivých doporučení. Výběrem doporučení přejdete na článek, který vysvětluje, jak implementovat doporučení v Security Center.

| Doporučení | Popis |
| --- | --- |
| [Povolení auditování a detekce hrozeb na serverech SQL](security-center-enable-auditing-on-sql-servers.md) |Doporučuje zapnout auditování a detekci hrozeb pro SQL Database servery. (Pouze služba SQL Database. Neobsahuje Microsoft SQL Server běžící na vašich virtuálních počítačích.) |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje zapnout auditování a detekci hrozeb pro databáze SQL Database. (Pouze služba SQL Database. Neobsahuje Microsoft SQL Server běžící na vašich virtuálních počítačích.) |
| [Povolení transparentního šifrování dat](security-center-enable-transparent-data-encryption.md) |Doporučuje, abyste povolili šifrování pro databáze SQL. (Pouze služba SQL Database.) |

Pokud chcete zobrazit doporučení pro prostředky Azure, vyberte dlaždici **doporučení** v okně Security Center. V okně **doporučení** vyberte doporučení pro zobrazení podrobností. V tomto příkladu vybereme možnost **Povolit auditování & detekci hrozeb na serverech SQL**.

![Doporučení][4]

Jak vidíte níže, Security Center zobrazí servery SQL, kde nejsou povolené auditování a detekce hrozeb. Po zapnutí auditování můžete nakonfigurovat nastavení detekce hrozeb a nastavení e-mailu, aby se přijímaly výstrahy zabezpečení. Detekce hrozeb vás upozorní, když detekuje neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze. Výstrahy se zobrazují na řídicím panelu Security Center.
![Auditování a detekce hrozeb][5]

Postupujte podle kroků v [části SQL Database detekce hrozeb v Azure Portal](../sql-database/sql-database-threat-detection-portal.md) zapněte a nakonfigurujte detekci hrozeb a nakonfigurujte seznam e-mailů, které budou dostávat výstrahy zabezpečení při detekci aktivit neobvyklé.

Další informace o doporučeních najdete v tématu [Správa doporučení zabezpečení](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorovat stav zabezpečení
Po povolení [zásad zabezpečení](tutorial-security-policy.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  Stav zabezpečení prostředků můžete zobrazit na dlaždici **stav zabezpečení prostředku** . Když kliknete na **data** na dlaždici **stav zabezpečení prostředků** , otevře se okno **datové prostředky** s doporučeními SQL pro problémy, jako je auditování a transparentní šifrování dat, které není povolené. Také obsahuje doporučení pro obecný stav databáze.
![Stav zabezpečení prostředku][6]

Další informace najdete v tématu [monitorování stavu zabezpečení](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Spravovat a reagovat na výstrahy zabezpečení
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z [detekce hrozeb Azure SQL](../sql-database/sql-database-threat-detection.md)a také dalších prostředků Azure, zjišťuje skutečné hrozby a snižuje falešně pozitivní výsledky. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

Výstrahy zobrazíte tak, že v okně Security Center vyberete dlaždici **výstrahy zabezpečení** . V okně **výstrahy zabezpečení** výběrem výstrahy zobrazíte další informace o událostech, které výstrahu aktivovaly, a o tom, jaké kroky je potřeba provést k nápravě útoku. V tomto příkladu vybereme možnost **potenciálního INJEKTÁŽE SQL**.
![Výstrahy zabezpečení][7]

Jak vidíte níže, Security Center poskytuje další podrobnosti, které nabízejí přehled o tom, co aktivovalo výstrahu, cílový prostředek, pokud se použije zdrojová IP adresa, a doporučení k nápravě.
![Potenciální injektáže SQL][8]

Další informace najdete v tématu [Správa a reakce na výstrahy zabezpečení](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Další postup
* [Nejčastější](security-center-faq.md) dotazy k Security Center – Přečtěte si nejčastější dotazy týkající se používání služby.
* [Průvodce plánováním a provozem Security Center](security-center-planning-and-operations-guide.md) – řiďte se sadou kroků a úloh, které optimalizují použití Security Center na základě požadavků na zabezpečení a modelu správy cloudu ve vaší organizaci.
* [Security Center zabezpečení dat](security-center-data-security.md) – Zjistěte, jak Security Center shromažďuje a zpracovává data o prostředcích Azure, včetně informací o konfiguraci, metadatech, protokolů událostí, souborů výpisu stavu systému a dalších.
* [Zpracování incidentů zabezpečení](security-center-incident.md) – Naučte se používat funkce výstrah zabezpečení v Security Center, které vám pomůžou při zpracovávání incidentů zabezpečení.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
