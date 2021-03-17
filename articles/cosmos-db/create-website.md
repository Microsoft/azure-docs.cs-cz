---
title: Nasazení webové aplikace s Azure Cosmos DB šablonou
description: Naučte se, jak nasadit účet Azure Cosmos, Azure App Service Web Apps a ukázkovou webovou aplikaci pomocí šablony Azure Resource Manager.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333575"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Nasazení Azure Cosmos DB a Azure App Service pomocí webové aplikace z GitHubu pomocí šablony Azure Resource Manager
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V tomto kurzu se dozvíte, jak provést žádné dotykové nasazení webové aplikace, která se připojuje k Azure Cosmos DB při prvním spuštění bez nutnosti vyjímat a vkládat jakékoli informace o připojení z Azure Cosmos DB `appsettings.json` nebo do nastavení aplikace App Services Azure v Azure Portal. Všechny tyto akce jsou provedeny pomocí Azure Resource Manager šablony v rámci jedné operace. V tomto příkladu nasadíme [ukázku Azure Cosmos DB TODO](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) z [kurzu webové aplikace](sql-api-dotnet-application.md).

Správce prostředků šablony jsou poměrně flexibilní a umožňují vytvářet složitá nasazení napříč všemi službami v Azure. To zahrnuje pokročilé úlohy, jako je například nasazení aplikací z GitHubu a vkládání informací o připojení do nastavení aplikace Azure App Service v Azure Portal. V tomto kurzu se dozvíte, jak provést následující akce pomocí jedné šablony Správce prostředků.

* Nasaďte účet Azure Cosmos.
* Nasazení plánu hostování Azure App Service.
* Nasaďte Azure App Service.
* Pomocí účtu Azure Cosmos založit koncový bod a klíče do nastavení aplikace App Service v Azure Portal.
* Nasaďte webovou aplikaci z úložiště GitHub do App Service.

Výsledné nasazení má plně funkční webovou aplikaci, která se může připojit k Azure Cosmos DB bez nutnosti vyjímat a vkládat adresu URL koncového bodu Azure Cosmos DB nebo ověřovací klíče z Azure Portal.

## <a name="prerequisites"></a>Předpoklady

> [!TIP]
> I když tento kurz nepředpokládá předchozí zkušenosti s Azure Resource Managermi šablonami nebo JSON, měli byste upravit odkazované šablony nebo možnosti nasazení, a proto se vyžaduje znalost každé z těchto oblastí.

## <a name="step-1-deploy-the-template"></a>Krok 1: nasazení šablony

Nejdřív kliknutím na tlačítko **nasadit do Azure** níže otevřete Azure Portal a vytvořte vlastní nasazení. Šablonu Azure Resource Management můžete zobrazit také v [galerii šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) .

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

V Azure Portal vyberte předplatné, které chcete nasadit, a vyberte nebo vytvořte novou skupinu prostředků. Pak vyplňte následující hodnoty.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Snímek obrazovky uživatelského rozhraní nasazení šablony":::

* **Region** – to je vyžadováno správce prostředků. Zadejte stejnou oblast, kterou používá parametr Location, kde se nacházejí vaše prostředky.
* **Název aplikace** – tento název se používá u všech prostředků tohoto nasazení. Ujistěte se, že jste zvolili jedinečný název, aby nedošlo ke konfliktu se stávajícími účty Azure Cosmos DB a App Service.
* **Location (umístění** ) – oblast, ve které jsou vaše prostředky nasazeny.
* **App Service plánování úrovně** plánu App Service cenové úrovně plánu.
* **App Service plánování instancí** – počet pracovních procesů pro plán služby App Service.
* **Adresa URL úložiště** – úložiště webové aplikace na GitHubu.
* **Větev** – větev pro úložiště GitHub.
* **Název databáze** – název databáze Azure Cosmos.
* **Název kontejneru** – název kontejneru Azure Cosmos.

Po vyplnění hodnot kliknutím na tlačítko **vytvořit** spusťte nasazení. Dokončení tohoto kroku by mělo trvat 5 až 10 minut.

> [!TIP]
> Šablona neověřuje, že název Azure App Service a název účtu Azure Cosmos, který je zadaný v šabloně, jsou platné a dostupné. Důrazně doporučujeme ověřit dostupnost názvů, které plánujete před odesláním nasazení.


## <a name="step-2-explore-the-resources"></a>Krok 2: Prozkoumejte prostředky

### <a name="view-the-deployed-resources"></a>Zobrazit nasazené prostředky

Až šablona nasadí prostředky, můžete si ji teď zobrazit ve své skupině prostředků.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Skupina prostředků":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Zobrazit Cosmos DB koncový bod a klíče

Pak otevřete účet Azure Cosmos na portálu. Na následujícím snímku obrazovky vidíte koncový bod a klíče pro účet Azure Cosmos.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos klíče":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Zobrazit Azure Cosmos DB klíče v nastavení aplikace

Potom přejděte do Azure App Service ve skupině prostředků. Kliknutím na kartu Konfigurace zobrazíte nastavení aplikace pro App Service. Nastavení aplikace obsahuje účet Cosmos DB a hodnoty primárního klíče, které jsou nezbytné pro připojení k Cosmos DB, a také názvy databází a kontejnerů, které byly předány z nasazení šablony.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Nastavení aplikace":::

### <a name="view-web-app-in-deployment-center"></a>Zobrazit webovou aplikaci v centru nasazení

Dál přejdete do centra nasazení App Service. Tady uvidíte body úložiště v úložišti GitHubu, které jste předali do šablony. Následující stav také indikuje úspěch (aktivní), což znamená, že aplikace byla úspěšně nasazena a spuštěna.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Deployment Center":::

### <a name="run-the-web-application"></a>Spuštění webové aplikace

Kliknutím na **Procházet** v horní části centra nasazení otevřete webovou aplikaci. Webová aplikace se otevře na domovské obrazovce. Klikněte na **vytvořit nový** a do polí zadejte nějaká data a klikněte na Uložit. Výsledná obrazovka zobrazuje data uložená v Cosmos DB.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Domovská obrazovka":::

## <a name="step-3-how-does-it-work"></a>Krok 3: jak to funguje

Existují tři prvky potřebné k tomu, aby fungovaly.

### <a name="reading-app-settings-at-runtime"></a>Čtení nastavení aplikace za běhu

Nejprve aplikace potřebuje požádat o koncový bod Cosmos DB a klíč ve `Startup` třídě ve webové aplikaci ASP.NET MVC. [Ukázka Cosmos DB to udělat](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) může běžet místně, kde můžete zadat informace o připojení do appsettings.jsna. Při nasazení se ale tento soubor nasadí s aplikací. Pokud tyto řádky červeného přístupu k nastavením z appsettings.js, pokusí se z nastavení aplikace v Azure App Service.

:::image type="content" source="./media/create-website/startup.png" alt-text="Snímek obrazovky ukazuje metodu s několika řetězcovými proměnnými, které jsou označeny červeně, včetně databaseName, ContainerName, Account a Key.":::

### <a name="using-special-azure-resource-management-functions"></a>Používání speciálních funkcí správy prostředků Azure

Aby byly tyto hodnoty k dispozici pro aplikaci při nasazení, může šablona Azure Resource Manager požádat o tyto hodnoty z účtu Cosmos DB pomocí speciálních funkcí správy prostředků Azure, včetně [odkazů](../azure-resource-manager/templates/template-functions-resource.md#reference) a [klíče listkey](../azure-resource-manager/templates/template-functions-resource.md#listkeys) , které přeplňují hodnoty z účtu Cosmos DB a vkládat je do hodnot nastavení aplikace s názvy klíčů, které odpovídají tomu, co se používá v aplikaci výše ve formátu {section: Key}. Například, `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Klíče šablony":::

### <a name="deploying-web-apps-from-github"></a>Nasazení webových aplikací z GitHubu

Nakonec musíme nasadit webovou aplikaci z GitHubu do App Service. To se provádí pomocí JSON níže. Jako typ a název tohoto prostředku se musí zadat pozor dvě věci. `"type": "sourcecontrols"` `"name": "web"` Hodnoty vlastností a jsou pevně kódované a neměly by být změněny.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Nasazení z GitHubu":::

## <a name="next-steps"></a>Další kroky

Blahopřejeme vám. Nasadili jste Azure Cosmos DB, Azure App Service a ukázkovou webovou aplikaci, která má automaticky informace o připojení potřebné pro připojení k Cosmos DB, vše v jedné operaci a bez nutnosti vyjímat a vkládat citlivé informace. Když použijete tuto šablonu jako výchozí bod, můžete ji upravit tak, aby se stejně tak nasadila vaše vlastní webové aplikace.

* Pro šablonu Azure Resource Manager pro tuto ukázku přejít na [galerii šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp)
* Zdrojový kód pro ukázkovou aplikaci přejde na [Cosmos DB a provede aplikaci na GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
