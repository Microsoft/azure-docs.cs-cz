---
title: Úloha Azure DevOps pro Průzkumník dat Azure
description: V tomto tématu zjistíte, jak vytvořit kanál pro vydávání verzí a nasazení
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388997"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Úloha Azure DevOps pro Průzkumník dat Azure

[Služby Azure DevOps](https://azure.microsoft.com/services/devops/) poskytuje vývojové nástroje pro spolupráci jako vysoce výkonných kanálů, bezplatná privátní úložiště Git, konfigurovatelné karty kanban a rozsáhlé automatizované a průběžné testování možnosti. [Kanály Azure](https://azure.microsoft.com/services/devops/pipelines/) je Azure DevOps schopností, který vám umožní spravovat CI/CD a nasaďte svůj kód pomocí vysoce výkonných kanálů, které pracují s libovolný jazyk, platformu a cloudu.
[Průzkumník služby Azure Data - správce příkazů](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) je úloha kanály Azure, která umožňuje vytvářet kanály pro vydávání a nasazovat vaše databáze se změní na vašich databází Průzkumník dat Azure. Je k dispozici zdarma [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Tento dokument popisuje jednoduchý příklad týkající se použití **Průzkumník dat Azure – správce příkazů** úloh k nasazení schématu se změní na vaši databázi. Kompletní kanálů CI/CD, najdete v tématu [dokumentace ke službě Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Nastavení clusteru Průzkumník dat Azure:
    * [Clusteru Průzkumník dat Azure a databáze](/azure/data-explorer/create-cluster-database-portal)
    * Vytvoření aplikace Azure Active Directory (Azure AD) pomocí [zřizování aplikaci Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Udělení přístupu k aplikaci Azure AD ve vaší databázi Průzkumník dat Azure pomocí [Správa oprávnění k databázi Průzkumníka služby Azure Data](/azure/data-explorer/manage-database-permissions).
* Instalace nástroje Azure DevOps:
    * [Zaregistrujte si bezplatnou organizace](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Vytvořit organizaci](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Vytvoření projektu v Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kód prostřednictvím Gitu](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Vytváření složek

Vytvořte následující ukázka složky (*funkce*, *zásady*, *tabulky*) v úložišti Git. Kopírování souborů z [tady](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) do příslušných složek jako zobrazené níže a potvrďte změny. Ukázkové soubory jsou k dispozici ke spuštění následujícího pracovního postupu.

![Vytváření složek](media/devops/create-folders.png)

> [!TIP]
> Při vytváření vlastních pracovních postupů, doporučujeme nastavit váš kód idempotentní. Například použít [.create sloučení tabulky](/azure/kusto/management/tables#create-merge-tables) místo [.create tabulky](/azure/kusto/management/tables#create-table)a použijte [.create nebo alter](/azure/kusto/management/functions#create-or-alter-function) místo funkce [.create](/azure/kusto/management/functions#create-function) funkce.

## <a name="create-a-release-pipeline"></a>Vytvoření kanálu verze

1. Přihlaste se k vaší [Azure DevOps organizace](https://dev.azure.com/).
1. Vyberte **kanály** > **verze** z nabídky na levé straně a vyberte **nový kanál**.

    ![Nový kanál](media/devops/new-pipeline.png)

1. **Nový kanál verze** otevře se okno. V **kanály** kartě **vyberte šablonu** podokně, vyberte **prázdný úlohy**.

     ![Vybrat šablonu](media/devops/select-template.png)

1. Vyberte **fáze** tlačítko. V **fáze** podokno, přidejte **název fáze**. Vyberte **Uložit** uložte svůj kanál.

    ![Název fáze](media/devops/stage-name.png)

1. Vyberte **přidání artefaktu** tlačítko. V **přidání artefaktu** podokně, vyberte úložiště, které se nachází váš kód, zadejte příslušné informace a klikněte na tlačítko **přidat**. Vyberte **Uložit** uložte svůj kanál.

    ![Přidání artefaktu](media/devops/add-artifact.png)

1. V **proměnné** kartu, vyberte možnost **+ přidat** Vytvořte proměnnou pro **adresu URL koncového bodu** , který se použije v úloze. Zápis **název** a **hodnotu** koncového bodu. Vyberte **Uložit** uložte svůj kanál. 

    ![Vytvořit proměnnou](media/devops/create-variable.png)

    Najít Endpoint_URL, na stránku přehled vaší **Explorer Cluster se službou Azure Data** ve službě Azure portal obsahuje URI clusteru Průzkumník dat Azure. Vytvořit identifikátor URI v následujícím formátu `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Například https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![URI clusteru Azure Průzkumník dat](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Vytvoření úkolů pro nasazení

1. V **kanálu** klikněte na tlačítko na **1 úloha, úloha 0** přidat úkoly. 

    ![Přidat úlohy](media/devops/add-task.png)

1. Vytvořte tři úkoly nasazení **tabulky**, **funkce**, a **zásady**, v uvedeném pořadí. 

1. V **úlohy** kartu, vyberte možnost **+** podle **úlohu agenta**. Vyhledejte **Azure Data Explorer**. V **Marketplace**, nainstalujte **Průzkumník dat Azure – správce příkazů** rozšíření. Vyberte **přidat** v **spustit příkaz Průzkumník dat Azure**.

     ![Přidání příkazů pro správu](media/devops/add-admin-commands.png)

1. Klikněte na **Kusto příkaz** na levé straně a aktualizace úloh s následujícími informacemi:
    * **Zobrazovaný název**: Název úkolu
    * **Cesta k souboru**: V **tabulky** úkolů, zadejte */Tables/* .csl, protože soubory vytvoření tabulky jsou v *tabulky* složky.
    * **Adresa URL koncového bodu**: Zadejte `EndPoint URL`proměnné vytvořené v předchozím kroku.
    * Vyberte **koncový bod služby používá** a vyberte **+ nová**.

    ![Úloha aktualizace Kusto příkaz](media/devops/kusto-command-task.png)

1. Zadejte následující údaje **připojení služby přidat Průzkumník dat Azure** okno:

    |Nastavení  |Navrhovaná hodnota  |
    |---------|---------|
    |**Název připojení**     |    Zadejte název pro identifikaci tohoto koncového bodu služby     |
    |**Adresa Url clusteru**    |    Hodnotu lze nalézt v oddílu přehled vašeho clusteru Průzkumník dat Azure na webu Azure Portal | 
    |**Id instančního objektu**    |    Zadejte ID aplikace AAD (vytvořen jako předpoklad)     |
    |**Klíč instančního objektu aplikace**     |    Zadejte klíč aplikace AAD (vytvořen jako předpoklad)    |
    |**Id tenanta AAD**    |      Zadejte tenanta služby AAD (např. microsoft.com, contoso.com...)    |

    Vyberte **povolit všechny kanály, abyste mohli používat toto připojení** zaškrtávací políčko. Vyberte **OK**.

    ![Přidat připojení služby](media/devops/add-service-connection.png)

1. Opakujte kroky 1 až 5 jiného dvakrát k nasazení souborů z *funkce* a *zásady* složek. Vyberte **Uložit**. V **úlohy** kartu, najdete v článku tři úkoly vytvořené: **Nasazení tabulky**, **nasazení služby Functions**, a **nasazení zásad**.

    ![Nasazení všech složek](media/devops/deploy-all-folders.png)

1. Vyberte **+ vydání** > **vytvořit vydání** vytvořit vydanou verzi.

    ![Vytvoření vydané verze](media/devops/create-release.png)

1. V **protokoly** kartu, zkontrolujte stav nasazení je úspěšné.

    ![Je nasazení úspěšné](media/devops/deployment-successful.png)

Teď jste dokončili zřízení kanál pro vydávání verzí pro nasazení tři úkoly na předprodukční režim.