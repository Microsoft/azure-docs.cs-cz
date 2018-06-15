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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2018
ms.author: barclayn
ms.openlocfilehash: e52cee2cb642de6e54270c597e6ed99f7162d0ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641454"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Zabezpečení služeb a technologií, které jsou k dispozici v Azure

V našem diskuze s aktuálním a budoucím Azure zákazníků jsme se často dotaz "máte seznam všechny služby související se zabezpečením a technologie, které Azure nabízí nabízet?"

Při hodnocení možnosti poskytovatele cloudové služby, je vhodné mít tyto informace. Proto uvádíme tohoto seznamu, které vám pomůžou začít.

Tento seznam bude v průběhu času změnit a růst, stejně jako Azure. Ujistěte se, že zkontrolujte tuto stránku v pravidelných intervalech zůstane aktuální na našem související se zabezpečením službách a technologiích.

## <a name="general-azure-security"></a>Obecné Azure zabezpečení
|Služba|Popis|
|--------|--------|
|[Azure&nbsp;zabezpečení&nbsp;Center](../security-center/security-center-intro.md)| Řešení ochrany zatížení cloudu, které poskytuje správu zabezpečení a ochrana před internetovými útoky pokročilé v rámci úlohy hybridní cloud.|
|[Azure Key Vault](../key-vault/key-vault-overview.md)| Tajné klíče zabezpečeného úložiště pro hesla, připojovací řetězce a další informace, které je třeba zachovat aplikace pro práci. |
|[Log Analytics](../log-analytics/log-analytics-overview.md)|Služba monitorování, který shromažďuje telemetrická data a dalších dat a poskytuje modul dotazu jazyka a analytics k poskytování služby Statistika provozu pro aplikace a prostředky. Můžete použít samostatně nebo s jinými službami, jako je například Security Center. |
|[Prostředí Azure pro vývoj/testování](../devtest-lab/devtest-lab-overview.md)|Služba, která pomáhá vývojářům a testerům, sada rychle vytvořit prostředí v Azure při minimalizaci odpady a řízení nákladů.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](azure-security-disk-encryption-overview.md)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpečení úložiště
|Služba|Popis|
|------|--------|
| [Azure&nbsp;úložiště&nbsp;služby&nbsp;šifrování](../storage/common/storage-service-encryption.md)|Funkce zabezpečení, která automaticky šifruje vaše data v úložišti Azure.   |
|[StorSimple šifrované hybridní úložiště](../storsimple/storsimple-ova-overview.md)| Řešení integrované úložiště, který spravuje úlohy úložiště mezi místními zařízeními a cloudového úložiště Azure.|
|[Azure šifrování na straně klienta](../storage/common/storage-client-side-encryption.md)| Řešení šifrování na straně klienta, které šifruje data uvnitř klientské aplikace před nahráním do úložiště Azure; Při stahování také dešifruje data. |
| [Sdílené přístupové podpisy úložiště Azure](../storage/common/storage-dotnet-shared-access-signature-part-1.md)|Sdílený přístupový podpis poskytuje Delegovaný přístup k prostředkům ve vašem účtu úložiště.  |
|[Klíče účtu úložiště Azure](../storage/common/storage-create-storage-account.md)| Metodu řízení přístupu pro úložiště Azure, který se používá k ověřování při přístupu k účtu úložiště. |
|[Azure sdílené složky s šifrování protokolu SMB 3.0](../storage/files/storage-files-introduction.md)|Technologie zabezpečení sítě, která umožňuje automatické šifrování pro protokol sdílení souborů Server Message Block (SMB) sítě. |
|[Azure Storage Analytics](https://docs.microsoft.com/en-us/rest/api/storageservices/Storage-Analytics)| Protokolování a generování metriky technologie pro data ve vašem účtu úložiště. |

<!------>

## <a name="database-security"></a>Zabezpečení databází
|Služba|Popis|
|------|--------|
| [Azure&nbsp;SQL&nbsp;brány Firewall](../sql-database/sql-database-firewall-configure.md)|Funkce řízení přístupu sítě, která chrání před útoky ze sítě do databáze. |
|[Azure&nbsp;SQL&nbsp;buňky&nbsp;úrovně šifrování](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologie zabezpečení databáze, která zajišťuje šifrování na podrobné úrovni.  |
| [Azure&nbsp;SQL&nbsp;šifrování připojení](../sql-database/sql-database-control-access.md)|Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům. |
| [Azure SQL vždy šifrování](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chrání citlivá data, třeba čísla platebních karet nebo national identifikačními čísly (například USA čísel sociálního pojištění), uloženy v databázi Azure SQL Database nebo SQL Server.  |
| [Azure&nbsp;SQL&nbsp;transparentní šifrování dat](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkce zabezpečení databáze, která zašifruje úložiště celé databáze. |
| [Databáze Azure SQL auditování](../sql-database/sql-database-auditing.md)|Databáze auditování funkce, která sleduje události databáze a zápisu, které mají auditu přihlášení účtu úložiště Azure.  |


## <a name="identity-and-access-management"></a>Správa identit a přístupu
|Služba|Popis|
|------|--------|
| [Azure&nbsp;Role&nbsp;na základě&nbsp;řízení přístupu](../active-directory/role-based-access-control-configure.md)|Funkce řízení přístupu navržená tak, aby uživatelé pro přístup k prostředkům, které jsou nutné pro přístup na základě jejich rolí v rámci organizace.  |
| [Azure Active Directory](../active-directory/active-directory-whatis.md)|Ověřování založené na cloudové úložiště, které podporuje více klientů, cloudového adresáře a více identit správy služeb v rámci Azure.  |
| [Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-overview.md)|Služba identity management, který umožňuje řídit jak zákazníci registrace, přihlášení a spravovat jejich profilů při použití aplikace založené na Azure.   |
| [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-overview.md)| Verze založené na cloudu a spravované služby Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)| Poskytování zabezpečení, která používá několik různých formy ověřování a ověřování probíhá před povolením přístupu k zabezpečeným informacím. |

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii
|Služba|Popis|
|------|--------|
| [Azure&nbsp;zálohování](../backup/backup-introduction-to-azure-backup.md)| Na základě Azure služby použít k zálohování a obnovení dat v cloudu Azure. |
| [Azure&nbsp;lokality&nbsp;obnovení](../site-recovery/site-recovery-overview.md)|Online služba, která replikuje úlohy běžící na fyzické a virtuální počítače (VM) z primární lokality do sekundárního umístění za účelem umožnění obnovy služby po selhání. |

## <a name="networking"></a>Sítě
|Služba|Popis|
|------|--------|
| [Síť&nbsp;zabezpečení&nbsp;skupiny](../virtual-network/virtual-networks-nsg.md)| Funkce řízení přístupu na základě sítě pomocí 5-n-tice povolit nebo odepřít rozhodnutí.  |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md)| Síťové zařízení, použít jako koncový bod VPN umožňující mezi různými místy přístup k virtuálním sítím Azure.  |
| [Azure Application Gateway](../application-gateway/application-gateway-introduction.md)|Upřesnit webové aplikace načíst vyrovnávání, který může směrovat podle adresy URL a provést snižování zátěže protokolu SSL. |
| [Nástroj pro vyrovnávání zatížení Azure](../load-balancer/load-balancer-overview.md)|Vyrovnávání zatížení TCP/UDP aplikace sítě. |
| [Azure ExpressRoute](../expressroute/expressroute-introduction.md)| Vyhrazené sítě WAN propojení mezi místní sítí a virtuálních sítí Azure. |
| [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)| Globální Vyrovnávání zatížení DNS.|
| [Proxy aplikace Azure](../active-directory/active-directory-application-proxy-get-started.md)| Ověřování front-end sloužící k zabezpečení vzdáleného přístupu pro webové aplikace hostované na místě. |