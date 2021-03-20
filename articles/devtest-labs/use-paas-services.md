---
title: Používejte služby PaaS (Platform as a Service) v Azure DevTest Labs
description: Naučte se používat služby platformy jako služby (pass) v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: eec37527386098174906dc2737d7b763241da3f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478735"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Používejte služby PaaS (Platform as a Service) v Azure DevTest Labs
PaaS se v DevTest Labs podporuje přes funkci prostředí. Prostředí v DevTest Labs jsou podporovaná předem nakonfigurovanými šablonami Azure Resource Manager v úložišti Git. Prostředí můžou obsahovat prostředky PaaS i IaaS. Umožňují vytvářet komplexní systémy, které mohou zahrnovat prostředky Azure, jako jsou virtuální počítače, databáze, virtuální sítě a webové aplikace, které jsou přizpůsobené pro práci dohromady. Tyto šablony umožňují konzistentní nasazení a vylepšenou správu prostředí pomocí správy zdrojového kódu. 

Správně nastavené systémy umožňují následující scénáře: 

- Vývojáři, kteří mají nezávislé a více prostředí
- Asynchronní testování v různých konfiguracích
- Integrace do pracovních a produkčních kanálů bez jakýchkoli změn šablony
- Díky vývojovým počítačům a prostředím v rámci stejného testovacího prostředí se zlepšuje snadné řízení a generování sestav nákladů.  

Poskytovatel prostředků DevTest Labs vytvoří prostředky v zastoupení uživatele testovacího prostředí, takže uživatel testovacího prostředí nemusí mít k dispozici žádná další oprávnění, aby mohl používat prostředky PaaS. Následující obrázek ukazuje cluster Service Fabric jako prostředí v testovacím prostředí.

![Service Fabric clusteru jako prostředí](./media/create-environment-service-fabric-cluster/cluster-created.png)

Další informace o nastavení prostředí najdete v tématu [vytvoření prostředí s více virtuálními počítači a PaaS prostředky pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md). DevTest Labs má veřejné úložiště Azure Resource Manager šablon, které můžete použít k vytváření prostředí, aniž byste se museli připojovat k externímu zdroji GitHub sami. Další informace o veřejných prostředích najdete v tématu [Konfigurace a používání veřejných prostředí v Azure DevTest Labs](devtest-lab-configure-use-public-environments.md).

Ve velkých organizacích vývojové týmy obvykle poskytují prostředí, jako jsou přizpůsobená/izolovaná testovací prostředí. Může existovat prostředí, která budou používat všechny týmy v rámci obchodní jednotky nebo oddělení. Tato skupina IT může chtít poskytovat prostředí, která můžou používat všichni týmy v organizaci.  

## <a name="customizations"></a>Vlastní nastavení

#### <a name="sandbox"></a>Sandbox 
Vlastník testovacího prostředí může přizpůsobit testovací prostředí, aby se změnila role uživatele ze **čtenáře** na **přispěvatele** v rámci skupiny prostředků. Tato funkce je na stránce **Nastavení testovacího prostředí** v části **Konfigurace a zásady** testovacího prostředí. Tato změna role umožňuje uživateli přidávat nebo odebírat prostředky v tomto prostředí. Pokud chcete přístup omezit, použijte zásady Azure. Tato funkce umožňuje přizpůsobit prostředky nebo konfiguraci bez přístupu na úrovni předplatného.

#### <a name="custom-tokens"></a>Vlastní tokeny
K dispozici jsou některé vlastní informace o testovacím prostředí, které jsou mimo skupinu prostředků a jsou specifické pro prostředí, ke kterým může šablona přistupovat. Tady jsou některé z nich: 

- Identifikace sítě testovacího prostředí
- Umístění
- Účet úložiště, ve kterém jsou uložené soubory šablon Správce prostředků. 
 
#### <a name="lab-virtual-network"></a>Virtuální síť testovacího prostředí
Článek [připojení prostředí k virtuální síti testovacího prostředí](connect-environment-lab-virtual-network.md) popisuje, jak upravit šablonu správce prostředků pro použití `$(LabSubnetId)` tokenu. Po vytvoření prostředí se `$(LabSubnetId)` token nahradí první značkou podsítě, kde možnost **použít v možnosti vytvoření virtuálního počítače** je nastavená na **hodnotu true**. Umožňuje našemu prostředí používat dříve vytvořené sítě. Pokud chcete použít stejné šablony Správce prostředků v prostředích v testu jako pracovní a produkční, použijte `$(LabSubnetId)` jako výchozí hodnotu v parametru správce prostředků šablony. 

#### <a name="environment-storage-account"></a>Účet úložiště prostředí
DevTest Labs podporuje použití [vnořených správce prostředků šablon](../azure-resource-manager/templates/linked-templates.md). Článek [[nasazení vnořených Azure Resource Manager šablon pro testovací prostředí](deploy-nested-template-environments.md) vysvětluje, jak používat  `_artifactsLocation` `_artifactsLocationSasToken` TOKENy k vytvoření identifikátoru URI pro šablonu správce prostředků ve stejné složce jako nebo ve vnořené složce hlavní šablony. Další informace o těchto dvou tokenech najdete v části **artefakty nasazení** v tématu [Azure Resource Manager – Průvodce osvědčenými postupy](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Zkušenosti uživatele

## <a name="developer"></a>Vývojář
Vývojáři používají stejný pracovní postup k vytvoření virtuálního počítače pro vytvoření konkrétního prostředí. Vyberou prostředí vs. bitovou kopii počítače a zadají potřebné informace požadované šablonou. Každý vývojář, který má prostředí, umožňuje nasazení změn a vylepšené ladění vnitřních smyček. Prostředí lze kdykoli vytvořit pomocí nejnovější šablony.  Tato funkce umožňuje, aby se prostředí zničila a znovu vytvořila, aby se snížilo prostoje při ručním vytváření systému nebo obnovování při testování chyb.  

### <a name="testing"></a>Testování
Prostředí DevTest Labs umožňují asynchronně nezávislé testování konkrétního kódu a konfigurací. Běžný postup je nastavit prostředí s kódem z jednotlivé žádosti o přijetí změn a spustit jakékoli automatizované testování. Po dokončení automatizovaného testování je možné provést jakékoli Manuální testování v konkrétním prostředí. Tento proces se obvykle provádí jako součást kanálu CI/CD. 

## <a name="management-experience"></a>Prostředí pro správu

### <a name="cost-tracking"></a>Sledování nákladů
Funkce sledování nákladů zahrnuje prostředky Azure v různých prostředích v rámci celkového trendu nákladů. Náklady podle prostředků nerozdělí různé prostředky v rámci prostředí, ale prostředí zobrazí jako samostatné náklady.

### <a name="security"></a>Zabezpečení
Správně nakonfigurované předplatné Azure s DevTest Labs může [omezit přístup k prostředkům Azure jenom prostřednictvím testovacího prostředí](devtest-lab-add-devtest-user.md). V prostředích může vlastník testovacího prostředí povolit uživatelům přístup k prostředkům PaaS se schválenými konfiguracemi bez povolení přístupu k jiným prostředkům Azure. V situaci, kdy uživatelé testovacího prostředí přizpůsobují prostředí, může vlastník testovacího prostředí dovolit přístup přispěvatele. Přístup přispěvatele umožňuje uživateli testovacího prostředí přidat nebo odebrat prostředek Azure jenom v rámci spravované skupiny prostředků. Umožňuje snazší sledování a správu a umožňuje přístup k předplatnému přispěvateli uživatelů.

### <a name="automation"></a>Automation
Automatizace je klíčovou komponentou pro velký rozsah, efektivní ekosystém. Automatizace je nutná pro zpracování správy nebo sledování více prostředí napříč předplatnými a Labs.

### <a name="cicd-pipeline"></a>Kanál CI/CD
Služba PaaS Services v DevTest Labs vám může pomoci vylepšit kanál CI/CD tím, že se zaměřuje na nasazení, kde je testovací prostředí řízeno přístupem.

## <a name="next-steps"></a>Další kroky
Podrobnosti o prostředích najdete v následujících článcích: 

- 
- [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md)
- [Konfigurace a použití veřejných prostředí v Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Vytvoření prostředí pomocí samostatného Service Fabricho clusteru v Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Připojte prostředí k virtuální síti testovacího prostředí v Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Integrace prostředí do kanálů Azure DevOps CI/CD](integrate-environments-devops-pipeline.md)
 





