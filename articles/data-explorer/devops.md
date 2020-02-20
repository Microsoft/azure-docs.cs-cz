---
title: Úloha Azure DevOps pro Azure Průzkumník dat
description: V tomto tématu se naučíte vytvořit kanál verze a nasazení.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472039"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Úloha Azure DevOps pro Azure Průzkumník dat

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) poskytuje vývojové nástroje pro spolupráci, jako jsou vysoce výkonné kanály, bezplatná soukromá úložiště Git, konfigurovatelné kanbanové desky a rozsáhlé automatizované a průběžné testování možností. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je funkce Azure DevOps, která umožňuje spravovat CI/CD a nasazovat svůj kód s využitím vysoce výkonných kanálů, které fungují s jakýmkoli jazykem, platformou a cloudem.
[Azure Průzkumník dat – příkazy pro správu](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) jsou úlohy Azure Pipelines, která umožňuje vytvářet kanály vydání a nasazovat změny databáze do databází Azure Průzkumník dat. Je dostupná zdarma v [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Tento dokument popisuje jednoduchý příklad použití úlohy **příkazy Azure Průzkumník dat – Správce** k nasazení změn schématu do vaší databáze. Kompletní kanály CI/CD najdete v [dokumentaci ke službě Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Nastavení clusteru Azure Průzkumník dat:
    * [Cluster a databáze Azure Průzkumník dat](/azure/data-explorer/create-cluster-database-portal)
    * Vytvoření aplikace Azure Active Directory (Azure AD) prostřednictvím [zřizování aplikace Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app)
    * Spravujte přístup k vašemu Aplikace Azure AD v databázi Azure Průzkumník dat tím, že povolíte [správu oprávnění databáze azure Průzkumník dat](/azure/data-explorer/manage-database-permissions).
* Nastavení Azure DevOps:
    * [Zaregistrujte si bezplatnou organizaci](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Vytvoření organizace](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Vytvoření projektu v Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kód pomocí Gitu](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Vytváření složek

V úložišti Git vytvořte následující ukázkové složky (*funkce*, *zásady*, *tabulky*). Zkopírujte soubory [odsud do příslušných](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) složek, jak vidíte níže, a potvrďte změny. K dispozici jsou ukázkové soubory pro provedení následujícího pracovního postupu.

![Vytváření složek](media/devops/create-folders.png)

> [!TIP]
> Při vytváření vlastního pracovního postupu doporučujeme, abyste idempotentníi kódu. Například použijte [. Create-Merge Table](/azure/kusto/management/create-table-command#create-merge-table) místo [. Create Table](/azure/kusto/management/create-table-command)a použijte funkci [. Create-nebo ALTER](/azure/kusto/management/create-alter-function) namísto funkce [. Create](/azure/kusto/management/create-function) .

## <a name="create-a-release-pipeline"></a>Vytvoření kanálu verze

1. Přihlaste se ke svojí [organizaci Azure DevOps](https://dev.azure.com/).
1. V nabídce na levé straně vyberte **kanály** > **vydání** a vyberte **Nový kanál**.

    ![Nový kanál](media/devops/new-pipeline.png)

1. Otevře se okno **Nový kanál vydání** . Na kartě **kanály** v podokně **Vybrat šablonu** vyberte **prázdná úloha**.

     ![Vybrat šablonu](media/devops/select-template.png)

1. Tlačítko pro výběr **fáze** V podokně **fáze** přidejte **název fáze**. Vyberte **Uložit** a kanál uložte.

    ![Pojmenování fáze](media/devops/stage-name.png)

1. Vyberte tlačítko **Přidat artefakt** . V podokně **Přidat artefakt** vyberte úložiště, kde existuje váš kód, vyplňte příslušné informace a klikněte na **Přidat**. Vyberte **Uložit** a kanál uložte.

    ![Přidání artefaktu](media/devops/add-artifact.png)

1. Na kartě **proměnné** vyberte **+ Přidat** a vytvořte proměnnou pro **adresu URL koncového bodu** , která se použije v úloze. Napište **název** a **hodnotu** koncového bodu. Vyberte **Uložit** a kanál uložte. 

    ![Vytvořit proměnnou](media/devops/create-variable.png)

    Pokud chcete najít Endpoint_URL, stránka přehled vašeho **clusteru azure Průzkumník dat** v Azure Portal obsahuje identifikátor URI clusteru Azure Průzkumník dat. Sestavte identifikátor URI v následujícím formátu `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Například https:\//kustodocs.westus.kusto.Windows.NET? DatabaseName = SampleDB

    ![Identifikátor URI clusteru Azure Průzkumník dat](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Vytvoření úloh k nasazení

1. Na kartě **kanál** klikněte na **1 úlohu, 0 úkol** a přidejte úkoly. 

    ![Přidat úlohy](media/devops/add-task.png)

1. V tomto pořadí vytvořte tři úkoly pro nasazení **tabulek**, **funkcí**a **zásad**. 

1. Na kartě **úlohy** vyberte **+** podle **úlohy agenta**. Vyhledejte **Azure Data Explorer**. Na **webu Marketplace**nainstalujte rozšíření **příkazy Azure Průzkumník dat – admin** . Pak vyberte **Přidat** v **příkazu Spustit Azure Průzkumník dat**.

     ![Přidat příkazy správce](media/devops/add-admin-commands.png)

1. Na levé straně klikněte na **příkaz Kusto** a aktualizujte úlohu pomocí následujících informací:
    * **Zobrazovaný název**: název úlohy
    * **Cesta k souboru**: v **úloze tabulky** zadejte */Tables/* . csl, protože soubory pro vytváření tabulek jsou ve složce *tabulky* .
    * **Adresa URL koncového bodu**: zadejte `EndPoint URL`proměnnou vytvořenou v předchozím kroku.
    * Vyberte **použít koncový bod služby** a vyberte **+ Nový**.

    ![Úloha příkazu Update Kusto](media/devops/kusto-command-task.png)

1. V okně **Přidat připojení služby Azure Průzkumník dat** zadejte následující informace:

    |Nastavení  |Navrhovaná hodnota  |
    |---------|---------|
    |**Název připojení**     |    Zadejte název pro identifikaci koncového bodu služby.     |
    |**Adresa URL clusteru**    |    Hodnotu najdete v části Přehled clusteru Azure Průzkumník dat v Azure Portal | 
    |**ID instančního objektu**    |    Zadejte ID aplikace AAD (vytvořenou jako předpoklad).     |
    |**Klíč aplikace instančního objektu**     |    Zadejte klíč aplikace AAD (vytvořený jako předpoklad).    |
    |**ID tenanta AAD**    |      Zadejte svého tenanta AAD (například microsoft.com, contoso.com...).    |

    Zaškrtněte políčko **povolí všem kanálům používat toto připojení** . Vyberte **OK**.

    ![Přidat připojení služby](media/devops/add-service-connection.png)

1. Opakováním kroků 1-5 dalších dvou nasaďte soubory ze složek *Functions* and *Policies* . Vyberte **Save** (Uložit). Na kartě **úlohy** se podívejte na tři vytvořené úlohy: **nasadit tabulky**, **nasadit funkce**a **nasadit zásady**.

    ![Nasadit všechny složky](media/devops/deploy-all-folders.png)

1. Vyberte **+ Release** > **vytvořit vydání** a vytvořte vydání.

    ![Vytvoření vydané verze](media/devops/create-release.png)

1. Na kartě **protokoly** ověřte, zda je stav nasazení úspěšný.

    ![Nasazení bylo úspěšné.](media/devops/deployment-successful.png)

Nyní jste dokončili vytváření kanálu vydání pro nasazení tří úkolů do předprodukčního prostředí.