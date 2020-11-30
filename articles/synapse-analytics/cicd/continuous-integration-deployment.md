---
title: Průběžná integrace a doručování pro pracovní prostor synapse
description: Naučte se používat průběžnou integraci a doručování k nasazení změn v pracovním prostoru z jednoho prostředí (vývoj, testování, produkce) do jiného.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5dbd49312b58dc656e2239e8a0a4acea614023de
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317123"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Průběžná integrace a doručování pro Azure synapse Workspace

## <a name="overview"></a>Přehled

Průběžná integrace (CI) je proces automatizace sestavení a testování kódu pokaždé, když člen týmu potvrdí změny ve správě verzí. Průběžné nasazování (CD) je proces sestavení, testování, konfigurace a nasazení z několika testovacích nebo přípravných prostředí do produkčního prostředí.

Pro pracovní prostor Azure synapse můžete průběžnou integraci a doručování (CI/CD) přesunout všechny entity z jednoho prostředí (vývoj, testování, produkce) do jiného. Pokud chcete svůj pracovní prostor zvýšit na jiný pracovní prostor, existují dvě části: použití [šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview) k vytvoření nebo aktualizaci prostředků pracovního prostoru (fondů a pracovního prostoru). Migrujte artefakty (skripty SQL, Poznámkový blok, definice úlohy Spark, kanály, datové sady, toky dat atd.) pomocí nástrojů CI/CD synapse v Azure DevOps. 

V tomto článku se dozvíte, jak pomocí kanálu pro vydávání Azure automatizovat nasazení pracovního prostoru synapse do více prostředí.

## <a name="prerequisites"></a>Požadavky

-   Pracovní prostor použitý pro vývoj byl nakonfigurován s úložištěm Git v nástroji Studio, viz [Správa zdrojového kódu v synapse studiu](source-control.md).
-   Projekt Azure DevOps se připravil pro běh kanálu vydávání verzí.

## <a name="set-up-a-release-pipelines"></a>Nastavení kanálů vydání

1.  V [Azure DevOps](https://dev.azure.com/)otevřete projekt vytvořený pro vydání verze.

1.  Na levé straně stránky vyberte **kanály** a pak vyberte **vydané verze**.

    ![Výběr kanálů, vydání](media/create-release-1.png)

1.  Vyberte **Nový kanál**, nebo pokud už máte kanály, vyberte **Nový** a pak **Nový kanál verze**.

1.  Vyberte **prázdnou šablonu úlohy** .

    ![Vybrat prázdnou úlohu](media/create-release-select-empty.png)

1.  Do pole **název fáze** zadejte název vašeho prostředí.

1.  Vyberte **Přidat artefakt** a pak vyberte úložiště Git nakonfigurované pro vývoj synapse Studio. Vyberte úložiště Git, které jste použili pro správu šablon ARM fondů a pracovního prostoru. Pokud jako zdroj použijete GitHub, budete muset vytvořit připojení služby pro svůj účet GitHub a úložiště Pull. Další informace o [připojení služby](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints) 

    ![Přidat větev publikování](media/release-creation-github.png)

1.  Vyberte větev šablony ARM pro aktualizace prostředků. U **výchozí verze** vyberte možnost **nejnovější z výchozí větve**.

    ![Přidat šablonu ARM](media/release-creation-arm-branch.png)

1.  Vyberte [větev publikování](source-control.md#configure-publishing-settings) úložiště pro **výchozí větev**. Ve výchozím nastavení je tato větev publikování `workspace_publish` . U **výchozí verze** vyberte možnost **nejnovější z výchozí větve**.

    ![Přidání artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Nastavení úlohy fáze pro vytvoření a aktualizaci prostředku ARM 

Přidejte úlohu nasazení Azure Resource Manager pro vytvoření nebo aktualizaci prostředků, včetně pracovního prostoru a fondů:

1. V zobrazení fáze vyberte možnost **Zobrazit úlohy fáze**.

    ![Zobrazení fáze](media/release-creation-stage-view.png)

1. Vytvoří nový úkol. Vyhledejte **nasazení šablony ARM** a pak vyberte **Přidat**.

1. V úloze nasazení vyberte předplatné, skupinu prostředků a umístění pro cílový pracovní prostor. V případě potřeby zadejte přihlašovací údaje.

1. V seznamu **Akce** vyberte **vytvořit nebo aktualizovat skupinu prostředků**.

1. Vyberte tlačítko se třemi tečkami (**...**) vedle pole **Šablona** . Vyhledat šablonu Azure Resource Manager svého cílového pracovního prostoru

1. Vybrat **...** vedle pole **parametry šablony** pro výběr souboru parametrů.

1. Vybrat **...** vedle pole **parametry šablony přepsání** a zadejte požadované hodnoty parametrů pro cílový pracovní prostor. 

1. Vyberte **přírůstkový** **režim nasazení**.
    
    ![nasazení pracovního prostoru a fondů](media/pools-resource-deploy.png)

1. Volitelné Přidejte **Azure PowerShell** pro přiřazení role pracovní prostor udělení a aktualizace. Pokud k vytvoření pracovního prostoru synapse použijete kanál vydaných verzí, bude objekt služby kanálu přidán jako výchozí správce pracovního prostoru. Můžete spustit PowerShell pro udělení přístupu k pracovnímu prostoru jiným účtům. 
    
    ![udělit oprávnění](media/release-creation-grant-permission.png)

 > [!WARNING]
> V režimu úplného nasazení se **odstraní** prostředky, které jsou ve skupině prostředků, ale nejsou zadané v nové šabloně správce prostředků. Další informace najdete v tématu [režimy nasazení Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes) .

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Nastavení úlohy fáze pro nasazení artefaktů 

Pomocí rozšíření [nasazení pracovního prostoru synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) nasaďte další položky v pracovním prostoru synapse, jako je datová sada, skript SQL, Poznámkový blok, definice úlohy Spark, tok Dataflow, kanál, propojená služba, přihlašovací údaje a IR (Integration Runtime).  

1. Vyhledejte a získejte rozšíření z **Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Získat rozšíření](media/get-extension-from-market.png)

1. Vyberte organizaci pro instalaci rozšíření. 

     ![Instalace rozšíření](media/install-extension.png)

1. Zajistěte, aby měl Princip služby DevOps kanálu služby Azure udělené oprávnění k předplatnému a také byl přiřazen jako správce pracovního prostoru pro cílový pracovní prostor. 

1. Vytvoří nový úkol. Vyhledejte **nasazení v pracovním prostoru synapse** a pak vyberte **Přidat**.

     ![Přidat rozšíření](media/add-extension-task.png)

1.  V úloze zadejte související informace o úložišti Git **workspace_publish** a vyberte skupinu prostředků, oblast, název a cloudové prostředí pro cílový pracovní prostor. Zadejte parametry a hodnoty, pokud potřebujete.

    ![Nasazení pracovního prostoru synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> Ve scénářích CI/CD musí být typ prostředí Integration runtime (IR) v různých prostředích stejný. Například pokud máte v prostředí pro vývoj v místním prostředí IR, stejný IR musí být také typu v místním prostředí v jiných prostředích, jako je například test a produkce. Podobně pokud sdílíte prostředí Integration runtime v několika fázích, je nutné nakonfigurovat prostředí Integration runtime jako propojená místně ve všech prostředích, jako je vývoj, testování a produkce.

## <a name="create-release-for-deployment"></a>Vytvořit vydání pro nasazení 

Po uložení všech změn můžete vybrat **vytvořit vydání** a ručně vytvořit vydání. Informace o automatizaci vytváření vydání najdete v tématu [triggery vydané verze Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/release/triggers) .

   ![Vyberte vytvořit vydání.](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Osvědčené postupy pro CI/CD

Pokud používáte integraci Git s vaším pracovním prostorem synapse a máte kanál CI/CD, který přesouvá vaše změny z vývoje do testu a následně do produkčního prostředí, doporučujeme tyto osvědčené postupy:

-   **Integrace Gitu**. Nakonfigurujte jenom váš pracovní prostor vývojového synapse s integrací Gitu. Změny pracovních prostorů testů a výroby se nasazují prostřednictvím CI/CD a nepotřebují integraci Git.
-   **Příprava fondů před migrací artefaktů**. Pokud máte skript SQL nebo Poznámkový blok připojený ke fondům v pracovním prostoru pro vývoj, očekává se stejný název fondů v různých prostředích. 
-   **Infrastruktura jako kód (IAC)**. Správa infrastruktury (sítí, virtuálních počítačů, nástrojů pro vyrovnávání zatížení a topologie připojení) v popisném modelu používá stejnou verzi jako DevOps tým používá ke zdrojovému kódu. 
-   **Ostatní**. Podívejte se [na osvědčené postupy pro artefakty ADF](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd) .


