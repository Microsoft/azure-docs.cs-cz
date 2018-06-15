---
title: Ochrana služby Azure SQL a data v Azure Security Center | Microsoft Docs
description: Tento dokument adresy doporučení v Azure Security Center, které vám pomůžou chránit vaše data a služba Azure SQL a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 0c3a11e9a86767641533b16de1b96b4c59bfdf51
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866272"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrana služby Azure SQL a data v Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a datům a aplikacím.

Tento článek se zaměřuje na doporučení, která se týkají služby Azure SQL a data. Doporučení center kolem povolení auditování pro servery Azure SQL a databáze, povolení šifrování u databází SQL a povolení šifrování vašeho účtu úložiště Azure.  Použijte v následující tabulce vám pomohou pochopit dostupné doporučení služby a data SQL a co každé z nich dělá Pokud použijete ho jako odkaz.

## <a name="available-sql-service-and-data-recommendations"></a>K dispozici doporučení služby a dat SQL
| Doporučení | Popis |
| --- | --- |
| [Povolení auditování a detekce hrozeb na SQL serverech](security-center-enable-auditing-on-sql-servers.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro servery Azure SQL (služba Azure SQL pouze; neobsahuje SQL běžících na virtuálních počítačích). |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje zapnout auditování a zjišťování hrozeb pro databáze Azure SQL (služba Azure SQL pouze; neobsahuje SQL běžících na virtuálních počítačích). |
| [Povolit transparentní šifrování dat v databázích SQL](security-center-enable-transparent-data-encryption.md) |Doporučuje se, že povolíte šifrování pro databáze SQL (pouze služby Azure SQL). |

## <a name="see-also"></a>Viz také
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů v Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací v Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě v Azure Security Center](security-center-network-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
