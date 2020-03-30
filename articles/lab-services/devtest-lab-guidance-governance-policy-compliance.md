---
title: Zásady společnosti a dodržování předpisů v laboratořích Azure DevTest Labs
description: Tento článek obsahuje pokyny k řízení zásad společnosti a dodržování předpisů pro infrastrukturu Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560509"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Řízení infrastruktury Azure DevTest Labs – zásady společnosti a dodržování předpisů
Tento článek obsahuje pokyny k řízení zásad společnosti a dodržování předpisů pro infrastrukturu Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Veřejné vs. soukromé úložiště artefaktů

### <a name="question"></a>Otázka
Kdy by měla organizace používat veřejné úložiště artefaktů vs. soukromé úložiště artefaktů v devTest Labs?

### <a name="answer"></a>Odpověď
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) poskytuje počáteční sadu softwarových balíčků, které se nejčastěji používají. Pomáhá s rychlým nasazením, aniž by bylo nutné investovat čas na reprodukci běžných vývojářských nástrojů a doplňků. Můžete nasadit vlastní soukromé úložiště. Můžete použít veřejné a soukromé úložiště paralelně. Můžete také zakázat veřejné úložiště. Kritéria pro nasazení soukromého úložiště by měla být řízena následujícími otázkami a aspekty:

- Má organizace požadavek mít firemní licencovaný software jako součást své nabídky DevTest Labs? Pokud je odpověď ano, pak by měl být vytvořen soukromý úložiště.
- Vyvíjí organizace vlastní software, který poskytuje konkrétní operaci, která je vyžadována jako součást celkového procesu zřizování? Pokud je odpověď ano, pak by měl být nasazen soukromý repozitář.
- Pokud zásady zásad správného řízení organizace vyžaduje izolaci a externí úložiště nejsou pod přímou správou konfigurace organizace, soukromé úložiště artefaktů by měla být nasazena. V rámci tohoto procesu lze zkopírovat a integrovat první kopii veřejného úložiště se soukromým úložištěm. Veřejné úložiště pak může být zakázáno, aby k němu už nikdo v rámci organizace neměl přístup. Tento přístup vynutí, aby všichni uživatelé v rámci organizace měli pouze jedno úložiště schválené organizací a minimalizovalo posun konfigurace.

### <a name="single-repository-or-multiple-repositories"></a>Jedno úložiště nebo více úložišť 

### <a name="question"></a>Otázka
Má organizace plánovat jedno úložiště nebo povolit více úložišť?

### <a name="answer"></a>Odpověď
Jako součást celkové strategie správy zásad správného řízení a konfigurace vaší organizace doporučujeme použít centralizované úložiště. Při použití více úložišť se mohou v průběhu času stát sily nespravovaného softwaru. S centrální úložiště více týmů můžete využívat artefakty z tohoto úložiště pro své projekty. Vynucuje standardizaci, zabezpečení, snadnou správu a eliminuje zdvojování úsilí. V rámci centralizace jsou doporučovány následující postupy pro dlouhodobé řízení a udržitelnost:

- Přidružte azure úložiště ke stejnému tenantovi Azure Active Directory, který předplatné Azure používá pro ověřování a autorizaci.
- Vytvořte skupinu s názvem **Všichni vývojáři devTest Labs** ve službě Azure Active Directory, která je centrálně spravovaná. Každý vývojář, který přispívá k vývoji artefaktů by měl být umístěn v této skupině.
- Stejná skupina Azure Active Directory slouží k poskytování přístupu k úložišti Azure Repos a do testovacího prostředí.
- V Azure Repos větvení nebo rozvětvení by měl být použit k samostatné úložiště ve vývoji z úložiště primární produkční ho. Obsah se do hlavní větve přidává pouze s žádostí o přijetí vyžádat po řádné kontrole kódu. Jakmile recenzent kódu změnu schválí, vývojář hlavního zákazníka, který je zodpovědný za údržbu hlavní větve, sloučí aktualizovaný kód. 

## <a name="corporate-security-policies"></a>Podnikové zásady zabezpečení

### <a name="question"></a>Otázka
Jak může organizace zajistit, aby byly zavedeny podnikové zásady zabezpečení?

### <a name="answer"></a>Odpověď
Organizace toho může dosáhnout následujícím způsobem:

1. Vypracování a publikování komplexní bezpečnostní politiky. Zásady formulují pravidla přijatelného použití spojená s pomocí softwaru, cloudových prostředků. Definuje také to, co jasně porušuje zásady. 
2. Vytvořte vlastní bitovou kopii, vlastní artefakty a proces nasazení, který umožňuje orchestraci v rámci sféry zabezpečení, která je definována pomocí služby Active Directory. Tento přístup vynucuje hranice podniku a nastavuje společnou sadu ovládacích prvků prostředí. Tyto ovládací prvky proti prostředí, které může vývojář zvážit při vývoji a sledování zabezpečeného životního cyklu vývoje jako součást jejich celkového procesu. Cílem je také vytvořit prostředí, které není příliš omezující, což může bránit rozvoji, ale přiměřený soubor kontrol. Zásady skupiny v organizační jednotce (OU), která obsahuje virtuální počítače testovacího prostředí, mohou být podmnožinou celkových zásad skupiny, které se nacházejí v produkčním prostředí. Alternativně mohou být další sadou, která řádně zmírní všechna zjištěná rizika.

## <a name="data-integrity"></a>Integrita dat

### <a name="question"></a>Otázka
Jak může organizace zajistit integritu dat, aby zajistila, že vývojáři vzdálené komunikace nebudou moci odebrat kód nebo zavést malware nebo neschválený software?

### <a name="answer"></a>Odpověď
Existuje několik vrstev kontroly ke zmírnění hrozby od externích konzultantů, dodavatelů nebo zaměstnanců, kteří jsou vzdálené ke spolupráci v DevTest Labs. 

Jak již bylo uvedeno dříve, první krok musí mít přijatelné použití politiky vypracovány a definovány, že jasně nastiňuje důsledky, když někdo poruší politiku. 

První vrstva ovládacích prvků pro vzdálený přístup je použít zásady vzdáleného přístupu prostřednictvím připojení VPN, které není přímo připojeno k testovacímu prostředí. 

Druhá vrstva ovládacích prvků je použít sadu objektů zásad skupiny, které brání kopírování a vkládání přes vzdálenou plochu. Síťová politika by mohla být implementována tak, aby neumožňovala odchozí msty z prostředí, jako jsou služby FTP a RDP, mimo prostředí. Uživatelem definované směrování může vynutit veškerý síťový provoz Azure zpět do místního prostředí, ale směrování nemůže odpovídat všem adresám URL, které by mohly povolit nahrávání dat, pokud nejsou řízeny prostřednictvím proxy serveru pro skenování obsahu a relací. Veřejné IP adresy by mohly být omezeny v rámci virtuální sítě podporující DevTest Labs neumožňuje přemostění externího síťového prostředku.

Nakonec je třeba použít stejný typ omezení v celé organizaci, což by muselo také zohlednit všechny možné metody vyměnitelných médií nebo externích adres URL, které by mohly přijmout příspěvek obsahu. Poraďte se se svým bezpečnostním odborníkem a zkontrolujte a implementujte zásady zabezpečení. Další doporučení naleznete v tématu [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Další kroky
Viz [Migrace a integrace aplikací](devtest-lab-guidance-governance-application-migration-integration.md).
