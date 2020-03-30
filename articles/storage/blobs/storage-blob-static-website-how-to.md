---
title: Hostování statického webu ve službě Azure Storage
description: Zjistěte, jak poskytovat statický obsah (HTML, CSS, JavaScript a obrazové soubory) přímo z kontejneru v účtu GPv2 úložiště Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247006"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage

Statický obsah (HTML, CSS, JavaScript a obrazové soubory) můžete poskytovat přímo z kontejneru v účtu GPv2 úložiště Azure. Další informace najdete [v tématu Statický hosting webů ve službě Azure Storage](storage-blob-static-website.md).

Tento článek ukazuje, jak povolit statické hostování webů pomocí portálu Azure, Azure CLI nebo PowerShellu.

## <a name="enable-static-website-hosting"></a>Povolit statický hosting webových stránek

Statický hosting webových stránek je funkce, kterou musíte povolit na účtu úložiště.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Chcete-li začít, přihlaste se na [portál Azure.](https://portal.azure.com/)

2. Vyhledejte svůj účet úložiště a zobrazte přehled účtu.

3. Vyberte **Statický web,** chcete-li zobrazit konfigurační stránku pro statické weby.

4. Výběrem **možnosti Povoleno** povolíte statický hosting webu pro účet úložiště.

5. V poli **název dokumentu Rejstříku** zadejte výchozí stránku indexu (například: *index.html*). 

   Výchozí stránka indexu se zobrazí, když uživatel přejde do kořenového adresáře statického webu.  

6. V poli **Cesta dokumentu Chyba** zadejte výchozí chybovou stránku (například *404.html*). 

   Výchozí chybová stránka se zobrazí, když se uživatel pokusí přejít na stránku, která na statickém webu neexistuje.

7. Klikněte na **Uložit**. Portál Azure teď zobrazuje koncový bod statického webu. 

    ![Povolení statického hostování webových stránek pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli" />

Statické hostování webových stránek můžete povolit pomocí [rozhraní Příkazového řádku Azure (CLI).](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

1. Nejprve otevřete [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), nebo pokud jste [nainstalovali](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI místně, otevřete aplikaci konzoly příkazů, jako je Windows PowerShell.

2. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický web.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Nahraďte `<subscription-id>` zástupnou hodnotu ID předplatného.

3. Povolte statický hosting webových stránek.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

   * Nahraďte `<error-document-name>` zástupný symbol názvem chybového dokumentu, který se uživatelům zobrazí, když prohlížeč požádá o stránku na vašem webu, která neexistuje.

   * Nahraďte `<index-document-name>` zástupný symbol názvem rejstříkového dokumentu. Tento dokument je obvykle "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

Statické hostování webových stránek můžete povolit pomocí modulu Azure PowerShell.

1. Otevřete příkazové okno prostředí Windows PowerShell.

2. Ověřte, že máte modul Azure PowerShell Az verze 0.7 nebo novější.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

3. Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

   ```powershell
   Connect-AzAccount
   ```

4. Pokud je vaše identita přidružená k více než jednomu předplatnému, nastavte aktivní předplatné na předplatné účtu úložiště, který bude hostovat váš statický web.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Nahraďte `<subscription-id>` zástupnou hodnotu ID předplatného.

5. Získejte kontext účtu úložiště, který definuje účet úložiště, který chcete použít.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * `<resource-group-name>` Nahraďte zástupnou hodnotu názvem skupiny prostředků.

   * `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

6. Povolte statický hosting webových stránek.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Nahraďte `<error-document-name>` zástupný symbol názvem chybového dokumentu, který se uživatelům zobrazí, když prohlížeč požádá o stránku na vašem webu, která neexistuje.

   * Nahraďte `<index-document-name>` zástupný symbol názvem rejstříkového dokumentu. Tento dokument je obvykle "index.html".

---

## <a name="upload-files"></a>Nahrání souborů 

### <a name="portal"></a>[Portál](#tab/azure-portal)

Tyto pokyny ukazují, jak nahrát soubory pomocí verze Průzkumníka úložiště, která se zobrazí na webu Azure Portal. Můžete však také použít verzi [Průzkumníka úložiště,](https://azure.microsoft.com/features/storage-explorer/) která běží mimo portál Azure. Můžete použít [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI nebo jakoukoli vlastní aplikaci, která může nahrát soubory do **kontejneru $web** vašeho účtu. Podrobný kurz, který nahrává soubory pomocí kódu Sady Visual Studio, najdete v [článku Kurz: Hostování statického webu v úložišti objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Vyberte **Průzkumník a úložiště (náhled).**

2. Rozbalte uzel **KONTEJNERY BLOB** a vyberte **kontejner $web.**

3. Zvolte tlačítko **Odeslat** pro nahrávání souborů.

   ![Nahrání souborů](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Pokud chcete, aby prohlížeč zobrazil obsah souboru, ujistěte se, `text/html`že je typ obsahu tohoto souboru nastaven na . 

   ![Kontrola typů obsahu](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Průzkumník úložiště automaticky nastaví tuto vlastnost pro `text/html` `.html`běžně rozpoznaná rozšíření, například . V některých případech to však budete muset nastavit sami. Pokud tuto vlastnost nenastavíte na `text/html`, prohlížeč vyzve uživatele ke stažení souboru namísto vykreslení obsahu. Chcete-li tuto vlastnost nastavit, klepněte pravým tlačítkem myši na soubor a potom klepněte na příkaz **Vlastnosti**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nahrajte objekty do *kontejneru $web* ze zdrojového adresáře.

> [!NOTE]
> Pokud používáte Azure Cloud Shell, nezapomeňte `\` přidat řídicí znak při `$web` odkazování na `\$web`kontejner (například: ). Pokud používáte místní instalaci příkazového řádku Azure, pak nebudete muset použít řídicí znak.

Tento příklad předpokládá, že spouštěte příkazy z relace Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

* Nahraďte `<source-path>` zástupný symbol cestou k umístění souborů, které chcete nahrát.

> [!NOTE]
> Pokud používáte instalaci umístění azure cli, pak můžete použít cestu do libovolného umístění `C:\myFolder`v místním počítači (například: .
>
> Pokud používáte Azure Cloud Shell, budete muset odkazovat na sdílenou složku, která je viditelná pro Cloud Shell. Toto umístění může být sdílená složka samotné sdílené složky cloudu nebo existující sdílená složka, kterou připojíte z prostředí Cloud Shell. Další informace o tom, jak to udělat, najdete [v tématu Zachovat soubory v Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nahrajte objekty do *kontejneru $web* ze zdrojového adresáře.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* `<path-to-file>` Nahraďte zástupnou hodnotu plně kvalifikovanou cestou k souboru, který chcete nahrát (například: `C:\temp\index.html`).

* Nahraďte `<blob-name>` zástupnou hodnotu názvem, který chcete zařadit do `index.html`výsledného objektu blob (například: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Vyhledání adresy URL webu pomocí portálu Azure

Stránky webu můžete zobrazit z prohlížeče pomocí veřejné adresy URL webu.

### <a name="portal"></a>[Portál](#tab/azure-portal)

<a id="portal-find-url" />

V podokně, které se zobrazí vedle stránky s přehledem účtu účtu vašeho účtu úložiště, vyberte **Statický web**. Adresa URL vašeho webu se zobrazí v poli **Primární koncový bod.**

![Metrika metriky statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli-find-url" />

Najděte veřejnou adresu URL statického webu pomocí následujícího příkazu:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

* `<resource-group-name>` Nahraďte zástupnou hodnotu názvem skupiny prostředků.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Najděte veřejnou adresu URL statického webu pomocí následujícího příkazu:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* `<resource-group-name>` Nahraďte zástupnou hodnotu názvem skupiny prostředků.

* `<storage-account-name>` Nahraďte zástupnou hodnotu názvem svého účtu úložiště.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Povolení metrik na statických webových stránkách

Jakmile aktivujete metriky, statistiky návštěvnosti souborů v **kontejneru $web** se vykazují na řídicím panelu metrik.

1. V části **Monitor** v nabídce účtu úložiště klikněte na **Metriky.**

   > [!div class="mx-imgBorder"]
   > ![Odkaz na metriky](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > Data metrik y jsou generovány hákování do různých metrik api. Portál zobrazuje pouze členy rozhraní API používané v daném časovém rámci, aby se zaměřily pouze na členy, které vracejí data. Chcete-li zajistit, že budete moci vybrat potřebného člena rozhraní API, je prvním krokem rozšíření časového rámce.

2. Klikněte na tlačítko časového rámce, zvolte časový rámec a pak klepněte na **tlačítko Použít**.

   ![Časový rozsah metrik statických webů azure storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. V rozevíracím souboru *Obor názvů* vyberte **objekt Blob.**

   ![Azure Storage statické weby metriky obor názvů](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Pak vyberte metriku **odchozího přenosu.**

   ![Metrika metriky statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Z voliče *agregace* vyberte **Součet.**

   ![Azure Storage statické weby agregace metriky](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Klikněte na tlačítko **Přidat filtr** a zvolte název **rozhraní API** z voliče *vlastností.*

   ![Název rozhraní API statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Chcete-li vyplnit přehled metrik, zaškrtněte políčko vedle položky **GetWebContent** ve voliči *hodnot.*

   ![Metriky statických webů Azure Storage GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > Zaškrtávací políčko **GetWebContent** se zobrazí pouze v případě, že člen rozhraní API byl použit v daném časovém rámci. Portál zobrazuje pouze členy rozhraní API používané v daném časovém rámci, aby se zaměřily pouze na členy, které vracejí data. Pokud nemůžete najít konkrétního člena rozhraní API v tomto seznamu, rozbalte časový rámec.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak nakonfigurovat vlastní doménu pomocí statického webu. Viz [Mapování vlastní domény na koncový bod úložiště objektů blob Azure](storage-custom-domain-name.md).

