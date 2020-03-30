---
title: 'Úvodní příručka: Vytvoření clusteru Průzkumníka dat Azure & DB'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster a databázi Azure Data Exploreru a ingestovat (načíst) data.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/22/2019
ms.openlocfilehash: ed0c570449a0c21e9eace1273228539db7c208da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80238643"
---
# <a name="quickstart-create-an-azure-data-explorer-cluster-and-database"></a>Rychlý start: Vytvoření clusteru a databáze Azure Data Exploreru

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Cli](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Arm šablona](create-cluster-database-resource-manager.md)


Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Pokud chcete použít Azure Data Explorer, musíte nejdříve vytvořit cluster a v něm vytvořit jednu nebo více databází. Do databáze potom ingestujete (načtete) data, abyste se na ně mohli dotazovat spouštěním dotazů. V tomto rychlém startu vytvoříte cluster a databázi.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

## <a name="create-a-cluster"></a>Vytvoření clusteru

Vytvořte cluster Průzkumníka dat Azure s definovanou sadou výpočetních prostředků a prostředků úložiště ve skupině prostředků Azure.

1. V levém horním rohu portálu vyberte tlačítko **+ Vytvořit zdroj.**

1. Vyhledejte *Azure Data Explorer*.

   ![Prohledávat prostředky](media/create-cluster-database-portal/search-resources.png)

1. V dolní části obrazovky v části **Azure Data Explorer** vyberte **Vytvořit**.

1. Vyplňte základní podrobnosti o clusteru následujícími informacemi.

   ![Formulář pro vytvoření clusteru](media/create-cluster-database-portal/create-cluster-form2.png)

    **Nastavení** | **Navrhovaná hodnota** | **Popis pole**
    |---|---|---|
    | Předplatné | Vaše předplatné | Vyberte předplatné Azure, které chcete použít pro svůj cluster.|
    | Skupina prostředků | Vaše skupina prostředků | Použijte existující skupinu prostředků nebo vytvořte novou skupinu prostředků. |
    | Název clusteru | Jedinečný název clusteru | Zvolte jedinečný název, který identifikuje váš cluster. K názvu clusteru, který zadáte, bude připojen název domény *[oblast].kusto.windows.net*. Název může obsahovat jenom malá písmena a číslice. Musí obsahovat 4 až 22 znaků.
    | Region (Oblast) | *Západní USA* nebo *Západní USA 2* | Tento rychlý start vyberte *možnost Usa* nebo ZÁPADNÍ *USA 2* (pokud používáte zóny dostupnosti). Pro produkční systém vyberte oblast, která nejlépe vyhovuje vašim potřebám.
    | Zóny dostupnosti | *1,* *2*a/nebo *3* | Umístěte instance clusteru do různých zón dostupnosti ve stejné oblasti (volitelné). [Zóny dostupnosti Azure](/azure/availability-zones/az-overview) jsou jedinečná fyzická umístění ve stejné oblasti Azure. Chrání cluster Průzkumníka dat Azure a data před částečným selháním oblasti. Uzly clusteru jsou ve výchozím nastavení vytvořeny ve stejném datovém centru. Výběrem několika zón dostupnosti můžete eliminovat jeden bod selhání a zajistit vysokou dostupnost. Nasazení do zón dostupnosti je možné pouze při vytváření clusteru a nelze je později změnit.
    | Specifikace výpočetních prostředků | *D13_v2* | Pro tento rychlý start vyberte specifikaci nejnižší ceny. Pro produkční systém vyberte specifikaci, která nejlépe vyhovuje vašim potřebám.
    | | | |

1. Vyberte **Zkontrolovat + vytvořit,** chcete-li zkontrolovat podrobnosti clusteru, a **vytvořit** pro zřízení clusteru. Zřizování obvykle trvá asi 10 minut.

1. Po dokončení nasazení vyberte **Přejít na prostředek**.

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
    | Doba uchovávání | *3650* | Časové rozpětí (ve dnech), pro které je zaručeno, že data jsou k dispozici pro dotaz. Časový rozsah se začíná měřit od okamžiku, kdy jsou data ingestována.
    | Doba uložení v mezipaměti | *31* | Časové rozpětí (ve dnech), pro které chcete zachovat často dotazovaná data k dispozici v úložišti SSD nebo RAM, nikoli v dlouhodobějším úložišti.
    | | | |

1. Chcete-li vytvořit databázi, vyberte **vytvořit.** Vytvoření obvykle trvá méně než minutu. Po dokončení procesu budete zpět na kartě **Přehled** clusteru.

## <a name="run-basic-commands-in-the-database"></a>Spuštění základních příkazů v databázi

Teď, když máte cluster a databázi, můžete spouštět dotazy a příkazy. V databázi ještě nemáte žádná data, i tak ale můžete vidět, jak nástroje fungují.

1. V rámci svého clusteru vyberte **Dotaz**. Vložte `.show databases` příkaz do okna dotazu a vyberte **Spustit**.

    ![Příkaz Show databases](media/create-cluster-database-portal/show-databases.png)

    Sada výsledků dotazu obsahuje databázi **TestDatabase**, což je jediná databáze v clusteru.

1. Vložte `.show tables` příkaz do okna dotazu a vyberte **Spustit**.

    Tento příkaz vrátí prázdnou sadu výsledků dotazu, protože zatím nemáte žádné tabulky. Tabulku přidáte v dalším článku v této sérii.

## <a name="stop-and-restart-the-cluster"></a>Zastavení a restartování clusteru

Cluster můžete v závislosti na potřebách firmy zastavit a restartovat.

1. Zastavíte ho tak, že v horní části karty **Přehled** vyberete možnost **Zastavit**.

    Při zastavení clusteru nejsou data k dispozici pro dotazy a nemůže ingestovat nová data.

1. Cluster restartujete tak, že v horní části karty **Přehled** vyberete možnost **Spustit**.

    Při restartování clusteru trvá přibližně 10 minut, než bude k dispozici (například když byl původně zřízen). Chvíli pak také trvá načtení dat do aktivní mezipaměti.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu sledovat další rychlé starty a kurzy, udržujte prostředky, které jste vytvořili. V opačném případě vyčistěte skupinu prostředků, abyste se vyhnuli vzniku nákladů.

1. Na webu Azure Portal vyberte **skupiny prostředků** úplně vlevo a pak vyberte skupinu prostředků, která obsahuje cluster Průzkumníka dat.  

1. Vyberte **Odstranit skupinu prostředků,** chcete-li odstranit celou skupinu prostředků. Pokud používáte existující skupinu prostředků, můžete odstranit pouze cluster Průzkumníka dat.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rychlý start: Ingestování dat z centra událostí do Azure Data Exploreru](ingest-data-event-hub.md)


