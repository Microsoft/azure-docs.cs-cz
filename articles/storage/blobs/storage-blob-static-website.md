---
title: Hostování statického webu ve službě Azure Storage
description: Azure Storage statické hostování webů a poskytuje nákladově efektivní a škálovatelné řešení pro hostování moderních webových aplikací.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.custom: devx-track-javascript
ms.openlocfilehash: b8864201fc5bf86a5451c790a51141cee46bffeb
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432515"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage

Statický obsah (HTML, CSS, JavaScript a soubory obrázků) můžete obsluhovat přímo z kontejneru úložiště s názvem *$Web*. Hostování obsahu v Azure Storage umožňuje používat architektury bez serveru, které zahrnují [Azure Functions](/azure/azure-functions/functions-overview) a jiné služby PaaS (Platform as a Service).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

> [!NOTE]
> Pokud vaše lokalita závisí na kódu na straně serveru, použijte místo toho [Azure App Service](/azure/app-service/overview) .
Ujistěte se, že jste vytvořili účet úložiště úrovně Standard pro obecné účely v2. Statické weby nejsou k dispozici v žádném jiném typu účtu úložiště.

## <a name="setting-up-a-static-website"></a>Nastavení statického webu

Hostování statického webu je funkce, kterou musíte povolit v účtu úložiště.

Pokud chcete povolit hostování statických webů, vyberte název výchozího souboru a pak zadejte cestu k vlastní stránce 404. Pokud kontejner úložiště objektů BLOB s názvem **$Web** v účtu ještě neexistuje, vytvoří se pro vás jeden. Přidejte soubory vašeho webu do tohoto kontejneru.

Podrobné pokyny najdete [v tématu hostování statického webu v Azure Storage](storage-blob-static-website-how-to.md).

![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Soubory v kontejneru **$Web** rozlišují velká a malá písmena, obsluhovány prostřednictvím požadavků anonymního přístupu a jsou k dispozici pouze prostřednictvím operací čtení.

## <a name="uploading-content"></a>Nahrává se obsah

Pomocí kteréhokoli z těchto nástrojů můžete nahrát obsah do kontejneru **$Web** :

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Modul Azure PowerShellu](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozšíření editoru Visual Studio Code](/azure/developer/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Zobrazení obsahu

Uživatelé mohou zobrazit obsah webu z prohlížeče pomocí veřejné adresy URL webu. Adresu URL můžete najít pomocí Azure Portal, Azure CLI nebo PowerShellu. Viz [najít adresu URL webu](storage-blob-static-website-how-to.md#portal-find-url).

Pokud server vrátí chybu 404 a nezadali jste dokument s chybou, když jste web povolili, bude uživateli vrácena výchozí stránka 404.

> [!NOTE]
> Pro statický web není podporována [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

### <a name="regional-codes"></a>Regionální kódy

Adresa URL vašeho webu obsahuje regionální kód. Například adresa URL `https://contosoblobaccount.z22.web.core.windows.net/` obsahuje regionální kód `z22` .

I když tento kód musí zůstat v adrese URL, je k dispozici pouze pro interní použití a tento kód nebude nutné používat žádným jiným způsobem.

Indexový dokument, který zadáte při povolování hostování statických webů, se zobrazí, když uživatelé otevřou web a nezadáte konkrétní soubor (například: `https://contosoblobaccount.z22.web.core.windows.net` ).

### <a name="secondary-endpoints"></a>Sekundární koncové body

Pokud nastavíte [redundanci v sekundární oblasti](../common/storage-redundancy.md#redundancy-in-a-secondary-region), můžete k obsahu webu získat přístup také pomocí sekundárního koncového bodu. Vzhledem k tomu, že jsou data replikována do sekundárních oblastí asynchronně, nejsou soubory, které jsou k dispozici v sekundárním koncovém bodu, synchronizovány se soubory, které jsou dostupné na primárním koncovém bodu.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Dopad nastavení úrovně veřejného přístupu webového kontejneru

Můžete upravit úroveň veřejného přístupu kontejneru **$Web** , ale nemá žádný vliv na primární koncový bod statického webu, protože tyto soubory jsou obsluhovány prostřednictvím požadavků anonymního přístupu. To znamená, že veřejný přístup ke všem souborům (jen pro čtení).

Následující snímek obrazovky ukazuje nastavení úrovně veřejného přístupu v Azure Portal:

![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/anonymous-read-access-configure/configure-public-access-container.png)

I když primární koncový bod statického webu není ovlivněný, změna úrovně veřejného přístupu ovlivní primární koncový bod služby BLOB Service.

Pokud například změníte úroveň veřejného přístupu kontejneru **$Web** z **privátního (bez anonymního přístupu)** do **objektu BLOB (anonymní přístup pro čtení jenom pro objekty BLOB)**, pak se úroveň veřejného přístupu k primárnímu koncovému bodu statického webu `https://contosoblobaccount.z22.web.core.windows.net/index.html` nezmění.

Veřejný přístup k primárnímu koncovému bodu služby BLOB Service se ale `https://contosoblobaccount.blob.core.windows.net/$web/index.html` změní z Private na Public. Nyní mohou uživatelé tento soubor otevřít pomocí některého z těchto dvou koncových bodů.

Zakázání veřejného přístupu na účtu úložiště nemá vliv na statické weby, které jsou hostované v tomto účtu úložiště. Další informace najdete v tématu [Konfigurace anonymního veřejného přístupu pro čtení pro kontejnery a objekty blob](anonymous-read-access-configure.md).

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapování vlastní domény na statickou adresu URL webu

Svůj statický Web můžete zpřístupnit prostřednictvím vlastní domény.

Pro vlastní doménu je snazší povolit přístup HTTP, protože je Azure Storage nativně podporuje. Pokud chcete povolit protokol HTTPS, musíte použít Azure CDN, protože Azure Storage ještě nativně nepodporuje protokol HTTPS s vlastními doménami. Podrobné pokyny najdete v tématu [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md) .

Pokud je účet úložiště nakonfigurovaný tak, aby [vyžadoval zabezpečený přenos](../common/storage-require-secure-transfer.md) přes protokol HTTPS, musí uživatelé použít koncový bod HTTPS.

> [!TIP]
> Zvažte hostování vaší domény v Azure. Další informace najdete v tématu [hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Přidávání hlaviček protokolu HTTP

V rámci funkce statického webu neexistuje žádný způsob, jak konfigurovat hlavičky. Můžete však použít Azure CDN k přidání hlaviček a k přidávání (nebo přepisování) hodnot hlaviček. Přečtěte si [referenční informace k modulu Standard rules pro Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Pokud chcete použít hlavičky k řízení ukládání do mezipaměti, přečtěte si téma [řízení Azure CDN chování při ukládání do mezipaměti s pravidly pro ukládání do](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)mezipaměti.

## <a name="pricing"></a>Ceny

Můžete povolit hostování statických webů zdarma. Účtuje se vám jenom úložiště objektů blob, které vaše lokalita využívá, a provozní náklady. Další podrobnosti o cenách pro Azure Blob Storage najdete na [stránce s cenami azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriky

Metriky můžete povolit na statických stránkách webu. Po povolení metrik se na řídicím panelu metriky nahlásí Statistiky provozu souborů v kontejneru **$Web** .

Pokud chcete povolit metriky na stránkách statického webu, přečtěte si téma [Povolení metrik na statických stránkách webu](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Další kroky

* [Hostování statického webu v Azure Storage](storage-blob-static-website-how-to.md)
* [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Sestavení první webové aplikace bez serveru](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
