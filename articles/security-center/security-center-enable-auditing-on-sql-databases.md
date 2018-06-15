---
title: Povolení auditování a zjišťování hrozeb u databází SQL v Azure Security Center | Microsoft Docs
description: Tento dokument se dozvíte, jak provést doporučení Azure Security Center ** povolit auditování a zjišťování hrozeb v SQL databáze **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: terrylan
ms.openlocfilehash: 8f4febdaa4497fee0dc690b59cd6eaa415c5e5cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866202"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Povolení auditování a zjišťování hrozeb u databází SQL v Azure Security Center
Azure Security Center bude doporučujeme vám zapnout auditování a zjišťování hrozeb pro všechny SQL databáze, pokud auditování a detekce hrozeb ještě není povolené. Auditování a hrozeb, že detekce vám může pomoct zajistit dodržování předpisů, porozumět databázové aktivitě a proniknout do nesrovnalostí a anomálií, které by mohly být známkou problémů obchodního charakteru nebo by mohly vzbuzovat podezření narušení zabezpečení.

Jakmile jste zapnuli auditování můžete nakonfigurovat nastavení detekce hrozeb a e-mailů, aby výstrahy zabezpečení. Detekce hrozeb zjistila nezvyklé databázové aktivity, které indikují potenciální ohrožení databáze. To vám umožňuje zjistit a reagovat na potenciální hrozby, kdy k nim dojde.

Toto doporučení se vztahují ke službě Azure SQL pouze; neobsahuje SQL běžících na virtuálních počítačích.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** vyberte **povolení auditování a detekce hrozeb v databázích SQL**.  Tím se otevře **povolení auditování a detekce hrozeb v databázích SQL** okno.

   ![Povolení auditování pro databáze SQL][1]
2. Vyberte databázi SQL, chcete-li povolit auditování. Tím se otevře **auditování a detekce hrozeb** okno.

3. Na **auditování a detekce hrozeb** vyberte **ON** v části **auditování**.

   ![Zapnout auditování a zjišťování hrozeb][2]
4. Postupujte podle kroků v [detekce hrozeb databáze SQL na webu Azure portal](../sql-database/sql-database-threat-detection-portal.md) zapnout a nakonfigurovat detekce hrozeb a ke konfiguraci seznamu e-mailů, které budou dostávat upozornění zabezpečení při zjištění nezvyklých aktivit.

## <a name="see-also"></a>Viz také
Tento článek vám ukázal, jak provést doporučení Security Center "Povolit auditování a detekce hrozeb v databázích SQL." Další informace o zabezpečení databáze SQL, naleznete v následujících tématech:

* [Zabezpečení služby SQL Database](../sql-database/sql-database-security-overview.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
