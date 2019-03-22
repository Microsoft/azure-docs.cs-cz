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
ms.openlocfilehash: 599b4325e0eb9aa7f6ccca2626c6d4b14f38149d
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339325"
---
# <a name="about-azure-devtest-labs"></a>Azure DevTest Labs
Azure DevTest Labs je služba, která umožňuje vývojářům na tým, který efektivně samoobslužné služby virtuálních počítačů a/nebo prostředky PaaS, které potřebují pro vývoj, testování, školení a demoverze atd., aniž byste museli čekat pro konstantní schválení na nástroje, které potřebují. 

Testovací prostředí už skládá předem nakonfigurované základních tříd nebo šablony Resource Manageru se všechny potřebné nástroje a software, který mohou vývojáři vytvářet prostředí. Vývojáři mohou vytvářet prostředí za pár minut na rozdíl od hodin nebo dnů. 

Pomocí DevTest Labs, můžete otestovat nejnovější verzi vaší aplikace provedením následujících úloh:

- Rychlé zřizování prostředí Windows a Linuxem pomocí opakovaně použitelných šablon a artefaktů
- Snadné integrace svého kanálu nasazení s DevTest Labs můžete zřizovat prostředí na vyžádání
- Vertikálně navýšit kapacitu zátěžového testování zřízením několika testovacích agentů a vytvářet předem zřízená prostředí pro školení a demoverze.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]
> 
> 

## <a name="capabilities"></a>Možnosti
DevTest Labs poskytuje následující možnosti pro vývojáře, kteří pracují s virtuálními počítači:

- Rychle vytvořte virtuální počítač pomocí následujících méně než pěti jednoduchých krocích.
- Vyberte si z uspořádaný seznam bází virtuálních počítačů, které jsou nakonfigurované, schváleny a vedoucí týmu nebo centrální IT.
- Vytvoření počítačů z předem vytvořených vlastních imagí, které mají veškerý software a nástroje nainstalovaný v bitové kopii. 
- Vytvoření počítačů ze vzorce, které jsou v podstatě vlastní Image a nejnovější verzi softwaru, který je nainstalován v době vytvoření virtuálního počítače.
- Instalace artefaktů, které jsou nasazené na virtuálním počítači po jeho zřízení rozšíření.
- Nastavit automatické vypnutí a auto start plány na počítačích, které lze vypnout na konci dne a pak nahoru a systémem příští ráno.
- Jednoduše deklarace identity předem vytvořené virtuální počítače bez nutnosti projít procesem vytvoření počítače. 

DevTest Labs poskytuje následující možnosti pro vývojáře, kteří pracují s PaaS prostředí:

- Vytvářejte rychle po méně než třemi jednoduchými kroky prostředí PaaS Azure Resource Manageru.
- Vyberte si z uspořádaný seznam šablon Resource Manageru, které jsou nakonfigurované, schválení a vedoucí týmu nebo centrální IT.
- Uveďte do provozu prázdné skupiny prostředků (Sandbox) pomocí šablony Resource Manageru a prozkoumejte celou Azure při zachování stále v rámci testovacího prostředí.
- DevTest Labs poskytuje následující výhody při vytváření, konfigurace a správa pro vývojáře a testovací prostředí v cloudu

Kromě modelu samoobslužné služby pro vývojáře v týmu, tato služba umožňuje centrálního oddělení IT řídit odpad, optimalizovat náklady na prostředky a nepřekročíte s rozpočty provedením následujících úloh: 

- Nastavení automatického vypnutí a automaticky spustit plány na virtuálních počítačích.
- Nastavení zásad na počet uživatelů, virtuální počítače můžete vytvořit.
- Nastavení zásad o velikostech virtuálních počítačů a Galerie imagí uživatelé si mohou vybrat z.
- Sledování nákladů a nastavení cíle v testovacím prostředí.
- Oznámení na vysokou plánované náklady pro testovací prostředí, takže můžete provádět potřebné akce. 

DevTest Labs poskytuje následující výhody při vytváření, konfigurace a správa prostředí v cloudu:

## <a name="cost-control-and-governance"></a>Řízení nákladů a zásad správného řízení
DevTest Labs usnadňuje kontrolu nákladů, neboť umožňuje provádět následující úlohy:

- Nastavení zásad testovacího prostředí – například počet virtuálních počítačů (VM) na uživatele a počet virtuálních počítačů podle testovacího prostředí. 
- Vytvoření zásad pro automatické vypínání a spouštění virtuálních počítačů.
- Umožňuje sledovat náklady na virtuální počítače a PaaS prostředky spuštěné ve vašem testovacím prostředí, takže abyste mohli zůstat v mezích rozpočtu předdefinované. 
- Pomáhá vývojářům zůstat v rámci testovacího prostředí tak, aby není skončit rozbíhání všechny prostředky mimo to buď v podkladové skupiny prostředků nebo předplatného.

## <a name="quickly-get-to-ready-to-test"></a>Rychlá připravené testu
DevTest Labs umožňuje vytvářet předem zřízená prostředí se vším, co váš tým potřebuje k začít s vývojem a testování aplikací. Deklarace identity jednoduše prostředí, kde je nainstalován poslední úspěšný build vaší aplikace a začít okamžitě pracovat. Nebo pro vytvoření prostředí ještě rychleji a jednodušeji pomocí kontejnerů.

## <a name="create-once-use-everywhere"></a>Vytvořte jednou, používejte všude
Zaznamenání a sdílení šablon prostředí PaaS a artefaktů v rámci vašeho týmu nebo organizace – vše ve správě zdrojového kódu – vytvořit pro vývojáře a snadno testovacích prostředí.

## <a name="worry-free-self-service"></a>Bez obav samoobslužné funkce
DevTest Labs umožňuje vývojářům a testerům rychle a snadno vytvoří virtual machines (IaaS) a PaaS prostředky v rámci několika kliknutími používáte sadu předem nakonfigurované pro tyto prostředky.

## <a name="use-iaas-and-paas-resources"></a>Použít prostředky IaaS a PaaS 
DevTest Labs také umožňuje vývojářům využít více prostředků PaaS jako Web Apps, clustery Service Fabric, Sharepointové farmy a tak dále. v testovacím prostředí pomocí šablony Resource Manageru. Pomocí šablon Resource Manageru z našeho veřejného prostředí úložiště nebo připojení k vlastním úložišti Git začít používat PaaS v labs testovacího prostředí. Můžete také sledovat náklady na tyto prostředky zůstat v rámci předdefinovaných rozpočtů. 

## <a name="integrate-with-your-existing-toolchain"></a>Integrace se stávající sadou nástrojů
Použití předem vytvořených moduly plug-in nebo naše API k zřizování vývojových/testovacích prostředí přímo z vašeho nástroje pro upřednostňovaného kontinuální integrace (CI), integrovaného vývojového prostředí (IDE) nebo kanálu automatických vydání. Můžete také použít Naše komplexní nástroj příkazového řádku.

## <a name="next-steps"></a>Další postup
Viz následující články: 

- Další informace o službě DevTest Labs, naleznete v tématu [koncepce DevTest Labs](devtest-lab-concepts.md)
- Návod s podrobnými pokyny najdete v tématu [kurzu: Nastavení testovacího prostředí pomocí Azure DevTest Labs](tutorial-create-custom-lab.md)


