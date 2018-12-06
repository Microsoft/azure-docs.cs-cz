---
title: Služba Azure Security Center a Azure SQL Database | Dokumentace Microsoftu
description: Tento článek popisuje, jak Security Center vám může pomoci zabezpečit vaše databáze ve službě Azure SQL Database.
services: sql-database
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 4535b141d177e49567d99a88d0c3883f5ddf746a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956802"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Služba Azure Security Center a Azure SQL Database
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Tento článek popisuje, jak Security Center vám může pomoci zabezpečit vaše databáze ve službě Azure SQL Database.

## <a name="why-use-security-center"></a>Proč používat Security Center?
Security Center pomáhá chránit data ve službě SQL Database tím, že poskytuje vhled do zabezpečení všech vašich serverů a databází. Se službou Security Center můžete:

* Můžete Definujte zásady pro šifrování SQL Database a auditování.
* Monitorování zabezpečení SQL Database prostředků napříč všemi předplatnými.
* Rychle identifikovat a opravit problémy se zabezpečením.
* Integrujte upozornění z [detekce hrozeb Azure SQL Database](../sql-database/sql-database-threat-detection.md).

Kromě ochrany vašich prostředků SQL Database, Security Center také poskytuje monitorování zabezpečení a správu pro Azure virtual machines, Cloud Services, App Services, virtuální sítě a další. Další informace o službě Security Center [tady](security-center-intro.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Úroveň Free služby Security Center je povolená s vaším předplatným. Další informace o Security Center na úrovních Free a Standard najdete v tématu [ceny Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Security Center podporuje přístup na základě rolí. Další informace o řízení přístupu na základě role (RBAC) v Azure najdete v tématu [řízení přístupu na základě Role v Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Security Center – nejčastější dotazy najdete informace o [zpracování oprávnění ve službě Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Přístup ke službě Security Center
Služba Security Center je přístupná prostřednictvím [portálu Azure](https://azure.microsoft.com/features/azure-portal/). [Přihlaste se k portálu](https://portal.azure.com/) a vyberte **Security Center možnost**.

![Security Center – možnost][1]

**Security Center** se otevře okno.
![Okno Security Center][2]

## <a name="set-security-policy"></a>Nastavení zásad zabezpečení
Zásady zabezpečení definují sadu ovládacích prvků, které se doporučují pro prostředky v rámci daného předplatného nebo skupiny prostředků. Ve službě Security Center určíte zásady pro vaše předplatná nebo skupiny prostředků podle potřeb zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Můžete nastavit zásadu, která zobrazit doporučení pro účely auditování SQL a SQL transparentní šifrování dat (TDE).

* Když zapnete **SQL auditování a detekce hrozeb**, Security Center doporučuje pro dodržování předpisů, rozšířeného zjišťování a vyšetřování účely musí být povoleno auditování přístupu ke službě Azure Database.
* Když zapnete **transparentní šifrování dat SQL**, Security Center doporučuje šifrování v klidovém stavu povolit pro Azure SQL Database, přidružené zálohy a soubory protokolů transakcí.

Pokud chcete nastavit zásady zabezpečení, vyberte **zásady** dlaždici na okno Security Center. Na **zásady zabezpečení** okno, vyberte předplatné, ve kterém chcete zásadu zabezpečení povolit. Vyberte **zásady ochrany před únikem informací** a zapnout **na** doporučení zabezpečení, které chcete v tomto předplatném používat.
![Zásady zabezpečení][3]

Další informace najdete v tématu [nastavení zásad zabezpečení](security-center-azure-policy.md).

## <a name="manage-security-recommendation"></a>Správa doporučení zabezpečení
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

Jakmile nastavíte zásadu zabezpečení, Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Doporučení jsou zobrazené v tabulkovém formátu, přičemž každý řádek představuje jedno konkrétní doporučení. Následující tabulku použijte jako odkaz vám pomůžou pochopit dostupné doporučení pro Azure SQL Database a co jednotlivá doporučení dělá, když je použijete. Výběr doporučení vás nasměruje na článek, který vysvětluje, jak dané doporučení implementovat v Security Center.

| Doporučení | Popis |
| --- | --- |
| [Povolení auditování a detekce hrozeb na SQL serverech](security-center-enable-auditing-on-sql-servers.md) |Doporučuje, zapněte auditování a detekce hrozeb pro servery SQL Database. (Pouze služby SQL Database. Nezahrnuje Microsoft SQL Server běžící na virtuálních počítačích.) |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje, zapněte auditování a detekci hrozeb u databází SQL Database. (Pouze služby SQL Database. Nezahrnuje Microsoft SQL Server běžící na virtuálních počítačích.) |
| [Povolení transparentního šifrování dat](security-center-enable-transparent-data-encryption.md) |Doporučuje povolit šifrování pro databáze SQL. (Služba SQL Database pouze.) |

Pokud chcete zobrazit doporučení pro vaše prostředky Azure, vyberte **doporučení** dlaždici na okno Security Center. Na **doporučení** okno, vyberte na doporučení zobrazit podrobnosti. V tomto příkladu vybereme **povolit auditování a detekce hrozeb na SQL serverech**.

![Doporučení][4]

Jak je znázorněno níže, Security Center zobrazuje SQL servery, kde nejsou povolené auditování a detekce hrozeb. Po zapnutí auditování, můžete nakonfigurovat nastavení detekce hrozeb a nastavení e-mailu pro výstrahy zabezpečení. Detekce hrozeb vás upozorní, když se zjistí neobvyklé databázové aktivity, které indikují potenciální ohrožení zabezpečení databáze. Výstrahy jsou zobrazeny v řídicím panelu Security Center.
![Auditování a detekce hrozeb][5]

Postupujte podle kroků v [detekce hrozeb služby SQL Database na webu Azure Portal](../sql-database/sql-database-threat-detection-portal.md) zapnout a konfigurovat detekce hrozeb a nakonfigurujte seznam emailů, které budou přijímat výstrahy zabezpečení po detekci neobvyklých aktivit.

Další informace o doporučeních najdete v tématu [Správa doporučení zabezpečení](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-azure-policy.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  Můžete zobrazit stav zabezpečení vašich prostředků **stav zabezpečení prostředku** dlaždici. Po kliknutí na **Data** v **stav zabezpečení prostředku** dlaždici **datové prostředky** se otevře okno s SQL doporučeními pro problémy, jako je zakázané auditování nebo transparentní dat šifrování není povoleno. Také obsahuje doporučení pro obecný stav databáze.
![Stav zabezpečení prostředku][6]

Další informace najdete v tématu [sledování stavu zabezpečení](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Správa a reakce na výstrahy zabezpečení
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z [zjišťování hrozeb Azure SQL](../sql-database/sql-database-threat-detection.md), stejně jako ostatní prostředky Azure, zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

Pokud chcete zobrazit upozornění, vyberte **výstrahy zabezpečení** dlaždici na okno Security Center. Na **výstrahy zabezpečení** okno, vyberte výstrahy získat další informace o události, které se aktivuje upozornění a co, pokud existuje, krok za krokem vás potřeba provést k nápravě útoku. V tomto příkladu vybereme **útok prostřednictvím injektáže SQL potenciální**.
![Výstrahy zabezpečení][7]

Jak je znázorněno níže, Security Center poskytuje další podrobnosti, které nabízejí přehled o tom, co vyvolalo výstrahu, cílový prostředek, případnou zdrojovou IP adresu a doporučení, jak provést nápravu.
![Potenciální útok prostřednictvím injektáže SQL][8]

Další informace najdete v tématu [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Další postup
* [Security Center – nejčastější dotazy](security-center-faq.md) – přečtěte si nejčastější dotazy k používání této služby.
* [Průvodce plánováním a provozem služby Security Center](security-center-planning-and-operations-guide.md) – postupujte podle sadu kroků a úloh, abyste přizpůsobili použití služby Security Center na základě vaší organizace požadavky na zabezpečení a modelu správy cloudu.
* [Zabezpečení dat ve službě Security Center](security-center-data-security.md) – zjistěte, jak služba Security Center shromažďuje a zpracovává data o prostředcích Azure, včetně informací o konfiguraci, metadat, protokolů událostí, souborů se stavem systému a dalších.
* [Řešení bezpečnostních incidentů](security-center-incident.md) – zjistěte, jak vám funkce výstrah zabezpečení ve službě Security Center, které vám pomohou řešit bezpečnostní incidenty.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
