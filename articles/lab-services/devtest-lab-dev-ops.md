---
title: Integrace Azure DevTest Labs a DevOps | Dokumentace Microsoftu
description: Další informace o použití testovacích prostředí Azure DevTest Labs v rámci kontinuální integrace (CI) a průběžné doručování (CD) kanálů v podnikovém prostředí.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078921"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrace Azure DevTest Labs a Azure DevOps
DevOps je metodologie vývoje softwaru, který software development (vývoj) se integruje s operations (Ops) pro systém. Tento systém může poskytovat nové funkce, aktualizace a opravy v souladu s firemními cíli. Tato metoda zahrnuje všechno od návrhu nové funkce na základě cílů, vzorce používání a zpětné vazby od zákazníků; k opravě, obnovení a posílení zabezpečení systému, když dojde k problémům. Snadno identifikované součástí této metody je kontinuální integrace (CI) / kanálu průběžného doručování (CD). Kanál CI/CD přijímá informace, kódu a prostředků z potvrzení změn prostřednictvím řady kroků, které zahrnují sestavování, testování a nasazení, k vytvoření systému. Tento článek se zaměřuje na různé způsoby, jak efektivně použití testovacích prostředí v rámci kanálu v podnikovém prostředí. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Výhody použití testovacích prostředí v pracovním postupu DevOps 

### <a name="focused-access"></a>Cílený přístup 
Použití testovacího prostředí jako součást umožňuje konkrétní ekosystém pro přidružení k na omezenou skupinu uživatelů. Tým nebo skupinu, která pracuje v běžné oblasti nebo konkrétní funkci obvykle, bude mít přiřazené testovacího prostředí.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikace infrastrukturu v cloudu 
Vývojáři můžou rychle nastavit vývojový ekosystém, který obsahuje pole vývoj se zdrojový kód a nástroje. Vývojář můžete také vytvořit prostředí, které je téměř stejný jako konfigurace výroby továren. Pomůže s vývojem pro rychlejší vnitřní smyčku. 

### <a name="pre-production"></a>Předprodukční režim 
V kanálu CI/CD s testovacím prostředí usnadňuje více různých předprodukčních prostředích nebo počítače, které běží ve stejnou dobu pro asynchronní testování. Různé podporu infrastruktury jako je například agenty sestavení je možné nasadit a spravovat v rámci testovacího prostředí. 

## <a name="devops-with-devtest-labs"></a>DevOps s DevTest Labs 

### <a name="development--operation"></a>Vývoj / operace 
Testovací prostředí se musí zaměřit na tým, který pracuje v oblasti funkce. Toto společné zaměření umožňuje sdílení prostředků konkrétní oblasti, jako jsou nástroje, skripty nebo šablony Resource Manageru. Toto řešení umožňuje rychlejší změny při omezování nepříznivé vlivy na menší skupinu. Tyto sdílené prostředky umožňuje vývojářům vytváření vývojových virtuálních počítačů s nezbytné kódu, nástroje a konfigurací. Jejich lze vytvořit buď dynamicky nebo mít systém, který vytváří základní Image s použitím přizpůsobení. Nejen můžou vývojáři vytvářet virtuální počítače, ale také můžete vytvořit prostředí DevTest Labs podle potřeby šablony pro vytváření příslušných prostředků Azure v testovacím prostředí. Všechny změny a destruktivní práce lze provést proti testovacím prostředí bez ovlivnění nikdo jiný. Vezměte v úvahu scénář, ve kterém je samostatný systém nainstalovaný na počítači zákazníka. V tomto scénáři je vylepšené DevTest Labs. vytvoření virtuálního počítače, který zahrnuje instalaci dalšího softwaru pomocí artefaktů a předem vytvářet zákaznických konfigurací pro rychlejší vnitřní smyčku testování kódu. 
  
## <a name="cicd-pipeline"></a>Kanál CI/CD 
Kanál CI/CD je jedním z nejdůležitějších součástí v DevOps, které přesunout kód z žádost o přijetí změn pro vývojáře, se integruje s existující kód a nasadí do produkce ekosystému. Všechny prostředky nemusí být v rámci testovacího prostředí. Například Jenkins hostitele může nastavit mimo prostředí jako více trvalý prostředek. Tady jsou některé konkrétní příklady integrace labs do kanálu. 

### <a name="build"></a>Sestavení 
Kanál sestavení se zaměřuje na vytvoření balíčku komponent, které budou testovaných společně na předána kanál pro vydávání verzí. Testovací prostředí můžou být součástí kanálu sestavení jako umístění pro agenty sestavení a další prostředky na podporu. Schopnost dynamicky vytváření infrastruktury umožňuje pro větší kontrolu. Možnost mít několik prostředí v testovacím prostředí každé sestavení může běžet asynchronně při používání ID sestavení jako součást informací o prostředí k jednoznačné identifikaci prostředky, které konkrétního sestavení.   

Pro agenty sestavení testovacího prostředí umožňuje omezit přístup zvyšuje úroveň zabezpečení a snižuje možnost náhodného poškození.  

### <a name="test"></a>Test 
DevTest Labs umožňuje automatizovat vytváření prostředků Azure (virtuální počítače, prostředí), který lze pro automatizované a manuální testování kanálu CI/CD. Virtuální počítače by se vytvořily pomocí artefaktů nebo vzorce, které můžete vytvořit různé vlastní konfigurace pro testování informace z procesu sestavení.   

### <a name="release"></a>Vydat 
DevTest Labs se běžně používá pro ověření v části vydání předtím, než se kód nasazuje. Je to podobné testování v části sestavení. Produkční prostředky by se neměly nasazovat v DevTest Labs. 

### <a name="customization"></a>Přizpůsobení 
V Azure DevOps se stávající úlohy, které umožňují zpracování virtuálních počítačů a prostředí v rámci konkrétní labs. Azure DevOps služby jsou jeden způsob, jak spravovat kanál CI/CD, můžete integrovat testovacího prostředí do libovolný systém, který podporuje možnost volat rozhraní REST API, spouštění skriptů prostředí PowerShell nebo pomocí Azure CLI. 

I když některé Správce kanálu CI/CD má existující modulů plug-in open source, která může spravovat prostředky v rámci Azure a DevTest Labs. Budete muset použít vlastní skripty podle konkrétních potřeb kanálu.  To na paměti, při provádění úlohy hlavního názvu služby používat k příslušné roli získat přístup k testovacím prostředí. Služby, které obvykle potřebuje přístup k testovacím prostředí roli Přispěvatel. Další informace najdete v tématu [integrovat Azure DevTests Labs do Azure DevOps průběžné integrace a doručování kanálu](devtest-lab-integrate-ci-cd-vsts.md). 
 