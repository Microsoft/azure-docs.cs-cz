---
title: O Azure DevTest Labs | Microsoft Docs
description: Přečtěte si, jak DevTest Labs usnadňuje vytváření, správu a monitorování virtuálních počítačů Azure.
ms.topic: article
ms.date: 06/20/2020
ms.openlocfilehash: b7e6493e5fb1c99dfa7962ed464dca3ebc6b0c7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480197"
---
# <a name="about-azure-devtest-labs"></a>Služba Azure Devtest Labs
Azure DevTest Labs umožňuje vývojářům v týmech efektivně spravovat virtuální počítače a prostředky PaaS bez čekání na schválení.

DevTest Labs vytváří cvičení skládající se z předem konfigurovaných základů nebo šablon Azure Resource Manager. Mají všechny potřebné nástroje a software, které můžete použít k vytváření prostředí. Během několika minut můžete vytvořit prostředí, a to na rozdíl od hodin nebo dnů.

Pomocí DevTest Labs můžete testovat nejnovější verze svých aplikací, a to provedením následujících úloh:

- Pomocí opakovaně použitelných šablon a artefaktů rychle zřídí prostředí Windows a Linux.
- Prostřednictvím snadné integrace svého kanálu nasazení s DevTest Labs můžete zřizovat prostředí na vyžádání.
- Škálujte zátěžové testování tím, že zřizujete více testovacích agentů a vytvoříte předem zřízené prostředí pro školení a ukázky.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Možnosti
DevTest Labs poskytuje vývojářům, kteří pracují s virtuálními počítači, následující možnosti:

- Rychlé vytváření virtuálních počítačů pomocí méně než pěti jednoduchých kroků:
- Vyberte si z uspořádaného seznamu základů virtuálních počítačů, které jsou nakonfigurované, schválené a autorizované týmem vedoucím nebo centrálním IT.
- Vytvořte virtuální počítače z předem vytvořených vlastních imagí, které mají již nainstalovaného veškerého softwaru a nástrojů. 
- Vytvořte virtuální počítače ze vzorců, které jsou v podstatě vlastními bitovými kopiemi v kombinaci s nejnovějšími sestaveními softwaru, který je nainstalovaný při vytváření virtuálních počítačů. 
- Instalace artefaktů, které jsou rozšířeními nasazenými na virtuálních počítačích po jejich zřízení.
- Nastavte automatické vypínání a automatické spouštění plánů na virtuálních počítačích.
- Vyžádá předem vytvořený virtuální počítač, aniž by procházel procesem vytváření.

DevTest Labs poskytuje vývojářům, kteří pracují s PaaS prostředími, následující možnosti:

- Pomocí Správce prostředků můžete rychle vytvářet prostředí PaaS pomocí méně než tří jednoduchých kroků.
- Vyberte si z nespravovaného seznamu šablon Správce prostředků, které jsou nakonfigurované a autorizované vedoucím nebo centrálním týmem.
- Pokud chcete prozkoumat Azure v rámci kontextu testovacího prostředí, zastavte si prázdnou skupinu prostředků (sandbox) pomocí šablony Správce prostředků.

DevTest Labs také umožňuje centrální IT řízení odpadů, optimalizaci nákladů na prostředky a udržení v rámci rozpočtů, a to provedením následujících úloh:  

- Nastavení automatického vypínání a automatického spouštění plánů na virtuálních počítačích.
- Nastavení zásad pro počet virtuálních počítačů, které uživatelé mohou vytvořit.
- Nastavení zásad pro velikosti virtuálních počítačů a obrázků z Galerie, ze kterých si uživatelé vyberou
- Sledování nákladů a nastavení cílů v laboratořích.
- Získání upozornění na vysoce plánované náklady na cvičení, abyste mohli podniknout potřebné akce.

DevTest Labs přináší následující výhody při vytváření, konfiguraci a správě prostředí v cloudu.

## <a name="cost-control-and-governance"></a>Řízení nákladů a zásady správného řízení
DevTest Labs usnadňuje řízení nákladů tím, že vám umožní provádět následující úlohy:

- [Nastavte zásady v laboratořích](devtest-lab-set-lab-policy.md), například počet virtuálních počítačů na uživatele nebo na testovací prostředí. 
- Vytvořte [zásady pro automatické vypnutí](devtest-lab-set-lab-policy.md) a spuštění virtuálních počítačů.
- Sledujte náklady na virtuální počítače a PaaS prostředky, které provedete v rámci [vašeho rozpočtu](devtest-lab-configure-cost-management.md).
- Zůstaňte v kontextu cvičení, abyste nemuseli vytvářet prostředky mimo ně.

## <a name="quickly-get-to-ready-to-test"></a>Rychlá příprava na testování
DevTest Labs umožňuje vytvářet předem zajištěná prostředí s podporou všeho, co váš tým potřebuje k vývoji a testování aplikací. Stačí [uplatnit deklaraci prostředí](devtest-lab-add-claimable-vm.md) , kde je nainstalované poslední dobré sestavení vaší aplikace, a začít pracovat. Nebo používejte kontejnery pro i rychlejší vytváření štíhlého prostředí.

## <a name="create-once-use-everywhere"></a>Vytvořte jednou, používejte všude
Zachyťte a sdílejte [šablony prostředí](devtest-lab-create-environment-from-arm.md) PaaS a [artefakty](add-artifact-repository.md) v rámci svého týmu nebo organizace – to vše ve správě zdrojového kódu, abyste mohli snadno vytvářet vývojová a testovací prostředí.

## <a name="worry-free-self-service"></a>Samoobslužná služba bez obav
DevTest Labs umožňuje vývojářům a testerům rychle a snadno [vytvořit virtuální počítače s IaaS](devtest-lab-add-vm.md) a [prostředky PaaS](devtest-lab-create-environment-from-arm.md) pomocí sady předem nakonfigurovaných prostředků.

## <a name="use-iaas-and-paas-resources"></a>Používání prostředků IaaS a PaaS 
Vývojáři také mohou PaaS prostředky, jako jsou clustery Azure Service Fabric, Web Apps funkce Azure App Service a farmy služby SharePoint pomocí šablon Správce prostředků. Pokud chcete začít PaaS v laboratořích, použijte šablony z [úložiště veřejného prostředí](devtest-lab-configure-use-public-environments.md) nebo [Připojte testovací prostředí k vlastnímu úložišti Git](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories). Můžete také sledovat náklady na tyto prostředky, abyste zůstali ve svém rozpočtu.

## <a name="integrate-with-your-existing-toolchain"></a>Integrace s existujícím sada nástrojů
Využijte předem připravené moduly plug-in nebo rozhraní API k zřizování prostředí pro vývoj a testování přímo z preferovaného [Nástroje pro kontinuální integraci (CI)](devtest-lab-integrate-ci-cd.md), integrovaného vývojového prostředí (IDE) nebo kanálu automatizovaného vydání. Můžete také použít komplexní nástroj příkazového řádku.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- Další informace o DevTest Labs najdete v tématu [Koncepty DevTest Labs](devtest-lab-concepts.md).
- Návod s podrobnými pokyny najdete v tématu [kurz: nastavení testovacího prostředí pomocí Azure DevTest Labs](tutorial-create-custom-lab.md).