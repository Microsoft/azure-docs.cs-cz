---
title: Povolit transparentní šifrování dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento dokument popisuje, jak implementovat doporučení služby Azure Security Center **povolit transparentní šifrování dat**.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1f49930449c5ca342159f2a4eca3d7167dfed925
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244640"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Povolit transparentní šifrování dat ve službě Azure Security Center
Azure Security Center vám doporučí povolit transparentní šifrování dat (TDE) u databází SQL, pokud již není povolené šifrování TDE. Transparentní šifrování dat chrání vaše data a pomáhá splnit požadavky na dodržování předpisů tím, že šifruje vaše databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu, bez nutnosti změn do vaší aplikace. Další informace najdete tady [transparentního šifrování dat s využitím Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Toto doporučení platí pro služby Azure SQL. nezahrnuje SQL spuštěných na virtuálních počítačích.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V **doporučení** okně vyberte **povolit transparentní šifrování dat**.
   ![Povolení transparentního šifrování dat][1]
2. Tím se otevře **povolit transparentní šifrování dat v databázích SQL** okno. Vyberte databázi SQL na povolit transparentní šifrování dat na.
   ![Vyberte Povolit transparentní šifrování dat na SQL DB][2]
3. Na **transparentní šifrování dat** okně vyberte **ON** pod šifrování dat a vyberte **Uložit** na horním pásu karet v okně.
   ![Zapnout transparentní šifrování dat][3]

   Po povolení šifrování TDE u vybrané databáze SQL **stav šifrování** se změní na **šifrované**.    

   ![Stav šifrování][4]

## <a name="see-also"></a>Další informace najdete v tématech
Tento článek vám ukázali, jak implementovat doporučení služby Security Center "Povolit transparentní šifrování dat." Další informace o SQL TDE, naleznete v následujících tématech:

* [Transparentní šifrování dat s využitím Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Začínáme s transparentní šifrování dat (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – zjistěte, jak vám doporučení pomáhají chránit prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – zjistěte, jak můžete monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – získejte nejnovější zprávy zabezpečení Azure a informace.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
