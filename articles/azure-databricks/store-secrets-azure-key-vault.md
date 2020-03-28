---
title: Výuka – přístup k úložišti objektů blob pomocí trezoru klíčů pomocí Azure Databricks
description: Tento kurz popisuje, jak získat přístup k azure blob storage z Azure Databricks pomocí tajných kódů uložených v trezoru klíčů.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 15399d5a00c13141877dcf44640df2c1f9b9ba5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75889055"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Kurz: Přístup k úložišti objektů Blob Azure z Datových cihel Azure pomocí Azure Key Vault

Tento kurz popisuje, jak získat přístup k azure blob storage z Azure Databricks pomocí tajných kódů uložených v trezoru klíčů.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření účtu úložiště a kontejneru objektů blob
> * Vytvoření trezoru klíčů Azure a přidání tajného klíče
> * Vytvoření pracovního prostoru Azure Databricks a přidání tajného oboru
> * Přístup k kontejneru objektů blob z Azure Databricks

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure](https://portal.azure.com/).

> [!Note]
> Tento kurz nelze provést pomocí **bezplatného zkušebního předplatného Azure**.
> Pokud máte bezplatný účet, přejděte na svůj profil a změňte předplatné na **průběžně placené**. Další informace najdete na stránce [bezplatného účtu Azure](https://azure.microsoft.com/free/). Potom [odeberte limit útraty](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)a [požádejte o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) pro virtuální procesory ve vaší oblasti. Když vytvoříte pracovní prostor Azure Databricks, můžete vybrat **zkušební (premium - 14denní jednotku DBU)** a poskytnout tak pracovnímu prostoru přístup k bezplatným dbům Azure Databricks Azure na 14 dní.

## <a name="create-a-storage-account-and-blob-container"></a>Vytvoření účtu úložiště a kontejneru objektů blob

1. Na webu Azure Portal vyberte Vytvořit**úložiště** **prostředků** > . Pak vyberte **účet úložiště**.

   ![Vyhledání prostředku účtu úložiště Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Vyberte předplatné a skupinu prostředků nebo vytvořte novou skupinu prostředků. Pak zadejte název účtu úložiště a zvolte umístění. Vyberte **zkontrolovat + vytvořit**.

   ![Nastavení vlastností účtu úložiště](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Pokud je ověření neúspěšné, vyřazte problémy a akci opakujte. Pokud je ověření úspěšné, vyberte **Vytvořit** a počkejte na vytvoření účtu úložiště.

4. Přejděte na nově vytvořený účet úložiště a v části Služby na stránce **Přehled** vyberte **Objekty blob** v části **Služby.** Pak vyberte **+ Kontejner** a zadejte název kontejneru. Vyberte **OK**.

   ![Vytvořit nový kontejner](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Vyhledejte soubor, který chcete nahrát do kontejneru úložiště objektů blob. Pokud soubor nemáte, vytvořte pomocí textového editoru nový textový soubor s určitými informacemi. V tomto příkladu soubor s názvem **hw.txt** obsahuje text "hello world" Uložte textový soubor místně a nahrajte ho do kontejneru úložiště objektů blob.

   ![Nahrání souboru do kontejneru](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Vraťte se ke svému účtu úložiště a v části **Nastavení**vyberte **Přístupové klávesy** . Zkopírujte **název účtu úložiště** a klíč **1** do textového editoru pro pozdější použití v tomto kurzu.

   ![Najít přístupové klíče účtu úložiště](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Vytvoření trezoru klíčů Azure a přidání tajného klíče

1. Na webu Azure Portal vyberte **Vytvořit prostředek** a do vyhledávacího pole zadejte **Trezor klíčů.**

   ![Vytvoření vyhledávacího pole o prostředcích Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Zdroj trezoru klíčů je automaticky vybrán. Vyberte **Vytvořit**.

   ![Vytvoření prostředku trezoru klíčů](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na stránce **Vytvořit úschovnu klíčů** zadejte následující informace a zachovejte výchozí hodnoty pro zbývající pole:

   |Vlastnost|Popis|
   |--------|-----------|
   |Name (Název)|Jedinečný název trezoru klíčů.|
   |Předplatné|Zvolte předplatné.|
   |Skupina prostředků|Zvolte skupinu prostředků nebo vytvořte novou.|
   |Umístění|Zvolte umístění.|

   ![Vlastnosti trezoru klíčů Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Po zadání výše uvedených informací vyberte **Vytvořit**. 

4. Přejděte do nově vytvořeného trezoru klíčů na webu Azure portal a vyberte **Tajné kódy**. Potom vyberte **+ Generovat/Importovat**. 

   ![Generovat nový tajný klíč trezoru klíčů](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na stránce **Vytvořit tajný klíč** zadejte následující informace a zachovejte výchozí hodnoty pro zbývající pole:

   |Vlastnost|Hodnota|
   |--------|-----------|
   |Možnosti nahrávání|Ruční|
   |Name (Název)|Popisný název klíče účtu úložiště.|
   |Hodnota|key1 z vašeho účtu úložiště.|

   ![Vlastnosti nového tajného klíče trezoru klíčů](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Uložte název klíče do textového editoru pro použití později v tomto kurzu a vyberte **vytvořit**. Potom přejděte do nabídky **Vlastnosti.** Zkopírujte **název DNS** a **ID prostředku** do textového editoru pro pozdější použití v kurzu.

   ![Kopírování názvu DNS a ID prostředku trezoru klíčů Azure](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Vytvoření pracovního prostoru Azure Databricks a přidání tajného oboru

1. Na webu Azure Portal vyberte **Vytvořit zdroj** > **Analytics** > **Azure Databricks**.

    ![Datové cihly na webu Azure Portal](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. V části **Azure Databricks Service**zadejte následující hodnoty pro vytvoření pracovního prostoru Databricks.

   |Vlastnost  |Popis  |
   |---------|---------|
   |Název pracovního prostoru     | Zadejte název pracovního prostoru Databricks.        |
   |Předplatné     | Z rozevíracího seznamu vyberte své předplatné Azure.        |
   |Skupina prostředků     | Vyberte stejnou skupinu prostředků, která obsahuje trezor klíčů. |
   |Umístění     | Vyberte stejné umístění jako trezor klíčů Azure. Pro všechny dostupné oblasti najdete v [tématu služby Azure dostupné podle oblasti](https://azure.microsoft.com/regions/services/).        |
   |Cenová úroveň     |  Zvolte úroveň **Standard** nebo **Premium**. Další informace o těchto úrovních najdete na [stránce s cenami za Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Vlastnosti pracovního prostoru Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Vyberte **Vytvořit**.

3. Přejděte na nově vytvořený prostředek Azure Databricks na webu Azure Portal a vyberte **Spustit pracovní prostor**.

   ![Spuštění pracovního prostoru Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Jakmile se váš pracovní prostor Azure Databricks otevře v samostatném okně, přidejte k adrese URL **#secrets/createScope.** Adresa URL by měla mít následující formát: 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**.
   

5. Zadejte název oboru a zadejte název DNS azure trezoru klíčů a ID prostředků, které jste uložili dříve. Uložte název oboru v textovém editoru pro použití později v tomto kurzu. Potom vyberte **Create** (Vytvořit).

   ![Vytvoření tajného oboru v pracovním prostoru Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Přístup k kontejneru objektů blob z Azure Databricks

1. Na domovské stránce pracovního prostoru Azure Databricks vyberte **Nový cluster** v části **Běžné úkoly**.

   ![Vytvoření nového poznámkového bloku Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Zadejte název clusteru a vyberte **Vytvořit cluster**. Vytvoření clusteru trvá několik minut.

3. Po vytvoření clusteru přejděte na domovskou stránku pracovního prostoru Azure Databricks a v části **Běžné úkoly**vyberte **Nový poznámkový blok** .

   ![Vytvoření nového poznámkového bloku Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Zadejte název poznámkového bloku a nastavte jazyk na Python. Nastavte cluster na název clusteru, který jste vytvořili v předchozím kroku.

5. Spusťte následující příkaz pro připojení kontejneru úložiště objektů blob. Nezapomeňte změnit hodnoty pro následující vlastnosti:

   * název kontejneru
   * název vašeho účtu úložiště
   * název připojení
   * config-key
   * název oboru
   * název klíče

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** je cesta DBFS představující, kde bude připojen kontejner úložiště objektů blob nebo složka uvnitř kontejneru (zadaná ve zdroji).
   * **conf-key** může `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` být buď nebo`fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **název oboru** je název tajného oboru, který jste vytvořili v předchozí části. 
   * **název klíče** je název jejich tajného klíče, který jste vytvořili pro klíč účtu úložiště v trezoru klíčů.

   ![Vytvoření připojení úložiště objektů blob v poznámkovém bloku](./media/store-secrets-azure-key-vault/command1.png)

6. Spuštěním následujícího příkazu přečtěte textový soubor v kontejneru úložiště objektů blob do datového rámce. Změňte hodnoty v příkazu tak, aby odpovídaly názvu a názvu souboru připojení.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Čtení souboru do datového rámce](./media/store-secrets-azure-key-vault/command2.png)

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

9. Všimněte si, že po připojení bylo odpojeno, můžete už číst z účtu úložiště objektů blob.

   ![Odpojit chybu účtu úložiště](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat tuto aplikaci, odstraňte celou skupinu prostředků pomocí následujících kroků:

1. Z nabídky na levé straně na webu Azure Portal vyberte **skupiny prostředků** a přejděte do skupiny prostředků.

2. Vyberte **Odstranit skupinu prostředků** a zadejte název skupiny prostředků. Vyberte **Odstranit**. 

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku, kde se dozvíte, jak implementovat prostředí VNet injektované databricks s koncovým bodem služby povoleným pro Cosmos DB.
> [!div class="nextstepaction"]
> [Kurz: Implementace Datových cihel Azure s koncovým bodem Cosmos DB](service-endpoint-cosmosdb.md)
