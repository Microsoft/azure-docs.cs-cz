---
title: Ochrana služby Azure SQL a dat ve službě Azure Security Center | Dokumentace Microsoftu
description: Tato dokument řeší doporučení ve službě Azure Security Center, které vám pomohou chránit data a služby Azure SQL a zůstaňte souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2017
ms.author: terrylan
ms.openlocfilehash: 45f5dc840f015793912e314ab3d47e54a409708e
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126662"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Ochrana služby Azure SQL a dat ve službě Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a datům a aplikacím.

Tento článek se zabývá doporučení, která se vztahují na služby Azure SQL a data. System center doporučení týkající se povolení auditování pro servery Azure SQL Server a databáze, povolení šifrování databází SQL a povolení šifrování účtu úložiště Azure.  V následující tabulce použijte jako odkaz vám pomůžou pochopit dostupné doporučení služby a dat SQL a každý z nich, co dělá když je použijete.
### <a name="monitor-data-security"></a>Monitorování zabezpečení dat

Po kliknutí na **Zabezpečení dat** v části **Prevence** se otevře okno **Datové prostředky** s doporučeními pro SQL a službu Storage. Také obsahuje [doporučení](security-center-sql-service-recommendations.md) pro obecný stav databáze. Další informace o šifrování úložiště najdete v tématu [Povolení šifrování účtu úložiště Azure v Azure Security Center](security-center-enable-encryption-for-storage-account.md).

![Datové prostředky](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

V části **Doporučení SQL** můžete kliknout na jakékoli doporučení a získáte další podrobnosti o další akci pro řešení problému. Následující příklad ukazuje rozbalení doporučení **Auditování databáze a detekce hrozeb u databází SQL**.

![Podrobnosti o doporučení SQL](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Okno **Povolit auditování a detekci hrozeb u databází SQL** obsahuje následující informace:

* Seznam databází SQL
* Server, na kterém jsou umístěné
* Informace o tom, zda bylo toto nastavení zděděno ze serveru nebo zda je v této databázi jedinečné
* Aktuální stav
* Závažnosti problému

Když kliknete na databázi, abyste vyřešili toto doporučení, otevře se okno **Auditování a detekce hrozeb**, jak je znázorněno na následující obrazovce.

![Auditování a detekce hrozeb](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

Pokud chcete povolit auditování, vyberte **Zapnuto** pod možností **Auditování**.

## <a name="available-sql-service-and-data-recommendations"></a>K dispozici doporučení služby a dat SQL
| Doporučení | Popis |
| --- | --- |
| [Povolení auditování a detekce hrozeb na SQL serverech](security-center-enable-auditing-on-sql-servers.md) |Doporučuje, zapněte auditování a detekce hrozeb pro servery Azure SQL (služby Azure SQL pouze; neobsahuje SQL spuštěných na virtuálních počítačích). |
| [Povolení auditování a detekce hrozeb v databázích SQL](security-center-enable-auditing-on-sql-databases.md) |Doporučuje, zapněte auditování a detekci hrozeb u databází Azure SQL (služby Azure SQL pouze; neobsahuje SQL spuštěných na virtuálních počítačích). |
| [Povolit transparentní šifrování dat v databázích SQL](security-center-enable-transparent-data-encryption.md) |Doporučuje povolit šifrování pro SQL Database (jenom služby Azure SQL). |

## <a name="see-also"></a>Další informace najdete v tématech
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů pomocí Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana aplikací pomocí Azure Security Center](security-center-application-recommendations.md)
* [Ochrana sítě pomocí Azure Security Center](security-center-network-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
