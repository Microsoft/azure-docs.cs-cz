---
title: Použití služeb Platform-as-a-Service (PaaS) ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Další informace o použití služby Platform-as-a-Service (Pass) ve službě Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 865ae0b3f7a7965698a67183a4c820ba71f49cd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833913"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Použití služeb Platform-as-a-Service (PaaS) ve službě Azure DevTest Labs
PaaS je podporované ve službě DevTest Labs prostřednictvím funkce prostředí. Předem nakonfigurovaných šablon Azure Resource Manageru v úložišti Git podporuje prostředí v DevTest Labs. Prostředí může obsahovat prostředky PaaS a IaaS. Umožňují vám k vytváření komplexních systémů, které mohou obsahovat prostředky Azure, jako je například virtuálních počítačů, databází, virtuálních sítí a webových aplikací, které jsou přizpůsobené vzájemnou spolupráci. Tyto šablony umožňují konzistentní nasazení a lepší správu prostředí s využitím správy zdrojového kódu. 

Správné nastavení systému A umožňuje následující scénáře: 

- Vývojáři mají nezávislé a více prostředí
- Testování v různých konfiguracích asynchronně
- Integrace do pracovního a produkčního prostředí kanály bez uložení změn šablona
- Snadné správy a vytváření sestav nákladů s prostředí v rámci stejného testovacího prostředí a počítačích vývojářů zlepšuje.  

Poskytovatele prostředků DevTest Labs vytvoří jménem uživatele testovacího prostředí, prostředky, takže žádná zvláštní oprávnění musí být věnována uživatele testovacího prostředí. pro povolení využívání prostředků PaaS. Následující obrázek ukazuje cluster Service Fabric jako prostředí v testovacím prostředí.

![Cluster Service Fabric jako prostředí](./media/create-environment-service-fabric-cluster/cluster-created.png)

Další informace o nastavení prostředí, naleznete v tématu [vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md). DevTest Labs má veřejném úložišti šablon Azure Resource Manageru, které vám umožní vytvářet prostředí bez nutnosti připojení k externímu zdroji Githubu sami. Další informace o veřejných prostředích, najdete v části [konfigurace a použití veřejných prostředích ve službě Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Ve velkých organizacích vývojové týmy obvykle poskytují prostředí, jako je přizpůsobené/izolované testovací prostředí. Můžou existovat prostředí, které jsou pro všechny týmy v rámci organizační jednotky nebo divize. Skupina IT může být vhodné poskytnout své prostředí, které mohou využívat všechny týmy v organizaci.  

## <a name="customizations"></a>Vlastní nastavení

#### <a name="sandbox"></a>Sandbox 
Vlastník testovacího prostředí můžete přizpůsobit testovací prostředí, chcete-li změnit na roli uživatele z **čtečky** k **Přispěvatel** v rámci skupiny prostředků. Tato funkce je v **nastavení testovacího prostředí** stránky **konfigurace a zásad** testovacího prostředí. Tato změna v roli umožňuje uživateli přidat nebo odebrat prostředky v tomto prostředí. Pokud chcete omezit přístup další, pomocí zásad Azure. Tato funkce umožňuje přizpůsobit prostředky nebo konfigurace bez přístupu na úrovni předplatného.

#### <a name="custom-tokens"></a>Vlastní tokeny
Existuje informací vlastního testovacího prostředí, které je mimo skupinu prostředků a je specifické pro prostředí, která přístup k šabloně. Tady jsou některé z nich: 

- Identifikace v síti testovacího prostředí
- Location
- Účet úložiště, ve kterém jsou uložené soubory šablon Resource Manageru. 
 
#### <a name="lab-virtual-network"></a>Virtuální síť testovacího prostředí
[Prostředí připojení k virtuální síti testovacího prostředí](connect-environment-lab-virtual-network.md) článek popisuje, jak upravit šablony Resource Manageru pro použití `$(LabSubnetId)` token. Po vytvoření prostředí `$(LabSubnetId)` token je nahrazen první podsíť označit kde **použití ve virtuálním počítači vytvořit** je možnost nastavená na **true**. To umožňuje naše prostředí pro použití dříve vytvořili sítě. Pokud chcete používat stejné šablony Resource Manageru v prostředí v testu jako přípravným a produkčním prostředím, použijte `$(LabSubnetId)` jako výchozí hodnotu v parametru šablony Resource Manageru. 

#### <a name="environment-storage-account"></a>Účet úložiště prostředí
DevTest Labs podporuje použití [vnořené šablony Resource Manageru](../azure-resource-manager/resource-group-linked-templates.md). [[Nasazení vnořených šablon Azure Resource Manageru pro testovací prostředí](deploy-nested-template-environments.md) článek vysvětluje, jak používat `_artifactsLocation` a `_artifactsLocationSasToken` tokeny ve stejné složce jako nebo vnořený vytvořit identifikátor URI pro šablony Resource Manageru složka hlavní šablony. Další informace o těchto dvou tokenů, najdete v článku **artefakty nasazení** část [Azure Resource Manageru – Průvodce ověřenými postupy](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Činnost koncového uživatele

## <a name="developer"></a>Developer
Vývojáři použít stejný pracovní postup pro vytvoření virtuálního počítače k vytvoření konkrétní prostředí. Vyberte prostředí, a image počítače a zadejte potřebné informace vyžadované šablonou. Každý vývojář má prostředí umožňuje nasazení změny a ladění vylepšené vnitřní smyčku. Prostředí lze vytvořit kdykoli pomocí nejnovější šablony.  Tato funkce umožňuje se odstraní a znovu vytvořen, aby pomoct snížit prostoje odpadne ruční vytváření systému a zotavení z chyb testování prostředí.  

### <a name="testing"></a>Testování
Prostředí DevTest Labs umožňují nezávislé testování konkrétního kódu a konfigurace asynchronně. Běžnou praxí je nastavení prostředí s kódem z jednotlivých žádosti a spustit automatizované testy. Po dokončení spuštění automatizovaného testování, manuální testování jde provést proti konkrétní prostředí. Tento proces se obvykle provádí v rámci kanálu CI/CD. 

## <a name="management-experience"></a>Prostředí pro správu

### <a name="cost-tracking"></a>Sledování nákladů
Funkce sledování nákladů zahrnuje prostředků Azure v rámci různých prostředí jako součást celkové náklady na vývoj. Náklady podle prostředků nedojde k narušení v různých zdrojích v rámci prostředí, ale zobrazí prostředí jako jednu cenu.

### <a name="security"></a>Zabezpečení
Správně nakonfigurované předplatné Azure s DevTest Labs můžete [omezit přístup k prostředkům Azure jenom přes testovací prostředí](devtest-lab-add-devtest-user.md). V prostředích vlastník testovacího prostředí můžete povolit uživatelům přístup k prostředkům PaaS s konfiguracemi schválené bez povolení přístupu k jiným prostředkům Azure. Ve scénáři, kde uživatelé testovacího prostředí přizpůsobovat prostředí vlastník testovacího prostředí můžete povolit přístup přispěvatele. Přístup přispěvatele umožňuje uživateli testovacího prostředí pro přidání nebo odebrání prostředků pouze v rámci spravovanou skupinu prostředků Azure. Umožňuje jednodušší sledování a správě povolí uživateli přístup přispěvatele k předplatnému.

### <a name="automation"></a>Automation
Služba Automation je klíčovou komponentou pro rozsáhlé efektivní ekosystém. Služba Automation je nutné ke zpracování, Správa nebo sledování více prostředí napříč předplatná a praktická cvičení.

### <a name="cicd-pipeline"></a>Kanál CI/CD
Služby PaaS ve službě DevTest Labs můžete Pomozte vylepšit kanálu CI/CD s, zaměřuje nasazení, kde je přístup řízen testovacího prostředí.

## <a name="next-steps"></a>Další postup
Naleznete v následujících článcích pro podrobnosti o prostředí: 

- 
- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a používání veřejných prostředích ve službě Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Vytvoření prostředí s samostatný cluster Service Fabric v Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Připojení prostředí k virtuální síti testovacího prostředí ve službě Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrace prostředí kanálech Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





