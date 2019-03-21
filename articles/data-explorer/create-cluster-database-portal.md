---
title: 'Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster a databázi Azure Data Exploreru a ingestovat (načíst) data.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 02/21/2019
ms.openlocfilehash: 1aaaba58b4af90891caf98708f7e280f7eea6e70
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285816"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Rychlý start: Vytvoření clusteru Průzkumník dat Azure a databáze

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Použití Průzkumníku dat Azure, nejprve vytvoříte cluster a vytvořit jednu nebo více databází v tomto clusteru. Pak můžete ingestovat data (načíst) do databáze tak, aby u ní můžete spouštět dotazy. V tomto rychlém startu vytvoříte cluster a databázi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Vytvoření clusteru

Vytvoření clusteru Průzkumník dat Azure s definovanou sadou výpočetních a úložných prostředků ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *Azure Data Explorer*.

   ![Prohledávat prostředky](media/create-cluster-database-portal/search-resources.png)

1. V dolní části obrazovky v části **Azure Data Explorer** vyberte **Vytvořit**.

1. Vyplňte údaje o základní cluster s následujícími informacemi.

   ![Formulář pro vytvoření clusteru](media/create-cluster-database-portal/create-cluster-form.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj cluster.|
    | Skupina prostředků | *test-resource-group* | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Název clusteru | Jedinečný název clusteru | Zvolte jedinečný název, který identifikuje váš cluster. Například *mydataexplorercluster*. K názvu clusteru, který zadáte, bude připojen název domény *[oblast].kusto.windows.net*. Název může obsahovat jenom malá písmena a číslice. Musí se skládat ze 3 až 22 znaků.
    | Umístění | *Západní USA* | V tomto rychlém startu vyberte *Západní USA*. Pro produkční systém vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | Specifikace výpočetních prostředků | *D13_v2* | Pro tento rychlý start vyberte specifikaci nejnižší ceny. Pro produkční systém vyberte specifikaci, která nejlépe vyhovuje vašim potřebám.
    | | |

1. Vyberte **zkontrolujte + vytvořit** zobrazíte podrobnosti o clusteru, a **vytvořit** ke zřízení clusteru. Zřizování obvykle trvá asi 10 minut.

1. Po dokončení nasazení vyberte **přejít k prostředku**.

    ![Přejít k prostředku](media/create-cluster-database-portal/notification-resource.png)

## <a name="create-a-database"></a>Vytvoření databáze

Teď jste připraveni na druhý krok procesu: vytvoření databáze.

1. Na kartě **Přehled** vyberte **Vytvořit databázi**.

    ![Krok 2: vytvoření databáze](media/create-cluster-database-portal/database-creation.png)

1. Do formuláře zadejte následující informace.

    ![Formulář vytvoření databáze](media/create-cluster-database-portal/create-database.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Název databáze | *TestDatabase* | Název databáze musí být v rámci clusteru jedinečný.
    | Doba uchovávání | *3650* | Časové období (ve dnech) u kterého je zaručeno, že je udržuje k dispozici pro dotazy. Časový rozsah se začíná měřit od okamžiku, kdy jsou data ingestována.
    | Doba uložení v mezipaměti | *31* | Časový interval (ve dnech), pro které chcete zachovat často načtená data k dispozici v úložišti SSD nebo paměti RAM, nikoli v dlouhodobé úložiště.
    | | | |

1. Vyberte **vytvořit** k vytvoření databáze. Vytvoření obvykle trvá méně než minutu. Po dokončení procesu budete zpět na kartě **Přehled** clusteru.

## <a name="run-basic-commands-in-the-database"></a>Spuštění základních příkazů v databázi

Teď, když máte cluster a databázi, můžete spouštět dotazy a příkazy. V databázi ještě nemáte žádná data, i tak ale můžete vidět, jak nástroje fungují.

1. V rámci svého clusteru vyberte **Dotaz**. Vložte příkaz `.show databases` do okna dotazu vyberte **spustit**.

    ![Příkaz Show databases](media/create-cluster-database-portal/show-databases.png)

    Sada výsledků dotazu obsahuje databázi **TestDatabase**, což je jediná databáze v clusteru.

1. Vložte příkaz `.show tables` do okna dotazu a vyberte **spustit**.

    Tento příkaz vrátí prázdnou sadu výsledků dotazu, protože zatím nemáte žádné tabulky. Tabulku přidáte v dalším článku v této sérii.

## <a name="stop-and-restart-the-cluster"></a>Zastavení a restartování clusteru

Cluster můžete v závislosti na potřebách firmy zastavit a restartovat.

1. Zastavíte ho tak, že v horní části karty **Přehled** vyberete možnost **Zastavit**.

    Při zastavení clusteru nejsou data k dispozici pro dotazy a nemůže ingestovat nová data.

1. Cluster restartujete tak, že v horní části karty **Přehled** vyberete možnost **Spustit**.

    Při restartování clusteru trvá asi 10 minut, než je k dispozici (např. když byla původně zřízena). Chvíli pak také trvá načtení dat do aktivní mezipaměti.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít postupovat podle dalšími rychlými starty a kurzy, zachovat prostředky, které jste vytvořili. V opačném případě odstraňte vaší skupiny prostředků, aby se zabránilo bez nákladů.

1. Na webu Azure Portal, vyberte **skupiny prostředků** úplně vlevo a pak vyberte skupinu prostředků, který obsahuje Průzkumník dat clusteru.  

1. Vyberte **odstranit skupinu prostředků** odstranit celou skupinu prostředků. Pokud používáte některou ze stávajících skupin prostředků, můžete odstranit pouze Průzkumník dat clusteru.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestovat data z centra událostí do Průzkumníku dat Azure](ingest-data-event-hub.md)


