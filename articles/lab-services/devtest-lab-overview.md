---
title: O azure devtest labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak devTest Labs můžou usnadnit vytváření, správu a monitorování virtuálních počítačů Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561451"
---
# <a name="about-azure-devtest-labs"></a>Služba Azure Devtest Labs
Azure DevTest Labs umožňuje vývojářům v týmech efektivně spravovat virtuální počítače (VM) a paaS prostředky bez čekání na schválení.

DevTest Labs vytvoří testovací prostředí skládající se z předem nakonfigurovaných základen nebo šablon Azure Resource Manager. Ty mají všechny potřebné nástroje a software, které můžete použít k vytvoření prostředí. Prostředí můžete vytvořit během několika minut, na rozdíl od hodin nebo dnů.

Pomocí devTest Labs můžete otestovat nejnovější verze aplikací následujícím způsobem:

- Rychle zřašte prostředí Windows a Linux pomocí opakovaně použitelných šablon a artefaktů.
- Prostřednictvím snadné integrace svého kanálu nasazení s DevTest Labs můžete zřizovat prostředí na vyžádání.
- Škálujte zátěžové testování zřízením více testovacích agentů a vytvořením předem zřízených prostředí pro školení a ukázky.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Možnosti
DevTest Labs poskytuje vývojářům pracujícím s virtuálními počítačemi následující funkce:

- Vytvořte virtuální ms rychle podle následujících méně než pět jednoduchých kroků.
- Vyberte si z kurátorského seznamu základen virtuálních her, které jsou nakonfigurované, schválené a autorizované vedoucím týmu nebo centrálním IT.
- Vytvořte virtuální počítače z předem vytvořených vlastních irek, které mají veškerý software a nástroje, které už jsou nainstalované. 
- Vytvořte virtuální počítače ze vzorců, které jsou v podstatě vlastní image v kombinaci s nejnovější sestavení softwaru, který je nainstalován při vytvoření virtuálních počítače. 
- Nainstalujte artefakty, které jsou rozšíření nasazené na virtuálních počítačích po jejich zřízení.
- Nastavte plány automatického vypínání a automatického spuštění na virtuálních počítačích.
- Nárokujte předem vytvořený virtuální virtuální ms, aniž byste museli projít procesem vytváření.

DevTest Labs poskytuje vývojářům pracujícím s prostředími PaaS následující funkce:

- Pomocí Správce prostředků můžete rychle vytvářet prostředí PaaS pomocí méně než tří jednoduchých kroků.
- Vyberte si z kurátorského seznamu šablon Správce prostředků, které jsou nakonfigurované a autorizované vedoucím týmu nebo centrálním IT oddělením.
- Stočte prázdnou skupinu prostředků (izolovaného prostoru) pomocí šablony Správce prostředků k prozkoumání Azure v kontextu testovacího prostředí.

DevTest Labs také umožňuje centrálnímu IT kontrolovat odpady, optimalizovat náklady na zdroje a zůstat v rámci rozpočtů tím, že provádí následující úkoly:  

- Nastavení plánů automatického vypnutí a automatického spuštění na virtuálních počítačích.
- Nastavení zásad na počet virtuálních počítačů, které mohou uživatelé vytvořit.
- Nastavení zásad pro velikosti virtuálních počítačů a image galerie, ze kterých si uživatelé vybírají.
- Sledování nákladů a stanovení cílů v laboratořích.
- Získání oznámení o vysoké předpokládané náklady pro laboratoře, takže můžete provést nezbytná opatření.

DevTest Labs poskytuje následující výhody při vytváření, konfiguraci a správě prostředí v cloudu.

## <a name="cost-control-and-governance"></a>Kontrola nákladů a správa
DevTest Labs usnadňuje řízení nákladů tím, že vám umožní dělat následující úkoly:

- [Nastavte zásady pro vaše testovací prostředí](devtest-lab-get-started-with-lab-policies.md), jako je počet virtuálních počítačů na uživatele nebo na testovací prostředí. 
- Vytvořte [zásady pro automatické vypnutí](devtest-lab-set-lab-policy.md) a spuštění virtuálních ms.
- Sledujte náklady na virtuální počítače a paas prostředky se točil do laboratoře, aby zůstali v rámci [svého rozpočtu](devtest-lab-configure-cost-management.md).
- Zůstaňte v kontextu svých laboratoří, abyste nezaváli zdroje mimo ně.

## <a name="quickly-get-to-ready-to-test"></a>Rychlé přepychu je připraveno k testování
DevTest Labs vám umožní vytvářet předem zřízené prostředí vybavené vším, co váš tým potřebuje k vývoji a testování aplikací. Stačí [nárokovat prostředí,](devtest-lab-add-claimable-vm.md) kde je nainstalováno poslední dobré sestavení vaší aplikace a začít pracovat. Nebo použijte kontejnery pro ještě rychlejší a štíhlejší vytváření prostředí.

## <a name="create-once-use-everywhere"></a>Vytvořit jednou, používat všude
Zachyťte a sdílejte [šablony prostředí](devtest-lab-create-environment-from-arm.md) PaaS a [artefakty](add-artifact-repository.md) v rámci vašeho týmu nebo organizace – vše ve složce zdrojového kódu – a snadno vytvářejte vývojářská a testovací prostředí.

## <a name="worry-free-self-service"></a>Bezstarostná samoobsluha
DevTest Labs umožňuje vývojářům a testerům rychle a snadno [vytvářet virtuální počítače IaaS](devtest-lab-add-vm.md) a [prostředky PaaS](devtest-lab-create-environment-from-arm.md) pomocí sady předem nakonfigurovaných prostředků.

## <a name="use-iaas-and-paas-resources"></a>Použití prostředků IaaS a PaaS 
Vývojáři můžou taky spouštět prostředky PaaS, jako jsou clustery Azure Service Fabric, funkce Webové aplikace služby Azure App Service a farmy SharePoint, pomocí šablon Správce prostředků. Chcete-li začít s PaaS v testovacích prostředích, použijte šablony z [úložiště veřejného prostředí](devtest-lab-configure-use-public-environments.md) nebo [připojte testovací prostředí k vlastnímu úložišti Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Můžete také sledovat náklady na tyto zdroje, abyste zůstali v rámci svého rozpočtu.

## <a name="integrate-with-your-existing-toolchain"></a>Integrace se stávajícím nářadím
Pomocí předem provedených modulů plug-in nebo rozhraní API zřizujte vývojová/testovací prostředí přímo z preferovaného [nástroje pro průběžnou integraci (CI),](devtest-lab-integrate-ci-cd-vsts.md)integrovaného vývojového prostředí (IDE) nebo kanálu s automatickými verzemi. Můžete také použít komplexní nástroj příkazového řádku.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- Další informace o devTest Labs najdete v tématu [DevTest Labs concepts](devtest-lab-concepts.md).
- Návod s podrobnými pokyny najdete [v tématu Kurz: Nastavení testovacího prostředí pomocí Azure DevTest Labs](tutorial-create-custom-lab.md).