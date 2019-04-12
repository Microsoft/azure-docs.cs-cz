---
title: Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak DevTest Labs mohli ji snadno vytvářet, spravovat a monitorovat virtuální počítače Azure
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
ms.openlocfilehash: e07149865d2dda52e33003964c2852a8aaccf76f
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59493191"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs
Azure DevTest Labs umožňuje vývojářům na týmy efektivně samostatně spravovat virtuální počítače (VM) a prostředků PaaS bez čekání na schválení.

DevTest Labs vytvoří testovací prostředí, který se skládá z předem nakonfigurované základních tříd nebo šablony Azure Resource Manageru. Ty se musí všechny potřebné nástroje a software, který vám umožní vytvářet prostředí. Za pár minut, hodin nebo dnů na rozdíl od můžete vytvořit prostředí.

Pomocí DevTest Labs můžete otestovat nejnovější verze aplikace provedením následujících úloh:

- Umožňuje rychle zajišťovat prostředí Windows a Linuxem pomocí opakovaně použitelných šablon a artefaktů.
- Snadné integrace svého kanálu nasazení s DevTest Labs můžete zřizovat prostředí na vyžádání.
- Vertikálně navýšit kapacitu zátěžového testování zřízením několika testovacích agentů a vytvářet předem zřízená prostředí pro školení a demoverze.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Možnosti
DevTest Labs poskytuje následující možnosti pro vývojáře, kteří pracují s virtuálními počítači:

- Rychle vytvořte virtuální počítače pomocí následujících méně než pěti jednoduchých krocích.
- Vyberte si z uspořádaný seznam bází virtuálních počítačů, které jsou nakonfigurované, schváleny a vedoucí týmu nebo centrální IT.
- Vytvoření virtuálních počítačů z předem vytvořených vlastních imagí, které mají veškerý software a nástroje, které jsou už nainstalované. 
- Vytvoření virtuálních počítačů z vlastních imagí v kombinaci s nejnovější sestavení softwaru nainstalovaného při vytváření virtuálních počítačů.
- Instalace artefaktů, které jsou nasazené na virtuálních počítačích po budou rozšíření.
- Nastavit automatické vypínání a automaticky spouštěná plány na virtuálních počítačích.
- Deklarace identity předem vytvořené virtuální počítač bez nutnosti kontaktovat v procesu vytváření.

DevTest Labs poskytuje následující možnosti pro vývojáře, kteří pracují s PaaS prostředí:

- Pomocí Resource Manageru můžete rychle vytvořit prostředí PaaS pomocí následujících méně než třemi jednoduchými kroky.
- Vyberte si z uspořádaný seznam šablon Resource Manageru, které jsou konfigurovány a vedoucí týmu nebo centrální IT.
- Uveďte do provozu prázdné skupiny prostředků (sandbox) s použitím šablony Resource Manageru k prozkoumání Azure v rámci testovacího prostředí.

DevTest Labs umožňuje také centrálního oddělení IT řídit odpad, optimalizovat náklady na prostředky a zůstat v rámci rozpočty provedením následujících úloh: 

- Nastavení automatického vypínání a automatického spuštění plány na virtuálních počítačích.
- Nastavení zásad na počtu virtuálních počítačů, které uživatelé můžou vytvářet.
- Nastavení zásad pro velikosti virtuálních počítačů a Image z galerie, které uživatelé můžou vybrat ze.
- Sledování nákladů a nastavení cíle na testovacích prostředích.
- Oznámení na vysokou plánované náklady pro laboratorní cvičení, takže můžete provádět potřebné akce.

DevTest Labs poskytuje následující výhody při vytváření, konfigurace a správa prostředí v cloudu.

## <a name="control-costs-and-governance"></a>Náklady na řízení a zásad správného řízení
DevTest Labs usnadňuje kontrolu nákladů, neboť umožňuje provádět následující úlohy:

- Nastavení zásad pro vaše testovací prostředí, jako je třeba počet virtuálních počítačů na uživatele nebo počet v testovacím prostředí. 
- Vytvoření zásad pro automatické vypínání a spouštění virtuálních počítačů.
- Sledování nákladů na virtuální počítače a PaaS prostředky pojidlem nahoru uvnitř labs zůstat v mezích rozpočtu.
- Zůstaňte v rámci kontextu vaší laboratoře tak nemusíte aktivovat prostředky mimo ně.

## <a name="quickly-get-to-ready-to-test"></a>Rychlá připravené testu
DevTest Labs umožňuje vytvářet předem zřízená prostředí se vším, co váš tým potřebuje k vývoji a testování aplikací. Jenom zažádat o prostředí, kde je nainstalován poslední úspěšný build vaší aplikace a začít pracovat. Nebo použijte kontejnery pro vytvoření prostředí ještě rychlejší, štíhlejší.

## <a name="create-once-use-everywhere"></a>Vytvořte jednou, používejte všude
Zaznamenání a sdílení šablon prostředí PaaS a artefaktů v rámci týmu nebo organizace – vše ve správě zdrojového kódu – umožní snadno vytvářet vývojářské a testovací prostředí.

## <a name="save-time-on-setup"></a>Ušetříte čas při instalaci  
Můžete snadno vytvořit virtuální počítače IaaS a PaaS prostředky pomocí sadu předem nakonfigurovaných prostředků.

## <a name="use-iaas-and-paas-resources"></a>Použít prostředky IaaS a PaaS 
Vývojáři také aktivovat prostředky PaaS, jako je například clustery Azure Service Fabric, funkce Web Apps služby Azure App Service a farmy služby SharePoint pomocí šablon Resource Manageru. Začít používat PaaS v laboratořích, použití šablony z prostředí veřejného úložiště nebo testovacím prostředí se připojit k úložišti Git. Můžete také sledovat náklady na tyto prostředky zůstat v mezích rozpočtu.

## <a name="integrate-with-your-existing-toolchain"></a>Integrace se stávající sadou nástrojů
Použití předem vytvořené moduly plug-in nebo rozhraní API umožňují zřizování vývoj a testování prostředí přímo z vašeho nástroje pro upřednostňovaného kontinuální integrace (CI), integrovaného vývojového prostředí (IDE) nebo kanálu automatických vydání. Můžete také použít komplexní nástroj příkazového řádku.

## <a name="next-steps"></a>Další postup
Viz následující články:

- Další informace o službě DevTest Labs, naleznete v tématu [koncepce DevTest Labs](devtest-lab-concepts.md).
- Návod s podrobnými pokyny najdete v tématu [kurzu: Nastavení testovacího prostředí pomocí Azure DevTest Labs](tutorial-create-custom-lab.md).


