---
title: Integrace prostředí do Azure kanály ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak do prostředí Azure DevTest Labs integrovat Azure DevOps kontinuální integrace (CI) a průběžné doručování (CD) kanálů.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: deb5595ac6a8b0d189e5594fda8e4b60480d038c
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357405"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integrace prostředí kanálech Azure DevOps CI/CD
Můžete použít rozšíření Azure DevTest Labs úlohy, která je nainstalovaná ve službě Azure DevOps Services (dříve označované jako Visual Studio Team Services), tak jednoduše integrovat kontinuální integraci (CI) / kanálu průběžného doručování (CD) sestavení a-vydané verze s využitím Azure DevTest Labs. Tato rozšíření usnadňují rychlé nasazení [prostředí](devtest-lab-test-env.md) pro konkrétní testovací úloha a až po dokončení testu ji odstranit. 

Tento článek ukazuje, jak vytvořit a nasadit prostředí, a potom odstranit prostředí a vše v jednom dokončení kanálu. By obvykle provádíte každý z těchto úloh jednotlivě ve vašem vlastním vlastní sestavení, testování a nasazení kanálu. Rozšíření použitá v tomto článku jsou kromě těchto [vytvářet a odstraňovat úkoly virtuálních počítačů DTL –](devtest-lab-integrate-ci-cd-vsts.md):

- Vytvoření prostředí
- Odstranění prostředí

## <a name="before-you-begin"></a>Před zahájením
Než svůj kanál CI/CD můžete integrovat s Azure DevTest Labs, nainstalovat [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) rozšíření z Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Vytvoření a konfigurace testovacího prostředí pro prostředí
Tato část popisuje postup vytvoření a konfigurace testovacího prostředí, ve kterém se nasadí do prostředí Azure do.

1. [Vytvoření testovacího prostředí](devtest-lab-create-lab.md) pokud ho ještě nemáte. 
2. Konfigurace testovacího prostředí a vytvořit prostředí šablonu podle pokynů v tomto článku: [Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru](devtest-lab-create-environment-from-arm.md).
3. V tomto příkladu použijte existující šablony rychlý start Azure [ https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/ ](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/).
4. Kopírovat **201-web-app-redis-cache-sql-database** do složky **ArmTemplate** složky v úložišti nakonfigurovali v kroku 2.

## <a name="create-a-release-definition"></a>Vytvoření definice verze
Vytvořte definici vydané verze, postupujte takto:

1.  Na **verze** karty **sestavení a vydání centra**, vyberte **znaménko plus (+)** tlačítko.
2.  V **definice vydané verze vytvořit** okna, vyberte **prázdný** šablonu a pak vyberte **Další**.
3.  Vyberte **zvolte později**a pak vyberte **vytvořit** k vytvoření nové definice vydané verze pomocí jeden výchozí prostředí a bez propojených artefaktů.
4.  Otevřete místní nabídku v novou definici vydané verze, vyberte **tlačítko se třemi tečkami (...)**  další název prostředí a pak vyberte **nakonfigurovat proměnné**.
5.  V **konfigurovat - prostředí** okna proměnných, které můžete použít v úlohách definic vydání, zadejte následující hodnoty:
1.  Pro **administratorLogin**, zadejte přihlašovací jméno správce SQL.
2.  Pro **administratorLoginPassword**, zadejte heslo pro přihlašovací jméno správce SQL. Pomocí ikony "visacího zámku nezobrazuje" Skrýt a zabezpečené heslo.
3.  Pro **databaseName**, zadejte název databáze SQL.
4.  Tyto proměnné jsou specifické pro ukázková prostředí, různá prostředí může mít různé proměnné.

## <a name="create-an-environment"></a>Vytvoření prostředí
Další fáze nasazení je vytvoření prostředí pro vývoj nebo testování účely.

1. V definici vydané verze vyberte **přidat úkoly**.
2. Na **úlohy** kartu, přidat úkol Azure DevTest Labs vytvořit prostředí. Úkol nakonfigurujte následujícím způsobem:
    1. Pro **předplatné Azure RM**, vyberte připojení **dostupných připojení služby Azure** seznamu, nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](/azure/devops/pipelines/library/service-endpoints).
2. Pro **název testovacího prostředí**, vyberte název instance, kterou jste vytvořili dříve *.
3. Pro **název úložiště**, vyberte úložiště, kde doručený do šablony Resource Manageru (201) *.
4. Pro **název šablony**, vyberte název prostředí, který jste uložili na váš zdrojový kód úložiště *. 
5. **Název testovacího prostředí**, **název úložiště**, a **název šablony** jsou popisný reprezentace ID prostředků Azure. Ručně zadat popisný název způsobit chyby, použijte rozevírací seznamy můžete vybrat informace.
6. Pro **název prostředí**, zadejte název, který jedinečným způsobem identifikovat instanci prostředí v testovacím prostředí.  Musí být jedinečný v rámci testovacího prostředí.
7. **Soubor parametrů** a **parametry**, Povolit vlastní parametry předávané do prostředí. Jeden nebo oba je možné nastavit hodnoty parametrů. V tomto příkladu se použije sekci parametrů. Použijte název proměnné, které jste definovali v prostředí, například: `-administratorLogin “$(administratorLogin)” -administratorLoginPassword “$(administratorLoginPassword)” -databaseName “$(databaseName)” -cacheSKUCapacity 1`
8. Informace v rámci šablony prostředí je možné předat prostřednictvím v části výstupů šablony. Zkontrolujte **vytvořit výstupní proměnné na základě výstupu šablony prostředí** tak data můžete použít další úkoly. `$(Reference name.Output Name)` je vzorek dodržovat. Například pokud byl název odkazu DTL – a název výstupního v šabloně umístění proměnné by `$(DTL.location)`.

## <a name="delete-the-environment"></a>Odstranění prostředí
Závěrečná fáze je odstranit prostředí, které jste nasadili ve vaší instanci Azure DevTest Labs. Obvykle byste odstranit prostředí po spuštění úlohy vývoj nebo testy, které je třeba na nasazených prostředků.

V definici vydané verze vyberte **přidat úkoly**a pak na **nasadit** kartu, přidejte **Azure DevTest Labs odstranit prostředí** úloh. Nakonfigurujte následujícím způsobem:

1. Pokud chcete odstranit virtuální počítač, přečtěte si téma [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Pro **předplatné Azure RM**, vyberte připojení **dostupných připojení služby Azure** seznamu, nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](/azure/devops/pipelines/library/service-endpoints).
    2. Pro **název testovacího prostředí**, vyberte testovací prostředí, které se prostředí nachází.
    3. Pro **název prostředí**, zadejte název prostředí, která se má odebrat.
2. Zadejte název definice vydané verze a pak ho uložte.

## <a name="next-steps"></a>Další postup
Viz následující články: 
- [Vytvoření prostředí více virtuálních počítačů pomocí šablon Resource Manageru](devtest-lab-create-environment-from-arm.md).
- Šablony rychlý start Resource Manageru pro DevTest Labs automatizace z [úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- [Řešení potíží s VSTS stránky](/azure/devops/pipelines/troubleshooting)

