---
title: Vytvoření pracovního prostoru Azure Databricks ve vlastním rychlém startu Virtual Network
description: Tento článek popisuje, jak nasadit Azure Databricks do vaší virtuální sítě.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 404aa1d345bb1e30a0fe55d033d49d7dd8ba5a1b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893407"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Rychlý Start: vytvoření pracovního prostoru Azure Databricks ve vlastním Virtual Network

Výchozí nasazení Azure Databricks vytvoří novou virtuální síť, která je spravovaná datacihly. V tomto rychlém startu se dozvíte, jak vytvořit Azure Databricks pracovní prostor ve vlastní virtuální síti. V tomto pracovním prostoru také vytvoříte cluster Apache Spark. 

Další informace o tom, proč se můžete rozhodnout vytvořit pracovní prostor Azure Databricks ve vlastní virtuální síti, najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network (vkládání virtuální sítě)] (/databricks/Administration-Guide/Cloud-Configurations/Azure/VNET-inject).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal](https://portal.azure.com/).

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce Azure Portal vyberte **vytvořit prostředek**. Pak vyberte **sítě > virtuální síť**.

    ![Vytvoření virtuální sítě v Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. V části **vytvořit virtuální síť**použijte následující nastavení: 

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Name (Název)|datacihly – rychlý Start|Vyberte název vaší virtuální sítě.|
    |Adresní prostor|10.1.0.0/16|Rozsah adres virtuální sítě v zápisu CIDR. Rozsah CIDR musí být mezi/16 a/24.|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|datacihly – rychlý Start|Vyberte **vytvořit novou** a zadejte nový název skupiny prostředků pro váš účet.|
    |Umístění|\<Vyberte oblast nejbližší vašim uživatelům.\>|Vyberte zeměpisnou polohu, kde můžete hostovat svou virtuální síť. Použijte umístění, které je nejblíže vašim uživatelům.|
    |Název podsítě|default|Vyberte název výchozí podsítě ve vaší virtuální síti.|
    |Rozsah adres podsítě|10.1.0.0/24|Rozsah adres podsítě v zápisu CIDR. Musí být obsažený v adresním prostoru virtuální sítě. Rozsah adres podsítě, která se používá, se nedá upravit.|

    ![Vytvoření virtuální sítě v Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Až se nasazení dokončí, přejděte do virtuální sítě a v části **Nastavení**vyberte **adresní prostor** . Do pole *Přidat další rozsah adres*zadejte `10.179.0.0/16` a vyberte **Uložit**.

    ![Adresní prostor virtuální sítě Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

1. V nabídce Azure Portal vyberte **vytvořit prostředek**. Pak vyberte **Analytics > datacihly**.

    ![Vytvoření pracovního prostoru Azure Databricks v Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. V části **služba Azure Databricks**použijte následující nastavení:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Název pracovního prostoru|datacihly – rychlý Start|Vyberte název pracovního prostoru Azure Databricks.|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|datacihly – rychlý Start|Vyberte stejnou skupinu prostředků, kterou jste použili pro virtuální síť.|
    |Umístění|\<Vyberte oblast nejbližší vašim uživatelům.\>|Vyberte stejné umístění jako vaše virtuální síť.|
    |Cenová úroveň|Vyberte si z úrovně Standard nebo Premium.|Další informace o cenových úrovních najdete na stránce s [cenami datacihly](https://azure.microsoft.com/pricing/details/databricks/).|
    |Nasazení pracovního prostoru Azure Databricks v Virtual Network (VNet)|Ano|Toto nastavení umožňuje nasadit Azure Databricks pracovní prostor ve vaší virtuální síti.|
    |Virtual Network|datacihly – rychlý Start|Vyberte virtuální síť, kterou jste vytvořili v předchozí části.|
    |Název veřejné podsítě|public-subnet|Použijte výchozí název veřejné podsítě.|
    |Rozsah CIDR veřejné podsítě|10.179.64.0/18|Použijte rozsah CIDR až do a včetně/26.|
    |Název privátní podsítě|private-subnet|Použijte výchozí název privátní podsítě.|
    |Rozsah CIDR privátní podsítě|10.179.0.0/18|Použijte rozsah CIDR až do a včetně/26.|

    ![Vytvoření pracovního prostoru Azure Databricks v Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po dokončení nasazení přejděte k prostředku Azure Databricks. Všimněte si, že partnerský vztah virtuálních sítí je zakázaný. Všimněte si také skupiny prostředků a spravované skupiny prostředků na stránce Přehled. 

    ![Přehled Azure Databricks v Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Spravovanou skupinu prostředků nelze upravovat a nepoužívá se k vytváření virtuálních počítačů. Virtuální počítače můžete vytvářet jenom ve skupině prostředků, kterou spravujete.

    ![Azure Databricks spravovaná skupina prostředků](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>Vytvoření clusteru

> [!NOTE]
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

1. Vraťte se do služby Azure Databricks a na stránce **Přehled** vyberte **Spustit pracovní prostor** .

2. Vyberte **clustery** >  **+ vytvořit cluster**. Pak vytvořte název clusteru, jako je *datacihly-rychlý Start-cluster*, a přijměte zbývající výchozí nastavení. Vyberte **Vytvořit cluster**.

    ![Vytvořit cluster Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Jakmile je cluster spuštěný, vraťte se do spravované skupiny prostředků v Azure Portal. Všimněte si nových virtuálních počítačů, disků, IP adres a síťových rozhraní. V každé veřejné a privátní podsíti se vytvoří síťové rozhraní s IP adresami.  

    ![Po vytvoření clusteru Azure Databricks spravovanou skupinu prostředků.](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Vraťte se do pracovního prostoru Azure Databricks a vyberte cluster, který jste vytvořili. Pak přejděte na kartu **vykonavatelé** na stránce **uživatelského rozhraní Spark** . Všimněte si, že adresy pro ovladač a prováděcí moduly jsou v rozsahu privátní podsítě. V tomto příkladu je ovladač 10.179.0.6 a prováděcí moduly jsou 10.179.0.4 a 10.179.0.5. Vaše IP adresy můžou být odlišné.

    ![Azure Databricks prováděcích modulů uživatelského rozhraní Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit). Tím se cluster zastaví.

Pokud se cluster automaticky zastaví neukončíte ručně, k dispozici, jste vybrali **po provedení \_ \_ počet minut nečinnosti** zaškrtávací políčko při vytváření clusteru. V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

Pokud nechcete cluster znovu použít, můžete odstranit skupinu prostředků, kterou jste vytvořili v Azure Portal.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili cluster Spark v Azure Databricks, který jste nasadili do virtuální sítě. V dalším článku se dozvíte, jak zadat dotaz na SQL Server kontejner Docker pro Linux ve virtuální síti pomocí služby JDBC z poznámkového bloku Azure Databricks.  

> [!div class="nextstepaction"]
>[Dotazování kontejneru Docker SQL Server Linux ve virtuální síti z poznámkového bloku Azure Databricks](vnet-injection-sql-server.md)
