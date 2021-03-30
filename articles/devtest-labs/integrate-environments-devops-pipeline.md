---
title: Integrace prostředí do Azure Pipelines v Azure DevTest Labs
description: Naučte se integrovat Azure DevTest Labs prostředí do kanálů Azure DevOps Continuous Integration (CI) a průběžného doručování (CD).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483019"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrace prostředí do kanálů Azure DevOps CI/CD
Pomocí rozšíření Azure DevTest Labs úlohy, které je nainstalováno v Azure DevOps Services (dříve označované jako Visual Studio Team Services), můžete snadno integrovat kanál pro vytváření sestav průběžné integrace (CI)/průběžné doručování (CD-Release) pomocí Azure DevTest Labs. Tato rozšíření usnadňují rychlé nasazení [prostředí](devtest-lab-test-env.md) pro konkrétní testovací úlohu a jejich odstranění po dokončení testu. 

Tento článek ukazuje, jak vytvořit a nasadit prostředí a pak odstranit prostředí, a to vše v jednom kompletním kanálu. Každý z těchto úloh byste obvykle prováděli jednotlivě ve vlastním vlastním kanálu sestavení-test-nasazení. K těmto [úlohám virtuálního počítače pro vytváření a odstraňování ODložených](devtest-lab-integrate-ci-cd.md)souborů se používají rozšíření použitá v tomto článku:

- Vytvoření prostředí
- Odstranění prostředí

## <a name="before-you-begin"></a>Než začnete
Než budete moci integrovat kanál CI/CD s Azure DevTest Labs, nainstalujte rozšíření [Azure DevTest Labs úlohy](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) z Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Vytvoření a konfigurace testovacího prostředí pro prostředí
V této části se dozvíte, jak vytvořit a nakonfigurovat testovací prostředí, do kterého se prostředí Azure nasadí.

1. [Vytvořte testovací prostředí](devtest-lab-create-lab.md) , pokud ho ještě nemáte. 
2. Nakonfigurujte testovací prostředí a vytvořte šablonu prostředí podle pokynů uvedených v tomto článku: [vytvoření prostředí s více virtuálními počítači a PaaS prostředky pomocí šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. V tomto příkladu použijte existující šablonu Azure pro rychlý Start [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Zkopírujte do složky **ArmTemplate** v úložišti nakonfigurovaném v kroku 2 složku **201-Web-App-Redis-Cache-SQL**

## <a name="create-a-release-definition"></a>Vytvoření definice verze
Chcete-li vytvořit definici vydané verze, postupujte následovně:

1.  Na kartě **vydání** centra vydaných **verzí Build &** vyberte tlačítko **znaménko plus (+)** .
2.  V okně **vytvořit definici vydané verze** vyberte **prázdnou** šablonu a pak vyberte **Další**.
3.  Vyberte **zvolit později** a pak vyberte **vytvořit** a vytvořte novou definici vydané verze s jedním výchozím prostředím a bez propojených artefaktů.
4.  Pokud chcete otevřít místní nabídku, v nové definici vydané verze vyberte **tři tečky (...)** vedle názvu prostředí a pak vyberte **Konfigurovat proměnné**.
5.  V okně **Konfigurace prostředí** pro proměnné, které používáte v úlohách definice vydaných verzí, zadejte následující hodnoty:
1.  Pro **administratorLogin** zadejte přihlašovací jméno správce SQL.
2.  Pro **administratorLoginPassword** zadejte heslo, které chcete použít pro přihlášení správce SQL. Pro skrytí a zabezpečení hesla použijte ikonu "visacího zámku nezobrazuje".
3.  Pro **DatabaseName** zadejte název SQL Database.
4.  Tyto proměnné jsou specifické pro ukázková prostředí, různá prostředí mohou mít různé proměnné.

## <a name="create-an-environment"></a>Vytvoření prostředí
Další fází nasazení je vytvořit prostředí, které se bude používat pro účely vývoje nebo testování.

1. V části definice verze vyberte **Přidat úlohy**.
2. Na kartě **úlohy** přidejte Azure DevTest Labs úlohy vytvořit prostředí. Nakonfigurujte úlohu následujícím způsobem:
    1. U **předplatného Azure RM** vyberte připojení v seznamu **dostupné připojení služby Azure** nebo vytvořte další omezené oprávnění k vašemu předplatnému Azure. Další informace najdete v tématu [Azure Resource Manager koncového bodu služby](/azure/devops/pipelines/library/service-endpoints).
2. V poli **název testovacího prostředí** vyberte název instance, kterou jste vytvořili dříve *.
3. V poli **název úložiště** vyberte úložiště, kde byla šablona Správce prostředků (201) vložená do *.
4. Jako **název šablony** vyberte název prostředí, které jste uložili do úložiště zdrojového kódu *. 
5. Název **testovacího prostředí**, **název úložiště** a **název šablony** jsou popisné reprezentace ID prostředků Azure. Ruční zadání popisného názvu způsobí selhání, a to pomocí rozevíracích seznamů pro výběr informací.
6. Do pole **Název prostředí** zadejte název pro jedinečnou identifikaci instance prostředí v testovacím prostředí.  Musí být jedinečný v rámci testovacího prostředí.
7. **Soubor parametrů** a **parametry** umožňují předat vlastní parametry do prostředí. K nastavení hodnot parametrů lze použít buď nebo obojí. V tomto příkladu se použije oddíl Parameters. Použijte názvy proměnných, které jste definovali v prostředí, například: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Informace v šabloně prostředí lze předat prostřednictvím části Output v šabloně. Ověřte **Vytvoření výstupních proměnných na základě výstupu šablony prostředí** , aby ostatní úlohy mohly data používat. `$(Reference name.Output Name)` je vzor, který se má sledovat. Například pokud je referenční název odkládací složky a název výstupu v šabloně byl umístění, proměnná by byla `$(DTL.location)` .

## <a name="delete-the-environment"></a>Odstranění prostředí
Poslední fází je odstranit prostředí, které jste nasadili v instanci Azure DevTest Labs. Prostředí byste obvykle odstranili po spuštění úloh vývoje nebo spuštění testů, které potřebujete na nasazených prostředcích.

V části definice verze vyberte **Přidat úlohy** a potom na kartě **nasadit** přidejte úlohu **Azure DevTest Labs odstranit prostředí** . Nakonfigurujte ho následujícím způsobem:

1. Pokud chcete virtuální počítač odstranit, přečtěte si téma [Azure DevTest Labs úlohy](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. U **předplatného Azure RM** vyberte připojení v seznamu **dostupné připojení služby Azure** nebo vytvořte další omezené oprávnění k vašemu předplatnému Azure. Další informace najdete v tématu [Azure Resource Manager koncového bodu služby](/azure/devops/pipelines/library/service-endpoints).
    2. V poli **název testovacího** prostředí vyberte testovací prostředí, ve kterém prostředí existuje.
    3. Do pole **Název prostředí** zadejte název prostředí, které se má odebrat.
2. Zadejte název definice vydané verze a pak ji uložte.

## <a name="next-steps"></a>Další kroky
Viz následující články: 
- [Vytváření prostředí s více virtuálními počítači pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
- Šablony pro rychlý Start Správce prostředků pro automatizaci DevTest Labs z [úložiště GitHub DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Stránka Poradce při potížích s VSTS](/azure/devops/pipelines/troubleshooting)

