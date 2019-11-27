---
title: Přístup k úložišti objektů BLOB pomocí trezoru klíčů – Azure Databricks
description: V tomto kurzu se dozvíte, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí tajných kódů uložených v trezoru klíčů.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 16339590c3cfb11235ef4763c2e32c71b521c709
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539069"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Kurz: přístup k Azure Blob Storage z Azure Databricks pomocí Azure Key Vault

V tomto kurzu se dozvíte, jak získat přístup k Azure Blob Storage z Azure Databricks pomocí tajných kódů uložených v trezoru klíčů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště a kontejneru objektů BLOB
> * Vytvoření Azure Key Vault a přidání tajného klíče
> * Vytvoření pracovního prostoru Azure Databricks a přidání oboru tajného klíče
> * Přístup k kontejneru objektů BLOB z Azure Databricks

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na web [Azure Portal ](https://portal.azure.com/).

> [!Note]
> Tento kurz se nedá provést pomocí **předplatného Azure free zkušební verze**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte si předplatné na **průběžné platby**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Pak [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) pro vCPU ve vaší oblasti. Když vytváříte pracovní prostor Azure Databricks, můžete vybrat cenovou úroveň **DBU (Premium-14-days)** a poskytnout tak přístup k pracovnímu prostoru zdarma Premium Azure Databricks DBU po dobu 14 dnů.

## <a name="create-a-storage-account-and-blob-container"></a>Vytvoření účtu úložiště a kontejneru objektů BLOB

1. V Azure Portal vyberte **vytvořit prostředek** > **úložiště**. Pak vyberte **účet úložiště**.

   ![Najít prostředek účtu úložiště Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Vyberte předplatné a skupinu prostředků nebo vytvořte novou skupinu prostředků. Pak zadejte název účtu úložiště a zvolte umístění. Vyberte **zkontrolovat + vytvořit**.

   ![Nastavení vlastností účtu úložiště](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Pokud není ověření úspěšné, vyřešte problém a zkuste to znovu. Pokud je ověření úspěšné, vyberte **vytvořit** a počkejte, než se účet úložiště vytvoří.

4. Přejděte k nově vytvořenému účtu úložiště a v části **služby** na stránce **Přehled** vyberte **objekty blob** . Pak vyberte **+ Container** a zadejte název kontejneru. Vyberte **OK**.

   ![Vytvořit nový kontejner](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Vyhledejte soubor, který chcete nahrát do kontejneru úložiště objektů BLOB. Pokud soubor nemáte, pomocí textového editoru vytvořte nový textový soubor s některými informacemi. V tomto příkladu soubor s názvem " **HW. txt** " obsahuje text "Hello World". Uložte textový soubor místně a nahrajte ho do kontejneru úložiště objektů BLOB.

   ![Odeslat soubor do kontejneru](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Vraťte se do účtu úložiště a v části **Nastavení**vyberte **přístupové klíče** . Zkopírujte **název účtu úložiště** a **klíč 1** do textového editoru pro pozdější použití v tomto kurzu.

   ![Najít přístupové klíče účtu úložiště](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Vytvoření Azure Key Vault a přidání tajného klíče

1. V Azure Portal vyberte **vytvořit prostředek** a do vyhledávacího pole zadejte **Key Vault** .

   ![Vytvoření vyhledávacího pole prostředku Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Prostředek Key Vault je automaticky vybrán. Vyberte **Vytvořit**.

   ![Vytvoření prostředku Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na stránce **Vytvoření trezoru klíčů** zadejte následující informace a u zbývajících polí nechte výchozí hodnoty:

   |Vlastnost|Popis|
   |--------|-----------|
   |Název|Jedinečný název vašeho trezoru klíčů.|
   |Předplatné|Vyberte předplatné.|
   |Skupina prostředků|Vyberte skupinu prostředků nebo vytvořte novou.|
   |Umístění|Zvolte umístění.|

   ![Vlastnosti trezoru klíčů Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Po zadání výše uvedených informací vyberte **Vytvořit**. 

4. V Azure Portal přejděte k nově vytvořenému trezoru klíčů a vyberte **tajné klíče**. Pak vyberte **+ Generovat/importovat**. 

   ![Vygenerovat nový tajný klíč trezoru klíčů](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na stránce **vytvořit tajný kód** zadejte následující informace a u zbývajících polí nechte výchozí hodnoty:

   |Vlastnost|Hodnota|
   |--------|-----------|
   |Možnosti nahrání|Ruční|
   |Název|Popisný název klíče účtu úložiště|
   |Hodnota|klíč1 z vašeho účtu úložiště.|

   ![Vlastnosti nového tajného klíče trezoru klíčů](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Uložte název klíče v textovém editoru pro pozdější použití v tomto kurzu a vyberte **vytvořit**. Pak přejděte do nabídky **vlastnosti** . Zkopírujte **název DNS** a **ID prostředku** do textového editoru pro pozdější použití v tomto kurzu.

   ![Kopírovat Azure Key Vault název DNS a ID prostředku](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Vytvoření pracovního prostoru Azure Databricks a přidání oboru tajného klíče

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Analýza** > **Azure Databricks**.

    ![Datacihly na Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. V části **Azure Databricks služba**zadejte následující hodnoty pro vytvoření pracovního prostoru datacihly.

   |Vlastnost  |Popis  |
   |---------|---------|
   |Název pracovního prostoru     | Zadejte název pracovního prostoru Databricks.        |
   |Předplatné     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
   |Skupina prostředků     | Vyberte stejnou skupinu prostředků, která obsahuje váš Trezor klíčů. |
   |Umístění     | Vyberte stejné umístění jako vaše Azure Key Vault. Všechny dostupné oblasti najdete v tématu [služby Azure dostupné v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).        |
   |Cenová úroveň     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Vlastnosti pracovního prostoru datacihly](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Vyberte **Vytvořit**.

3. Přejděte k nově vytvořenému prostředku Azure Databricks v Azure Portal a vyberte **Spustit pracovní prostor**.

   ![Spustit Azure Databricks pracovní prostor](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Po otevření pracovního prostoru Azure Databricks v samostatném okně přidejte k adrese URL **#secrets/CreateScope** . Adresa URL by měla mít následující formát: 

   **https://< \location >. azuredatabricks. NET/#secrets/CreateScope**.

5. Zadejte název oboru a zadejte Azure Key Vault název DNS a ID prostředku, které jste předtím uložili. Uložte název oboru v textovém editoru pro pozdější použití v tomto kurzu. Potom vyberte **Create** (Vytvořit).

   ![Vytvoření oboru tajného kódu v pracovním prostoru Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Přístup k kontejneru objektů BLOB z Azure Databricks

1. Na domovské stránce pracovního prostoru Azure Databricks v části **běžné úlohy**vyberte **nový cluster** .

   ![Vytvoření nového poznámkového bloku Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Zadejte název clusteru a vyberte **vytvořit cluster**. Dokončení vytváření clusteru trvá několik minut.

3. Po vytvoření clusteru přejděte na domovskou stránku pracovního prostoru Azure Databricks a v části **běžné úkoly**vyberte **Nový Poznámkový blok** .

   ![Vytvoření nového poznámkového bloku Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Zadejte název poznámkového bloku a nastavte jazyk na Python. Nastavte cluster na název clusteru, který jste vytvořili v předchozím kroku.

5. Spusťte následující příkaz pro připojení kontejneru úložiště objektů BLOB. Nezapomeňte změnit hodnoty pro následující vlastnosti:

   * název vašeho kontejneru
   * název účtu úložiště
   * název připojení
   * konfigurační klíč
   * obor – název
   * název klíče

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **Mount-Name** je DBFS cesta, která představuje, kde bude připojen kontejner BLOB Storage nebo složka uvnitř kontejneru (určený ve zdroji).
   * **klíč conf-Key** může být buď `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net`, nebo `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **Scope-Name** je název oboru tajného klíče, který jste vytvořili v předchozí části. 
   * **klíč-name** je název tajného klíče, který jste vytvořili pro klíč účtu úložiště ve vašem trezoru klíčů.

   ![Vytvořit připojení úložiště objektů BLOB v poznámkovém bloku](./media/store-secrets-azure-key-vault/command1.png)

6. Spusťte následující příkaz, který přečte textový soubor v kontejneru úložiště objektů blob do datového rámce. Změňte hodnoty v příkazu tak, aby odpovídaly názvu svého připojení a názvu souboru.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Přečíst soubor pro datový rámec](./media/store-secrets-azure-key-vault/command2.png)

7. K zobrazení obsahu souboru použijte následující příkaz.

   ```python
   df.show()
   ```
   ![Zobrazit datový rámec](./media/store-secrets-azure-key-vault/command3.png)

8. Pokud chcete odpojit úložiště objektů blob, spusťte následující příkaz:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Odpojit účet úložiště](./media/store-secrets-azure-key-vault/command4.png)

9. Všimněte si, že po odpojení připojení už nebudete moct číst z účtu úložiště BLOB.

   ![Chyba odpojení účtu úložiště](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte celou skupinu prostředků pomocí následujících kroků:

1. V nabídce na levé straně v Azure Portal vyberte **skupiny prostředků** a přejděte do skupiny prostředků.

2. Vyberte **Odstranit skupinu prostředků** a zadejte název skupiny prostředků. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak implementovat virtuální síť s vloženým prostředím datacihly s koncovým bodem služby, který je povolený pro Cosmos DB.
> [!div class="nextstepaction"]
> [Kurz: implementace Azure Databricks s koncovým bodem Cosmos DB](service-endpoint-cosmosdb.md)
