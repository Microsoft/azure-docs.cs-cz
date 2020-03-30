---
title: Hostování statického webu ve službě Azure Storage
description: Azure Storage statické webhosting, poskytuje nákladově efektivní, škálovatelné řešení pro hostování moderních webových aplikací.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370487"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage

Statický obsah (HTML, CSS, JavaScript a obrazové soubory) můžete poskytovat přímo z kontejneru úložiště s názvem *$web*. Hostování obsahu ve službě Azure Storage umožňuje používat architektury bez serveru, které zahrnují [funkce Azure](/azure/azure-functions/functions-overview) a další služby Platform as a service (PaaS).

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Pokud váš web závisí na kódu na straně serveru, použijte místo toho [službu Azure App Service.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Nastavení statického webu

Statický hosting webových stránek je funkce, kterou musíte povolit na účtu úložiště.

Chcete-li povolit statický web hosting, vyberte název výchozího souboru a volitelně zadejte cestu k vlastní stránce 404. Pokud kontejner úložiště objektů blob s názvem **$web** v účtu ještě neexistuje, vytvoří se pro vás. Přidejte soubory webu do tohoto kontejneru.

Podrobné pokyny najdete v [tématu Hostování statického webu ve službě Azure Storage](storage-blob-static-website-how-to.md).

![Metrika metriky statických webů Azure Storage](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Soubory v **kontejneru $web** rozlišují malá a velká písmena, jsou obsluhovány prostřednictvím anonymních žádostí o přístup a jsou k dispozici pouze prostřednictvím operací čtení.

## <a name="uploading-content"></a>Nahrávání obsahu

K nahrání obsahu do kontejneru **$web** můžete použít některý z těchto nástrojů:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Modul Azure PowerShellu](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Průzkumník úložišť Azure](https://azure.microsoft.com/features/storage-explorer/)
> * [Kanály Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozšíření editoru Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Zobrazení obsahu

Uživatelé mohou zobrazit obsah webu z prohlížeče pomocí veřejné adresy URL webu. Adresu URL najdete pomocí portálu Azure, Azure CLI nebo PowerShellu. Tuto tabulku použijte jako vodítko.

|Nástroj| Doprovodné materiály |
|----|----|
|**Portál Azure** | [Vyhledání adresy URL webu pomocí portálu Azure](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Vyhledání adresy URL webu pomocí příkazového příkazového příkazu Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modul Azure PowerShellu** | [Vyhledání adresy URL webu pomocí PowerShellu](storage-blob-static-website-how-to.md#powershell-find-url) |

Adresa URL vašeho webu obsahuje regionální kód. Například adresa `https://contosoblobaccount.z22.web.core.windows.net/` URL `z22`obsahuje místní kód .

Zatímco tento kód musí zůstat v adrese URL, je pouze pro interní použití a nebudete muset použít tento kód jiným způsobem.

Dokument indexu, který zadáte při povolení statického hostingu webu, se zobrazí, když uživatelé `https://contosoblobaccount.z22.web.core.windows.net`otevřou web a nezadají konkrétní soubor (například: ).  

Pokud server vrátí chybu 404 a při povolení webu jste nezadali chybový dokument, vrátí se uživateli výchozí stránka 404.

> [!NOTE]
> [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) není podporován statickým webem.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Dopad nastavení úrovně veřejného přístupu webového kontejneru

Můžete upravit úroveň veřejného přístupu **kontejneru $web,** ale to nemá žádný vliv na primární statický koncový bod webu, protože tyto soubory jsou obsluhovány prostřednictvím anonymních žádostí o přístup. To znamená veřejný (jen pro čtení) přístup ke všem souborům.

Následující snímek obrazovky ukazuje nastavení úrovně veřejného přístupu na webu Azure Portal:

![Snímek obrazovky znázorňující nastavení úrovně veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Zatímco primární statický koncový bod webu není ovlivněn, změna úrovně veřejného přístupu nemá vliv na koncový bod primární služby objektů blob.

Pokud například změníte úroveň veřejného přístupu **kontejneru $web** z **private (bez anonymního přístupu)** na **objekt Blob (anonymní přístup pro čtení pouze pro objekty BLOB)**, úroveň veřejného přístupu k primárnímu statickému koncovému bodu `https://contosoblobaccount.z22.web.core.windows.net/index.html` webu se nezmění.

Veřejný přístup ke koncovému bodu `https://contosoblobaccount.blob.core.windows.net/$web/index.html` primární služby objektů blob se však změní ze soukromého na veřejný. Nyní mohou uživatelé tento soubor otevřít pomocí některého z těchto dvou koncových bodů.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Mapování vlastní domény na statickou adresu URL webu

Statické webové stránky můžete zpřístupnit prostřednictvím vlastní domény. 

Je jednodušší povolit přístup HTTP pro vaši vlastní doménu, protože Azure Storage ji nativně podporuje. Chcete-li povolit protokol HTTPS, budete muset použít Azure CDN, protože Azure Storage ještě nativně nepodporuje protokol HTTPS s vlastními doménami. podrobné pokyny najdete [v tématu Mapování vlastní domény na koncový bod úložiště objektů blob Azure.](storage-custom-domain-name.md)

Pokud je účet úložiště nakonfigurován tak, aby [vyžadoval zabezpečený přenos](../common/storage-require-secure-transfer.md) přes protokol HTTPS, musí uživatelé použít koncový bod HTTPS. 

> [!TIP]
> Zvažte hostování domény v Azure. Další informace najdete [v tématu Hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>Přidání záhlaví protokolu HTTP

Neexistuje žádný způsob, jak nakonfigurovat záhlaví jako součást statické funkce webu. Azure CDN však můžete použít k přidání záhlaví a připojit (nebo přepsat) hodnoty záhlaví. Viz [Odkaz na modul standardních pravidel pro Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Pokud chcete k řízení ukládání do mezipaměti použít záhlaví, přečtěte si informace o [řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](https://docs.microsoft.com/azure/cdn/cdn-caching-rules).

## <a name="pricing"></a>Ceny

Můžete povolit statické webové stránky hosting zdarma. Fakturuje se vám jenom úložiště objektů blob, které váš web využívá, a provozní náklady. Další podrobnosti o cenách pro Azure Blob Storage najdete na [stránce s cenami úložiště objektů blob Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriky

Metriky můžete povolit na statických webových stránkách. Jakmile aktivujete metriky, statistiky návštěvnosti souborů v **kontejneru $web** se vykazují na řídicím panelu metrik.

Informace o povolení metrik na statických stránkách webu najdete v tématu [Povolení metrik na statických stránkách webu](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Další kroky

* [Hostování statického webu ve službě Azure Storage](storage-blob-static-website-how-to.md)
* [Mapování vlastní domény na koncový bod úložiště objektů blob Azure](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Vytvoření první webové aplikace bez serveru](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
