---
title: Povolit transparentní šifrování dat v Azure Security Center | Microsoft Docs
description: V tomto dokumentu se dozvíte, jak implementovat Azure Security Center doporučení **Enable transparentní šifrování dat**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "60703995"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Povolit transparentní šifrování dat v Azure Security Center
Azure Security Center se doporučuje povolit transparentní šifrování dat (TDE) v databázích SQL, pokud TDE ještě není povolený. TDE chrání vaše data a pomáhá splnit požadavky na dodržování předpisů tím, že šifruje vaši databázi, přidružené zálohy a soubory protokolu transakcí v klidovém prostředí, aniž by to vyžadovalo změny vaší aplikace. Další informace najdete v tématu [transparentní šifrování dat s Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Toto doporučení se vztahuje pouze na službu Azure SQL. nezahrnuje SQL běžící na vašich virtuálních počítačích.

> [!NOTE]
> Tento dokument vám tuto službu představí formou ukázkového nasazení.  Není to podrobný průvodce.
>
>

## <a name="implement-the-recommendation"></a>Implementace doporučení
1. V okně **doporučení** vyberte **Povolit transparentní šifrování dat**.
   ![Povolení transparentního šifrování dat][1]
2. Otevře se okno **povolit transparentní šifrování dat databáze SQL** . Vyberte databázi SQL, na které chcete povolit TDE.
   ![Vyberte SQL DB pro povolení TDE zapnuto.][2]
3. V okně **transparentní šifrování dat** vyberte **v** části šifrování dat možnost Uložit a na horním pásu karet okna vyberte **Uložit** .
   ![Zapnout TDE][3]

   Po povolení TDE ve vybrané databázi SQL se **stav šifrování** změní na **zašifrovaný**.    

   ![Stav šifrování][4]

## <a name="see-also"></a>Viz také:
Tento článek vám ukázal, jak implementovat Security Center doporučení "Enable transparentní šifrování dat". Další informace o TDE SQL najdete v následujících tématech:

* [transparentní šifrování dat s Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Začínáme s transparentní šifrování dat (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa doporučení zabezpečení v Azure Security Center](security-center-recommendations.md) – Přečtěte si, jak doporučení vám pomůžou chránit vaše prostředky Azure.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se monitorovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/) – Získejte nejnovější novinky a informace o zabezpečení Azure.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
