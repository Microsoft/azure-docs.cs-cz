---
title: Hostování statického webu ve službě Azure Storage
description: Azure Storage statický web hostovat, poskytuje nákladově efektivní, škálovatelné řešení pro hostování moderních webových aplikací.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/19/18
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 0663f569edd0dec47949053b6ecf7fe49fa24a17
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229298"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage
Účty úložiště GPv2 Azure umožňují poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru úložiště s názvem *$web*. Využití výhod hostování ve službě Azure Storage umožňuje používat architektur bez serveru, včetně [Azure Functions](/azure/azure-functions/functions-overview) a další služby PaaS.

Na rozdíl od hostoval statický web, jsou dynamické weby, které závisí na kódu na straně serveru nejlépe hostované pomocí [služby Azure App Service](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Jak to funguje?
Při povolení statického webu hostování na vašem účtu úložiště, vyberte název výchozího souboru a volitelně můžete zadat cestu k vlastní stránka 404. Protože tato funkce zapnutá, kontejner s názvem *$web* se vytvoří, pokud ještě neexistuje.

Soubory *$web* kontejneru jsou:

- odesílání prostřednictvím žádosti o anonymní přístup
- k dispozici pouze prostřednictvím objektu operace čtení
- malá a velká písmena
- k dispozici pro veřejné webové podle tohoto vzoru:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- k dispozici prostřednictvím koncového bodu úložiště objektů Blob podle tohoto vzoru:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Koncový bod služby Blob storage slouží k nahrávání souborů. Například soubor nahrát do tohoto umístění:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

je k dispozici v prohlížeči v místě, například takto:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

Vybrané výchozí název souboru se používá v kořenovém adresáři a všech jeho podadresářích, když není zadaný název souboru. Pokud nezadáte cesta dokumentu chyby serveru vrátí kód 404, je výchozí 404 – Stránka vrátí uživateli.

## <a name="cdn-and-ssl-support"></a>Podpora CDN a SSL

Aby váš statický web soubory k dispozici prostřednictvím protokolu HTTPS, naleznete v tématu [přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md). Jako součást tohoto procesu musíte *vaší sítě CDN přejděte na koncový bod webové* na rozdíl od koncový bod objektu blob. Budete muset počkat několik minut, než váš obsah je viditelné, protože konfigurace CDN není spuštěna ihned.


## <a name="custom-domain-names"></a>Vlastní názvy domén

Je možné [konfigurace vlastního názvu domény pro váš účet Azure Storage](storage-custom-domain-name.md) statického webu zpřístupnit prostřednictvím vlastní domény. Podrobný rozbor hostovat svoji doménu na [Azure, viz hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Ceny
Hostoval statický web je k dispozici bez dalších poplatků. Další podrobnosti o cenách pro službu Azure Blob Storage, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Rychlý start

### <a name="azure-portal"></a>portál Azure
Začněte tím, že otevřete Azure portal na adrese https://portal.azure.com a spustit na vašem účtu úložiště GPv2 pomocí následujících kroků:

1. Klikněte na **nastavení**
2. Klikněte na **statického webu**
3. Zadejte *název dokumentu indexu*. (Je společné hodnoty *index.html)*
4. Volitelně můžete zadat *cesta dokumentu chyby* na vlastní stránka 404. (Je společné hodnoty *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

V dalším kroku nahrajte vaše prostředky k *$web* kontejnerů pomocí webu Azure portal nebo pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) nahrát celou adresáře. Nezapomeňte zahrnout soubor, který odpovídá *název dokumentu indexu* jste vybrali při povolení funkce.

A konečně přejděte na koncový bod webové můžete otestovat váš web.

### <a name="azure-cli"></a>Azure CLI
Nainstalujte rozšíření ve verzi preview úložiště:

```azurecli-interactive
az extension add --name storage-preview
```
V případě více předplatných nastavte předplatné účtu úložiště GPv2, které chcete povolit svém rozhraní příkazového řádku:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
Povolení této funkce. Nezapomeňte nahradit všechny zástupné hodnoty, včetně závorkách a s vlastními hodnotami:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
Dotaz adresy URL koncového bodu webu:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Nahrání objektů *$web* kontejneru ze zdrojového adresáře. Ujistěte se, zda jste správně řídicí odkaz na *$web* kontejneru v příkazu. Například pokud používáte Azure CLI z cloud Shell na webu Azure Portal, řídicí *$web* kontejneru, jak je znázorněno:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Nasazení

Metody dostupné pro nasazení obsahu do kontejneru úložiště zahrnují následující:

- [AzCopy](../common/storage-use-azcopy.md)
- [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/)
- [Kanály Azure](https://code.visualstudio.com/tutorials/static-website/deploy-VSTS)
- [Rozšíření sady Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

Ve všech případech, ujistěte se, že kopírování souborů do *$web* kontejneru.

## <a name="metrics"></a>Metriky

K zapnutí metrik na stránkách statický web, klikněte na **nastavení** > **monitorování** > **metriky**.

Můžete například měřená data jsou generovány zapojení do různých metrik rozhraní API. Na portálu se zobrazí pouze členové rozhraní API použít v daném časovém rámci, abychom se mohli zaměřit jenom na členy, které nevracejí data. Aby se zajistilo, že budete moct vybrat nezbytné člena rozhraní API, prvním krokem je rozšířit časový rámec.

Klikněte na tlačítko časový rámec a vyberte **posledních 24 hodin** a potom klikněte na tlačítko **použít**

![Metriky Azure Storage statických webů časový rozsah](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

V dalším kroku vyberte **Blob** z *Namespace* rozevírací seznam.

![Obor názvů metriky statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Vyberte **odchozího přenosu dat** metriku.

![Azure Storage statických webů metriky metrika](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Vyberte **součet** z *agregace* selektor.

![Azure Storage statických webů metriky agregace](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Klikněte **přidat filtr** tlačítko a zvolte **název rozhraní API** z *vlastnost* selektor.

![Název metriky rozhraní API statických webů služby Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

A konečně, zaškrtněte políčko vedle položky **GetWebContent** v *hodnoty* selektor k naplnění sestavu metrik.

![Azure Storage metrics statických webů GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Po povolení provozu statistiky na soubory v *$web* kontejneru jsou hlášeny v řídicím panelu metrik.

## <a name="faq"></a>Nejčastější dotazy

**Funkce statických webů je k dispozici pro všechny typy účtů úložiště?**  
Ne, hostoval statický web je dostupná pouze v účtech GPv2 úložiště úrovně standard.

**Jsou virtuální sítě úložiště a pravidla brány firewall podporované na nový webový koncový bod?**  
Ano, nový webový koncový bod dodržuje pravidla virtuální sítě a brány firewall nakonfigurovaná pro účet úložiště.

**Webový koncový bod je velká a malá písmena?**  
Ano, je velká a malá písmena stejně jako koncový bod služby blob webový koncový bod. 

## <a name="next-steps"></a>Další postup
* [Přístup k objektům blob s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro koncový bod služby blob nebo web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Vytvořte svoji první aplikaci bez serveru web](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
