---
title: Používání služeb PaaS (Platform-as-a-Service) v laboratořích Azure DevTest Labs
description: Přečtěte si, jak používat služby platformy jako služby (Pass) v azure devtest labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169191"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Používání služeb PaaS (Platform-as-a-Service) v laboratořích Azure DevTest Labs
PaaS je podporován v DevTest Labs prostřednictvím funkce prostředí. Prostředí v devtest labs jsou podporované předem nakonfigurované šablony Azure Resource Manager v úložišti Git. Prostředí může obsahovat prostředky PaaS i IaaS. Umožňují vytvářet složité systémy, které mohou zahrnovat prostředky Azure, jako jsou virtuální počítače, databáze, virtuální sítě a webové aplikace, které jsou přizpůsobeny tak, aby spolupracovaly. Tyto šablony umožňují konzistentní nasazení a vylepšenou správu prostředí pomocí správy zdrojového kódu. 

Správně nastavený systém umožňuje následující scénáře: 

- Vývojáři mají nezávislé a více prostředí
- Testování v různých konfiguracích asynchronně
- Integrace do pracovních a produkčních kanálů bez jakýchkoli změn šablon
- S vývojových strojů a prostředí v rámci stejné laboratoře zlepšuje snadnou správu a vykazování nákladů.  

Zprostředkovatel prostředků DevTest Labs vytváří prostředky jménem uživatele testovacího prostředí, takže uživateli testovacího prostředí není třeba udělit žádná další oprávnění, která by umožnila použití prostředků PaaS. Následující obrázek znázorňuje cluster Service Fabric jako prostředí v testovacím prostředí.

![Cluster Service Fabric jako prostředí](./media/create-environment-service-fabric-cluster/cluster-created.png)

Další informace o nastavení prostředí najdete v tématu [Vytváření prostředí s více virtuálními počítači a prostředků PaaS pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs má veřejné úložiště šablon Azure Resource Manager, které můžete použít k vytvoření prostředí, aniž byste se museli sami připojovat k externímu zdroji GitHubu. Další informace o veřejných prostředích najdete [v tématu Konfigurace a použití veřejných prostředí v laboratořích Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Ve velkých organizacích vývojové týmy obvykle poskytují prostředí, jako jsou přizpůsobená/izolovaná testovací prostředí. Mohou existovat prostředí, která mají být používána všemi týmy v rámci organizační jednotky nebo divize. Skupina IT může chtít poskytnout své prostředí, které mohou být použity všemi týmy v organizaci.  

## <a name="customizations"></a>Přizpůsobení

#### <a name="sandbox"></a>Sandbox 
Vlastník testovacího prostředí můžete přizpůsobit testovací prostředí změnit roli uživatele z **čtečky** na **přispěvatele** v rámci skupiny prostředků. Tato funkce je na stránce **Nastavení testovacího prostředí** v části **Konfigurace a zásady** testovacího prostředí. Tato změna role umožňuje uživateli přidat nebo odebrat prostředky v rámci tohoto prostředí. Pokud chcete přístup dále omezit, použijte zásady Azure. Tato funkce umožňuje přizpůsobit prostředky nebo konfiguraci bez přístupu na úrovni předplatného.

#### <a name="custom-tokens"></a>Vlastní tokeny
Existuje několik vlastních informací testovacího prostředí, které je mimo skupinu prostředků a je specifické pro prostředí, ke kterým má šablona přístup. Zde jsou některé z nich: 

- Identifikace sítě v laboratoři
- Umístění
- Účet úložiště, ve kterém jsou uloženy soubory šablon Resource Manager. 
 
#### <a name="lab-virtual-network"></a>Virtuální síť Lab
[Připojení prostředí k virtuální síti testovacího prostředí](connect-environment-lab-virtual-network.md) článek popisuje, jak upravit šablonu Správce prostředků pro použití tokenu. `$(LabSubnetId)` Při vytvoření prostředí je `$(LabSubnetId)` token nahrazen první značkou podsítě, kde je **možnost vytvoření virtuálního počítače** nastavena na **hodnotu true**. Umožňuje našemu prostředí používat dříve vytvořené sítě. Pokud chcete používat stejné šablony Správce prostředků v prostředích v testovacím testu jako pracovní a produkční, použijte `$(LabSubnetId)` jako výchozí hodnotu v parametru šablony Správce prostředků. 

#### <a name="environment-storage-account"></a>Účet úložiště prostředí
DevTest Labs podporuje použití [vnořených šablon Správce prostředků](../azure-resource-manager/templates/linked-templates.md). Článek [[Nasazení vnořených šablon Azure Resource Manager pro testování prostředí](deploy-nested-template-environments.md) vysvětluje, jak používat `_artifactsLocation` a `_artifactsLocationSasToken` tokeny k vytvoření identifikátoru URI do šablony Správce prostředků ve stejné složce jako nebo ve vnořené složce hlavní šablony. Další informace o těchto dvou tokenech najdete v části **Artefakty nasazení** [ve Správci prostředků Azure – průvodce doporučenými postupy](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Zkušenosti uživatele

## <a name="developer"></a>Developer
Vývojáři používají stejný pracovní postup pro vytvoření virtuálního virtuálního mísu k vytvoření konkrétního prostředí. Vyberou prostředí vs. obraz stroje a zadají potřebné informace požadované šablonou. Každý vývojář, který má prostředí umožňuje nasazení změn a lepší vnitřní smyčky ladění. Prostředí lze vytvořit kdykoli pomocí nejnovější šablony.  Tato funkce umožňuje zničení a znovu vytvoření prostředí, aby se zkrátily prostoje z nutnosti ručního vytváření systému nebo zotavení z testování chyb.  

### <a name="testing"></a>Testování
Prostředí DevTest Labs umožňují nezávislé testování konkrétního kódu a konfigurací asynchronně. Běžnou praxí je nastavit prostředí s kódem z individuální žádosti o přijetí vyžádat a spustit jakékoli automatizované testování. Po spuštění automatizovaného testování až do konce, jakékoli ruční testování lze provést proti konkrétní prostředí. Obvykle se tento proces provádí jako součást kanálu CI/CD. 

## <a name="management-experience"></a>Zkušenosti s řízením

### <a name="cost-tracking"></a>Sledování nákladů
Funkce sledování nákladů zahrnuje prostředky Azure v různých prostředích jako součást trendu celkových nákladů. Náklady podle prostředků nerozdělují různé prostředky v rámci prostředí, ale zobrazují prostředí jako jednu cenu.

### <a name="security"></a>Zabezpečení
Správně nakonfigurované předplatné Azure s DevTest Labs můžete [omezit přístup k prostředkům Azure pouze prostřednictvím testovacího prostředí](devtest-lab-add-devtest-user.md). S prostředími může vlastník testovacího prostředí umožnit uživatelům přístup k prostředkům PaaS se schválenými konfiguracemi, aniž by umožnil přístup k jiným prostředkům Azure. Ve scénáři, kde uživatelé testovacího prostředí přizpůsobit prostředí, vlastník testovacího prostředí můžete povolit přístup přispěvatele. Přístup k přispěvateli umožňuje uživateli testovacího prostředí přidat nebo odebrat prostředky Azure pouze v rámci skupiny spravovaných prostředků. Umožňuje snadnější sledování a správu a umožňují přístup přispěvatele uživatele k předplatnému.

### <a name="automation"></a>Automatizace
Automatizace je klíčovou složkou pro rozsáhlý a efektivní ekosystém. Automatizace je nezbytná pro správu nebo sledování více prostředí napříč předplatnými a testovacími prostředími.

### <a name="cicd-pipeline"></a>Kanál CI/CD
Služby PaaS v devtest labs může pomoci zlepšit kanál CI/CD tím, že cílené nasazení, kde je přístup řízen testovacího prostředí.

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích naleznete v následujících článcích: 

- 
- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředí v laboratořích Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Vytvoření prostředí pomocí samostatného clusteru Service Fabric v laboratořích Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Připojení prostředí k virtuální síti testovacího prostředí v laboratořích Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrace prostředí do vašich kanálů Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





