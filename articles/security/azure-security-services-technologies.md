---
title: Zabezpečení Azure službách a technologiích | Microsoft Docs
description: Tento článek poskytuje kurátorované seznam zabezpečení Azure službách a technologiích.
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: e2e6e50946a009ad9fbd27b821fa6a0999c156f1
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-security-services-and-technologies"></a>Zabezpečení Azure službách a technologiích

V našem diskuze s aktuálním a budoucím Azure zákazníků jsme se často dotaz "máte seznam všech zabezpečení souvisejících službách a technologiích, které Azure nabízí?"

Při vyhodnocování možnosti poskytovatele cloudové služby, je vhodné mít tyto informace.

Toto je naše počáteční úsilí na poskytování seznamu. Tento seznam bude v průběhu času změnit a růst, stejně jako Azure. V seznamu je zařazený do kategorie a seznamu kategorií se taky zvýší v čase. Ujistěte se, že zkontrolujte tuto stránku v pravidelných intervalech zůstane aktuální na našem související se zabezpečením službách a technologiích.

## <a name="azure-security---general"></a>Azure Security – obecné

* [Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/)
* [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/)
* [Azure Disk Encryption](azure-security-disk-encryption.md)
* [Log Analytics](../log-analytics/log-analytics-overview.md)
* [Prostředí Azure pro vývoj/testování](https://azure.microsoft.com/documentation/services/devtest-lab/)

## <a name="azure-storage-security"></a>Zabezpečení úložiště Azure

* [Šifrování služby úložiště Azure](../storage/common/storage-service-encryption.md)
* [StorSimple šifrované hybridní úložiště](https://azure.microsoft.com/documentation/services/storsimple/)
* [Azure šifrování na straně klienta](../storage/common/storage-client-side-encryption.md)
* [Sdílené přístupové podpisy úložiště Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Klíče účtu úložiště Azure](../storage/common/storage-create-storage-account.md)
* [Azure sdílené složky s šifrování protokolu SMB 3.0](../storage/files/storage-dotnet-how-to-use-files.md)
* [Azure Storage Analytics](https://msdn.microsoft.com/library/hh343270.aspx)

## <a name="azure-database-security"></a>Zabezpečení databáze Azure

* [Brány Firewall Azure SQL](../sql-database/sql-database-firewall-configure.md)
* [Šifrování na úrovni buněk Azure SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)
* [Šifrování připojení Azure SQL](../sql-database/sql-database-control-access.md)
* [Ověřování Azure SQL](../sql-database/sql-database-control-access.md)
* [Azure SQL vždy šifrování](https://msdn.microsoft.com/library/mt163865.aspx)
* [Šifrování na úrovni sloupce Azure SQL](https://msdn.microsoft.com/library/ms179331.aspx)
* [Azure SQL transparentní šifrování dat](https://msdn.microsoft.com/library/dn948096.aspx)
* [Databáze Azure SQL auditování](../sql-database/sql-database-auditing.md)

## <a name="azure-identity-and-access-management"></a>Azure identita a správa přístupu

* [Řízení přístupu na základě Azure Role](../role-based-access-control/role-assignments-portal.md)
* [Azure Active Directory](../active-directory/active-directory-whatis.md)
* [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-get-started.md)
* [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)
* [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

* [Azure Backup](https://azure.microsoft.com/documentation/services/backup/)
* [Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery/)

## <a name="azure-networking"></a>Sítě Azure

* [Skupiny zabezpečení sítě](../virtual-network/virtual-networks-nsg.md)
* [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)
* [Nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md)
* [Azure ExpressRoute](../expressroute/expressroute-introduction.md)
* [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)
* [Proxy aplikace Azure](../active-directory/manage-apps/application-proxy-enable.md)
