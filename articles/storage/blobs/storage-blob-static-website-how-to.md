---
title: Hostování statického webu v Azure Storage
description: Naučte se poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru v účtu Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: d5b8121c7888903f3e4552a21a6ddc175ecc5176
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489084"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostování statického webu v Azure Storage

Statický obsah (HTML, CSS, JavaScript a soubory obrázků) můžete zpracovat přímo z kontejneru v účtu Azure Storage GPv2. Další informace najdete v tématu [statické hostování webů v Azure Storage](storage-blob-static-website.md).

V tomto článku se dozvíte, jak povolit hostování statických webů pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

<a id="portal" />

## <a name="portaltabazure-portal"></a>[Azure Portal](#tab/azure-portal)

Podrobný kurz najdete v tématu [kurz: hostování statického webu na BLOB Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Po povolení hostování statického webu můžete zobrazit stránky vaší lokality z prohlížeče pomocí veřejné adresy URL webu.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Vyhledejte adresu URL webu pomocí Azure Portal

V podokně, které se zobrazí vedle stránky přehled účtu úložiště, vyberte možnost **statický web**. Adresa URL vašeho webu se zobrazí v poli **primární koncový bod** .

![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

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

   * Nahraďte hodnotu zástupného znaku `<storage-account-name>` názvem vašeho účtu úložiště.

   * Zástupný symbol `<error-document-name>` nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * Zástupný text `<index-document-name>` nahraďte názvem dokumentu indexu. Tento dokument je běžně "index. html".

4. Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

   > [!NOTE]
   > Pokud používáte Azure Cloud Shell, nezapomeňte při odkazování na kontejner `$web` přidat řídicí znak `\` (například: `\$web`). Pokud používáte místní instalaci rozhraní příkazového řádku Azure CLI, nebudete muset používat řídicí znak.

   V tomto příkladu se předpokládá, že spouštíte příkazy z Azure Cloud Shell relace.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Nahraďte hodnotu zástupného znaku `<storage-account-name>` názvem vašeho účtu úložiště.

   * Nahraďte zástupný text `<source-path>` cestou k umístění souborů, které chcete nahrát.

   > [!NOTE]
   > Pokud používáte instalaci rozhraní příkazového řádku Azure CLI, můžete použít cestu k libovolnému umístění v místním počítači (například: `C:\myFolder`.
   >
   > Pokud používáte Azure Cloud Shell, budete muset odkazovat na sdílenou složku, která je viditelná pro Cloud Shell. Toto umístění může být sdílená složka samotné sdílené složky cloudu nebo existující sdílená složka, kterou připojujete z Cloud Shell. Další informace o tom, jak to udělat, najdete [v tématu trvalé soubory v Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Vyhledání adresy URL webu pomocí rozhraní příkazového řádku Azure

Obsah můžete zobrazit z prohlížeče pomocí veřejné adresy URL webu.

Adresu URL najdete pomocí následujícího příkazu:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Nahraďte hodnotu zástupného znaku `<storage-account-name>` názvem vašeho účtu úložiště.

* Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

<a id="powershell" />

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

   * Nahraďte hodnotu zástupného znaku `<storage-account-name>` názvem vašeho účtu úložiště.

6. Povolte hostování statických webů.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Zástupný symbol `<error-document-name>` nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * Zástupný text `<index-document-name>` nahraďte názvem dokumentu indexu. Tento dokument je běžně "index. html".

7. Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Hodnotu zástupného textu `<path-to-file>` nahraďte úplnou cestou k souboru, který chcete nahrát (například: `C:\temp\index.html`).

   * Nahraďte hodnotu zástupného symbolu `<blob-name>` názvem, který chcete výslednému objektu BLOB poskytnout (například: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Vyhledání adresy URL webu pomocí PowerShellu

Obsah můžete zobrazit z prohlížeče pomocí veřejné adresy URL webu.

Adresu URL najdete pomocí následujícího příkazu:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Nahraďte hodnotu zástupného symbolu `<resource-group-name>` názvem vaší skupiny prostředků.

* Nahraďte hodnotu zástupného znaku `<storage-account-name>` názvem vašeho účtu úložiště.

<a id="metrics" />

---

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

* [Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)
* [Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro objekt BLOB nebo webový koncový bod](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Sestavení první webové aplikace bez serveru](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
