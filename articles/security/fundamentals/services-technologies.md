---
title: Služby a technologie Azure security services | Dokumenty společnosti Microsoft
description: Tento článek obsahuje sestavený seznam služeb a technologií azure security.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: barclayn
ms.openlocfilehash: 741e6b9cad20645fdfc085623fd9492a12ac1dfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726560"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Bezpečnostní služby a technologie dostupné v Azure

V našich diskusích se současnými a budoucími zákazníky Azure se často ptají: "Máte seznam všech služeb a technologií souvisejících se zabezpečením, které Azure nabízí?"

Při vyhodnocování možností poskytovatele cloudových služeb je užitečné mít tyto informace. Takže jsme poskytli tento seznam, abyste mohli začít.

V průběhu času se tento seznam bude měnit a růst, stejně jako Azure. Ujistěte se, že kontrolovat tuto stránku v pravidelných intervalech, aby zůstali up-to-up na naše služby související se zabezpečením a technologií.

## <a name="general-azure-security"></a>Obecné zabezpečení Azure
|Služba|Popis|
|--------|--------|
|[Azure&nbsp;Security&nbsp;Center](/azure/security-center/security-center-intro)| Řešení ochrany cloudových úloh, které poskytuje správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Zabezpečené úložiště tajných klíčů pro hesla, připojovací řetězce a další informace, které potřebujete k tomu, aby vaše aplikace fungovaly. |
|[Protokoly azure monitoru](/azure/log-analytics/log-analytics-overview)|Služba monitorování, která shromažďuje telemetrická a další data a poskytuje modul dotazovacích jazyků a analýz, který poskytuje provozní přehledy pro vaše aplikace a prostředky. Lze použít samostatně nebo s jinými službami, jako je Security Center. |
|[Azure Vývoj/testovací laboratoře](/azure/lab-services/devtest-lab-overview)|Služba, která vývojářům a testerům pomáhá rychle vytvářet prostředí v Azure a současně minimalizovat plýtvání a řídit náklady.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpečení úložiště
|Služba|Popis|
|------|--------|
| [Šifrování&nbsp;&nbsp;služby Azure Storage Service&nbsp;](/azure/storage/common/storage-service-encryption)|Funkce zabezpečení, která automaticky šifruje vaše data v úložišti Azure.   |
|[Šifrování hybridního úložiště StorSimple](/azure/storsimple/storsimple-ova-overview)| Integrované řešení úložiště, které spravuje úlohy úložiště mezi místními zařízeními a cloudovým úložištěm Azure.|
|[Šifrování na straně klienta Azure](/azure/storage/common/storage-client-side-encryption)| Řešení šifrování na straně klienta, které šifruje data uvnitř klientských aplikací před odesláním do služby Azure Storage; také dešifruje data při stahování. |
| [Podpisy sdíleného přístupu azure úložiště](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům v účtu úložiště.  |
|[Klíče účtů úložiště Azure](/azure/storage/common/storage-create-storage-account)| Metoda řízení přístupu pro úložiště Azure, která se používá pro ověřování při přístupu k účtu úložiště. |
|[Sdílené položky Azure se šifrováním SMB 3.0](/azure/storage/files/storage-files-introduction)|Technologie zabezpečení sítě, která umožňuje automatické šifrování sítě pro protokol pro sdílení souborů Server Message Block (SMB). |
|[Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics)| Technologie protokolování a generování metrik pro data ve vašem účtu úložiště. |

<!------>

## <a name="database-security"></a>Zabezpečení databází
|Služba|Popis|
|------|--------|
| [Brána firewall Azure&nbsp;SQL&nbsp;](/azure/sql-database/sql-database-firewall-configure)|Funkce řízení přístupu k síti, která chrání před útoky na databázi na základě sítě. |
|[Šifrování&nbsp;&nbsp;na&nbsp;úrovni buněk Azure SQL](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologie zabezpečení databáze, která poskytuje šifrování na podrobné úrovni.  |
| [Šifrování&nbsp;&nbsp;připojení Azure SQL](/azure/sql-database/sql-database-control-access)|Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům. |
| [Vždy šifrovat Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chrání citlivá data, jako jsou čísla kreditních karet nebo národní identifikační čísla (například čísla sociálního pojištění v USA), uložená v databázích Azure SQL Database nebo SQL Server.  |
| [Transparentní&nbsp;&nbsp;šifrování dat Azure SQL](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkce zabezpečení databáze, která šifruje úložiště celé databáze. |
| [Auditování databáze Azure SQL](/azure/sql-database/sql-database-auditing)|Funkce auditování databáze, která sleduje události databáze a zapisuje je do protokolu auditování ve vašem účtu úložiště Azure.  |


## <a name="identity-and-access-management"></a>Správa identit a přístupu
|Služba|Popis|
|------|--------|
| [Řízení&nbsp;&nbsp;přístupu na základě&nbsp;rolí Azure](/azure/active-directory/role-based-access-control-configure)|Funkce řízení přístupu navržená tak, aby uživatelům umožňovala přístup pouze k prostředkům, ke kterým jsou požadovány přístup, na základě jejich rolí v rámci organizace.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Úložiště ověřování na základě cloudu, které podporuje víceklientský, cloudový adresář a více služeb správy identit v rámci Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Služba správy identit, která umožňuje kontrolu nad tím, jak se zákazníci přihlašují, přihlašují a spravují své profily při používání aplikací založených na Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Cloudová a spravovaná verze služby Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| Bezpečnostní ustanovení, které využívá několik různých forem ověřování a ověřování před povolením přístupu k zabezpečeným informacím. |

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii
|Služba|Popis|
|------|--------|
| [Azure&nbsp;Backup](/azure/backup/backup-introduction-to-azure-backup)| Služba založená na Azure slouží k zálohování a obnovení dat v cloudu Azure. |
| [Obnovení&nbsp;&nbsp;webu Azure](/azure/site-recovery/site-recovery-overview)|Online služba, která replikuje úlohy spuštěné na fyzických a virtuálních počítačích (VM) z primární lokality do sekundárního umístění, aby po selhání povolila obnovení služeb. |

## <a name="networking"></a>Síťové služby
|Služba|Popis|
|------|--------|
| [Skupiny&nbsp;zabezpečení sítě&nbsp;](/azure/virtual-network/virtual-networks-nsg)| Funkce řízení přístupu založená na síti pomocí 5 řazených členů řadění sítě k povolování nebo zamítnutí rozhodnutí.  |
| [Brána Azure VPN](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Síťové zařízení používané jako koncový bod VPN k povolení přístupu k virtuálním sítím Azure mezi místními.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Pokročilý správce zatížení webových aplikací, který může směrovat na základě adresy URL a provádět snižování zátěže SSL. |
|[Brána firewall webové aplikace](/azure/frontdoor/waf-overview) (WAF)|Funkce Application Gateway, která poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|Vyrovnávání zatížení aplikační sítě TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Vyhrazené propojení WAN mezi místními sítěmi a virtuálními sítěmi Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globální vyrovnávání zatížení DNS.|
| [Proxy aplikace Azure](/azure/active-directory/active-directory-application-proxy-get-started)| Ověřovací front-end používaný k zabezpečení vzdáleného přístupu pro místní webové aplikace. |
|[Azure Firewall](/azure/firewall/overview)|Spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|V kombinaci s osvědčenými postupy návrhu aplikace poskytuje ochranu proti útokům DDoS.|
|[Koncové body služby Virtuální síť](/azure/virtual-network/virtual-network-service-endpoints-overview)|Rozšiřuje privátní adresní prostor virtuální sítě a identitu vaší virtuální sítě na služby Azure prostředně prostředně přímého připojení.|