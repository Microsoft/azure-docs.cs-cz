---
title: Integrované systémy pro nasazení Azure odpojené rozhodnutí pro službu Azure Stack | Dokumentace Microsoftu
description: Určení při rozhodování o nasazení na víc uzlů připojeného k Azure stacku Azure pro plánování nasazení.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 4481bcf7a794423f98f45e4a21a139dbe4c32b4f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210777"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Integrované systémy pro nasazení Azure odpojené plánování rozhodnutí pro službu Azure Stack
Poté, co jste se rozhodli [jak budou integrovat Azure Stack v hybridním cloudovém prostředí](azure-stack-connection-models.md), můžete dokončit váš rozhodnutí o nasazení služby Azure Stack.

Můžete nasadit a používat služby Azure Stack bez připojení k Internetu. Odpojené nasazení jste ale omezeni na úložiště identity služby AD FS a model fakturace založená na kapacitě. Protože víceklientskou architekturu vyžaduje použití Azure Active Directory (Azure AD), se u odpojeného nasazení nepodporuje víceklientskou architekturu. 

Tuto možnost zvolte, pokud jste:
- Máte zabezpečení nebo jiná omezení, které vyžadují, abyste nasazení Azure Stack v prostředí, které není připojené k Internetu.
- Chcete blokovat data (včetně dat o využití) od odeslání do Azure
- Chcete použít Azure Stack čistě jako řešení privátního cloudu, který je nasazen na firemním intranetu a nemají zájem o hybridní scénáře.

> [!TIP]
> V některých případech tohoto typu prostředí se také označuje jako *podmořské scénář*.

Odpojené nasazení neomezuje mohli později připojení vaší instance služby Azure Stack do Azure pro hybridní scénáře virtuálních počítačů tenanta. To znamená, že nemáte připojení k Azure během nasazení nebo nechcete používat jako úložiště identit Azure AD.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funkce, které jsou poškozeny nebo není k dispozici v odpojeném nasazení 
Azure Stack je navržena pro spolupráci při připojení k Azure, takže je důležité si uvědomit, že jsou některé funkce a funkce, které jsou narušena nebo zcela není k dispozici v odpojeném režimu. 

|Funkce|Dopad na odpojeném režimu|
|-----|-----|
|Nasazení virtuálních počítačů pomocí rozšíření DSC ke konfiguraci po nasazení virtuálního počítače|Zrakově postižené – rozšíření DSC hledá k Internetu nejnovější WMF.|
|Nasazení virtuálních počítačů pomocí rozšíření Docker spouštět příkazy Dockeru|Zrakově postižené – Docker zkontroluje Internet pro nejnovější verzi a tato kontrola selže.|
|Odkazy na dokumentaci v portálu Azure Stack|Není k dispozici – odkazy, jako jsou odeslat zpětnou vazbu, pomoc, rychlém startu atd, které využívají internetové adresy URL nebudou fungovat.|
|Upozornění opravy/omezení rizik, která odkazuje na je online nápravy Průvodce|Není k dispozici – nápravy škod způsobených výstrah odkazy, které využívají internetové adresy URL nebudou fungovat.|
|Marketplace – možnost a vyberte a přidejte balíčky Galerie přímo z Azure Marketplace|Zrakově postižené – když nasadíte Azure Stack v odpojeném režimu (bez žádné připojení k Internetu), nelze stažení položek z marketplace pomocí portálu Azure Stack. Můžete však použít [marketplace syndikace nástroj](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) stažení položek z marketplace k počítači, který má připojení k Internetu a přenést je do prostředí Azure Stack.|
|Správa nasazení služby Azure Stack pomocí účtů Azure Active Directory federation|Není k dispozici – tato funkce vyžaduje připojení k Azure. Služba AD FS s místní instancí Active Directory musí použít.|
|App Services|Zrakově postižené – webové aplikace může vyžadovat přístup k Internetu pro aktualizaci obsahu.|
|Rozhraní příkazového řádku (CLI)|Zrakově postižené – rozhraní příkazového řádku snížila funkce z hlediska ověřování a zřizování instančních objektů.|
|Visual Studio – Cloud discovery|Cloud Discovery zrakově postižené – zjistí různých cloudů nebo nebude vůbec fungovat.|
|Visual Studio – AD FS|Zrakově postižené – pouze pro Visual Studio Enterprise podporuje služby AD FS.
Telemetrická data|Není k dispozici – Telemetrická data pro Azure Stack jako i pro případný třetích stran Galerie balíčků, které jsou závislé na telemetrická data.|
|Certifikáty|Není k dispozici – připojení k Internetu je vyžadován pro seznam odvolaných certifikátů (CRL) a protokolu protokolu (Online Certificate Status OSCP) služby v rámci protokolu HTTPS.|
|Key Vault|Zrakově postižené – běžným případem použití pro službu Key Vault je, aby aplikace čtení tajných klíčů v době běhu. Pro tuto aplikaci musí instančního objektu v adresáři. Ve službě Azure Active Directory běžní uživatelé (bez oprávnění správce) jsou ve výchozím nastavení povoleno přidat instančních objektů. Ve službě AD (pomocí služby AD FS) nejsou. To umístí mezní začátku do konce prostředí vzhledem k tomu, že jeden musí directory správce přidat libovolnou aplikaci vždy projít.| 

## <a name="learn-more"></a>Další informace
- Informace o případy použití, nákup, partnery a dodavateli hardwaru OEM, najdete v článku [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stránce produktu.
- Informace o plánu a geografické dostupnosti pro službu Azure Stack integrované systémy, najdete v dokumentu white paper: [Azure Stack: Rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Další informace o Microsoft Azure Stack balení a ceny [stáhnout PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další postup
[Integrace sítě datového centra](azure-stack-network.md)
