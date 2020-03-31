---
title: Vytvoření pracovního prostoru Azure Databricks ve vlastním rychlém startu virtuální sítě
description: Tento článek popisuje, jak nasadit Azure Databricks do vaší virtuální sítě.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132678"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>Úvodní příručka: Vytvoření pracovního prostoru Azure Databricks ve vlastní virtuální síti

Výchozí nasazení Azure Databricks vytvoří novou virtuální síť, kterou spravuje Databricks. Tento rychlý start ukazuje, jak místo toho vytvořit pracovní prostor Azure Databricks ve vlastní virtuální síti. Můžete také vytvořit cluster Apache Spark v rámci tohoto pracovního prostoru. 

Další informace o tom, proč se můžete rozhodnout vytvořit pracovní prostor Azure Databricks ve vlastní virtuální síti, najdete [v tématu Nasazení Datových cihel Azure ve virtuální síti Azure (Injektáž virtuální sítě).](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/databricks/).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

> [!Note]
> Tento kurz nelze provést pomocí **bezplatného zkušebního předplatného Azure**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte předplatné na **průběžně placené**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Potom [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro virtuální procesory ve vaší oblasti. Když vytvoříte pracovní prostor Azure Databricks, můžete vybrat **zkušební (premium - 14denní jednotku DBU)** a poskytnout tak pracovnímu prostoru přístup k bezplatným dbům Azure Databricks Azure na 14 dní.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Potom vyberte **možnost Síť ová> virtuální síť**.

    ![Vytvoření virtuální sítě na webu Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. V části **Vytvořit virtuální síť**použijte následující nastavení: 

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|databricks-rychlý start|Vyberte **Vytvořit nový** a zadejte nový název skupiny prostředků pro svůj účet.|
    |Name (Název)|databricks-rychlý start|Vyberte název virtuální sítě.|
    |Region (Oblast)|\<Vyberte oblast nejbližší vašim uživatelům.\>|Vyberte geografické umístění, kde můžete hostovat virtuální síť. Použijte umístění, které je vašim uživatelům nejblíže.|

    ![Základy pro virtuální síť na Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. Vyberte **Další: Adresy IP >** a použijte následující nastavení. Pak vyberte **Zkontrolovat + vytvořit**.
    
    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Adresní prostor IPv4|10.2.0.0/16|Rozsah adres virtuální sítě v zápisu CIDR. Rozsah CIDR musí být mezi /16 a /24.|
    |Název podsítě|default|Vyberte název výchozí podsítě ve virtuální síti.|
    |Rozsah adres podsítě|10.2.0.0/24|Rozsah adres podsítě v zápisu CIDR. Musí být obsaženv adresním prostoru virtuální sítě. Rozsah adres používané podsítě nelze upravovat.|

    ![Nastavení konfigurací IP adres pro virtuální síť na webu Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. Na kartě **Revize + vytvoření** vyberte **Vytvořit** pro nasazení virtuální sítě. Po dokončení nasazení přejděte do virtuální sítě a v části **Nastavení**vyberte **Adresní prostor** . V poli s nápisem Přidat `10.179.0.0/16` další rozsah *adres*vložte a vyberte **Uložit**.

    ![Adresní prostor virtuální sítě Azure](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Vytvoření pracovního prostoru Azure Databricks

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**. Pak vyberte **Analytics > Databricks**.

    ![Vytvoření pracovního prostoru Azure Databricks na webu Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. V části **Azure Databricks Service**použijte následující nastavení:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Název pracovního prostoru|databricks-rychlý start|Vyberte název pracovního prostoru Azure Databricks.|
    |Předplatné|\<Vaše předplatné\>|Vyberte předplatné Azure, které chcete použít.|
    |Skupina prostředků|databricks-rychlý start|Vyberte stejnou skupinu prostředků, kterou jste použili pro virtuální síť.|
    |Umístění|\<Vyberte oblast nejbližší vašim uživatelům.\>|Zvolte stejné umístění jako vaše virtuální síť.|
    |Cenová úroveň|Zvolte úroveň Standard nebo Premium.|Další informace o cenových úrovních najdete na [stránce s cenami Databricks](https://azure.microsoft.com/pricing/details/databricks/).|

    ![Vytvoření základů pracovního prostoru Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. Po dokončení zadávání nastavení na stránce **Základy** vyberte **Další: Síťové >** a použijte následující nastavení:

    |Nastavení|Navrhovaná hodnota|Popis|
    |-------|---------------|-----------|
    |Nasazení pracovního prostoru Azure Databricks ve virtuální síti (Virtuální síť)|Ano|Toto nastavení umožňuje nasadit pracovní prostor Azure Databricks ve vaší virtuální síti.|
    |Virtual Network|databricks-rychlý start|Vyberte virtuální síť, kterou jste vytvořili v předchozí části.|
    |Název veřejné podsítě|veřejná podsíť|Použijte výchozí název veřejné podsítě.|
    |Rozsah CIDR veřejné podsítě|10.179.64.0/18|Použijte rozsah CIDR až do /26 včetně.|
    |Název soukromé podsítě|privátní podsíť|Použijte výchozí název soukromé podsítě.|
    |Rozsah CIDR privátní podsítě|10.179.0.0/18|Použijte rozsah CIDR až do /26 včetně.|

    ![Přidání informací o virtuální síti do pracovního prostoru Azure Databricks na webu Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. Po dokončení nasazení přejděte na prostředek Azure Databricks. Všimněte si, že partnerský vztah virtuální sítě je zakázán. Všimněte si také skupiny prostředků a spravované skupiny prostředků na stránce s přehledem. 

    ![Přehled Azure Databricks na Webu Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    Spravovaná skupina prostředků není upravitelná a nepoužívá se k vytváření virtuálních počítačů. Virtuální počítače můžete vytvářet pouze ve skupině prostředků, kterou spravujete.

    ![Skupina spravovaných prostředků Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    Pokud se nasazení pracovního prostoru nezdaří, pracovní prostor je stále vytvořen ve stavu selhání. Odstraňte neúspěšný pracovní prostor a vytvořte nový pracovní prostor, který vyřeší chyby nasazení. Při odstranění nezdařeného pracovního prostoru se odstraní také skupina spravovaných prostředků a všechny úspěšně nasazené prostředky.

## <a name="create-a-cluster"></a>Vytvoření clusteru

> [!NOTE]
> Pokud chcete k vytvoření clusteru Azure Databricks použít bezplatný účet, přejděte na svůj profil a změňte své předplatné na **Průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/).

1. Vraťte se do služby Azure Databricks a na stránce **Přehled** vyberte **Spustit pracovní prostor.**

2. Vyberte **clustery** > **+ vytvořit cluster**. Potom vytvořte název clusteru, například *databricks-quickstart-cluster*, a přijměte zbývající výchozí nastavení. Vyberte **Vytvořit cluster**.

    ![Vytvoření clusteru Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. Po spuštění clusteru se vraťte do spravované skupiny prostředků na webu Azure Portal. Všimněte si nových virtuálních počítačů, disků, IP adresy a síťových rozhraní. Síťové rozhraní je vytvořeno v každé veřejné a soukromé podsíti s adresami IP.  

    ![Skupina spravovaných prostředků Azure Databricks po vytvoření clusteru](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Vraťte se do pracovního prostoru Azure Databricks a vyberte cluster, který jste vytvořili. Pak přejděte na kartu **Executors** na stránce **Sípového ui.** Všimněte si, že adresy ovladače a prováděcích modulů jsou v rozsahu soukromých podsítí. V tomto příkladu je ovladač 10.179.0.6 a prováděcí moduly jsou 10.179.0.4 a 10.179.0.5. Vaše IP adresy se mohou lišit.

    ![Vykonavatelé ui Azure Databricks Spark](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto článku můžete cluster ukončit. Pokud to chcete udělat, v levém podokně v pracovním prostoru Azure Databricks vyberte **Clusters** (Clustery). U clusteru, který chcete ukončit, přesuňte kurzor na tři tečky pod sloupcem **Actions** (Akce) a vyberte ikonu **Terminate** (Ukončit). Tím zastavíte cluster.

Pokud cluster ručně neukončíte, bude automaticky ukončen za předpokladu, že jste při vytváření clusteru zaškrtli políčko **Ukončit po \_ \_ minutách nečinnosti.** V takovém případě se cluster automaticky zastaví, pokud byl po stanovenou dobu neaktivní.

Pokud nechcete znovu použít cluster, můžete odstranit skupinu prostředků, kterou jste vytvořili na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořili cluster Spark v Azure Databricks, které jste nasadili do virtuální sítě. Přejdete k dalšímu článku, kde se dozvíte, jak zadat dotaz na kontejner SQL Server Linux Docker ve virtuální síti pomocí JDBC z poznámkového bloku Azure Databricks.  

> [!div class="nextstepaction"]
>[Dotazování na linuxový kontejner Docker SQL Serveru ve virtuální síti z poznámkového bloku Azure Databricks](vnet-injection-sql-server.md)
