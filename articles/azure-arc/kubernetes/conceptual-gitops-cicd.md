---
title: Pracovní postup CI/CD pomocí GitOps – Kubernetes s podporou ARC Azure
services: azure-arc
ms.service: azure-arc
ms.date: 02/26/2021
ms.topic: conceptual
author: tcare
ms.author: tcare
description: Tento článek obsahuje koncepční přehled pracovního postupu CI/CD pomocí GitOps
keywords: GitOps, Kubernetes, K8s, Azure, Helm, ARC, AKS, Azure Kubernetes Service, Containers, CI, CD, Azure DevOps
ms.openlocfilehash: 044275db0977a20474aa1451324486ad1750a7f9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693064"
---
# <a name="overview"></a>Přehled

Moderní nasazení Kubernetes se zapokojují v několika aplikacích, clusterech a prostředích. Pomocí GitOps můžete snadněji spravovat tato složitá nastavení a sledovat požadovaný stav prostředí Kubernetes deklarativně pomocí Gitu. Pomocí běžných nástrojů Git ke sledování stavu clusteru můžete zvýšit zodpovědnost, usnadnit vyšetřování chyb a povolit automatizaci správu prostředí.

Tento článek obsahuje koncepční přehled o tom, jak zajistit, aby se GitOpsa realita v celém životním cyklu aplikace pomocí Azure ARC, Azure Repos a Azure Pipelines. Projděte si ucelený příklad jedné změny v aplikaci od vývojáře až po GitOps prostředí Kubernetes.

## <a name="architecture"></a>Architektura

Vezměte v úvahu aplikaci nasazenou do jednoho nebo více prostředí Kubernetes.

![Architektura CI/CD GitOps](./media/gitops-arch.png)
### <a name="application-repo"></a>Úložiště aplikace
Úložiště aplikace obsahuje kód aplikace, na kterém vývojáři pracují během své vnitřní smyčky. Šablony nasazení aplikace v tomto úložišti v obecném tvaru, jako je například Helm nebo Kustomize, jsou v tomto úložišti aktivní. Hodnoty specifické pro prostředí nejsou uloženy. Změny tohoto úložiště vyvolávají kanál PR nebo CI, který spouští proces nasazení.
### <a name="container-registry"></a>Container Registry
Registr kontejnerů obsahuje všechny první a jiné image, které se používají v prostředích Kubernetes. Označte image aplikací First stran pomocí značek pro čtení a potvrzení Git, které se používá k sestavení image. Ukládání imagí třetích stran do mezipaměti pro zabezpečení, rychlost a odolnost. Nastavte plán pro včasné testování a integraci aktualizací zabezpečení. Další informace najdete v tématu [ACR pro využívání a udržování veřejného obsahu](https://docs.microsoft.com/azure/container-registry/tasks-consume-public-content) pro příklad.
### <a name="pr-pipeline"></a>Kanál PR
PR na úložiště aplikace se po úspěšném spuštění kanálu PR gated. Tento kanál spouští základní brány kvality, například linting a testování částí kódu aplikace. Kanál testuje aplikace a šablony Lints fázemi a Helm používané pro nasazení do prostředí Kubernetes. Image Docker by se měly sestavit a testovat, ale nevloženy. Doba trvání kanálu je poměrně krátká, aby umožňovala rychlou iteraci.
### <a name="ci-pipeline"></a>Kanál CI
Kanál CI aplikace spouští všechny kroky kanálu žádosti o přijetí změn a rozšiřuje testy a kontroly nasazení. Kanál se dá spustit pro každé potvrzení nebo v pravidelných tempo se skupinou potvrzení. V této fázi proveďte testování aplikace, které je příliš dlouhé pro kanál žádosti o přijetí změn. Vložení imagí Docker do Container Registry po sestavení v přípravě na nasazení. Nahrazenou šablonu lze linted pomocí sady testovacích hodnot. Image používané v modulu runtime služby by měly být v tomto okamžiku linted, sestaveny a testovány. Konkrétně v sestavení CI se artefakty publikují pro krok CD, aby se využívaly při přípravě na nasazení.
### <a name="flux"></a>Flux
Tok je služba, která běží v jednotlivých clusterech a zodpovídá za údržbu požadovaného stavu. Služba často dotazuje úložiště GitOps na změny v clusteru a použije je.
### <a name="cd-pipeline"></a>Kanál CD
Kanál CD se automaticky aktivuje úspěšně sestaveními CI. Používá dříve publikované šablony, nahrazuje hodnoty prostředí a otevře úložiště žádosti o přijetí změn do úložiště GitOps, aby pomohlo požádat o změnu v požadovaném stavu jednoho nebo více clusterů Kubernetes. Správci clusteru kontrolují změnu stavu PR a schvalují sloučení úložiště GitOps. Kanál pak čeká na dokončení žádosti o přijetí změn, což umožňuje, aby tok vybral změnu stavu.
### <a name="gitops-repo"></a>Úložiště GitOps
Úložiště GitOps představuje aktuální požadovaný stav všech prostředí v clusterech. Všechny změny tohoto úložiště vybírají služba toků v každém clusteru a nasazené. PR jsou vytvořeny se změnami požadovaného stavu, zkontrolováno a sloučeno. Tyto PR obsahují změny obou šablon nasazení a výsledných vygenerovaných manifestů Kubernetes. Vygenerované manifesty nízké úrovně zabraňují jakýmkoli překvapenímám za náhradou šablony tím, že umožňují pečlivou kontrolu změn obvykle nezpracovaných na úrovni šablon.
### <a name="kubernetes-clusters"></a>Clustery Kubernetes
Jeden nebo více clusterů Kubernetes s podporou ARC Azure slouží k různým prostředím, které aplikace potřebuje. Jeden cluster může například sloužit jako vývoj a prostředí pro kontrolu a odpovědi prostřednictvím různých oborů názvů. Druhý cluster může poskytovat snadnější oddělení prostředí a přesnější kontrolu.
## <a name="example-workflow"></a>Ukázkový pracovní postup
Jako vývojář aplikace, Alice:
* Zapisuje kód aplikace.
* Určuje, jak spustit aplikaci v kontejneru Docker.
* Definuje šablony, které spouštějí kontejner a závislé služby v clusteru Kubernetes.

I když Alice ví, že aplikace potřebuje možnost běžet v několika prostředích, neví o konkrétní nastavení pro každé prostředí.

Předpokládejme, že Alice chce provést změnu aplikace, která změní image Docker použitou v šabloně nasazení aplikace.

1. Alice změní šablonu nasazení, nabídne ji do vzdálené větve úložiště aplikace a otevře pro revizi TRVALOu žádost o přijetí změn.
2. Alice se zeptá svého týmu, aby zkontroloval změnu.
    * Kanál PR spustí ověření.
    * Po úspěšném spuštění kanálu se tým odhlásí a změna se sloučí.
3. Kanál CI ověřuje a úspěšně dokončí změnu od Alice.
    * Tato změna je bezpečná pro nasazení do clusteru a artefakty se uloží do spuštění kanálu CI.
4. Změna Alice slučuje a aktivuje kanál CD.
    * Kanál CD vybírá artefakty uložené v průběhu spuštění kanálu náležícího do modulu CI.
    * Kanál CD dosadí šablony s hodnotami konkrétního prostředí a ve fázích změny v existujícím stavu clusteru v úložišti GitOps.
    * Kanál CD vytvoří žádost o přijetí změn do úložiště GitOps s požadovanými změnami stavu clusteru.
5. Tým od Alice kontroluje a schvaluje jeho žádost o přijetí změn.
    * Změna je sloučena do cílové větve odpovídající prostředí.
6. Během několika minut zaznamená změna v úložišti GitOps a vyžádá si změnu od Alice.
    * Z důvodu změny image Docker je u aplikace pod vyžadována aktualizace.
    * Tok aplikuje změnu na cluster.
7. Alice testuje koncový bod aplikace, aby ověřil, že nasazení bylo úspěšně dokončeno.
   > [!NOTE]
   > Pro další prostředí, která cílí na nasazení, prochází kanál CD vytvořením žádosti o přijetí změn pro další prostředí a opakováním kroků 4-7. Tento proces vyžaduje dodatečné schválení pro nasazení nebo prostředí riskier, jako je například změna zabezpečení nebo produkční prostředí.
8.  Jakmile všechna prostředí obdrží úspěšná nasazení, kanál se dokončí.

## <a name="next-steps"></a>Další kroky
[Konfigurace a GitOps s povoleným Kubernetes ARC Azure](./conceptual-configurations.md)
