---
title: Služba Azure Security Center a Azure SQL Database | Microsoft Docs
description: Tento článek ukazuje, jak Security Center vám může pomoct zabezpečit vaše databáze v Azure SQL Database.
services: sql-database
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: b507a62db9a80866005cb63d2008fb14612b516f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31415941"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Služba Azure Security Center a Azure SQL Database
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) pomáhá předcházet hrozbám, zjišťovat je a reagovat na ně. Poskytuje integrované bezpečnostní sledování a správu zásad ve vašich předplatných Azure, pomáhá zjišťovat hrozby, kterých byste si jinak nevšimli, a spolupracuje s řadou řešení zabezpečení.

Tento článek ukazuje, jak Security Center vám může pomoct zabezpečit vaše databáze v Azure SQL Database.

## <a name="why-use-security-center"></a>Proč používat Security Center?
Security Center pomáhá zabezpečit data v databázi SQL tím, že poskytuje přehled o zabezpečení serverů a databází. Security Center můžete:

* Definujte zásady pro šifrování SQL Database a auditování.
* Sledování zabezpečení prostředků databáze SQL ve vašich předplatných.
* Rychle identifikovat a opravit problémy se zabezpečením.
* Integrovat výstrahy z [detekce hrozeb Azure SQL Database](../sql-database/sql-database-threat-detection.md).

Kromě pomáhá chránit prostředky SQL Database, Security Center také poskytuje sledování zabezpečení a správu pro virtuální počítače Azure, cloudové služby, aplikační služby, virtuální sítě a další. Další informace o službě Security Center [zde](security-center-intro.md).

## <a name="prerequisites"></a>Požadavky
Pokud chcete začít využívat Security Center, musíte mít předplatné pro Microsoft Azure. Úroveň Free služby Security Center je povolená s vaším předplatným. Další informace o volné a standardní úrovně Security Center, najdete v části [Security Center ceny](https://azure.microsoft.com/pricing/details/security-center/).

Security Center podporuje přístup na základě rolí. Další informace o řízení přístupu na základě role (RBAC) v Azure najdete v tématu [řízení přístupu na základě Role v Azure Active Directory](../role-based-access-control/role-assignments-portal.md). Security Center – nejčastější dotazy poskytuje informace o [zpracování oprávnění ve službě Security Center](security-center-faq.md#permissions).

## <a name="access-security-center"></a>Přístup ke službě Security Center
Služba Security Center je přístupná prostřednictvím [portálu Azure](https://azure.microsoft.com/features/azure-portal/). [Přihlaste se k portálu](https://portal.azure.com/) a vyberte **Security Center možnost**.

![Možnost Security Center][1]

**Security Center** otevře se okno.
![Okno Security Center][2]

## <a name="set-security-policy"></a>Nastavení zásad zabezpečení
Zásady zabezpečení definuje sadu ovládacích prvků, které se doporučují pro prostředky v rámci zadané předplatné nebo skupinu prostředků. V Security Center určíte zásady pro předplatné nebo prostředek skupin podle potřeb zabezpečení vaší společnosti a podle typu aplikací nebo citlivosti dat v každém předplatném.

Můžete nastavit zásadu zobrazíte doporučení pro auditování SQL a SQL transparentní šifrování dat (šifrování TDE).

* Když zapnete **auditování SQL a zjišťování hrozeb**, Security Center doporučuje pro dodržování předpisů, rozšířeného zjišťování a vyšetřování účely musí být povoleno auditování přístupu k databázi Azure.
* Když zapnete **SQL transparentní šifrování dat**, Security Center doporučí šifrování povolit pro vaši databázi SQL Azure, přidružených záloh a souborů protokolů transakci.

Pokud chcete nastavit zásadu zabezpečení, vyberte **zásad** dlaždici v okně Security Center. Na **zásady zabezpečení** okně vyberte předplatné, na kterém chcete zásadu zabezpečení povolit. Vyberte **zásada Zabránění** a vypnout **na** doporučení zabezpečení, které chcete použít u tohoto předplatného.
![Zásady zabezpečení][3]

Další informace najdete v tématu [nastavovat zásady zabezpečení](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Správa doporučení zabezpečení
Security Center pravidelně analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří doporučení. Doporučení vás provedou procesem konfigurace potřebných kontrol.

Jakmile nastavíte zásadu zabezpečení, Security Center analyzuje stav zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení. Doporučení se zobrazí ve formátu tabulky, kde každý řádek představuje jeden konkrétní doporučení. Následující tabulku použijte jako odkaz vám pomohou pochopit dostupné doporučení pro Azure SQL Database a co jednotlivá doporučení nepodporuje, pokud ji použijete. Výběr doporučení přejdete na článek, který vysvětluje, jak provést doporučení ve službě Security Center.

| Doporučení | Popis |
| --- | --- |
| [Povolení auditování a zjišťování hrozeb na serverech SQL](security-center-enable-auditing-on-sql-servers.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro servery SQL Database. (Pouze služby SQL Database. Neobsahuje Microsoft SQL Server běžící na virtuálních počítačích.) |
| [Povolení auditování a zjišťování hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro databáze SQL Database. (Pouze služby SQL Database. Neobsahuje Microsoft SQL Server běžící na virtuálních počítačích.) |
| [Povolení transparentního šifrování dat](security-center-enable-transparent-data-encryption.md) |Doporučuje se, že povolíte šifrování pro databáze SQL. (Služba SQL Database pouze.) |

Pokud chcete zobrazit doporučení pro vaše prostředky Azure, vyberte **doporučení** dlaždici v okně Security Center. Na **doporučení** okně, vyberte doporučení zobrazíte podrobnosti. V tomto příkladu budeme vyberte **povolení auditování a detekce hrozeb na serverech SQL**.

![Doporučení][4]

Jak vidíte níže, Security Center zobrazuje SQL servery, kde nejsou povoleno auditování a zjišťování hrozeb. Po zapnutí auditování, nakonfigurujete nastavení detekce hrozeb a nastavení e-mailu pro výstrahy zabezpečení. Detekce hrozeb vás upozorní, když se zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze. Výstrahy se zobrazují na řídicím panelu Security Center.
![Auditování a detekce hrozeb][5]

Postupujte podle kroků v [detekce hrozeb SQL Database na portálu Azure](../sql-database/sql-database-threat-detection-portal.md) zapnout a nakonfigurovat detekce hrozeb a ke konfiguraci seznamu e-mailů, které budou dostávat upozornění zabezpečení při zjištění nezvyklých aktivit.

Další informace o doporučení, najdete v části [Správa doporučení zabezpečení](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Monitorování stavu zabezpečení
Po povolení [zásad zabezpečení](security-center-policies.md) pro prostředky předplatného bude služba Security Center analyzovat zabezpečení vašich prostředků, aby identifikovala potenciální ohrožení zabezpečení.  Můžete zobrazit stav zabezpečení vašich prostředků v **stav zabezpečení prostředků** dlaždici. Když kliknete na tlačítko **Data** v **stav zabezpečení prostředků** dlaždici **datové prostředky** otevře se okno s SQL doporučeními pro problémy, jako je například auditování a transparentní šifrování dat není povoleno. Také obsahuje doporučení pro obecný stav databáze.
![Stav zabezpečení prostředků][6]

Další informace najdete v tématu [sledování stavu zabezpečení](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Správa a reakce na výstrahy zabezpečení
Security Center automaticky shromažďuje, analyzuje a integruje data protokolu z [detekce hrozeb SQL Azure](../sql-database/sql-database-threat-detection.md), stejně jako ostatní prostředky Azure, zjistily skutečné hrozby a snížil počet falešných poplachů. Seznam upřednostňovaných výstrah zabezpečení se zobrazí ve službě Security Center spolu s informacemi, které potřebujete k rychlému prozkoumání problému, a doporučeními týkajícími se řešení útoku.

Pokud chcete zobrazit výstrahy, vyberte **výstrahy zabezpečení** dlaždici v okně Security Center. Na **výstrahy zabezpečení** okně, vyberte výstrahu Další informace o událostech, které spustí výstrahu a co, pokud existuje, kroky je potřeba provést k nápravě útoku. V tomto příkladu budeme vyberte **Injektáž SQL potenciální**.
![Výstrahy zabezpečení][7]

Jak vidíte níže, Security Center nabízí další podrobnosti, které nabízejí získání náhledu na příčinu výstrahy, cílový prostředek, případnou zdrojovou IP adresu a doporučení, jak provést nápravu.
![Potenciální Injektáž SQL][8]

Další informace najdete v tématu [Správa a zpracování výstrah zabezpečení](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Další postup
* [Security Center – nejčastější dotazy](security-center-faq.md) – přečtěte si nejčastější dotazy o použití této služby.
* [Průvodce plánováním a operace Security Center](security-center-planning-and-operations-guide.md) – postupujte podle sadu kroků a úloh, abyste přizpůsobili použití služby Security Center na základě vaší organizace požadavky na zabezpečení a modelu správy cloudu.
* [Zabezpečení dat Security Center](security-center-data-security.md) – zjistěte, jak Security Center shromažďuje a zpracovává data o vašich prostředků Azure, včetně informací o konfiguraci, metadata, protokoly událostí, soubory se stavem systému a další.
* [Zpracování incidentů zabezpečení](security-center-incident.md) -Další informace o použití funkce výstrahy zabezpečení ve službě Security Center vám pomůže při zpracování bezpečnostní incidenty v oblasti.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
