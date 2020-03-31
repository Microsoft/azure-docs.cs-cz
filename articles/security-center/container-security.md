---
title: Zabezpečení kontejnerů v Azure Security Center | Dokumenty společnosti Microsoft
description: Přečtěte si o funkcích zabezpečení kontejnerů Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125140"
---
# <a name="container-security-in-security-center"></a>Zabezpečení kontejneru v Centru zabezpečení

Azure Security Center je nativní řešení Azure pro zabezpečení kontejnerů. Security Center je také optimální jediné podokno prostředí skla pro zabezpečení vašich cloudových úloh, virtuálních počítačů, serverů a kontejnerů.

Tento článek popisuje, jak vám Centrum zabezpečení pomáhá vylepšovat, monitorovat a udržovat zabezpečení kontejnerů a jejich aplikací. Dozvíte se, jak Security Center pomáhá s těmito základními aspekty zabezpečení kontejnerů:

* Správa zranitelnosti
* Kalení prostředí kontejneru
* Ochrana runtime

[![Karta zabezpečení kontejneru Centra zabezpečení Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Pokyny k používání těchto funkcí naleznete [v tématu Sledování zabezpečení kontejnerů](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Správa zranitelnosti – skenování obrázků kontejnerů
Chcete-li sledovat váš registr kontejnerů Azure založený na ARM, ujistěte se, že jste na standardní úrovni Security Center (viz [ceny).](/azure/security-center/security-center-pricing) Potom povolte volitelný balíček registrů kontejnerů. Po zasunutí nového obrázku centrum zabezpečení prohledá obrázek pomocí skeneru od předního dodavatele prohledávání chyb zabezpečení společnosti Qualys.

Když se zjistí problémy – pomocí Qualys nebo Security Center – budete upozorněni na řídicím panelu Centra zabezpečení. Pro každou chybu zabezpečení, Security Center poskytuje žalovatelné doporučení, spolu s klasifikací závažnosti a pokyny, jak tento problém napravit. Podrobnosti o doporučeních centra zabezpečení pro kontejnery naleznete v [referenčním seznamu doporučení](recommendations-reference.md#recs-containers).

Security Center filtruje a klasifikuje nálezy ze skeneru. Když je obrázek v pořádku, Centrum zabezpečení jej jako takový označí. Security Center generuje doporučení zabezpečení pouze pro bitové kopie, které mají problémy, které mají být vyřešeny. Oznámením pouze v případě problémů centrum zabezpečení snižuje možnost nežádoucích informačních výstrah.

## <a name="environment-hardening"></a>Kalení prostředí

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Průběžné sledování konfigurace Dockeru
Azure Security Center identifikuje nespravované kontejnery hostované na virtuálních počítačích IaaS Linux nebo jiných počítačích s Linuxem s kontejnery Dockeru. Security Center průběžně vyhodnocuje konfigurace těchto kontejnerů. Potom je porovná s [Centrem pro internetovou bezpečnost (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center obsahuje celou sadu pravidel cis Docker benchmark a upozorní vás, pokud vaše kontejnery nesplňují žádný z ovládacích prvků. Při zjištění chybné konfigurace, Security Center generuje doporučení zabezpečení. Na **stránce doporučení** můžete zobrazit doporučení a napravit problémy. Zobrazí se také doporučení na kartě **Kontejnery,** která zobrazuje všechny virtuální počítače nasazené s Dockerem. 

Podrobnosti o příslušných doporučeních Centra zabezpečení, která se mohou zobrazit pro tuto funkci, naleznete v [části kontejneru](recommendations-reference.md#recs-containers) v referenční tabulce doporučení.

Při zkoumání problémů se zabezpečením virtuálního počítače, Security Center poskytuje další informace o kontejnerech v počítači. Tyto informace zahrnují verzi Dockeru a počet bitových kopií spuštěných na hostiteli. 

>[!NOTE]
> Tyto kontroly srovnávacího testu CIS se nespustí na instancích spravovaných AKS nebo virtuálních počítačích spravovaných databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Průběžné sledování vašich klastrů Kubernetes
Security Center spolupracuje se službou Azure Kubernetes Service (AKS), spravovanou službou orchestrace kontejnerů společnosti Microsoft pro vývoj, nasazování a správu kontejnerizovaných aplikací.

AKS poskytuje bezpečnostní ovládací prvky a přehled o stavu zabezpečení clusterů. Security Center používá tyto funkce k:
* Neustále sledovat konfiguraci clusterů AKS
* Generovat doporučení zabezpečení v souladu s oborovými standardy

Podrobnosti o příslušných doporučeních Centra zabezpečení, která se mohou zobrazit pro tuto funkci, naleznete v [části kontejneru](recommendations-reference.md#recs-containers) v referenční tabulce doporučení.

## <a name="run-time-protection---real-time-threat-detection"></a>Ochrana za běhu – detekce hrozeb v reálném čase

Security Center poskytuje detekci hrozeb v reálném čase pro kontejnerizovaná prostředí a generuje výstrahy pro podezřelé aktivity. Pomocí těchto informací můžete rychle opravit problémy se zabezpečením a vylepšit zabezpečení kontejnerů.

Detekujeme hrozby na úrovni hostitele a Clusteru AKS. Podrobné informace najdete v tématu [detekce hrozeb pro kontejnery Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>Nejčastější dotazy k zabezpečení kontejnerů

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Jaké typy ibi lze Azure Security Center prohledávat?
Security Center skenuje bitové kopie založené na operačním systému Linux, které poskytují přístup ke prostředí. 

Skener Qualys nepodporuje super minimalistické obrázky, jako jsou [obrázky dockeru](https://hub.docker.com/_/scratch/) nebo obrázky "Distroless", které obsahují pouze vaši aplikaci a její závislosti za běhu bez správce balíčků, prostředí nebo operačního režimu.

### <a name="how-does-azure-security-center-scan-an-image"></a>Jak Azure Security Center prohledá v image?
Bitová kopie je vytažena z registru. To je pak spustit v izolované pískoviště se skenerem Qualys, který extrahuje seznam známých chyb zabezpečení.

Security Center filtruje a klasifikuje nálezy ze skeneru. Když je obrázek v pořádku, Centrum zabezpečení jej jako takový označí. Security Center generuje doporučení zabezpečení pouze pro bitové kopie, které mají problémy, které mají být vyřešeny. Oznámením pouze v případě problémů centrum zabezpečení snižuje možnost nežádoucích informačních výstrah.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Jak často Azure Security Center prohledává moje obrázky?
Snímky jsou spuštěny při každém stisknutí.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Mohu získat výsledky testů prostřednictvím rozhraní REST API?
Ano. Výsledky jsou v rámci [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/). Také můžete použít Azure Resource Graph (ARG), Rozhraní API podobné Kusto pro všechny vaše prostředky: dotaz můžete načíst konkrétní prohledávku.
 

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení kontejnerů v Azure Security Center najdete v těchto souvisejících článcích:

* Chcete-li zobrazit stav zabezpečení prostředků souvisejících s kontejnery, podívejte se do části kontejnery [chránit vaše počítače a aplikace](security-center-virtual-machine-protection.md#containers).

* Podrobnosti o integraci se [službou Azure Kubernetes Service](azure-kubernetes-service-integration.md)

* Podrobnosti o [integraci s Azure Container Registry](azure-container-registry-integration.md)