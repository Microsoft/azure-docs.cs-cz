---
title: Přehled Azure Attestation
description: Přehled ověřování Microsoft Azure, řešení pro ověřování prostředí důvěryhodných spuštění (TEEs)
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: a84308ba06a38cea475fcb1bae022da16424a731
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90032992"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (Preview)

Ověřování identity Microsoft Azure (Preview) je řešení pro ověřování prostředí pro důvěryhodné spuštění (TEEs), jako je například [rozšíření Intel® software Guard](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) Enclaves a [zabezpečení na základě virtualizace](/windows-hardware/design/device-experiences/oem-vbs) (VBS) enclaves. Enklávy Attestation je proces pro ověření, že je enklávy zabezpečený a důvěryhodný.

Ověření identity je proces, který demonstruje, že binární soubory softwaru byly správně vytvořeny na důvěryhodné platformě. Vzdálené předávající strany pak mohou získat jistotu, že na důvěryhodném hardwaru je spuštěn pouze takový zamýšlený software. Ověření identity Azure je jednotná služba a architektura pro uživatele, které jsou pro ověřování identity zaměřené na zákazníky.

Azure Attestation umožňuje vymezit hraniční paradigma, jako je [Azure důvěrné výpočetní](../confidential-computing/overview.md) prostředí a inteligentní hraniční zabezpečení. Zákazníci požádali o možnost nezávisle ověřit umístění počítače, stav virtuálního počítače na daném počítači a prostředí, ve kterém enclaves na daném VIRTUÁLNÍm počítači běží. Azure Attestation bude tyto a spousta dalších žádostí o zákazníky.

Azure Attestation získává legitimaci od výpočetních entit, předává je do sady deklarací, ověřuje je u aplikací založených na deklaracích (například předávající strany a autority auditování) a vytváří kryptografické kontroly pro aplikace založené na deklaracích.

## <a name="use-cases"></a>Případy použití

Azure Attestation poskytuje komplexní služby ověření identity pro více prostředí a případy s výrazným použitím.

### <a name="sgx-attestation"></a>Ověření identity SGX

SGX odkazuje na izolaci na základě hardwarového výkonu, která je podporovaná na určitých modelech procesorů Intel. SGX umožňuje, aby kód běžel v upravených oddílech známých jako SGX enclaves. Oprávnění k přístupu a paměti se pak spravují pomocí hardwaru, aby se zajistilo minimum prostoru pro útoky se správnou izolací.

Klientské aplikace můžou být navržené tak, aby využívaly výhod SGX enclaves tím, že v těchto enclaves probíhají úlohy citlivé na zabezpečení. Takové aplikace pak můžou využít ověření Azure pro rutinní vytvoření vztahu důvěryhodnosti v enklávy a jeho schopnost přistupovat k citlivým datům.

### <a name="vbs-attestation"></a>VBS Attestation

VBS je softwarová architektura pro ochranu enklávy paměti založenou na technologii Hyper-V. Brání v kódu správce hostitele a správcům místních a cloudových služeb přístup k datům v VBS enklávy nebo ovlivnění jejího spuštění.

Podobně jako u technologie SGX podporuje Azure Attestation ověřování VBS enclaves proti nakonfigurovaným zásadám a vydávání prohlášení o certifikaci jako důkaz platnosti.

### <a name="open-enclave"></a>Otevřít enklávy
[Open enklávy](https://openenclave.io/sdk/) (OE) je kolekce knihoven, které cílí na vytvoření jedné sjednocené enclaving abstrakce pro vývojáře pro vytváření aplikací založených na Tee. Nabízí univerzální zabezpečený model aplikace, který minimalizuje určité platformy. Microsoft se zobrazí jako základní krok – kámen pro democratizing hardwarové technologie enklávy, jako je SGX, a zvyšování jejich přijetí v Azure.

OE standardizace specifické požadavky pro ověření enklávy důkazů. To se říká jako vysoce škálovatelný spotřebitel ověření identity Azure.

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation může běžet v TEE.

Azure Attestation je zásadní pro důvěrné výpočetní scénáře, protože provádí následující akce:

- Ověří, jestli je legitimace enklávy platná.
- Vyhodnotí enklávy legitimace proti zákaznicky definovaným zásadám.
- Spravuje a ukládá zásady pro konkrétního tenanta.
- Generuje a podepíše token, který používají předávající strany k interakci s enklávy.

Azure Attestation je sestavená tak, aby běžela ve dvou typech prostředí:
- Azure Attestation běží v TEE s povoleným SGX.
- Azure Attestation běží v TEE.

Zákazníci Azure Attestation vyjádřili požadavek, aby Microsoft mohl být v rámci důvěryhodných výpočetních bází (TCB) vydaný jako provozní. K tomu je potřeba zabránit entitám společnosti Microsoft, jako jsou správci virtuálních počítačů, hostitelé správců a vývojářů Microsoftu, v úpravách požadavků na ověření identity, zásad a tokenů vydaných službou Azure Attestation. Azure Attestation je taky sestavená tak, aby se spouštěla v TEE, kde se do SGX enklávy přesunou funkce ověření identity Azure, jako je ověřování nabídek, generování tokenů a podepisování tokenů.

## <a name="why-use-azure-attestation"></a>Proč používat ověřování Azure

Ověření identity Azure je upřednostňovanou volbou pro ověřování TEEs, protože nabízí následující výhody: 

- Sjednocené rozhraní pro ověřování více TEEs, například SGX enclaves a VBS enclaves
- Víceklientská služba, která umožňuje konfiguraci vlastních poskytovatelů a zásad ověřování identity pro omezení generování tokenů
- Nabízí výchozí poskytovatele, kteří můžou potvrzovat bez konfigurace od uživatelů.
- Chrání data při použití s implementací v SGX enklávy.
- Služba s vysokou dostupností, která bude nabízet smlouva SLA (SLA)

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>Podpora pro provozní kontinuitu a zotavení po havárii (BCDR)

Provozní [kontinuita a zotavení po havárii](/azure/best-practices-availability-paired-regions) (BCDR) pro Azure Attestation umožňuje zmírnit přerušení služeb vyplývající z významných problémů s dostupností nebo událostí po havárii v oblasti.

Níže jsou uvedené oblasti, které aktuálně podporuje BCDR.
- Východní USA 2 => spárována s Střed USA.
- Střed USA => spárována s Východní USA 2.

Clustery nasazené ve dvou oblastech budou za běžných okolností fungovat nezávisle. V případě chyby nebo výpadku jedné oblasti dojde k následujícímu:

- Azure Attestation BCDR poskytne bezproblémové převzetí služeb při selhání, ve kterém si zákazníci nemusí dělat další krok k obnovení.
- [Azure Traffic Manager](../traffic-manager/index.yml) pro oblast detekuje sondu stavu a přepne koncový bod do spárované oblasti.
- Existující připojení nebudou fungovat a budou se zobrazovat interní chyby serveru nebo problémy s časovým limitem.
- Všechny operace roviny ovládacího prvku budou zablokovány. Zákazníci nebudou moct vytvářet poskytovatele ověření identity a zásady aktualizace v primární oblasti.
- Všechny operace roviny dat, včetně volání ověřování, budou i nadále fungovat v primární oblasti.

## <a name="next-steps"></a>Další kroky
- Seznamte se se [základními koncepty Azure Attestation](basic-concepts.md)
- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)

