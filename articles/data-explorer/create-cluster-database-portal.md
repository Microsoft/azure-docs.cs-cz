---
title: 'Rychlý start: Vytvoření clusteru a databáze Azure Průzkumník dat'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster a databázi Azure Data Exploreru a ingestovat (načíst) data.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: cfab883f9b9b063bd51b9fdb7306d45371449180
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406086"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Rychlý start: Vytvoření clusteru a databáze Azure Průzkumník dat

> [!div class="op_single_selector"]
> * [Azure Portal](create-cluster-database-portal.md)
> * [Rozhraní příkazového řádku](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  


Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Pokud chcete použít Azure Průzkumník dat, musíte nejdřív vytvořit cluster a v tomto clusteru vytvořit jednu nebo víc databází. Pak data ingestujte do databáze, abyste na ni mohli spouštět dotazy. V tomto rychlém startu vytvoříte cluster a databázi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Vytvoření clusteru

Vytvořte cluster Azure Průzkumník dat s definovanou sadou výpočetních prostředků a prostředků úložiště ve skupině prostředků Azure.

1. Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

1. Vyhledejte *Azure Data Explorer*.

   ![Hledat prostředky](media/create-cluster-database-portal/search-resources.png)

1. V dolní části obrazovky v části **Azure Data Explorer** vyberte **Vytvořit**.

1. Do podrobností o základním clusteru vyplňte následující informace.

   ![Formulář pro vytvoření clusteru](media/create-cluster-database-portal/create-cluster-form2.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Subscription | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj cluster.|
    | Resource group | Vaše skupina prostředků | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Název clusteru | Jedinečný název clusteru | Zvolte jedinečný název, který identifikuje váš cluster. K názvu clusteru, který zadáte, bude připojen název domény *[oblast].kusto.windows.net*. Název může obsahovat jenom malá písmena a čísla. Musí obsahovat 4 až 22 znaků.
    | Oblast | *Západní USA* nebo *západní USA 2* | Pro tento rychlý Start vyberte *západní USA* nebo *západní USA 2* (Pokud používáte zóny dostupnosti). Pro produkční systém vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | Zóny dostupnosti | *1*, *2*a/nebo *3* | U oblastí, které podporují redundanci, vyberte, ve kterých zónách budou vaše instance umístěny (volitelné). Uzly clusteru se ve výchozím nastavení vytvoří ve stejném datovém centru. Pomocí [zóny dostupnosti Azure](/azure/availability-zones/az-overview) umístěte instance clusteru do různých zón dostupnosti ve stejné oblasti. Výběrem několika zón dostupnosti můžete eliminovat jediný bod selhání a zajistit vysokou dostupnost. 
    | Specifikace výpočetních prostředků | *D13_v2* | Pro tento rychlý start vyberte specifikaci nejnižší ceny. Pro produkční systém vyberte specifikaci, která nejlépe vyhovuje vašim potřebám.
    | | |

1. Vyberte **zkontrolovat + vytvořit** a Prohlédněte si podrobnosti o clusteru a **vytvořte** si cluster zřídit. Zřizování obvykle trvá přibližně 10 minut.

1. Po dokončení nasazení vyberte **Přejít k prostředku**.

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
    | Období udržení | *3650* | Časový rozsah (ve dnech), pro který je zaručeno, že jsou data udržována k dispozici pro dotaz. Časový rozsah se začíná měřit od okamžiku, kdy jsou data ingestována.
    | Doba uložení v mezipaměti | *31* | Časový rozsah (ve dnech), po který se mají často dotazovaná data uchovávat v úložišti SSD nebo RAM místo v případě dlouhodobého uložení.
    | | | |

1. Vyberte **vytvořit** a vytvořte databázi. Vytvoření obvykle trvá méně než minutu. Po dokončení procesu budete zpět na kartě **Přehled** clusteru.

## <a name="run-basic-commands-in-the-database"></a>Spuštění základních příkazů v databázi

Teď, když máte cluster a databázi, můžete spouštět dotazy a příkazy. V databázi ještě nemáte žádná data, i tak ale můžete vidět, jak nástroje fungují.

1. V rámci svého clusteru vyberte **Dotaz**. Vložte příkaz `.show databases` do okna dotazu a pak vyberte **Spustit**.

    ![Příkaz Show databases](media/create-cluster-database-portal/show-databases.png)

    Sada výsledků dotazu obsahuje databázi **TestDatabase**, což je jediná databáze v clusteru.

1. Vložte příkaz `.show tables` do okna dotazu a vyberte **Spustit**.

    Tento příkaz vrátí prázdnou sadu výsledků dotazu, protože zatím nemáte žádné tabulky. Tabulku přidáte v dalším článku v této sérii.

## <a name="stop-and-restart-the-cluster"></a>Zastavení a restartování clusteru

Cluster můžete v závislosti na potřebách firmy zastavit a restartovat.

1. Zastavíte ho tak, že v horní části karty **Přehled** vyberete možnost **Zastavit**.

    Při zastavení clusteru nejsou data k dispozici pro dotazy a nemůže ingestovat nová data.

1. Cluster restartujete tak, že v horní části karty **Přehled** vyberete možnost **Spustit**.

    Po restartování clusteru trvá přibližně 10 minut, než se bude k dispozici (například při jeho původním zřízení). Chvíli pak také trvá načtení dat do aktivní mezipaměti.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete postupovat podle dalších rychlých startů a kurzů, ponechte prostředky, které jste vytvořili. Jinak vyčistěte skupinu prostředků, abyste se vyhnuli nákladům.

1. V Azure Portal vyberte **skupiny prostředků** na zcela vlevo a pak vyberte skupinu prostředků, která obsahuje váš cluster Průzkumník dat.  

1. Pokud chcete odstranit celou skupinu prostředků, vyberte **Odstranit skupinu prostředků** . Pokud používáte existující skupinu prostředků, můžete se rozhodnout jenom odstranit cluster Průzkumník dat.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Ingestování dat z centra událostí do Azure Průzkumník dat](ingest-data-event-hub.md)


