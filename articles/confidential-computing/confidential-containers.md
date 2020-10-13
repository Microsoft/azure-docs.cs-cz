---
title: Důvěrné kontejnery ve službě Azure Kubernetes (AKS)
description: Přečtěte si o podpoře nezměněných kontejnerů v tajných kontejnerech.
services: container-service
author: agowdamsft
ms.topic: article
ms.date: 9/22/2020
ms.author: amgowda
ms.service: container-service
ms.openlocfilehash: 528b843e24e1d63e4822c253b3636ef490e8fe8e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995837"
---
# <a name="confidential-containers"></a>Důvěrné kontejnery

## <a name="overview"></a>Přehled

Umožněte vývojářům přenést **existující aplikaci Docker (novou nebo stávající)** a bezpečně ji Spouštějte ve službě Azure Kubernetes Service (AKS) prostřednictvím podpory důvěrného výpočetního uzlu.

Důvěrné kontejnery můžou chránit:

- Integrita dat 
- důvěrnost dat
- integrita kódu
- ochrana kódu kontejneru pomocí šifrování
- Hardwarová ujištění
- povolení spouštění existujících aplikací
- vytvořit kořenovou složku důvěryhodnosti hardwaru

TEE (hardware Trusted Execution Environment) je důležitou součástí, která poskytuje silné záruky prostřednictvím hardwarových a softwarových měření z komponent TCB (Trusted Computing Base). Ověřování těchto měření vám pomůžou s ověřením očekávaného výpočtu a ověření jakékoli manipulace s aplikacemi typu kontejner.

Důvěrné kontejnery podporují vlastní aplikace vyvinuté pomocí **Pythonu, Java, Node js atd. nebo zabalené softwarové aplikace jako Nginx, Redis Cache, MemCache**a tak dále, aby se spouštěly nezměněné v AKS.

Důvěrné kontejnery jsou nejrychlejší cestou k důvěrnosti kontejneru, včetně ochrany kontejnerů prostřednictvím šifrování, povolením zvedání a posunování s No/minimálními změnami v obchodní logice.

![Převod kontejneru důvěrné](./media/confidential-containers/conf-con-deploy-process.jpg)


## <a name="confidential-container-enablers"></a>Důvěrné příaktivátory kontejneru

Aby bylo možné spustit existující kontejner Docker, aplikace na důvěrných výpočetních uzlech vyžadují k využití speciální sady instrukcí procesoru abstraktní vrstvu nebo SGX software. Software SGX také umožňuje chránit kód citlivých aplikací a vytvořit přímé spuštění procesoru za účelem odebrání hostovaného operačního systému, hostitelského operačního systému nebo hypervisoru. Tato ochrana omezuje celkové oblasti útoků a ohrožení zabezpečení povrchu s využitím vrstev operačního systému nebo hypervisoru.

Důvěrné kontejnery jsou plně podporované v AKS a povolené prostřednictvím partnerů Azure a projektů s otevřeným zdrojovým softwarem (OSS). Vývojáři můžou zvolit poskytovatele softwaru na základě funkcí, integrace se službami Azure a podpory nástrojů.

## <a name="partner-enablers"></a>Aktivátory partnerů
> [!NOTE]
> Níže uvedená řešení nabízí partneři Azure a můžou se účtovat licenční poplatky. Ověřte prosím nezávisle na partnerském softwaru. 

### <a name="fortanix"></a>Fortanix

[Fortanix](https://www.fortanix.com/) nabízí vývojářům možnost výběru portálu a prostředí založeného na rozhraní PŘÍKAZového řádku, které umožní využít své aplikace v kontejneru, a převést je na SGX s podporou důvěrných kontejnerů, aniž by museli aplikaci upravovat nebo znovu kompilovat. Fortanix poskytuje flexibilitu pro spouštění a správu nejširší sady aplikací, včetně stávajících aplikací, nových enklávy aplikací a předem zabalených aplikací. Uživatelé můžou začít s uživatelským rozhraním [enklávy Manageru](https://em.fortanix.com/) nebo [rozhraními REST API](https://www.fortanix.com/api/em/) k vytváření důvěrných kontejnerů pomocí průvodce [rychlé zprovoznění](https://support.fortanix.com/hc/en-us/articles/360049658291-Fortanix-Confidential-Container-on-Azure-Kubernetes-Service) pro službu Azure Kubernetes.

![Proces nasazení Fortanix](./media/confidential-containers/fortanix-confidential-containers-flow.png)

### <a name="scone-scontain"></a>Scone (Scontain)

[Scone](https://scontain.com/index.html?lang=en) podporuje zásady zabezpečení, které mohou generovat certifikáty, klíče a tajné klíče, a zajišťuje, že jsou viditelné pouze pro ověřené služby aplikace. Tímto způsobem se služby aplikace automaticky potvrdí pomocí protokolu TLS – bez nutnosti upravovat aplikace ani protokol TLS. To je vysvětleno v nápovědě k aplikaci jednoduché baňky: https://sconedocs.github.io/flask_demo/  

SCONE může převést existující většinu binárních souborů do aplikací, které běží v enclaves, aniž by bylo nutné změnit aplikaci nebo tuto aplikaci znovu zkompilovat. SCONE také chrání interpretované jazyky, jako je Python, šifrováním datových souborů i souborů kódu Pythonu. Díky zásadám zabezpečení SCONE může jeden chránit šifrované soubory před neoprávněnými přístupy, úpravami a vráceními zpět. Postup "sconify" [zde](https://sconedocs.github.io/sconify_image/) je vysvětlena existující aplikace Python.

![Tok Scontain](./media/confidential-containers/scone-workflow.png)

Scone nasazení v tajných výpočetních uzlech s AKS jsou plně podporovaná a integrovaná. Začněte s ukázkovou aplikací tady https://sconedocs.github.io/aks/

### <a name="anjuna"></a>Anjuna

[Anjuna](https://www.anjuna.io/) poskytuje software platformy SGX, který umožňuje spouštět neupravené kontejnery v AKS. [Tady si můžete](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp)přečíst další informace o **nadcházejících** funkcích a toku uživatele.

Začněte s ukázkovými Redis Cache a [vlastní aplikací v](https://www.anjuna.io/microsoft-azure-confidential-computing-aks-lp) Pythonu

![Anjuna proces](./media/confidential-containers/anjuna-process-flow.png)

## <a name="oss-enablers"></a>Aktivátory OSS 
> [!NOTE]
> Níže uvedená řešení jsou nabízena prostřednictvím open source projektů a nejsou přímo přidružena k Azure důvěrné výpočetní službě (ACC) nebo společnosti Microsoft.  

### <a name="graphene"></a>Graphene

[Graphene](https://grapheneproject.io/) je jednoduchý hostovaný operační systém, který je navržený tak, aby spouštěl jednu aplikaci pro Linux s minimálními požadavky na hostitele. Graphene může spouštět aplikace v izolovaném prostředí s výhodami srovnatelnými s používáním kompletního operačního systému a má dobrou podporu nástrojů pro převod stávající aplikace kontejneru Docker na graphene stíněné kontejnery (GSR).

Začněte s ukázkovou aplikací a nasazením [v AKS.](https://graphene.readthedocs.io/en/latest/cloud-deployment.html#azure-kubernetes-service-aks)

### <a name="occlum"></a>Occlum
[Occlum](https://occlum.io/) je systém založený na paměti, LibOS (multi-Process Library) pro Intel SGX. Umožňuje, aby se starší aplikace spouštěly v SGX s malým množstvím změn zdrojového kódu. Occlum transparentně chrání důvěrnost uživatelských úloh a umožňuje snadné zvedání a posunování stávajících aplikací Docker.

Occlum podporuje nasazení AKS. Postupujte podle pokynů pro nasazení u různých ukázkových [aplikací.](https://github.com/occlum/occlum/blob/master/docs/azure_aks_deployment_guide.md)


## <a name="confidential-containers-demo"></a>Ukázka důvěrných kontejnerů
Prohlédněte si tajnou ukázku zdravotní péče s důvěrnými kontejnery. Ukázka je k dispozici [zde](https://github.com/Azure-Samples/confidential-container-samples/blob/main/confidential-healthcare-scone-confinf-onnx/README.md). 

> [!VIDEO https://www.youtube.com/embed/PiYCQmOh0EI]


## <a name="get-in-touch"></a>Buďte v kontaktu

Máte dotazy s implementací nebo se chcete stát EnableRSS? Odeslání e-mailu na adresu acconaks@microsoft.com

## <a name="reference-links"></a>Odkazy odkazů

[Microsoft Azure Attestation](../attestation/overview.md)

[DCsv2 Virtual Machines](virtual-machine-solutions.md)

[Azure Kubernetes Service (AKS)](../aks/intro-kubernetes.md)
