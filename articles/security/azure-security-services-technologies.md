---
title: Technologie a služby zabezpečení Azure | Dokumentace Microsoftu
description: Tento článek poskytuje uspořádaný seznam technologie a služby zabezpečení Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 7577a05302dcf23cc68666685c1537c0eb31f831
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115568"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Zabezpečení služeb a technologií, které jsou k dispozici v Azure

V našich diskuzích se současným i budoucím zákazníky Azure můžeme se často dotaz "máte seznam všech služeb souvisejících se zabezpečením a technologie, které Azure nabízí?"

Při hodnocení možnosti poskytovatele cloudové služby, je užitečné mít tyto informace. Proto nabízíme tohoto seznamu, které vám pomůžou začít.

Tento seznam bude v průběhu času změnit a růst, stejně jako Azure. Ujistěte se, projděte si tuto stránku v pravidelných intervalech, aby dostávat aktuální informace o našich služeb souvisejících se zabezpečením a technologie.

## <a name="general-azure-security"></a>Zabezpečení Azure obecné
|Služba|Popis|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](../security-center/security-center-intro.md)| Řešení ochrany úloh cloud, který zajišťuje správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| Zabezpečené úložiště tajných klíčů pro hesla, připojovací řetězce a další informace, které potřebujete k udržování propojení vašich aplikací pracovat. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|Monitorovací služba, která shromažďuje telemetrii a další data a poskytuje dotazovací jazyk a analytický modul k zajištění provozu vašich aplikací a prostředků. Je možné samostatně nebo s jinými službami, jako je Security Center. |
|[Azure Dev/Test Lab](../devtest-lab/devtest-lab-overview.md)|Služba, která pomáhá vývojářům a testerům rychle vytvářet prostředí v Azure plýtváním a kontrolou nad náklady.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpečení úložiště
|Služba|Popis|
|------|--------|
| [Azure&nbsp;Storage&nbsp;Service&nbsp;Encryption](../storage/common/storage-service-encryption.md)|Funkce zabezpečení, která automaticky šifruje vaše data ve službě Azure storage.   |
|[StorSimple šifrované hybridní úložiště](../storsimple/storsimple-ova-overview.md)| Integrované řešení úložiště, které spravuje úlohy úložiště mezi místními zařízeními a úložištěm cloudu Azure.|
|[Azure šifrování na straně klienta](../storage/common/storage-client-side-encryption.md)| Řešení šifrování na straně klienta, který šifruje data v klientských aplikacích před nahráním do služby Azure Storage; Při stahování také dešifruje data. |
| [Sdílené přístupové podpisy úložiště Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště.  |
|[Azure Storage Account Keys](../storage/common/storage-create-storage-account.md)| Metodu řízení přístupu pro Azure storage, který se používá k ověřování při přístupu k účtu úložiště. |
|[Sdílené složky Azure pomocí šifrování SMB 3.0](../storage/files/storage-files-introduction.md)|Technologie zabezpečení sítě, která umožňuje automatické šifrování pro protokol sdílení zprávy bloku SMB (Server) souborů sítě. |
|[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Storage-Analytics)| Protokolování a generování metriky technologie pro data ve vašem účtu úložiště. |

<!------>

## <a name="database-security"></a>Zabezpečení databází
|Služba|Popis|
|------|--------|
| [Azure&nbsp;SQL&nbsp;Firewall](../sql-database/sql-database-firewall-configure.md)|Funkce řízení přístupu síť, která chrání před útoky ze sítě do databáze. |
|[Azure&nbsp;SQL&nbsp;Cell&nbsp;Level Encryption](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologie zabezpečení databáze, která zajišťuje šifrování na jemné úrovni.  |
| [Azure&nbsp;SQL&nbsp;šifrování připojení](../sql-database/sql-database-control-access.md)|Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům. |
| [Azure SQL vždy šifrování](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chrání citlivá data, třeba čísla platebních karet nebo národní identifikační čísla (například USA čísla sociálního pojištění), uložená v databázích Azure SQL Database nebo SQL Server.  |
| [Azure&nbsp;SQL&nbsp;transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkce zabezpečení databáze, která šifruje úložiště celou databázi. |
| [Azure SQL Database auditování](../sql-database/sql-database-auditing.md)|Funkce, která sleduje události databáze a zapisuje je do auditování ve vašem účtu úložiště Azure protokolu auditování databáze.  |


## <a name="identity-and-access-management"></a>Správa identit a přístupu
|Služba|Popis|
|------|--------|
| [Azure&nbsp;Role&nbsp;na základě&nbsp;řízení přístupu](../active-directory/role-based-access-control-configure.md)|Funkce řízení přístupu umožňují uživatelům přístup k prostředkům, které jsou vyžadovány k přístupu na základě jejich rolí v rámci organizace.  |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)|Ověřování založené na cloudu úložiště podporující více tenantů, cloudový adresář a víc služeb správy identit v Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Služba pro správu identit, která umožňuje kontrolovat, jak zákazníci registrace, přihlášení a spravují své profily při používání aplikací založených na Azure.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Verze založené na cloudu a spravované služby Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Zřízení zabezpečení, která používá několik různých tvarů ověřování a ověřování před povolením přístupu k zabezpečeným informacím. |

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii
|Služba|Popis|
|------|--------|
| [Azure&nbsp;Backup](../backup/backup-introduction-to-azure-backup.md)| Služby založené na Azure service používá k zálohování a obnovení dat v cloudu Azure. |
| [Azure&nbsp;Site&nbsp;Recovery](../site-recovery/site-recovery-overview.md)|Online služba, která replikuje úlohy spuštěné na fyzických a virtuálních počítačů (VM) z primární lokality do sekundárního umístění k povolení obnovení služby po selhání. |

## <a name="networking"></a>Sítě
|Služba|Popis|
|------|--------|
| [Network&nbsp;Security&nbsp;Groups](../virtual-network/virtual-networks-nsg.md)| Funkce řízení přístupu na základě sítě pomocí 5-n-tice povolují nebo odpírají rozhodnutí.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Síťové zařízení používat, protože umožňuje koncovým bodem VPN mezi různými místy přístup k virtuálním sítím Azure.  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|Rozšířené webové aplikace pro vyrovnávání zátěže, která může směrovat na základě adresy URL a proveďte snižování zátěže protokolu SSL. |
|[Firewall webových aplikací](../application-gateway/waf-overview.md) (WAF)|Funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožení zabezpečení|
| [Nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md)|TCP/UDP aplikace služby Vyrovnávání zatížení sítě. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Vyhrazená síť WAN propojení mezi místními sítěmi a Azure Virtual Network. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Globální DNS nástroje pro vyrovnávání zatížení.|
| [Proxy aplikací Azure](../active-directory/active-directory-application-proxy-get-started.md)| Ověřování front-endu používá k zabezpečení vzdáleného přístupu pro webové aplikace hostované místně. |
|[Brány Firewall na Azure](../firewall/overview.md)|Služba zabezpečení spravované sítě založené na cloudu, která chrání vaše prostředky Azure Virtual Network.|
|[Azure DDoS protection](../virtual-network/ddos-protection-overview.md)|V kombinaci s osvědčené postupy pro navrhování aplikací, poskytuje ochranu před útoky DDoS.|
|[Koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md)|Rozšiřují privátní adresní prostor vaší virtuální sítě a identitu vaší virtuální sítě do služeb Azure přes přímé připojení.|