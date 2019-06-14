---
title: Hostování statického webu ve službě Azure Storage
description: Zjistěte, jak poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru v účtu úložiště GPv2 Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 61477767c59dd521e3f46db4445238a5a1ea759e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071439"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage

Můžete poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru v účtu úložiště GPv2 Azure. Další informace najdete v tématu [hostoval statický web ve službě Azure Storage](storage-blob-static-website.md).

Tento článek ukazuje, jak povolit hostoval statický web s využitím webu Azure portal, Azure CLI nebo Powershellu.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Podrobný návod najdete v části [kurzu: Hostování statického webu ve službě Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Po povolení statický web se zobrazí na stránkách webu z prohlížeče pomocí veřejné adresy URL webu.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Vyhledat adresu URL webu pomocí webu Azure portal

V podokně, které se zobrazí vedle stránka s přehledem účet vašeho účtu úložiště vyberte **statického webu**. Adresa URL vašeho webu se zobrazí v **primární koncový bod** pole.

![Azure Storage statických webů metriky metrika](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Použití Azure CLI

Můžete povolit pomocí hostoval statický web [rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Nejprve otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), nebo pokud jste [nainstalované](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI místně, spusťte příkaz konzolové aplikace jako je Windows PowerShell.

2. Z příkazového okna, kterou jste otevřeli instalaci rozšíření úložiště ve verzi preview.

   ```azurecli-interactive
   az extension add --name storage-preview
   ```

3. Pokud vaše identity je přidruženo více než jedno předplatné, nastavte vaše aktivní předplatné k odběru účtu úložiště, který bude hostovat váš statický Web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahradit `<subscription-id>` zástupnou hodnotu ID předplatného.

4. Povolte hostoval statický Web.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Nahradit `<storage-account-name>` zástupnou hodnotu s názvem účtu úložiště.

   * Nahradit `<error-document-name>` zástupný text názvu dokumentu chyby, které se zobrazí uživatelům, když prohlížeč požádá o stránku na webu, který neexistuje.

   * Nahradit `<index-document-name>` zástupný text na název dokumentu indexu. Tento dokument se často "index.html".

5. Nahrání objektů *$web* kontejneru ze zdrojového adresáře.

   > [!NOTE]
   > Pokud používáte Azure Cloud Shell, ujistěte se, že chcete přidat `\` řídicí znak k odkazování na `$web` kontejneru (například: `\$web`). Pokud používáte místní instalaci rozhraní příkazového řádku Azure, pak nebudete muset použít řídicí znak.

   Tento příklad předpokládá, že spouštíte příkazy z relace Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Nahradit `<storage-account-name>` zástupnou hodnotu s názvem účtu úložiště.

   * Nahradit `<source-path>` zástupný symbol cestu k umístění souborů, které chcete nahrát.

   > [!NOTE]
   > Pokud používáte umístění instalace Azure CLI, pak můžete použít cestu do jakéhokoli umístění na místním počítači (například: `C:\myFolder`.
   >
   > Pokud používáte Azure Cloud Shell, musíte odkazovat na sdílenou složku, která je viditelná pro Cloud Shell. Toto umístění může být sdílené cloudu sdílet samotné nebo existující sdílenou složku, která připojení ze služby Cloud Shell. Zjistěte, jak to provést, najdete v článku [trvalému ukládání souborů ve službě Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Vyhledat adresu URL webu pomocí příkazového řádku Azure

Můžete zobrazit obsah v prohlížeči pomocí veřejné adresy URL webu.

Vyhledejte adresu URL pomocí následujícího příkazu:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Nahradit `<storage-account-name>` zástupnou hodnotu s názvem účtu úložiště.

* Nahradit `<resource-group-name>` zástupnou hodnotu s názvem vaší skupiny prostředků.

<a id="powershell" />

## <a name="use-powershell"></a>Použití prostředí PowerShell

Můžete povolit hostoval statický web pomocí modulu Azure PowerShell.

1. Otevřete příkazové okno prostředí Windows PowerShell.

2. Ověřte, že máte Azure PowerShell verze modulu Az 0.7 nebo novější.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

4. Pokud vaše identity je přidruženo více než jedno předplatné, nastavte vaše aktivní předplatné k odběru účtu úložiště, který bude hostovat váš statický Web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahradit `<subscription-id>` zástupnou hodnotu ID předplatného.

5. Získáte kontext účtu úložiště, který definuje účet úložiště, který chcete použít.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Nahradit `<resource-group-name>` zástupnou hodnotu s názvem vaší skupiny prostředků.

   * Nahradit `<storage-account-name>` zástupnou hodnotu s názvem účtu úložiště.

6. Povolte hostoval statický Web.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Nahradit `<error-document-name>` zástupný text názvu dokumentu chyby, které se zobrazí uživatelům, když prohlížeč požádá o stránku na webu, který neexistuje.

   * Nahradit `<index-document-name>` zástupný text na název dokumentu indexu. Tento dokument se často "index.html".

7. Nahrání objektů *$web* kontejneru ze zdrojového adresáře.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Nahradit `<path-to-file>` zástupnou hodnotu s plně kvalifikovanou cestou k souboru, který chcete nahrát (například: `C:\temp\index.html`).

   * Nahradit `<blob-name>` zástupnou hodnotu s názvem, který chcete poskytnout výsledný objekt blob (například: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Vyhledat adresu URL webu s použitím prostředí PowerShell

Můžete zobrazit obsah v prohlížeči pomocí veřejné adresy URL webu.

Vyhledejte adresu URL pomocí následujícího příkazu:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Nahradit `<resource-group-name>` zástupnou hodnotu s názvem vaší skupiny prostředků.

* Nahradit `<storage-account-name>` zástupnou hodnotu s názvem účtu úložiště.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Zapnutí metrik na stránkách statického webu

Po povolení metrik, statistiky na soubory v provozu **$web** kontejneru jsou hlášeny v řídicím panelu metrik.

1. Klikněte na **nastavení** > **monitorování** > **metriky**.

   Můžete například měřená data jsou generovány zapojení do různých metrik rozhraní API. Na portálu se zobrazí pouze členové rozhraní API použít v daném časovém rámci, abychom se mohli zaměřit jenom na členy, které nevracejí data. Aby se zajistilo, že budete moct vybrat nezbytné člena rozhraní API, prvním krokem je rozšířit časový rámec.

2. Klikněte na tlačítko časový rámec a vyberte **posledních 24 hodin** a potom klikněte na tlačítko **použít**.

   ![Metriky Azure Storage statických webů časový rozsah](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Vyberte **Blob** z *Namespace* rozevírací seznam.

   ![Obor názvů metriky statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Vyberte **odchozího přenosu dat** metriku.

   ![Azure Storage statických webů metriky metrika](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Vyberte **součet** z *agregace* selektor.

   ![Azure Storage statických webů metriky agregace](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klikněte na tlačítko **přidat filtr** tlačítko a zvolte **název rozhraní API** z *vlastnost* selektor.

   ![Název metriky rozhraní API statických webů služby Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. zaškrtávací políčko vedle položky **GetWebContent** v *hodnoty* selektor k naplnění sestavu metrik.

   ![Azure Storage metrics statických webů GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Další postup

* [Hostování statického webu ve službě Azure Storage](storage-blob-static-website.md)
* [Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro koncový bod služby blob nebo web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Vytvořte svoji první aplikaci bez serveru web](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
