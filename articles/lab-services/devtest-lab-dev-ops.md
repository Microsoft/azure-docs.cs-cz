---
title: Integrace laboratoří Azure DevTest Labs a DevOps | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat testovací prostředí Azure DevTest Labs v rámci kanály průběžné integrace (CI)/ průběžné hodování (CD) v podnikovém prostředí.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078921"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrace laboratoří Azure DevTest Labs a Azure DevOps
DevOps je metodika vývoje softwaru, která integruje vývoj softwaru (Dev) s operacemi (Ops) pro systém. Tento systém může poskytovat nové funkce, aktualizace a opravy v souladu s obchodními cíli. Tato metodika zahrnuje vše od navrhování nových funkcí na základě cílů, vzorců využití a zpětné vazby od zákazníků. k upevnění, obnovení a kalení systému, když nastanou problémy. Snadno identifikovanou součástí této metodiky je potrubí průběžné integrace (CI)/ průběžného dodávek (CD). Kanál CI/CD přebírá informace, kód a prostředky z potvrzení prostřednictvím řady kroků, které zahrnují vytváření, testování a nasazení k vytvoření systému. Tento článek se zaměřuje na různé způsoby efektivní využití testovacích prostředí v rámci kanálu v podnikovém prostředí. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Výhody použití testovacích prostředí v pracovním postupu DevOps 

### <a name="focused-access"></a>Cílený přístup 
Použití testovacího prostředí jako součásti umožňuje konkrétnímu ekosystému přidružit se k omezené skupině lidí. Tým nebo skupina, která pracuje ve společné oblasti nebo v určité funkci, jim obvykle bude přiřazeno testovací prostředí.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikace infrastruktury v cloudu 
Vývojář může rychle nastavit vývojový ekosystém, který obsahuje vývojové pole se zdrojovým kódem a nástroji. Vývojář může také vytvořit prostředí, které je téměř totožné s konfigurací produkčního prostředí. Pomáhá s rychlejším vývojem vnitřní smyčky. 

### <a name="pre-production"></a>Předprodukce 
S testovací ho v kanálu CI/CD usnadňuje mít více různých předprodukčních prostředí nebo počítačů spuštěných současně pro asynchronní testování. Různé infrastruktury podpory, jako jsou agenti sestavení lze nasadit a spravovat v rámci testovacího prostředí. 

## <a name="devops-with-devtest-labs"></a>DevOps s DevTest Labs 

### <a name="development--operation"></a>Vývoj / Provoz 
Testovací prostředí by se mělo zaměřit na tým, který pracuje v oblasti funkcí. Toto společné fokus umožňuje sdílení prostředků specifických pro danou oblast, jako jsou nástroje, skripty nebo šablony Správce prostředků. Umožňuje rychlejší změny a zároveň omezuje negativní účinky na menší skupinu. Tyto sdílené prostředky umožňují vývojáři vytvářet vývojové virtuální počítače se všemi potřebnými kódy, nástroji a konfigurací. Mohou být vytvořeny buď dynamicky, nebo mají systém, který vytváří základní bitové kopie s přizpůsobením. Vývojář může nejen vytvářet virtuální počítače, ale taky můžou vytvářet prostředí DevTest Labs na základě nezbytných šablon k vytvoření příslušných prostředků Azure v testovacím prostředí. Jakékoli změny nebo destruktivní práce lze provést proti laboratornímu prostředí, aniž by to ovlivnilo někoho jiného. Zvažte scénář, kdy je produkt samostatný systém nainstalovaný v počítači zákazníka. V tomto scénáři DevTest Labs vylepšila vytvoření virtuálních počítačů, která zahrnuje instalaci dalšího softwaru pomocí artefaktů a konfigurace zákazníků před sestavením pro rychlejší testování kódu vnitřní smyčkou. 
  
## <a name="cicd-pipeline"></a>Ci/CD potrubí 
Kanál CI/CD je jednou z důležitých součástí v DevOps, které přesunoukód z požadavku na přijetí změny dat vývojáře, integruje jej s existujícím kódem a nasazuje jej do produkčního ekosystému. Všechny zdroje nemusí být v laboratoři. Například hostitele Jenkins es a mimo testovací prostředí jako trvalejší prostředek. Tady jsou některé konkrétní příklady integrace testovacích prostředí do kanálu. 

### <a name="build"></a>Sestavení 
Kanál sestavení je zaměřen na vytvoření balíčku součástí, které budou testovány společně, které mají být předány do kanálu vydání. Testovací prostředí může být součástí kanálu sestavení jako umístění pro agenty sestavení a další prostředky podpory. Schopnost dynamicky budovat infrastrukturu umožňuje větší kontrolu. Díky možnosti mít více prostředí v testovacím prostředí, každé sestavení lze spustit asynchronně při použití ID sestavení jako součást informací o prostředí jednoznačně identifikovat prostředky pro konkrétní sestavení.   

U agentů sestavení zvyšuje možnost testovacího prostředí omezit přístup zabezpečení a snižuje možnost náhodného poškození.  

### <a name="test"></a>Test 
DevTest Labs umožňuje kanálu CI/CD automatizovat vytváření prostředků Azure (Virtuální počítače, prostředí), které lze použít pro automatizované a ruční testování. Virtuální počítače by být vytvořeny pomocí artefakty nebo vzorce, které používají informace z procesu sestavení k vytvoření různých vlastníkonfigurace nezbytné pro testování.   

### <a name="release"></a>Vydat 
DevTest Labs se běžně používá pro ověření v části vydání před nasazením kódu. Je to podobné testování v části Sestavení. Produkční prostředky by neměly být nasazeny v rámci devTest Labs. 

### <a name="customization"></a>Přizpůsobení 
V Azure DevOps existují existující úkoly, které umožňují manipulaci s virtuálními počítači a prostředí v rámci konkrétních testovacích prostředí. Zatímco Služby Azure DevOps jsou jedním ze způsobů správy kanálu CI/CD, můžete integrovat testovací prostředí do libovolného systému, který podporuje možnost volat api REST, spouštět skripty PowerShellu nebo používat Azure CLI. 

Zatímco někteří správci kanálu CI/CD mají existující moduly plug-in s otevřeným zdrojovým kódem, které můžou spravovat prostředky v rámci Azure a DevTest Labs. Možná budete muset použít některé vlastní skriptování, aby vyhovovaly konkrétním potřebám kanálu.  S ohledem na to při provádění úlohy instanční objekt se používá s příslušnou roli získat přístup k testovacímu prostředí. Instanční objekt obvykle potřebuje přístup role přispěvatele do testovacího prostředí. Další informace najdete [v tématu Integrace Azure DevTests Labs do vašeho Azure DevOps průběžné integrace a doručování kanálu](devtest-lab-integrate-ci-cd-vsts.md). 
 