---
title: Hostování statického webu v Azure Storage
description: Naučte se poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru v účtu Azure Storage GPv2.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2ba0e836f1be97681d677158f5cb81c5b033e915
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008497"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostování statického webu v Azure Storage

Statický obsah (HTML, CSS, JavaScript a soubory obrázků) můžete zpracovat přímo z kontejneru v účtu pro [obecné účely v2](../common/storage-account-create.md) nebo [BlockBlobStorage](storage-blob-create-account-block-blob.md) . Další informace najdete v tématu [statické hostování webů v Azure Storage](storage-blob-static-website.md).

V tomto článku se dozvíte, jak povolit hostování statických webů pomocí Azure Portal, rozhraní příkazového řádku Azure nebo PowerShellu.

## <a name="enable-static-website-hosting"></a>Povolit hostování statického webu

Hostování statického webu je funkce, kterou musíte povolit v účtu úložiště.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

2. Vyhledejte svůj účet úložiště a zobrazte přehled účtu.

3. Vyberte možnost **statický web** pro zobrazení konfigurační stránky pro statické weby.

4. Vyberte **povoleno** , pokud chcete povolit statické hostování webů pro účet úložiště.

5. V poli **název dokumentu indexu** zadejte výchozí stránku indexu (například: *index.html*). 

   Výchozí stránka indexu se zobrazí, když uživatel přejde do kořenového adresáře vašeho statického webu.  

6. V poli **cesta k chybovému dokumentu** zadejte výchozí chybovou stránku (například: *404.html*). 

   Výchozí chybová stránka se zobrazí, když se uživatel pokusí přejít na stránku, která na vašem statickém webu neexistuje.

7. Klikněte na **Uložit**. Azure Portal teď zobrazuje váš koncový bod statického webu. 

    ![Povolení hostování statických webů pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

Můžete povolit hostování statického webu pomocí rozhraní příkazového [řádku Azure Command-Line](/cli/azure/).

1. Nejdřív otevřete [Azure Cloud Shell](../../cloud-shell/overview.md), nebo pokud jste rozhraní příkazového řádku Azure [nainstalovali](/cli/azure/install-azure-cli) místně, otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte své aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

3. Povolte hostování statických webů.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

   * `<error-document-name>`Zástupný symbol nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * `<index-document-name>`Zástupný symbol nahraďte názvem dokumentu indexu. Tento dokument je běžně "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

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

   Nahraďte `<subscription-id>` hodnotu zástupného symbolu číslem ID vašeho předplatného.

5. Získejte kontext účtu úložiště, který definuje účet úložiště, který chcete použít.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.

   * Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

6. Povolte hostování statických webů.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * `<error-document-name>`Zástupný symbol nahraďte názvem dokumentu chyby, který se zobrazí uživatelům, když prohlížeč požaduje stránku na vašem webu, který neexistuje.

   * `<index-document-name>`Zástupný symbol nahraďte názvem dokumentu indexu. Tento dokument je běžně "index.html".

---

## <a name="upload-files"></a>Nahrání souborů 

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V těchto pokynech se dozvíte, jak nahrávat soubory pomocí verze Průzkumník služby Storage, která se zobrazí v Azure Portal. Můžete ale také použít verzi [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) , která se spouští mimo Azure Portal. Můžete použít [AzCopy](../common/storage-use-azcopy-v10.md), POWERSHELL, CLI nebo jakoukoli vlastní aplikaci, která může nahrávat soubory do kontejneru **$Web** svého účtu. Podrobný kurz, který nahrává soubory pomocí sady Visual Studio Code, najdete v tématu [kurz: hostování statického webu na BLOB Storage](./storage-blob-static-website-host.md).

1. Vyberte **Průzkumník služby Storage (Preview)**.

2. Rozbalte uzel **kontejnery objektů BLOB** a potom vyberte kontejner **$Web** .

3. Klikněte na tlačítko **nahrát** a nahrajte soubory.

   ![Nahrání souborů](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Pokud máte v úmyslu zobrazit obsah souboru v prohlížeči, ujistěte se, že je typ obsahu tohoto souboru nastavený na `text/html` . 

   ![Kontrolovat typy obsahu](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Průzkumník služby Storage automaticky nastaví tuto vlastnost na `text/html` pro běžně rozpoznaná rozšíření, jako je například `.html` . V některých případech je však budete muset nastavit sami. Pokud tuto vlastnost nenastavíte na `text/html` , prohlížeč vyzve uživatele ke stažení souboru místo vykreslování obsahu. Tuto vlastnost nastavíte tak, že kliknete pravým tlačítkem na soubor a potom kliknete na **vlastnosti**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

V tomto příkladu se předpokládá, že spouštíte příkazy z Azure Cloud Shell relace.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Pokud prohlížeč vyzývá uživatele, aby si soubor stáhli místo vykreslování obsahu, můžete `--content-type 'text/html; charset=utf-8'` k příkazu připojit. 

* Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

* Nahraďte `<source-path>` zástupný text cestou k umístění souborů, které chcete odeslat.

> [!NOTE]
> Pokud používáte instalaci rozhraní příkazového řádku Azure CLI, můžete použít cestu k libovolnému umístění v místním počítači (například: `C:\myFolder` .
>
> Pokud používáte Azure Cloud Shell, budete muset odkazovat na sdílenou složku, která je viditelná pro Cloud Shell. Toto umístění může být sdílená složka samotné sdílené složky cloudu nebo existující sdílená složka, kterou připojujete z Cloud Shell. Další informace o tom, jak to udělat, najdete [v tématu trvalé soubory v Azure Cloud Shell](../../cloud-shell/persisting-shell-storage.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nahrajte objekty do kontejneru *$Web* ze zdrojového adresáře.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Pokud prohlížeč vyzývá uživatele, aby si soubor stáhli místo vykreslování obsahu, můžete `-Properties @{ ContentType = "text/html; charset=utf-8";}` k příkazu připojit.

* Nahraďte `<path-to-file>` hodnotu zástupného symbolu úplnou cestou k souboru, který chcete nahrát (například: `C:\temp\index.html` ).

* Nahraďte `<blob-name>` hodnotu zástupného symbolu názvem, který chcete výslednému objektu BLOB poskytnout (například: `index.html` ).

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Najít adresu URL webu

Stránky vaší lokality můžete zobrazit v prohlížeči pomocí veřejné adresy URL webu.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V podokně, které se zobrazí vedle stránky přehled účtu úložiště, vyberte možnost **statický web**. Adresa URL vašeho webu se zobrazí v poli **primární koncový bod** .

![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí následujícího příkazu Najděte veřejnou adresu URL vašeho statického webu:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

* Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pomocí následujícího příkazu Najděte veřejnou adresu URL vašeho statického webu:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Nahraďte `<resource-group-name>` hodnotu zástupného symbolu názvem vaší skupiny prostředků.

* Nahraďte `<storage-account-name>` hodnotu zástupného symbolu názvem vašeho účtu úložiště.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Povolit metriky na statických stránkách webu

Po povolení metrik se na řídicím panelu metriky nahlásí Statistiky provozu souborů v kontejneru **$Web** .

1. V nabídce účet úložiště v části **monitorování** klikněte na **metriky** .

   > [!div class="mx-imgBorder"]
   > ![Odkaz na metriky](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Data metrik se generují zapojováním do různých rozhraní API metrik. Portál zobrazí pouze členy rozhraní API používané v daném časovém rámci, aby bylo možné se zaměřit pouze na členy, kteří vracejí data. Aby bylo zajištěno, že budete moci vybrat potřebného člena rozhraní API, je prvním krokem rozšíření časového rámce.

2. Klikněte na tlačítko časový rámec, zvolte časový rámec a pak klikněte na **použít**.

   ![Časový rozsah metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. V rozevíracím seznamu *obor názvů* vyberte **objekt BLOB** .

   ![Obor názvů metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Pak vyberte metriku **odchozího** přenosu dat.

   ![Snímek obrazovky zobrazující neAzure Storageou výstupní metriku statického webu websites](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. V selektoru *agregace* vyberte **Sum** .

   ![Agregace metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klikněte na tlačítko **Přidat filtr** a v voliči *vlastností* vyberte možnost **název rozhraní API** .

   ![Název rozhraní API metrik Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Zaškrtněte políčko vedle položky **GetWebContent** v selektoru *hodnot* a naplňte sestavu metriky.

   ![GetWebContent metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Zaškrtávací políčko **GetWebContent** se zobrazí pouze v případě, že byl tento člen rozhraní API použit v daném časovém rámci. Portál zobrazí pouze členy rozhraní API používané v daném časovém rámci, aby bylo možné se zaměřit pouze na členy, kteří vracejí data. Pokud v tomto seznamu nemůžete najít konkrétního člena rozhraní API, rozbalte časový rámec.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak nakonfigurovat vlastní doménu s vaším statickým webem. Viz téma [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md).