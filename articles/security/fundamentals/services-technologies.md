---
title: Služby a technologie zabezpečení Azure | Microsoft Docs
description: Tento článek poskytuje seznam spravovaných služeb a technologií zabezpečení Azure.
services: security
documentationcenter: na
author: terrylanfear
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
ms.author: terrylan
ms.openlocfilehash: 83548063a796401792bd19e7ec27be36a0efa4e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80549204"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Služby a technologie zabezpečení dostupné v Azure

V našich diskuzích s aktuálním a budoucím zákazníkům Azure se často požádáme o seznam všech služeb a technologií souvisejících se zabezpečením, které Azure nabízí.

Když vyhodnocujete možnosti poskytovatele cloudové služby, je vhodné tyto informace získat. Proto jsme tento seznam získali, abychom vám mohli začít.

V průběhu času se tento seznam změní a rozroste stejně jako Azure. Nezapomeňte pravidelně kontrolovat tuto stránku, abyste měli stále aktuální informace o našich službách a technologiích souvisejících se zabezpečením.

## <a name="general-azure-security"></a>Obecné zabezpečení Azure
|Služba|Popis|
|--------|--------|
|[Azure &nbsp; Security &nbsp; Center](/azure/security-center/security-center-intro)| Řešení ochrany zátěže v cloudu, které poskytuje správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudy.|
|[Azure Key Vault](/azure/key-vault/key-vault-overview)| Zabezpečené úložiště tajných kódů pro hesla, připojovací řetězce a další informace, které potřebujete k zajištění fungování vašich aplikací. |
|[Protokoly Azure Monitor](/azure/log-analytics/log-analytics-overview)|Služba monitorování, která shromažďuje telemetrii a jiná data a poskytuje dotazovací jazyk a analytický modul pro poskytování provozních přehledů pro vaše aplikace a prostředky. Dá se použít samostatně nebo s jinými službami, jako je Security Center. |
|[Vývojové a testovací cvičení pro Azure](/azure/lab-services/devtest-lab-overview)|Služba, která vývojářům a testerům pomáhá rychle vytvářet prostředí v Azure během minimalizace odpadů a řízení nákladů.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Zabezpečení úložiště
|Služba|Popis|
|------|--------|
| [&nbsp; &nbsp; Šifrování služby Azure &nbsp; Storage](/azure/storage/common/storage-service-encryption)|Funkce zabezpečení, která automaticky šifruje vaše data ve službě Azure Storage.   |
|[StorSimple šifrované hybridní úložiště](/azure/storsimple/storsimple-ova-overview)| Integrované řešení úložiště, které spravuje úlohy úložiště mezi místními zařízeními a cloudovým úložištěm Azure.|
|[Šifrování Azure Client-Side](/azure/storage/common/storage-client-side-encryption)| Řešení šifrování na straně klienta, které šifruje data v klientských aplikacích před odesláním do Azure Storage; také dešifruje data při stahování. |
| [Azure Storage signatury sdíleného přístupu](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)|Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště.  |
|[Azure Storage klíče účtu](/azure/storage/common/storage-create-storage-account)| Metoda řízení přístupu pro Azure Storage, která se používá k ověřování při přístupu k účtu úložiště. |
|[Sdílené složky Azure s šifrováním SMB 3,0](/azure/storage/files/storage-files-introduction)|Technologie zabezpečení sítě, která umožňuje automatické šifrování sítě pro protokol sdílení souborů protokolu SMB (Server Message Block). |
|[Analýza úložiště Azure](/rest/api/storageservices/Storage-Analytics)| Technologie protokolování a generování metrik pro data v účtu úložiště. |

<!------>

## <a name="database-security"></a>Zabezpečení databází
|Služba|Popis|
|------|--------|
| [Azure &nbsp; SQL &nbsp; firewall](/azure/sql-database/sql-database-firewall-configure)|Funkce řízení přístupu k síti, která chrání před útoky prostřednictvím sítě do databáze. |
|[&nbsp;Šifrování na &nbsp; úrovni buňky SQL &nbsp; Azure](https://blogs.msdn.microsoft.com/sqlsecurity/2015/05/12/recommendations-for-using-cell-level-encryption-in-azure-sql-database/)| Technologie zabezpečení databáze, která poskytuje šifrování na podrobné úrovni.  |
| [&nbsp; &nbsp; Šifrování připojení Azure SQL](/azure/sql-database/sql-database-control-access)|Služba SQL Database zajišťuje zabezpečení řízením přístupu pomocí pravidel brány firewall omezujících připojení podle IP adresy, ověřovacích mechanismů vyžadujících po uživatelích prokázání identity a autorizačních mechanismů omezujících uživatelům přístup ke konkrétním akcím a datům. |
| [Vždycky se šifrování Azure SQL](/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017)|Chrání citlivá data, jako jsou třeba čísla kreditních karet nebo národní identifikační čísla (například čísla sociálního pojištění USA) uložená v databázích Azure SQL Database nebo SQL Server.  |
| [&nbsp;Transparentní šifrování dat Azure SQL &nbsp;](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?view=azuresqldb-current)| Funkce zabezpečení databáze, která šifruje úložiště celé databáze. |
| [Auditování Azure SQL Database](/azure/sql-database/sql-database-auditing)|Funkce auditování databáze, která sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage.  |


## <a name="identity-and-access-management"></a>Správa identit a přístupu
|Služba|Popis|
|------|--------|
| [&nbsp; &nbsp; Access Control na základě rolí Azure &nbsp;](/azure/active-directory/role-based-access-control-configure)|Funkce řízení přístupu navržená tak, aby uživatelům umožnila přístup pouze k prostředkům, které potřebují k přístupu na základě jejich rolí v rámci organizace.  |
| [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)|Cloudové úložiště ověřování, které podporuje víceklientské cloudové adresáře a služby více služeb správy identit v rámci Azure.  |
| [Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview)|Služba správy identit, která umožňuje řídit, jak se můžou zákazníci přihlašovat, přihlašovat a spravovat jejich profily při používání aplikací založených na Azure.   |
| [Azure Active Directory Domain Services](/azure/active-directory-domain-services/overview)| Cloudová a spravovaná verze Active Directory Domain Services. |
| [Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)| Zřizování zabezpečení, které využívá několik různých forem ověřování a ověření před tím, než povolí přístup k zabezpečeným informacím. |

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii
|Služba|Popis|
|------|--------|
| [&nbsp;Zálohování Azure](/azure/backup/backup-introduction-to-azure-backup)| Služba založená na Azure, která slouží k zálohování a obnovení dat v cloudu Azure. |
| [Azure &nbsp; site &nbsp; Recovery](/azure/site-recovery/site-recovery-overview)|Online služba, která replikuje úlohy spuštěné na fyzických a virtuálních počítačích z primární lokality do sekundárního umístění, aby bylo možné po selhání obnovit služby. |

## <a name="networking"></a>Sítě
|Služba|Popis|
|------|--------|
| [&nbsp;Skupiny zabezpečení &nbsp; sítě](/azure/virtual-network/virtual-networks-nsg)| Funkce řízení přístupu k síti pomocí 5 řazené kolekce členů k rozhodování o povolení nebo zamítnutí.  |
| [Azure VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways)| Síťové zařízení používané jako koncový bod VPN, které umožňuje přístup mezi místními sítěmi k virtuálním sítím Azure.  |
| [Azure Application Gateway](/azure/application-gateway/application-gateway-introduction)|Pokročilý nástroj pro vyrovnávání zatížení webové aplikace, který může směrovat na základě adresy URL a provádění přesměrování zpracování SSL. |
|[Firewall webových aplikací](/azure/frontdoor/waf-overview) (WAF)|Funkce Application Gateway, která poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení|
| [Azure Load Balancer](/azure/load-balancer/load-balancer-overview)|Aplikační Nástroj pro vyrovnávání zatížení sítě TCP/UDP. |
| [Azure ExpressRoute](/azure/expressroute/expressroute-introduction)| Vyhrazené propojení WAN mezi místními sítěmi a virtuálními sítěmi Azure. |
| [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview)| Globální nástroj pro vyrovnávání zatížení DNS.|
| [Azure Application proxy](/azure/active-directory/active-directory-application-proxy-get-started)| Ověřuje se front-end, který se používá k zabezpečení vzdáleného přístupu pro webové aplikace hostované v místním prostředí. |
|[Azure Firewall](/azure/firewall/overview)|Spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky Azure Virtual Network.|
|[Azure DDoS Protection](/azure/virtual-network/ddos-protection-overview)|V kombinaci s osvědčenými postupy pro návrh aplikace poskytuje ochranu před DDoS útoky.|
|[Koncové body služby Virtual Network](/azure/virtual-network/virtual-network-service-endpoints-overview)|Rozšiřuje privátní adresní prostor vaší virtuální sítě a identitu vaší virtuální sítě do služeb Azure přes přímé připojení.|