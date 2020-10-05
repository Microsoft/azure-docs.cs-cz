---
title: Přehled důvěrných výpočetních řešení Azure
description: Přehled výpočetních funkcí Azure důvěrné (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 16f45c39a329998f4b4da4ea89315683a0fab790
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90967584"
---
# <a name="confidential-computing-on-azure"></a>Důvěrné výpočetní prostředí v Azure

Důvěrné výpočetní prostředí Azure umožňuje izolovat citlivá data při jejich zpracovávání v cloudu. Mnohé z odvětví využívají důvěrné výpočetní prostředí k ochraně svých dat pomocí důvěrného výpočetního prostředí:

- Zabezpečená finanční data
- Ochrana informací o pacientech
- Spouštění procesů strojového učení na citlivých informacích
- Provádění algoritmů u šifrovaných datových sad z více zdrojů


## <a name="overview"></a>Přehled
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Víme, že zabezpečení dat v cloudu je důležité. Slyšeli jsme vaše obavy. Tady je jen pár otázek, které můžou mít naši zákazníci při přesunu citlivých úloh do cloudu: 

- Návody zajistěte, aby Microsoft nemohly získat přístup k nešifrovaným datům?
- Návody v rámci své společnosti zabránit bezpečnostním hrozbám od privilegovaných správců?
- Jaké jsou další způsoby, jak můžu zabránit třetím stranám v přístupu k citlivým datům zákazníků?

Microsoft Azure pomáhá minimalizovat plochu útoku a získat tak silnější ochranu dat. Azure už nabízí spoustu nástrojů pro ochranu uložených dat prostřednictvím modelů, jako je šifrování [**na**](../security/fundamentals/encryption-atrest.md) straně klienta a šifrování na straně serveru. Azure navíc nabízí mechanismy pro šifrování [**dat při přenosu**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) prostřednictvím zabezpečených protokolů, jako je TLS a HTTPS. Tato stránka představuje třetí nožku šifrování dat – šifrování **používaných dat**.

## <a name="introduction-to-confidential-computing"></a>Úvod do důvěrného výpočetního prostředí 

Důvěrné výpočetní prostředí je obor, který je definovaný [konsorciem důvěrného computingu](https://confidentialcomputing.io/) (CCC) – základ vyhrazený pro definování a urychlení přijetí důvěrného výpočetního prostředí. CCC definuje důvěrné výpočetní prostředky jako: Ochrana dat, která se používají, provádí výpočty v prostředí TEE (hardware-based Trusted Execution Environment).

TEE je prostředí, které vynutilo provádění pouze autorizovaného kódu. Žádná data v TEE nemůžou číst ani úmyslně poškodit žádný kód mimo toto prostředí. 

### <a name="lessen-the-need-for-trust"></a>Zmenšení nutnosti vztahu důvěryhodnosti
Spouštění úloh v cloudu vyžaduje důvěryhodnost. Tento vztah důvěryhodnosti udělíte různým poskytovatelům, kteří umožňují různé komponenty vaší aplikace.


**Dodavatelé softwaru aplikací**: důvěřovat softwaru nasazením Prem, pomocí Open Source nebo vytvořením interního aplikačního softwaru.

**Dodavatelé hardwaru**: důvěřovat hardwaru pomocí místního hardwaru nebo interního hardwaru. 

**Poskytovatelé infrastruktury**: důvěryhodní poskytovatelé cloudu nebo spravovat vlastní místní datová centra.


Důvěrné výpočetní prostředí Azure usnadňuje důvěřování poskytovateli cloudu tím, že snižuje nutnost důvěry v různých aspektech výpočetní cloudové infrastruktury. Důvěrné výpočetní prostředí Azure minimalizuje důvěru pro jádro hostitelského operačního systému, hypervisor, Správce virtuálních počítačů a správce hostitele.

### <a name="reducing-the-attack-surface"></a>Zmenšení prostoru pro útoky
Trusted Computing Base (TCB) odkazuje na všechny softwarové komponenty, firmware a softwarové součásti systému, které poskytují zabezpečené prostředí. Součásti uvnitř TCB se považují za "kritické". Pokud dojde k ohrožení jedné součásti uvnitř TCB, může být ohrožena bezpečnost celého systému. 

Nižší TCB znamená vyšší zabezpečení. Existuje méně nebezpečí vystavení různým chybám zabezpečení, malwaru, útokům a škodlivým lidem. Důvěrné výpočetní prostředí Azure má za cíl snížit TCB pro vaše cloudové úlohy tím, že nabídne TEEs. TEEs Snižte svůj TCB na důvěryhodné binární soubory, kód a knihovny. Pokud používáte infrastrukturu a služby Azure pro důvěrný výpočetní výkon, můžete ze svého TCB odebrat všechny společnosti Microsoft.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Použití Azure pro cloudově založené důvěrné výpočetní účely <a id="cc-on-azure"></a>

Důvěrné výpočetní prostředí Azure umožňuje využívat důvěrné výpočetní funkce ve virtualizovaném prostředí. Nyní můžete používat nástroje, software a cloudovou infrastrukturu k sestavování nad zabezpečeným hardwarem.  

**Zabránit neoprávněnému přístupu**: spouštějte citlivá data v cloudu. Důvěřovat tomu, že Azure poskytuje nejlepší možnou ochranu dat, a to s malým množstvím změn, co se ještě dnes děje.

**Dodržování předpisů**: migrujte do cloudu a udržujte plnou kontrolu nad daty, která budou splňovat předpisy státní správy pro ochranu osobních údajů a zabezpečení IP adresy organizace.

**Zabezpečená a nedůvěryhodná spolupráce**: řešení problémů s pracovním měřítkem napříč různými organizacemi, dokonce i soutěžiteli, k odemknutí rozsáhlých datových analýz a hlubších přehledů.

**Izolované zpracování**: nabízí novou vlnu produktů, které odstraňují zodpovědnost na soukromá data pomocí nenevidomého zpracování. Poskytovatel služby nemůže ani načíst uživatelská data. 

## <a name="get-started"></a>Začínáme
### <a name="azure-compute"></a>Azure Compute
Sestavujte aplikace nad důvěrné nabídky výpočetních IaaS v Azure.
- Virtual Machines (virtuální počítače): [DCsv2-Series](confidential-computing-enclaves.md)
- Azure Kubernetes (AKS): [orchestrace důvěrných kontejnerů](confidential-nodes-aks-overview.md)

### <a name="azure-security"></a>Zabezpečení Azure 
Zajistěte, aby byly vaše úlohy zabezpečené prostřednictvím metod ověřování a správy klíčů na základě hardwaru. 
- Ověření identity: [Microsoft Azure ověřování (Preview)](https://docs.microsoft.com/azure/attestation/overview)
- Správa klíčů: spravovaná – HSM (Preview)

### <a name="develop"></a>Vývoj
Začněte používat vývoj aplikací pracujících v enklávy a nasaďte důvěrné algoritmy pomocí důvěrného Inferencing Frameworku.
- Psaní aplikací pro spouštění na virtuálních počítačích s DCsv2: [Open-ENKLÁVY SDK](https://github.com/openenclave/openenclave)
- Důvěrné modely v ONNX Runtime: [důvěrné Inferencing (beta verze)](https://aka.ms/confidentialinference)

## <a name="next-steps"></a>Další kroky

Nasaďte virtuální počítač s DCsv2-Series a nainstalujte na něj sadu OE SDK.

> [!div class="nextstepaction"]
> [Nasazení virtuálního počítače s důvěrným výpočetním prostředím v Azure Marketplace](quick-create-marketplace.md)
