---
title: Index ukázek podrobných plánů
description: Index ukázek dodržování předpisů a standardních ukázek pro nasazování prostředí, zásad a základů architektury přechodu na cloud s využitím Azure Blueprints
ms.date: 09/11/2020
ms.topic: sample
ms.openlocfilehash: 7ac95dee88cb244ecb4b60738daae26bb0d8831a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031870"
---
# <a name="azure-blueprints-samples"></a>Ukázky Azure Blueprints

Následující tabulka obsahuje odkazy na ukázky pro Azure Blueprints. Všechny vzorky jsou v produkční kvalitě a připravené k nasazení ještě dnes. Pomohou vám splnit různé požadavky na dodržování předpisů.

## <a name="standards-based-blueprint-samples"></a>Ukázky podrobných plánů založené na standardech

| Ukázka | Popis |
|---------|---------|
| [Australian Government ISM PROTECTED](./ism-protected/index.md) | Poskytuje ochranné mantinely pro dodržování předpisů Australian Government ISM PROTECTED. |
| [Srovnávací test zabezpečení Azure](./azure-security-benchmark.md) | Poskytuje ochranné mantinely pro dodržování předpisů [Azure Security Benchmarku](../../../security/benchmarks/overview.md). |
| [Canada Federal PBMM](./canada-federal-pbmm/index.md) | Poskytuje ochranné mantinely pro dodržování předpisů Canada Federal Protected B, Medium Integrity, Medium Availability (PBMM). |
| [Srovnávací test CIS Microsoft Azure Foundations](./cis-azure-1-1-0.md)| Poskytuje sadu zásad, které pomáhají dodržovat doporučení srovnávacího testu CIS Microsoft Azure Foundations. |
| [DoD Impact Level 4](./dod-impact-level-4/index.md) | Poskytuje sadu zásad, které pomáhají dodržovat standard DoD Impact Level 4. |
| [DoD Impact Level 5](./dod-impact-level-5/index.md) | Poskytuje sadu zásad, které pomáhají dodržovat standard DoD Impact Level 5. |
| [FedRAMP Moderate](./fedramp-m/index.md) | Poskytuje sadu zásad, které pomáhají dodržovat standard FedRAMP Moderate. |
| [FedRAMP High](./fedramp-h/index.md) | Poskytuje sadu zásad, které pomáhají dodržovat standard FedRAMP High. |
| [HIPAA HITRUST 9.2](./hipaa-hitrust-9-2.md) | Poskytuje sadu zásad, které pomáhají dodržovat standard HIPAA HITRUST. |
| [IRS 1075](./irs-1075/index.md) | Poskytuje ochranné mantinely pro dodržování předpisů IRS 1075.|
| [ISO 27001](./iso27001/index.md) | Poskytuje ochranné mantinely pro dodržování předpisů ISO 27001. |
| [ISO 27001: Sdílené služby](./iso27001-shared/index.md) | Poskytuje sadu kompatibilních vzorů infrastruktury a ochranné mantinely zásad, které pomáhají zajistit osvědčení ISO 27001. |
| [Úloha ISO 27001 App Service Environment/SQL Database](./iso27001-ase-sql-workload/index.md) | Poskytuje další infrastrukturu pro ukázku podrobného plánu [ISO 27001: Sdílené služby](./iso27001-shared/index.md). |
| [Média](./media/index.md) | Poskytuje sadu zásad, které pomáhají dodržovat MPAA pro média. |
| [NIST SP 800-53 R4](./nist-sp-800-53-r4.md) | Poskytuje ochranné mantinely k dodržování předpisů pro NIST SP 800-53 R4. |
| [NIST SP 800-171 R2](./nist-sp-800-171-r2.md) | Poskytuje ochranné mantinely k dodržování předpisů pro NIST SP 800-171 R2. |
| [PCI-DSS v3.2.1](./pci-dss-3.2.1/index.md) | Poskytuje sadu zásad pomáhajících zajistit dodržování předpisů PCI-DSS v3.2.1. |
| [SWIFT CSP-CSCF v2020](./swift-2020/index.md) | Pomáhá zajistit dodržování předpisů SWIFT CSP-CSCF v2020. |
| [Zásady správného řízení UK OFFICIAL a UK NHS](./ukofficial/index.md) | Poskytují sadu kompatibilních vzorů infrastruktury a ochranných mantinelů zásad, které pomáhají zajistit osvědčení UK OFFICIAL a UK NHS. |
| [Základy CAF](./caf-foundation/index.md) | Poskytuje sadu ovládacích prvků, které vám pomůžou se správou cloudových aktiv v souladu s [architekturou přechodu na cloud pro Azure od Microsoftu (CAF)](/azure/architecture/cloud-adoption/governance/journeys/index). |
| [Cílová zóna migrace CAF](./caf-migrate-landing-zone/index.md) | Poskytuje sadu ovládacích prvků, které vám pomůžou nastavit migraci vaší první úlohy a spravovat cloudová aktiva v souladu s [architekturou přechodu na cloud pro Azure od Microsoftu (CAF)](/azure/architecture/cloud-adoption/migrate/index). |

## <a name="samples-strategy"></a>Strategie ukázek

:::image type="content" source="../media/blueprint-samples-strategy.png" alt-text="Strategie ukázek podrobných plánů" border="false":::

Podrobné plány Základy CAF a Cílová zóna migrace CAF předpokládají, že zákazník připravuje jedno stávající čisté předplatné pro migraci místních prostředků a úloh do Azure
(oblast A a B na obrázku).  

Ukázkové podrobné plány je možné iterovat a hledat vzory kustomizací, které zákazník využívá. Další možností je aktivně se zaměřit na podrobné plány, které jsou určené pro konkrétní odvětví, jako jsou finanční služby a elektronické obchodování (Horní část oblasti B). Podobně si představujeme vytvoření plánů pro komplexní architektonické aspekty, jako je více předplatných, vysoká dostupnost, prostředků v různých oblastech nebo zákazníci, kteří implementují řízení pro stávající předplatná a prostředky (oblasti C a D).

Existují i ukázkové plány, které jsou zaměřené na zákaznický scénář s vysokými požadavky na dodržování předpisů a vysokou architektonickou složitostí (oblast E na obrázku). Oblast F na obrázku je oblast, kterou budou zákazníci a partneři řešit tak, že využijí ukázkové podrobné plány a přizpůsobí si je konkrétním potřebám.

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)
