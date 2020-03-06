---
title: Hostování statického webu v Azure Storage
description: Naučte se poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru v účtu Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: e312cc0dc6c58bb33a737e1fc28dd6eb3578b764
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330250"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostování statického webu v Azure Storage

Statický obsah (HTML, CSS, JavaScript a soubory obrázků) můžete zpracovat přímo z kontejneru v účtu Azure Storage GPv2. Další informace najdete v tématu [statické hostování webů v Azure Storage](storage-blob-static-website.md).

V tomto článku se dozvíte, jak povolit hostování statických webů pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

## <a name="enable-static-website-hosting"></a>Povolit hostování statického webu

Hostování statického webu je funkce, kterou musíte povolit v účtu úložiště.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

2. Vyhledejte svůj účet úložiště a zobrazí přehled účtu.

3. Vyberte možnost **statický web** pro zobrazení konfigurační stránky pro statické weby.

4. Vyberte **povoleno** , pokud chcete povolit statické hostování webů pro účet úložiště.

5. V poli **název dokumentu indexu** zadejte výchozí stránku indexu (například *index. html*). 

   Když uživatel přejde do kořenového adresáře váš statický web, zobrazí se výchozí index stránky.  

6. V poli **cesta k chybovému dokumentu** zadejte výchozí chybovou stránku (například *404. html*). 

   Když se uživatel pokusí přejít na stránku, která neexistuje v váš statický web, se zobrazí výchozí chybová stránka.

7. Klikněte na **Uložit**. Na webu Azure portal se teď zobrazují váš koncový bod statického webu. 

    ![Povolení hostování statického webu pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

Můžete povolit hostování statického webu pomocí [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Nejdřív otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), nebo pokud jste rozhraní příkazového řádku Azure [nainstalovali](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) místně, otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahraďte hodnotu zástupného symbolu `<subscription-id>` číslem ID vašeho předplatného.

3. Povolte hostování statických webů.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

   * Zástupný symbol `<error-document-name>` nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * Zástupný text `<index-document-name>` nahraďte názvem dokumentu indexu. Tento dokument je běžně "index. html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

Hostování statického webu můžete povolit pomocí modulu Azure PowerShell.

1. Otevřete okno příkazového řádku Windows PowerShellu.

2. Ověřte, zda máte Azure PowerShell modul AZ verze 0,7 nebo novější.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

4. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahraďte hodnotu zástupného symbolu `<subscription-id>` číslem ID vašeho předplatného.

5. Získejte kontext účtu úložiště, který definuje účet úložiště, který chcete použít.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

   * Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

6. Povolte hostování statických webů.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zástupný symbol `<error-document-name>` nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * Zástupný text `<index-document-name>` nahraďte názvem dokumentu indexu. Tento dokument je běžně "index. html".

---

## <a name="upload-files"></a>Nahrání souborů 

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V těchto pokynech se dozvíte, jak nahrávat soubory pomocí verze Průzkumník služby Storage, která se zobrazí v Azure Portal. Můžete ale také použít verzi [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) , která se spouští mimo Azure Portal. Můžete použít [AzCopy](../common/storage-use-azcopy-v10.md), POWERSHELL, CLI nebo jakoukoli vlastní aplikaci, která může nahrávat soubory do kontejneru **$Web** svého účtu. Podrobný kurz, který nahrává soubory pomocí sady Visual Studio Code, najdete v tématu [kurz: hostování statického webu na BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Vyberte **Průzkumník služby Storage (Preview)** .

2. Rozbalte uzel **kontejnery objektů BLOB** a potom vyberte kontejner **$Web** .

3. Klikněte na tlačítko **nahrát** a nahrajte soubory.

   ![Nahrání souborů](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Pokud máte v úmyslu zobrazit obsah souboru v prohlížeči, ujistěte se, že je typ obsahu tohoto souboru nastaven na `text/html`. 

   ![Kontrolovat typy obsahu](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Průzkumník služby Storage automaticky nastaví tuto vlastnost na `text/html` pro běžně rozpoznaná rozšíření, jako je `.html`. V některých případech je však budete muset nastavit sami. Pokud tuto vlastnost nenastavíte na `text/html`, prohlížeč vyzvat uživatele ke stažení souboru místo vykreslování obsahu. Tuto vlastnost nastavíte tak, že kliknete pravým tlačítkem na soubor a potom kliknete na **vlastnosti**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

> [!NOTE]
> Pokud používáte Azure Cloud Shell, nezapomeňte při odkazování na kontejner `$web` přidat řídicí znak `\` (například: `\$web`). Pokud používáte místní instalaci rozhraní příkazového řádku Azure CLI, nebudete muset používat řídicí znak.

V tomto příkladu se předpokládá, že spouštíte příkazy z Azure Cloud Shell relace.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

* Nahraďte zástupný text `<source-path>` cestou k umístění souborů, které chcete nahrát.

> [!NOTE]
> Pokud používáte instalaci rozhraní příkazového řádku Azure CLI, můžete použít cestu k libovolnému umístění v místním počítači (například: `C:\myFolder`.
>
> Pokud používáte Azure Cloud Shell, budete muset odkazovat na sdílenou složku, která je viditelná pro Cloud Shell. Toto umístění může být sdílená složka samotné sdílené složky cloudu nebo existující sdílená složka, kterou připojujete z Cloud Shell. Další informace o tom, jak to udělat, najdete [v tématu trvalé soubory v Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Hodnotu zástupného textu `<path-to-file>` nahraďte úplnou cestou k souboru, který chcete nahrát (například: `C:\temp\index.html`).

* Nahraďte hodnotu zástupného symbolu `<blob-name>` názvem, který chcete výslednému objektu BLOB poskytnout (například: `index.html`).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Vyhledejte adresu URL webu pomocí Azure Portal

Stránky vaší lokality můžete zobrazit v prohlížeči pomocí veřejné adresy URL webu.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

<a id="portal-find-url" />

V podokně, které se zobrazí vedle stránky přehled účtu úložiště, vyberte možnost **statický web**. Adresa URL vašeho webu se zobrazí v poli **primární koncový bod** .

![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Pomocí následujícího příkazu Najděte veřejnou adresu URL vašeho statického webu:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

* Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Pomocí následujícího příkazu Najděte veřejnou adresu URL vašeho statického webu:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

* Nahraďte hodnotu zástupného symbolu `<storage-account-name>` názvem svého účtu úložiště.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Povolit metriky na statických stránkách webu

Po povolení metrik se na řídicím panelu metriky nahlásí Statistiky provozu souborů v kontejneru **$Web** .

1. Klikněte na **nastavení** > **monitorování** > **metriky**.

   Data metrik se generují zapojováním do různých rozhraní API metrik. Portál zobrazí pouze členy rozhraní API používané v daném časovém rámci, aby bylo možné se zaměřit pouze na členy, kteří vracejí data. Aby bylo zajištěno, že budete moci vybrat potřebného člena rozhraní API, je prvním krokem rozšíření časového rámce.

2. Klikněte na tlačítko časový rámec a vyberte **posledních 24 hodin** a pak klikněte na **použít**.

   ![Časový rozsah metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. V rozevíracím seznamu *obor názvů* vyberte **objekt BLOB** .

   ![Obor názvů metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Pak vyberte metriku **odchozího** přenosu dat.

   ![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. V selektoru *agregace* vyberte **Sum** .

   ![Agregace metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klikněte na tlačítko **Přidat filtr** a v voliči *vlastností* vyberte možnost **název rozhraní API** .

   ![Název rozhraní API metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Zaškrtněte políčko vedle položky **GetWebContent** v selektoru *hodnot* a naplňte sestavu metriky.

   ![GetWebContent metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak nakonfigurovat vlastní doménu s vaším statickým webem. Viz téma [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md).

