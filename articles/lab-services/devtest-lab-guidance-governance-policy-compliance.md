---
title: Zásady správného řízení infrastruktury Azure DevTest Labs
description: Tento článek obsahuje pokyny pro zásady správného řízení infrastruktury Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 8165efaa10eb31410f00dfa9bfffb2c02d7396ae
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872321"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Zásady správného řízení infrastruktury Azure DevTest Labs – firemní zásady a dodržování předpisů
Tento článek poskytuje pokyny pro firemní zásady a dodržování předpisů pro infrastrukturu Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Veřejné a úložiště privátního artefaktů

### <a name="question"></a>Otázka
Kdy organizace použít veřejné úložiště artefaktů a úložiště privátního artefaktů ve službě DevTest Labs?

### <a name="answer"></a>Odpověď
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) obsahuje počáteční sadu softwarové balíčky, které se běžně používají. Pomáhá s rychlým nasazením bez nutnosti investovat čas pro reprodukci běžné vývojářské nástroje a moduly. Je možné nasadit vlastní soukromé úložiště. Můžete použít veřejné a soukromé úložiště paralelně. Můžete také zakázat veřejného úložiště. Kritéria, která je nasadit do privátního úložiště by měl vycházet následující dotazy a důležité informace:

- Má organizace potřeba, aby podnikové licencovaného softwaru jako součást své nabídky DevTest Labs? Pokud odpovíte Ano, pak by měly být vytvořeny v privátním úložišti.
- Organizace vývoj vlastní software, který obsahuje konkrétní operací, který je vyžadován jako součást celkového během procesu zřizování? Pokud odpovíte Ano, by měly být nasazeny v privátním úložišti.
- Pokud zásady zásad správného řízení organizace vyžaduje izolace a externí úložiště se nevztahuje žádná smlouva s přímým přístupem configuration management organizací, musí být nasazené úložiště privátního artefaktů. Jako součást tohoto procesu je zkopírovat počáteční kopie veřejného úložiště a integrovat se na soukromé úložiště. Potom veřejného úložiště je možné zakázat tak, aby nikdo v rámci organizace můžete už přístup. Tento přístup všichni uživatelé v organizaci měli pouze jediné úložiště, které má schválený organizací vynutí a minimalizovat odchylky od konfigurace.

### <a name="single-repository-or-multiple-repositories"></a>Úložiště jedné nebo více úložišť 

### <a name="question"></a>Otázka
Organizace by měly plánování jednoho úložiště nebo povolit více úložišť?

### <a name="answer"></a>Odpověď
Jako součást celkového zásad správného řízení vaší organizace a strategie správy konfigurace doporučujeme použít centralizované úložiště. Při použití více úložišť mohou být sila nespravované software čase. S centrálním úložišti můžete využívat více týmů artefakty z tohoto úložiště pro své projekty. Vynucuje normalizace, zabezpečení, snadnou správu a odstraňuje duplicity úsilí. Jako součást centralizovaného tyto akce jsou doporučené postupy pro dlouhodobé správy a udržitelnosti:

- Úložiště Azure přidružení stejného tenanta služby Azure Active Directory, která je pro ověřování a autorizace pomocí předplatného Azure.
- Vytvořte skupinu s názvem **všichni vývojáři DevTest Labs** ve službě Azure Active Directory, který je centrálně spravovat. Kterýkoli vývojář, který přispívá k rozvoji artefaktů by měl umístit do této skupiny.
- Stejnou skupinu Azure Active Directory je možné poskytnout přístup k úložišti Azure úložišť a testovací prostředí.
- V úložišti Azure, větvení nebo větvení by měla sloužit k úložišti samostatné v vývoj z primární produkční úložiště. Obsah je pouze přidán do hlavní větve s žádostí o přijetí změn po přezkoumání kódu správné. Jakmile kód revidující změnu schválí, hlavní vývojář, který zodpovídá za údržbu v hlavní větvi, sloučí aktualizovaný kód. 

## <a name="corporate-security-policies"></a>Firemním zásadám zabezpečení

### <a name="question"></a>Otázka
Jak můžou organizace Ujistěte se, že firemním zásadám zabezpečení jsou na místě?

### <a name="answer"></a>Odpověď
Organizace mohou dosáhnout ji provedením následujících akcí:

1. Vývoj a publikování zásad zabezpečení. Zásady zabývá se výhodami pravidla podmínky použití spojené s na pomocí softwaru, cloudovým prostředím. Také definuje, co jasně porušuje zásady. 
2. Vyvíjejte vlastní image, vlastních artefaktů a proces nasazení, která umožňuje orchestraci v rámci sféry zabezpečení, která je definována s active directory. Tento přístup vynucuje hranic firmy a nastaví společnou sadu ovládacích prvků prostředí. Tyto ovládací prvky pro prostředí Vývojář můžete zvážit při vyvíjejí a postupujte podle zabezpečené vývojového cyklu jako součást svých celkový proces. Cílem je také poskytuje prostředí, který není příliš omezující této může bránit vývoje, ale přiměřené sadu ovládacích prvků. Zásady skupiny na organizační jednotku (OU), který obsahuje testovací prostředí virtuálních počítačů může být podmnožinou zásady celkový skupiny, které se nacházejí v produkčním prostředí. Alternativně je možné další sadu chcete všechny zjištěné riziko omezit správně.

## <a name="data-integrity"></a>Integrita dat

### <a name="question"></a>Otázka
Jak můžou organizace zajištění integrity dat k zajištění, že vývojáři vzdálené komunikace nelze odebrat kód nebo zavést malware nebo neschválených software?

### <a name="answer"></a>Odpověď
Existuje několik úrovní kontroly ke zmírnění hrozeb od externí konzultanty, smluvní pracovníci ani interní zaměstnance, kteří jsou vzdálené komunikace v ke spolupráci ve službě DevTest Labs. 

Jak bylo uvedeno dříve, první krok musí mít zásady podmínky použití zpracovávány a definice, která bude jasně uvádět důsledky, když někdo porušuje zásady. 

První vrstva ovládacích prvků pro vzdálený přístup, je použít zásady vzdáleného přístupu prostřednictvím připojení VPN, který není připojený přímo k testovacím prostředí. 

Druhá vrstva ovládacích prvků, je použít sadu objektů zásad skupiny, které brání kopírování a vkládání přes vzdálenou plochu. Nepovolí odchozí z prostředí, jako je například FTP a služby protokolu RDP z prostředí může implementovat zásady sítě. Uživatelsky definované směrování může vynutit veškerý síťový provoz Azure zpět do místního, ale směrování nejde účet pro všechny adresy URL, které může povolit nahrávání dat, není-li řídit prostřednictvím proxy serveru k prohledání obsahu a relací. Veřejné IP adresy může být v rámci virtuální sítě podporuje DevTest Labs k přemostění externího síťového prostředku, aby s omezeným přístupem.

Nakonec stejný typ omezení je potřeba použít napříč organizací, které byste museli také účet pro všechny možné metody vyměnitelného média nebo externí adresy URL, které by mohl přijmout příspěvek obsahu. Poraďte se s profesionální ke kontrole a implementovat zásadu zabezpečení zabezpečení. Další doporučení, najdete v tématu [Microsoft Security Kybernetických](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Další postup
Zobrazit [migraci aplikací a integrace](devtest-lab-guidance-governance-application-migration-integration.md).
