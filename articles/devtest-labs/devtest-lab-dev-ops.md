---
title: Integrace Azure DevTest Labs a DevOps | Microsoft Docs
description: Naučte se používat cvičení Azure DevTest Labs v rámci kanálů průběžné integrace (CI)/průběžného doručování (CD) v podnikovém prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85481064"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integrace Azure DevTest Labs a Azure DevOps
DevOps je metodologie vývoje softwaru, která integruje vývoj softwaru (dev) s operacemi (OPS) pro systém. Tento systém může poskytovat nové funkce, aktualizace a opravy v rámci sbližování s obchodními záměry. Tato metodika zahrnuje vše od navrhování nových funkcí na základě cílů, způsobů využití a zpětné vazby od zákazníků. pro opravu, obnovování a posílení zabezpečení systému, když dojde k problémům. Snadno identifikovaná součást této metodologie je kanál kontinuální integrace (CI)/průběžné doručování (CD). Kanál CI/CD přebírá informace, kód a prostředky z potvrzení prostřednictvím série kroků, které zahrnují sestavování, testování a nasazování, k vytvoření systému. Tento článek se zaměřuje na různé způsoby efektivního využití cvičení v rámci kanálu v podnikovém prostředí. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Výhody používání cvičení v DevOps pracovním postupu 

### <a name="focused-access"></a>Prioritní přístup 
Použití testovacího prostředí jako komponenty umožňuje konkrétnímu ekosystému přidružit k omezené skupině lidí. Tým nebo skupina, které pracují v běžné oblasti nebo konkrétní funkci, mají obvykle přiřazený testovací prostředí.   

### <a name="infrastructure-replication-in-the-cloud"></a>Replikace infrastruktury v cloudu 
Vývojář může rychle nastavit vývojový ekosystém, který obsahuje vývojové pole se zdrojovým kódem a nástroji. Vývojář může také vytvořit prostředí, které je téměř shodné s produkční konfigurací. Pomáhá s rychlejším vývojem vnitřních smyček. 

### <a name="pre-production"></a>Předprodukce 
Použití testovacího prostředí v kanálu CI/CD usnadňuje více různých předprodukčních prostředí nebo počítačů, které jsou spuštěny ve stejnou dobu pro asynchronní testování. V testovacím prostředí lze nasadit a spravovat různé infrastruktury podpory, například agenty sestavení. 

## <a name="devops-with-devtest-labs"></a>DevOps s DevTest Labs 

### <a name="development--operation"></a>Vývoj/operace 
Testovací prostředí by se mělo zaměřit na tým, který pracuje v oblasti funkcí. Tento běžný fokus umožňuje sdílení prostředků specifických pro oblast, jako jsou nástroje, skripty nebo šablony Správce prostředků. Umožňuje rychlejší změny a zároveň omezuje negativní vliv na menší skupinu. Tyto sdílené prostředky umožňují vývojářům vytvářet vývojové virtuální počítače se všemi potřebnými kódy, nástroji a konfigurací. Je možné je vytvořit dynamicky nebo mít systém, který vytváří základní image s vlastními nastaveními. Jenom vývojáři můžou vytvářet virtuální počítače, ale také můžou vytvářet prostředí DevTest Labs na základě potřebných šablon k vytvoření vhodných prostředků Azure v testovacím prostředí. Jakékoli změny nebo destruktivní práci lze provést v testovacím prostředí, aniž by to mělo vliv na někoho jiného. Vezměte v úvahu scénář, ve kterém je produkt samostatný systém nainstalovaný na počítači zákazníka. V tomto scénáři DevTest Labs vylepšili vytváření virtuálních počítačů, včetně instalace dalšího softwaru pomocí artefaktů a konfigurací zákazníků předběžného sestavení pro rychlejší testování vnitřních smyček kódu. 
  
## <a name="cicd-pipeline"></a>Kanál CI/CD 
Kanál CI/CD je jednou z důležitých součástí v DevOps, které přesouvají kód z žádosti o přijetí změn od vývojáře, integruje ho s existujícím kódem a nasadí ho do produkčního ekosystému. Všechny prostředky nemusejí být v testovacím prostředí. Například hostitel Jenkinse může být nastaven mimo testovací prostředí jako trvalý prostředek. Tady jsou některé konkrétní příklady integrace Labs do kanálu. 

### <a name="build"></a>Sestavení 
Kanál sestavení se zaměřuje na vytvoření balíčku součástí, které se budou testovat společně s kanálem pro vydávání verzí. Laboratoře můžou být součástí kanálu sestavení jako umístění pro agenty sestavení a další prostředky podpory. Možnost dynamického sestavování infrastruktury umožňuje lepší kontrolu. S možností mít více prostředí v testovacím prostředí, každé sestavení lze spustit asynchronně při použití ID sestavení jako součást informací o prostředí k jednoznačné identifikaci prostředků na konkrétní sestavení.   

Pro agenty sestavení je schopnost testovacího prostředí omezit přístup a snižuje se možnost náhodného poškození.  

### <a name="test"></a>Test 
DevTest Labs umožňuje kanálu CI/CD automatizovat vytváření prostředků Azure (virtuálních počítačů, prostředí), které se dají použít k automatizovanému a ručnímu testování. Virtuální počítače by se vytvořily pomocí artefaktů nebo vzorců, které používají informace z procesu sestavení k vytvoření různých vlastních konfigurací nezbytných pro testování.   

### <a name="release"></a>Vydat 
DevTest Labs se běžně používá pro ověření v oddílu Release před nasazením kódu. Je podobný testování v části sestavení. Produkční prostředky by se neměly nasazovat v rámci DevTest Labs. 

### <a name="customization"></a>Přizpůsobení 
V Azure DevOps existují úkoly, které umožňují manipulaci s virtuálními počítači a prostředími v rámci konkrétní laboratoře. I když Azure DevOps Services máte jeden ze způsobů správy kanálu CI/CD, můžete testovací prostředí integrovat do libovolného systému, který podporuje možnost volání rozhraní REST API, spouštění skriptů PowerShellu nebo použití rozhraní příkazového řádku Azure CLI. 

I když někteří manažeři kanálu CI/CD mají existující Open Source moduly plug-in, které mohou spravovat prostředky v rámci Azure a DevTest Labs. K přizpůsobení konkrétních potřeb kanálu budete možná muset použít některé vlastní skriptování.  V takovém případě se při provádění úlohy používá instanční objekt s odpovídající rolí k získání přístupu k testovacímu prostředí. Instanční objekt obvykle potřebuje přístup k testovacímu prostředí role přispěvatele. Další informace najdete v tématu [Integrace Azure DevTests Labs do služby Azure DevOps Continuous Integration a Delivery Pipeline](devtest-lab-integrate-ci-cd.md). 
 