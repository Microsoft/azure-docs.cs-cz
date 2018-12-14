---
title: Povolení auditování a detekce hrozeb u databází SQL v Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **povolení auditování a detekce hrozeb v databázích SQL**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 3de11b88f0bbf10cc0f1716cc0cefd128797cd5f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53341571"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Povolení auditování a detekce hrozeb u databází SQL v Azure Security Center
Azure Security Center vám doporučí, že můžete zapnout auditování a detekce hrozeb pro všechny databáze SQL, pokud auditování a detekce hrozeb ještě není povolené. Auditování a hrozeb, zjišťování vám mohou pomoci při zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

Jakmile jste zapnuli auditování můžete nakonfigurovat nastavení detekce hrozeb a e-mailů dostávat upozornění zabezpečení. Detekce hrozeb detekuje neobvyklé databázové aktivity značící potenciální ohrožení zabezpečení databáze. To umožňuje detekovat a reagovat na potenciální hrozby, když k nim dojde.

Toto doporučení platí pro služby Azure SQL. neměl by zahrnovat SQL spuštěných na virtuálních počítačích.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **povolit auditování a detekce hrozeb v databázích SQL**.  Tím se otevře **povolit auditování a detekce hrozeb v databázích SQL** okno.

   ![Povolení auditování pro databáze SQL][1]
2. Vyberte povolení auditování pro databáze serveru SQL. Tím se otevře **auditování a detekce hrozeb** okno.

3. Na **auditování a detekce hrozeb** okně vyberte **ON** pod **auditování**.

   ![Zapněte auditování a detekce hrozeb][2]
4. Postupujte podle kroků v [detekce hrozeb služby SQL Database na webu Azure Portal](../sql-database/sql-database-threat-detection-portal.md) zapnout a konfigurovat detekce hrozeb a nakonfigurovat seznam e-mailů, které budou přijímat výstrahy zabezpečení po detekci neobvyklých aktivit.

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Povolit auditování a detekci hrozeb u databází SQL." Další informace o zabezpečení služby SQL database, naleznete v následujících tématech:

* [Zabezpečení služby SQL Database](../sql-database/sql-database-security-overview.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
