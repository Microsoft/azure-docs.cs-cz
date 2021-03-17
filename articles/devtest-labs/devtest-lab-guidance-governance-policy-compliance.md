---
title: Zásady společnosti a dodržování předpisů v Azure DevTest Labs
description: Tento článek poskytuje pokyny k řízení zásad společnosti a dodržování předpisů pro Azure DevTest Labs infrastrukturu.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592343"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Zásady správného řízení infrastruktury Azure DevTest Labs – zásady společnosti a dodržování předpisů
Tento článek poskytuje pokyny k řízení zásad společnosti a dodržování předpisů pro Azure DevTest Labs infrastrukturu. 

## <a name="public-vs-private-artifact-repository"></a>Veřejné a soukromé úložiště artefaktů

### <a name="question"></a>Otázka
Kdy by organizace měla v DevTest Labs použít veřejné úložiště artefaktů a soukromé úložiště artefaktů?

### <a name="answer"></a>Odpověď
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) poskytuje počáteční sadu softwarových balíčků, které se nejčastěji používají. Pomáhá s rychlým nasazením, aniž by bylo nutné investovat čas k reprodukování běžných vývojářských nástrojů a doplňků. Můžete si vybrat, že nasadíte vlastní privátní úložiště. Veřejné a privátní úložiště můžete používat paralelně. Můžete se také rozhodnout zakázat veřejné úložiště. Kritéria pro nasazení privátního úložiště by měla být založená na následujících otázkách a ohledech:

- Má organizace požadavky na podnikový licencovaný software jako součást nabídky DevTest Labs? Pokud je odpověď ano, je třeba vytvořit privátní úložiště.
- Vyvíjí organizace vlastní software, který poskytuje konkrétní operace, která je vyžadována jako součást celkového procesu zřizování? Pokud je odpověď ano, měli byste nasadit privátní úložiště.
- Pokud zásada zásad správného řízení organizace vyžaduje izolaci a externí úložiště nejsou pod správou přímých konfigurací v organizaci, měla by být nasazené soukromé úložiště artefaktů. V rámci tohoto procesu je možné zkopírovat a integrovat počáteční kopii veřejného úložiště s privátním úložištěm. Veřejné úložiště pak můžete zakázat, aby k němu nikdo jiný nemohly získat přístup. Tento přístup vynutí, aby všichni uživatelé v organizaci měli jenom jedno úložiště schválené organizací a minimalizovali snížení konfigurace.

### <a name="single-repository-or-multiple-repositories"></a>Jedno úložiště nebo více úložišť 

### <a name="question"></a>Otázka
Měl by se použít plán organizace pro jedno úložiště nebo povolení více úložišť?

### <a name="answer"></a>Odpověď
V rámci celkové strategie správy zásad správného řízení a konfigurace vaší organizace doporučujeme, abyste používali centralizované úložiště. Když použijete více úložišť, můžou se v průběhu času stát siloy nespravovaného softwaru. S centrálním úložištěm může více týmů využívat artefakty z tohoto úložiště pro své projekty. Vynutil standardizaci, zabezpečení, snadné řízení a eliminuje duplicity úsilí. V rámci centralizované správy jsou následující akce Doporučené postupy pro dlouhodobou správu a udržitelnost:

- Přidružte Azure Repos ke stejnému Azure Active Directory tenanta, kterého předplatné Azure používá k ověřování a autorizaci.
- Vytvořte skupinu s názvem **všichni vývojáři DevTest Labs** v Azure Active Directory centrálně spravovaná. Do této skupiny by měl být umístěn jakýkoli vývojář, který přispívá ke vývojům artefaktů.
- Stejnou skupinu Azure Active Directory lze použít k poskytnutí přístupu k úložišti Azure Repos a testovacímu prostředí.
- V Azure Repos by se měly použít větvení nebo rozvětvení do samostatného cloudového úložiště z primárního provozního úložiště. Obsah se přidá do hlavní větve s žádostí o přijetí změn po správné revizi kódu. Jakmile recenzent kódu schválí změnu, vývojář zájemce, který zodpovídá za údržbu hlavní větve, sloučí aktualizovaný kód. 

## <a name="corporate-security-policies"></a>Podnikové zásady zabezpečení

### <a name="question"></a>Otázka
Jak může organizace zajistit, aby byly zásady zabezpečení společnosti zavedeny?

### <a name="answer"></a>Odpověď
Organizace ji může dosáhnout provedením následujících akcí:

1. Vývoj a publikování komplexních zásad zabezpečení. Zásady překloubují pravidla přijatelného použití související s používáním softwaru, cloudových prostředků. Definuje také, co jasně narušuje zásady. 
2. Vývoj vlastní image, vlastních artefaktů a procesu nasazení, který umožňuje orchestraci v rámci sféry zabezpečení, která je definovaná ve službě Active Directory. Tento přístup vynutil hranici společnosti a nastavuje společnou sadu ovládacích prvků pro životní prostředí. Tyto ovládací prvky pro prostředí může vývojář při vývoji brát v úvahu a v rámci celého procesu dodržujte životní cyklus bezpečného vývoje. Cílem je také poskytnout prostředí, které není v nedostatečném omezení, které může bránit vývoji, ale přiměřenou sadu ovládacích prvků. Zásady skupiny v organizační jednotce (OU), která obsahuje virtuální počítače testovacího prostředí, můžou být podmnožinou celkových zásad skupiny, které se nacházejí v produkčním prostředí. Alternativně může být další sada, která bude správně zmírnit všechna zjištěná rizika.

## <a name="data-integrity"></a>Integrita dat

### <a name="question"></a>Otázka
Jak může organizace zajistit integritu dat, aby vývojáři vzdálené komunikace nemohli odebrat kód nebo zavést malware nebo neschválený software?

### <a name="answer"></a>Odpověď
Existuje několik vrstev řízení pro zmírnění hrozby od externích konzultantů, dodavatelů nebo zaměstnanců, kteří pracují v DevTest Labs při spolupráci. 

Jak bylo uvedeno dříve, musí mít první krok v konceptu přijatelné zásady použití, které budou jasně vypsány a definovány tak, aby jasné, které důsledky porušují nějaké zásady. 

První vrstvou ovládacích prvků pro vzdálený přístup je použití zásady vzdáleného přístupu prostřednictvím připojení VPN, které není přímo připojené k testovacímu prostředí. 

Druhá vrstva ovládacích prvků je použít sadu objektů zásad skupiny, které zabraňují kopírování a vkládání prostřednictvím vzdálené plochy. Je možné implementovat zásady sítě, aby nepovolovaly odchozí služby z prostředí, jako jsou služby FTP a RDP, mimo prostředí. Směrování definované uživatelem může vynutit všechny síťové přenosy Azure zpátky do místního prostředí, ale směrování nemohlo být pro všechny adresy URL, které by mohly umožňovat nahrávání dat, pokud se neřídí proxy serverem pro skenování obsahu a relací. Veřejné IP adresy se můžou omezit v rámci virtuální sítě, které podporují DevTest Labs, aby nepovolovaly přemostění prostředků externí sítě.

V konečném případě je třeba použít stejný typ omezení v rámci organizace, což by muselo také zohlednit všechny možné metody vyměnitelných médií nebo externích adres URL, které by mohly přijmout obsah. Pokud chcete zkontrolovat a implementovat zásady zabezpečení, obraťte se na svého odborníka na zabezpečení. Další doporučení najdete v tématu [Microsoft Internet Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Další kroky
Viz [migrace a integrace aplikací](devtest-lab-guidance-governance-application-migration-integration.md).
