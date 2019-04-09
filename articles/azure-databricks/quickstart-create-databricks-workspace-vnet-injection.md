---
title: Vytvoření pracovního prostoru Azure Databricks ve virtuální síti
description: Tento článek popisuje, jak nasadit Azure Databricks k virtuální síti.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59288198"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>Rychlý start: Vytvoření pracovního prostoru Azure Databricks ve virtuální síti

Tento rychlý start ukazuje, jak vytvořit pracovní prostor Azure Databricks ve virtuální síti. Bude také vytvořit cluster Apache Spark v rámci pracovního prostoru.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. Na webu Azure Portal, vyberte **vytvořit prostředek** > **sítě** > **virtuální síť**.

2. V části **vytvořit virtuální síť**, použijte následující nastavení: 

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Název|databricks – rychlý start|Vyberte název vaší virtuální sítě.|
    |Adresní prostor|10.1.0.0/16|Rozsah adres virtuální sítě v zápisu CIDR|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|databricks – rychlý start|Vyberte **vytvořit nový** a zadejte nový název skupiny prostředků pro váš účet.|
    |Umístění|\<Vyberte oblast nejbližší vašim uživatelům.\>|Vyberte zeměpisné umístění, kde je možné hostovat vaše virtuální síť. Použijte umístění co nejblíže vašim uživatelům.|
    |Název podsítě|default|Vyberte název výchozí podsíť ve virtuální síti.|
    |Rozsah adres podsítě|10.1.0.0/24|Rozsah adres podsítě v zápisu CIDR. Musí spadat do adresního prostoru virtuální sítě. Nelze upravit rozsah adres podsítě, která se používá.|

    ![Vytvoření virtuální sítě na portálu Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Po dokončení nasazení přejděte k vaší virtuální sítě a vyberte **adresní prostor** pod **nastavení**. Do pole, které se říká *přidat další rozsah adres*, Vložit `10.179.0.0/16` a vyberte **Uložit**.

    ![Adresní prostor virtuální sítě Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

1. Na webu Azure Portal, vyberte **vytvořit prostředek** > **Analytics** > **Databricks**.

2. V části **služba Azure Databricks**, použijte následující nastavení:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Název pracovního prostoru|databricks – rychlý start|Zvolte název pracovního prostoru Azure Databricks.|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|databricks – rychlý start|Vyberte stejnou skupinu prostředků, které jste použili pro virtuální síť.|
    |Umístění|\<Vyberte oblast nejbližší vašim uživatelům.\>|Zvolte stejné umístění jako virtuální síť.|
    |Cenová úroveň|Vyberte mezi Standard nebo Premium.|Další informace o cenových úrovních najdete v tématu [stránku s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).|
    |Nasazení pracovního prostoru Azure Databricks ve službě Virtual Network|Ano|Toto nastavení umožňuje nasadit pracovnímu prostoru Azure Databricks ve vaší virtuální síti.|
    |Virtual Network|databricks – rychlý start|Vyberte virtuální síť, kterou jste vytvořili v předchozí části.|
    |Název veřejné podsítě|public-subnet|Použijte výchozí název veřejné podsítě.|
    |Veřejné podsítě CIDR rozsahu|10.179.64.0/18|Mezi /18 a /26 by měl být rozsah CIDR. pro tuto podsíť.|
    |Název podsítě privátní|private-subnet|Použití výchozího názvu privátní podsítě.|
    |Privátní podsítě CIDR rozsahu|10.179.0.0/18|Mezi /18 a /26 by měl být rozsah CIDR. pro tuto podsíť.|

    ![Vytvoření pracovního prostoru Azure Databricks na portálu Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po dokončení nasazení přejděte k prostředku Azure Databricks. Všimněte si, že je zakázána partnerský vztah virtuální sítě. Všimněte si také, skupinu prostředků a spravované skupiny prostředků na stránce Přehled. 

    ![Přehled služby Azure Databricks na portálu Azure portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Spravované skupině prostředků obsahuje fyzické umístění účtu úložiště (DBFS) pracovního procesu sg (skupina zabezpečení sítě), pracovní procesy virtuální sítě (virtuální síť). Je také místo, kde se vytvoří virtuální počítače, disk, IP adresy a síťové rozhraní. Tato skupina prostředků je uzamčené ve výchozím nastavení; ale když se spustí cluster ve virtuální síti, se vytvoří síťové rozhraní mezi pracovníky vnet ve spravované skupině prostředků a virtuální síť "centra".

    ![Azure Databricks spravované skupiny prostředků](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Vytvoření clusteru

> [!NOTE]
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

1. Vraťte se do služby Azure Databricks a vyberte **spustit pracovní prostor** na **přehled** stránky.

2. Vyberte **clustery** > **+ vytvořit Cluster**. Pak vytvořte název clusteru, jako je *databricks – rychlý start – cluster*a potvrďte zbývající výchozí nastavení. Vyberte **vytvoření clusteru**.

    ![Vytvoření clusteru Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Jakmile je cluster spuštěn, vraťte se do spravovanou skupinu prostředků na webu Azure Portal. Všimněte si nových virtuálních počítačů, disků, IP adresa a síťová rozhraní. V každé veřejné a privátní podsítě s IP adresami se vytvoří síťové rozhraní.  

    ![Azure Databricks spravovanou skupinu prostředků po vytvoření clusteru](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Vraťte se do pracovního prostoru Azure Databricks a vyberte cluster, který jste vytvořili. Přejděte na **prováděcí moduly** kartě **uživatelského rozhraní Spark** stránky. Všimněte si, že adresy pro ovladače a prováděcí moduly jsou v rozsahu privátní podsítě. V tomto příkladu je ovladač 10.179.0.6 a exekutory jsou 10.179.0.4 a 10.179.0.5. IP adresy mohou být odlišná.

    ![Prováděcí moduly Azure Databricks Spark uživatelského rozhraní](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit). Tím se zastaví clusteru.

Pokud se cluster automaticky zastaví neukončíte ručně, k dispozici, jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

Pokud nechcete, aby opakovaně používat cluster, můžete odstranit skupinu prostředků, kterou jste vytvořili na webu Azure Portal.

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořili cluster Spark v Azure Databricks, který jste nasadili do virtuální sítě. Přejděte k dalším článku se dozvíte, jak zadávat dotazy na kontejner Dockeru Linux SQL serveru ve virtuální síti pomocí JDBC ze Azure Databricks Poznámkový blok.  

> [!div class="nextstepaction"]
>[Dotaz kontejneru Dockeru Linux SQL serveru ve virtuální síti ze Azure Databricks Poznámkový blok](vnet-injection-sql-server.md)
