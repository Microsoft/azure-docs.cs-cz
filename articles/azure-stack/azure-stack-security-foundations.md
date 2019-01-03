---
title: Principy ovládacích prvků zabezpečení služby Azure Stack
description: Jako správce služeb Další informace o zabezpečení ovládacích prvků použitá ke službě Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2018
ms.author: patricka
ms.openlocfilehash: 8b478c1ba60df679d69d5fced660836c16079e6a
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727085"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Stav zabezpečení infrastruktury služby Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Důležité informace o zabezpečení a dodržování předpisů patří mezi hlavní ovladačů pro použití hybridních cloudů. Azure Stack je navržena pro tyto scénáře. Tento článek vysvětluje ovládacích prvků zabezpečení v místě pro službu Azure Stack.

Dvě vrstvy stavu zabezpečení ve službě Azure Stack existovat vedle sebe. První vrstva je infrastruktura Azure stacku, která zahrnuje hardwarové součásti až Azure Resource Manageru. První vrstva zahrnuje správce a Tenanta portálů. Druhá vrstva se skládá z úlohy vytvořené, nasazují a spravují tenanty. Druhá vrstva zahrnuje různé věci, třeba virtuálních počítačů a webů App Services.

## <a name="security-approach"></a>Zabezpečení přístupu

Stav zabezpečení pro službu Azure Stack je navržen pro ochranu před moderními hrozbami a byla vytvořena pro splnění požadavků z hlavní dodržování předpisů standardů. Stav zabezpečení infrastruktury služby Azure Stack v důsledku toho je postavená na dvou pilíře:

 - **Předpokládej chybu zabezpečení**  
Spuštění z předpokladu, že systém již došlo k nedodržení, zaměřte se na *zjišťování a omezit dopad těchto porušení* oproti pouze při prevenci proti útokům. 
 - **Posílené už ve výchozím nastavení**  
Že díky infrastruktuře provozované na jasně definovaném hardwaru a softwaru, služby Azure Stack *povolí, konfiguruje a ověří všechny funkce zabezpečení* ve výchozím nastavení.

Protože Azure Stack se dodává jako integrovaný systém, je definován stav zabezpečení infrastruktury Azure stacku společností Microsoft. Stejně jako v Azure, klienti jsou zodpovědné za definování stav zabezpečení svých úloh tenanta. Tento dokument obsahuje základní znalosti o stavu zabezpečení infrastruktury Azure stacku.

## <a name="data-at-rest-encryption"></a>Data šifrování neaktivních dat
Všechny služby Azure Stack infrastruktury a klientského data se šifrují v klidu pomocí Bitlockeru. Toto šifrování se chrání před fyzické ztráty či odcizení komponent úložiště služby Azure Stack. Další informace najdete v tématu [data šifrování neaktivních dat ve službě Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Data v šifrování přenosu
Součásti infrastruktury Azure stacku komunikaci pomocí kanálů, které jsou šifrované pomocí protokolu TLS 1.2. Certifikáty šifrování samoobslužných spravuje infrastrukturu. 

Všechny koncové body externí infrastruktury, jako jsou koncové body REST nebo na portálu Azure Stack podporovala TLS 1.2 pro zabezpečenou komunikaci. Pro tyto koncové body je třeba zadat šifrovací certifikáty, buď z jiného výrobce nebo certifikační autorita rozlehlé sítě. 

Certifikáty podepsané svým držitelem můžete využít pro tyto externí koncové body, Microsoft důrazně nedoporučuje jejich používání. 

## <a name="secret-management"></a>Správa tajných kódů
Infrastruktura Azure stacku používá velké množství tajné kódy, jako jsou hesla, aby fungoval. Většina z nich jsou automaticky otočit často, protože jsou účty Group-Managed služby, které otočit každých 24 hodin.

Zbývající tajné klíče, které nejsou účty služeb Group-Managed lze otočit ručně pomocí skriptu v privilegovaných koncový bod.

## <a name="code-integrity"></a>Integrita kódu
Azure Stack využívá nejnovější Windows serveru 2016 funkce zabezpečení. Jeden z nich je Windows Defender Device Guard, která zajišťuje seznamu povolených aplikací a zajišťuje, který pouze oprávnění kód běží v rámci infrastruktury Azure stacku. 

Autorizovaného kódu je podepsán společností Microsoft nebo partnera výrobce OEM. Podepsané autorizovaného kódu je zahrnuta v seznamu povolených softwaru uveden v zásadách definované microsoftem. Jinými slovy mohou být provedeny pouze software, který je schválená pro spuštění v infrastruktuře Azure Stack. Pokus o provedení neoprávněný kód blokovaný a je generována auditu.

Zásady Device Guard zabrání ve spuštění v infrastruktuře Azure Stack také třetích stran agentů nebo softwaru.

## <a name="credential-guard"></a>Credential Guard
Další funkce zabezpečení Windows serveru 2016 ve službě Azure Stack je Windows Defender Credential Guard, který se používá k ochraně přihlašovacích údajů k Azure Stack infrastruktury před Pass-the-Hash a Pass-the-Ticket útoky.

## <a name="antimalware"></a>Antimalware
Všechny komponenty ve službě Azure Stack (hostitele Hyper-V a virtuálních počítačů) je chráněný pomocí antivirové ochrany v programu Windows Defender.

V propojených scénářích se použijí antivirové aktualizace definic a stroje a více než jednou za den. V odpojených scénářů aktualizací antimalwarového softwaru se použijí jako součást měsíční aktualizace služby Azure Stack. Další informace najdete v tématu [aktualizovat antivirové ochrany Windows Defender ve službě Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Model omezeného správy
Správy ve službě Azure Stack je řízen pomocí tří vstupních bodů, každý s konkrétním účelem: 
1. [Portálu správce](azure-stack-manage-portals.md) poskytuje možnosti ukázat a kliknout pro každodenní operace správy.
2. Azure Resource Manageru zpřístupňuje všechny operace správy portálu správce prostřednictvím rozhraní REST API, Powershellu a rozhraní příkazového řádku Azure. 
3. Pro konkrétní operace nízké úrovně, například data center integrace nebo podporují scénáře, Azure Stack zpřístupňuje koncový bod Powershellu volá [privilegovaných koncový bod](azure-stack-privileged-endpoint.md). Tento koncový bod vystavuje pouze přidat na seznam povolených sadu rutin a výrazně se Audituje.

## <a name="network-controls"></a>Ovládací prvky sítě
Infrastruktura Azure stacku se dodává s víc vrstvami sítě seznamu řízení přístupu (ACL). Seznamy ACL zabránit neoprávněnému přístupu k součástem infrastruktury a omezit infrastruktury komunikaci jenom cesty, které jsou vyžadovány pro její fungování. 

Seznamy ACL sítě se vynucují ve třech vrstvách:
1.  Top-of-Rack přepínače
2.  Softwarově definované sítě
3.  Brány firewall operačního systému hostitele a virtuálního počítače

## <a name="regulatory-compliance"></a>Dodržování legislativní předpisů

Azure Stack je prošli Formální vyhodnocování auditování podnikem nezávislé třetí strany. Dokumentace o tom, jak infrastruktura Azure stacku splňuje použitelné ovládací prvky z několika standardy pro dodržování předpisů hlavní v důsledku toho je k dispozici. V dokumentaci k není kvůli standardy, včetně několik ovládacích prvků související s pracovníky a související s procesem certifikace služby Azure Stack. Místo toho zákazníci mohou tuto dokumentaci využít ke pokročilé podpoří jejich procesu certifikace.

Posouzení, která zahrnují následující normy:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) adresy odvětví platebních karet.
- [Matice CSA cloudu ovládacích prvků](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) je komplexní mapování mezi více standardy, včetně střední FedRAMP, ISO27001, HIPAA, HITRUST, ITAR, NIST SP800-53 a další.
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) pro zákazníky ze státní správy.

Dokumentace k dodržování předpisů najdete v [Microsoft Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/Blueprint). Průvodci dodržování předpisů jsou chráněné prostředky a vyžadují, abyste přihlásit pomocí přihlašovacích údajů Azure cloud service.

## <a name="next-steps"></a>Další postup

- [Zjistěte, jak otočit vaše tajné kódy ve službě Azure Stack](azure-stack-rotate-secrets.md)
- [PCI DSS a CSA-CCM dokumentů pro službu Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Ministerstva obrany USA a NIST dokumentů pro službu Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
