---
title: Povolení auditování a detekce hrozeb na SQL serverech v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **povolit auditování a detekce hrozeb na SQL serverech**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 042fca4d-7dab-4172-8614-e8c21ccb4960
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 5b185486ee1adfc3e9acc12aeb15162094ab6424
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310969"
---
# <a name="enable-auditing-and-threat-detection-on-sql-servers-in-azure-security-center"></a>Povolení auditování a detekce hrozeb na SQL serverech v Azure Security Center
Azure Security Center vám doporučí, zapněte auditování a detekce hrozeb pro všechny databáze na serverech Azure SQL, pokud auditování ještě není povolené. Auditování a hrozeb, zjišťování vám mohou pomoci při zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

Jakmile jste zapnuli auditování můžete nakonfigurovat nastavení detekce hrozeb a e-mailů dostávat upozornění zabezpečení. Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze. To umožňuje detekovat a reagovat na potenciální hrozby, když k nim dojde.

Toto doporučení platí pro služby Azure SQL. neměl by zahrnovat SQL Server běžící na virtuálních počítačích ve službách infrastruktury Azure (Azure IaaS).

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **povolit auditování a detekce hrozeb na SQL serverech**.  Tím se otevře **povolit auditování a detekce hrozeb na SQL serverech** okno.

   ![Povolení auditování pro servery SQL][1]
2. Vyberte Povolit auditování a detekce hrozeb na SQL server. Tím se otevře **auditování a detekce hrozeb** okno.

3. Na **auditování a detekce hrozeb** okně vyberte **ON** pod **auditování**.

   ![Zapnutí auditování nastavení][2]
4. Postupujte podle kroků v [auditování služby SQL database na webu Azure Portal](../sql-database/sql-database-auditing-portal.md) konfigurace úložiště, kam se budou ukládat vaše protokoly auditu. Účet úložiště předplatného pro shromažďování dat je výchozí účet úložiště.
5. Postupujte podle kroků v [Začínáme s detekcí hrozeb služby SQL Database](../sql-database/sql-database-threat-detection.md) zapnout a konfigurovat detekce hrozeb a nakonfigurovat seznam e-mailů, které budou přijímat výstrahy zabezpečení po detekci neobvyklých aktivit.

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat Security Center doporučení "Povolit auditování a detekce hrozeb na SQL serverech." Další informace o zabezpečení služby SQL database, naleznete v následujících tématech:

* [Zabezpečení služby SQL Database](../sql-database/sql-database-security-overview.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-azure-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png
