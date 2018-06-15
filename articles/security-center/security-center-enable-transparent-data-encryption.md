---
title: Povolit transparentní šifrování dat v Azure Security Center | Microsoft Docs
description: Tento dokument se dozvíte, jak provést doporučení Azure Security Center ** povolit transparentní dat šifrování **.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: terrylan
ms.openlocfilehash: 2a2963affdbff3710ad08f86c6ed4e6304335559
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866293"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Povolit transparentní šifrování dat v Azure Security Center
Azure Security Center doporučí, povolte šifrování transparentní dat (TDE) u databází SQL, pokud již není povolené šifrování TDE. Šifrování TDE chrání vaše data a pomáhá splnit požadavky na dodržování předpisů šifrováním vaší databáze, přidružených záloh a souborů protokolů transakci v klidu, bez nutnosti změny do vaší aplikace. Další informace najdete v další [transparentní šifrování dat s Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Toto doporučení se vztahují ke službě Azure SQL pouze; neobsahuje SQL běžících na virtuálních počítačích.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** vyberte **povolit transparentní šifrování dat**.
   ![Povolení transparentního šifrování dat][1]
2. Tím se otevře **povolit transparentní šifrování dat v databázích SQL** okno. Vyberte databázi SQL, chcete-li povolit šifrování TDE na.
   ![Vyberte databázi SQL, povolit šifrování TDE na][2]
3. Na **transparentní šifrování dat** vyberte **ON** pod šifrování dat a vyberte **Uložit** na pásu karet na horní části okna.
   ![Zapnout šifrování TDE][3]

   Jakmile povolíte šifrování TDE na vybranou databázi SQL, **stav šifrování** se změní na **šifrovaný**.    

   ![Stav šifrování][4]

## <a name="see-also"></a>Viz také
Tento článek vám ukázal, jak provést doporučení Security Center "Povolit transparentní šifrování dat." Další informace o šifrování TDE SQL, naleznete v následujících tématech:

* [Transparentní šifrování dat s databází Azure SQL](https://msdn.microsoft.com/library/dn948096)
* [Začínáme s transparentní šifrování dat (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky v Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o bezpečnosti Azure](http://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější informace zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
