---
title: Úloha Azure DevOps pro Azure Data Explorer
description: V tomto tématu se naučíte vytvořit kanál vydání a nasadit
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472039"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Úloha Azure DevOps pro Průzkumníka dat Azure

[Služby Azure DevOps services](https://azure.microsoft.com/services/devops/) poskytují nástroje pro spolupráci na vývoji, jako jsou vysoce výkonné kanály, bezplatné soukromé úložiště Git, konfigurovatelné kanbanové desky a rozsáhlé možnosti automatizovaného a průběžného testování. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) je funkce Azure DevOps, která umožňuje spravovat CI/CD k nasazení kódu s vysoce výkonnými kanály, které fungují s libovolným jazykem, platformou a cloudem.
[Azure Data Explorer – příkazy pro správu](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) je úloha Azure Pipelines, která umožňuje vytvářet kanály verzí a nasazovat změny databáze do databází Průzkumníka dat Azure. Je k dispozici zdarma na [webu Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Tento dokument popisuje jednoduchý příklad použití úlohy **Azure Data Explorer – příkazy pro správu** k nasazení změn schématu do databáze. Úplné kanály CI/CD najdete v [dokumentaci k Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Nastavení clusteru Průzkumníka dat Azure:
    * [Cluster a databáze Průzkumníka dat Azure](/azure/data-explorer/create-cluster-database-portal)
    * Vytvořte aplikaci Azure Active Directory (Azure AD) [zřízením aplikace Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Udělte přístup k aplikaci Azure AD v databázi Azure Data [Exploreru pomocí oprávnění ke správě databáze Azure Data Explorer](/azure/data-explorer/manage-database-permissions).
* Nastavení Azure DevOps:
    * [Zaregistrujte se do bezplatné organizace](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Vytvoření organizace](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Vytvoření projektu v Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kód s Gitem](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Vytváření složek

Vytvořte v úložišti Git následující ukázkové složky *(Funkce*, *Zásady*, *Tabulky).* Zkopírujte soubory [odtud](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) do příslušných složek, jak je vidět níže a potvrďte změny. Ukázkové soubory jsou k dispozici ke spuštění následujícího pracovního postupu.

![Vytváření složek](media/devops/create-folders.png)

> [!TIP]
> Při vytváření vlastního pracovního postupu doporučujeme, aby váš kód idempotentpotentní. Místo [tabulky .create-merge](/azure/kusto/management/create-table-command)použijte například tabulku [.create-merge](/azure/kusto/management/create-table-command#create-merge-table) a místo funkce [.create.create.create](/azure/kusto/management/create-function) table a use [.create-or-alter](/azure/kusto/management/create-alter-function) function instead of .create function .

## <a name="create-a-release-pipeline"></a>Vytvoření kanálu verze

1. Přihlaste se ke své [organizaci Azure DevOps](https://dev.azure.com/).
1. Vyberte **Potrubí** > **uvolnění** z levé nabídky a vyberte **Nový kanál**.

    ![Nový kanál](media/devops/new-pipeline.png)

1. Otevře se okno **kanálu Nové vydání.** Na kartě **Potrubí** vyberte v **podokně předlohy** možnost **Prázdná úloha**.

     ![Výběr šablony](media/devops/select-template.png)

1. Vyberte **tlačítko Vymezená plocha.** V podokně **Vymezená plocha** přidejte **název vymezené plochy**. Chcete-li kanál uložit, vyberte **uložit.**

    ![Pojmenujte fázi](media/devops/stage-name.png)

1. Vyberte **tlačítko Přidat artefakt.** V podokně **Přidat artefakt** vyberte úložiště, kde kód existuje, vyplňte příslušné informace a klepněte na tlačítko **Přidat**. Chcete-li kanál uložit, vyberte **uložit.**

    ![Přidání artefaktu](media/devops/add-artifact.png)

1. Na kartě **Proměnné** vyberte **+ Přidat** a vytvořte proměnnou pro adresu **URL koncového bodu,** která bude použita v úloze. Napište **Název** a **Hodnota** koncového bodu. Chcete-li kanál uložit, vyberte **uložit.** 

    ![Vytvořit proměnnou](media/devops/create-variable.png)

    Pokud chcete najít Endpoint_URL, stránka s přehledem **clusteru Azure Data Explorer** na webu Azure Portal obsahuje identifikátor URI clusteru Azure Data Explorer. Vytvořte identifikátor URI `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`v následujícím formátu .  Například https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![IDENTIFIKÁTOR URI clusteru Průzkumníka dat Azure](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Vytvoření úloh k nasazení

1. Na kartě **Potrubí** klikněte na **1 úlohu, 0 úkolů** pro přidání úkolů. 

    ![Přidání úkolů](media/devops/add-task.png)

1. Vytvořte tři úkoly pro nasazení **tabulek**, **funkcí**a **zásad**v tomto pořadí. 

1. Na kartě **Úkoly** vyberte **+** **podle úlohy agenta**. Vyhledejte **Azure Data Explorer**. Na **Marketplace**nainstalujte rozšíření **Azure Data Explorer – Příkazy pro správu.** Potom vyberte **Přidat** v **příkazu Spustit Průzkumník dat Azure**.

     ![Přidání příkazů správce](media/devops/add-admin-commands.png)

1. Klikněte na **Kusto Command** na levé straně a aktualizujte úkol s následujícími informacemi:
    * **Zobrazovaný název**: Název úkolu
    * **Cesta k souboru**: V **úloze Tabulky** zadejte */Tables/*.csl, protože soubory pro vytváření tabulek jsou ve složce *Tabulka.*
    * **Adresa URL koncového bodu**: zadejte proměnnou vytvořenou `EndPoint URL`v předchozím kroku.
    * Vyberte **Použít koncový bod služby** a vyberte **+ Nový**.

    ![Aktualizovat úlohu příkazu Kusto](media/devops/kusto-command-task.png)

1. V okně připojení **služby Přidat službu Azure Data Explorer** dokončete následující informace:

    |Nastavení  |Navrhovaná hodnota  |
    |---------|---------|
    |**Název připojení**     |    Zadejte název pro identifikaci tohoto koncového bodu služby.     |
    |**Adresa URL clusteru**    |    Hodnotu najdete v části přehledu clusteru Azure Data Explorer na webu Azure Portal. | 
    |**Id instančního objektu**    |    Zadejte ID aplikace AAD (vytvořeno jako předpoklad)     |
    |**Klíč aplikace instančního objektu**     |    Zadejte klíč aplikace AAD (vytvořený jako předpoklad)    |
    |**Id klienta AAD**    |      Zadejte svého klienta AAD (například microsoft.com, contoso.com...)    |

    Zaškrtněte políčko **Povolit všem kanálům použití tohoto připojení.** Vyberte **OK**.

    ![Přidání připojení služby](media/devops/add-service-connection.png)

1. Opakujte kroky 1-5 další dvakrát nasadit soubory ze složek *Funkce* a *zásady.* Vyberte **Uložit**. Na kartě **Úkoly** se podívejte na tři vytvořené úkoly: **Nasazení tabulek**, **Nasazení funkcí**a Nasazení **zásad**.

    ![Nasazení všech složek](media/devops/deploy-all-folders.png)

1. Chcete-li vytvořit verzi, vyberte **možnost + Uvolnit** > **vydání.**

    ![Vytvoření vydání](media/devops/create-release.png)

1. Na kartě **Protokoly** zkontrolujte, zda je stav nasazení úspěšný.

    ![Nasazení je úspěšné.](media/devops/deployment-successful.png)

Nyní jste dokončili vytvoření kanálu vydání pro nasazení tří úkolů do předprodukčního.