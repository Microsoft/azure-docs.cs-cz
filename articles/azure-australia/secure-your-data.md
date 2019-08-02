---
title: Zabezpečení dat v Azure Austrálie
description: Konfigurace Azure v rámci australských oblastí pro splnění konkrétních požadavků na zásady, předpisy a právní předpisy australské vlády.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0301d506fc5764dbfda496727da95b2a1f2e9c7e
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608346"
---
# <a name="data-security-in-azure-australia"></a>Zabezpečení dat v Azure Austrálie

Principy zabezpečení zákaznických dat jsou tyto:

* Ochrana dat pomocí šifrování
* Správa tajných kódů
* Omezení přístupu k datům

## <a name="encrypting-your-data"></a>Šifrování dat

Šifrování dat se dá použít na úrovni disku (v klidovém režimu), v databázích (na chvíli a v přenosu), v aplikacích (v přenosech) a v síti (v rámci přenosu). Existuje několik způsobů, jak v Azure dosáhnout šifrování:

|Služba/funkce|Popis|
|---|---|
|Storage Service Encryption|Šifrování služby Azure Storage je povolené na úrovni účtu úložiště, což způsobí, že se objekty blob bloku a objekty blob stránky při zápisu do Azure Storage automaticky šifrují. Když si přečtete data z Azure Storage, před vrácením se dešifruje službou úložiště. Použijte SSE k zabezpečení vašich dat, aniž byste museli upravovat nebo přidávat kód do žádné aplikace.|
|Azure Disk Encryption|Použijte Azure Disk Encryption k šifrování disků s operačním systémem a datových disků, které používá virtuální počítač Azure. Integrace s Azure Key Vault poskytuje kontrolu a pomáhá spravovat klíče pro šifrování disků.|
|Šifrování na úrovni aplikace na straně klienta|Šifrování na straně klienta je integrováno do klientských knihoven Java a .NET Storage, které mohou využívat Azure Key Vault rozhraní API, což usnadňuje implementaci. Pomocí Azure Key Vault můžete získat přístup k tajným klíčům v Azure Key Vault pro konkrétní jednotlivce, kteří používají Azure Active Directory.|
|Šifrování při přenosu|Základní šifrování dostupné pro připojení k Azure Austrálie podporuje protokol TLS (Transport Level Security) 1,2 a certifikáty X. 509. Kryptografické algoritmy standardu FIPS (Federal Information Processing Standard) 140-2 úrovně 1 se používají také pro síťová připojení infrastruktury mezi datovými centry Azure Austrálie.  Windows Server 2016, Windows 10, Windows Server 2012 R2, Windows 8.1 a sdílené složky Azure můžou používat protokol SMB 3,0 pro šifrování mezi virtuálním počítačem a sdílenou složkou. Pomocí šifrování na straně klienta Zašifrujte data před přenosem do úložiště v klientské aplikaci a dešifrujte data poté, co se přenese mimo úložiště.|
|Virtuální počítače s IaaS|Použijte Azure Disk Encryption. Zapněte Šifrování služby Storage k zašifrování souborů VHD, které se používají k zálohování těchto disků v Azure Storage, ale zašifruje jenom nově zapsaná data. To znamená, že pokud vytvoříte virtuální počítač a potom povolíte Šifrování služby Storage v účtu úložiště, který obsahuje soubor VHD, budou se šifrovat jenom změny, ne původní soubor VHD.|
|Šifrování na straně klienta|Jedná se o nejbezpečnější metodu šifrování dat, protože ji před přenosem zašifruje a zašifruje neaktivní data. Vyžaduje se ale, abyste do svých aplikací přidali kód pomocí úložiště, které nebudete chtít dělat. V těchto případech můžete použít HTTPS pro vaše data při přenosu a Šifrování služby Storage k šifrování neaktivních dat. Šifrování na straně klienta zahrnuje taky větší zátěž klienta – musíte se k tomu přihlédnout v plánech škálovatelnosti, zejména pokud šifrujete a převádíte velké objemy dat.|
|

Další informace o možnostech šifrování v Azure najdete v [příručce zabezpečení úložiště](https://docs.microsoft.com/azure/storage/storage-security-guide).

## <a name="protecting-data-by-managing-secrets"></a>Ochrana dat správou tajných klíčů

Pro ochranu dat v cloudu je nezbytná zabezpečená Správa klíčů. Zákazníci by se měli snažit zjednodušit správu klíčů a udržovat řízení klíčů používaných v cloudových aplikacích a službách k šifrování dat.

### <a name="managing-secrets"></a>Správa tajných kódů

* Použijte Key Vault k minimalizaci rizik tajných klíčů, které jsou zpřístupněny prostřednictvím pevně zakódovaných konfiguračních souborů, skriptů nebo ve zdrojovém kódu. Azure Key Vault šifruje klíče (například šifrovací klíče pro Azure Disk Encryption) a tajné klíče (například hesla) tak, že je ukládá na Standard FIPS 140-2 úrovně 2 (HSM) validované moduly hardwarového zabezpečení (). Pro zvýšení zabezpečení můžete v těchto HSM importovat nebo generovat klíče.
* Kód a šablony aplikace by měly obsahovat pouze odkazy na identifikátory URI (což znamená, že skutečná tajná klíče nejsou v kódu, konfiguraci nebo úložištích zdrojového kódu). To brání klíčovým útokům phishing v interních nebo externích úložištích, jako je například sklizeň-roboty na GitHubu.
* Využijte silné ovládací prvky RBAC v rámci Key Vault. Pokud důvěryhodný operátor opustí společnost nebo převede do nové skupiny v rámci společnosti, měla by jim být znemožněn přístup k tajným klíčům.  

Další informace najdete v tématu [Azure Key Vault](azure-key-vault.md)

## <a name="isolation-to-restrict-data-access"></a>Izolace pro omezení přístupu k datům

Izolace je vše o použití hranic, segmentace a kontejnerů k omezení přístupu k datům jenom na autorizované uživatele, služby a aplikace. Oddělení mezi klienty je například základním mechanismem zabezpečení pro víceklientské cloudové platformy, jako je například Microsoft Azure. Logická izolace pomáhá zabránit jednomu klientovi v konfliktu s operacemi jakéhokoli jiného tenanta.

#### <a name="per-customer-isolation"></a>Izolace podle zákazníka

Azure implementuje řízení přístupu k síti a oddělení přes izolaci sítě VLAN vrstvy 2, seznamy řízení přístupu, nástroje pro vyrovnávání zatížení a filtry IP.

Zákazníci mohou dále izolovat své prostředky mezi předplatnými, skupinami prostředků, virtuálními sítěmi a podsítěmi.

Další informace o izolaci v Microsoft Azure najdete v tématu věnovaném [izolaci ve veřejném cloudu Azure](../security/fundamentals/isolation-choices.md).

## <a name="next-steps"></a>Další postup

Přečtěte si článek o [VPN Gateway Azure](vpn-gateway.md)