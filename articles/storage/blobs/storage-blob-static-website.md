---
title: Hostování statického webu v Azure Storage
description: Azure Storage statické hostování webů a poskytuje nákladově efektivní a škálovatelné řešení pro hostování moderních webových aplikací.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 8dc5599e681d9aee84f884cd4990163a2481d386
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75708158"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostování statického webu v Azure Storage

Statický obsah (HTML, CSS, JavaScript a soubory obrázků) můžete obsluhovat přímo z kontejneru úložiště s názvem *$Web*. Hostování obsahu v Azure Storage umožňuje používat architektury bez serveru, které zahrnují [Azure Functions](/azure/azure-functions/functions-overview) a jiné služby PaaS (Platform as a Service).

> [!NOTE]
> Pokud vaše lokalita závisí na kódu na straně serveru, použijte místo toho [Azure App Service](/azure/app-service/overview) .

## <a name="setting-up-a-static-website"></a>Nastavení statického webu

Hostování statického webu je funkce, kterou musíte povolit v účtu úložiště.

Pokud chcete povolit hostování statických webů, vyberte název výchozího souboru a pak zadejte cestu k vlastní stránce 404. Pokud kontejner úložiště objektů BLOB s názvem **$Web** v účtu ještě neexistuje, vytvoří se pro vás jeden. Přidejte soubory vašeho webu do tohoto kontejneru.

Podrobné pokyny najdete [v tématu hostování statického webu v Azure Storage](storage-blob-static-website-how-to.md).

![Metrika metriky Azure Storage statických webů](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Soubory v kontejneru **$Web** rozlišují velká a malá písmena, obsluhovány prostřednictvím požadavků anonymního přístupu a jsou k dispozici pouze prostřednictvím operací čtení.

## <a name="uploading-content"></a>Nahrává se obsah

Pomocí kteréhokoli z těchto nástrojů můžete nahrát obsah do kontejneru **$Web** :

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Modul Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozšíření Visual Studio Code](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Zobrazení obsahu

Uživatelé mohou zobrazit obsah webu z prohlížeče pomocí veřejné adresy URL webu. Adresu URL můžete najít pomocí Azure Portal, Azure CLI nebo PowerShellu. Tuto tabulku použijte jako vodítko.

|Nástroj| Pokyny |
|----|----|
|**Azure Portal** | [Vyhledejte adresu URL webu pomocí Azure Portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Vyhledání adresy URL webu pomocí rozhraní příkazového řádku Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modul Azure PowerShell** | [Vyhledání adresy URL webu pomocí PowerShellu](storage-blob-static-website-how-to.md#powershell-find-url) |

Adresa URL vašeho webu obsahuje regionální kód. Například adresa URL `https://contosoblobaccount.z22.web.core.windows.net/` obsahuje oblastní `z22`kódu.

I když tento kód musí zůstat v adrese URL, je k dispozici pouze pro interní použití a tento kód nebude nutné používat žádným jiným způsobem.

Indexový dokument, který zadáte při povolování hostování statických webů, se zobrazí, když uživatelé otevřou web a nezadáte konkrétní soubor (například: `https://contosoblobaccount.z22.web.core.windows.net`).  

Pokud server vrátí chybu 404 a nezadali jste dokument s chybou, když jste web povolili, bude uživateli vrácena výchozí stránka 404.

> [!NOTE]
> Pro statický web není podporována [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Dopad nastavení úrovně veřejného přístupu webového kontejneru

Můžete upravit úroveň veřejného přístupu kontejneru **$Web** , ale nemá žádný vliv na primární koncový bod statického webu, protože tyto soubory jsou obsluhovány prostřednictvím požadavků anonymního přístupu. To znamená, že veřejný přístup ke všem souborům (jen pro čtení).

Následující snímek obrazovky ukazuje nastavení úrovně veřejného přístupu v Azure Portal:

![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

I když primární koncový bod statického webu není ovlivněný, změna úrovně veřejného přístupu ovlivní primární koncový bod služby BLOB Service.

Pokud například změníte úroveň veřejného přístupu kontejneru **$Web** z **privátního (bez anonymního přístupu)** do **objektu BLOB (anonymní přístup pro čtení jenom pro objekty BLOB)** , pak se úroveň veřejného přístupu k primárnímu koncovému bodu statického webu `https://contosoblobaccount.z22.web.core.windows.net/index.html` nemění.

Veřejný přístup k primárnímu koncovému bodu služby BLOB Service se ale `https://contosoblobaccount.blob.core.windows.net/$web/index.html` změní z Private na Public. Nyní mohou uživatelé tento soubor otevřít pomocí některého z těchto dvou koncových bodů.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) a podpora protokolu SSL (Secure Socket Layer)

Pokud chcete zajistit dostupnost svých statických webových souborů přes vaši vlastní doménu a HTTPS, přečtěte si téma [použití Azure CDN pro přístup k objektům blob s vlastními doménami přes protokol HTTPS](storage-https-custom-domain-cdn.md). V rámci tohoto procesu musíte síť CDN nasměrovat na primární koncový bod *statického webu* , a to na rozdíl od primárního koncového bodu *služby BLOB Service* . Je možné, že budete muset několik minut počkat, než se obsah zobrazí, protože se konfigurace CDN hned nespustí.

Když aktualizujete svůj statický web, nezapomeňte vymazat obsah uložený v mezipaměti na hraničních serverech CDN vyprázdněním koncového bodu CDN. Další informace najdete v tématu [Vyprázdnění koncového bodu Azure CDN](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> Protokol HTTPS se nativně podporuje prostřednictvím webového koncového bodu účtu, takže je webový koncový bod dostupný přes HTTP i HTTPS. Pokud je ale účet úložiště nakonfigurovaný tak, aby vyžadoval zabezpečený přenos přes protokol HTTPS, musí uživatelé použít koncový bod HTTPS. Další informace najdete v tématu [vyžadování zabezpečeného přenosu v Azure Storage](../common/storage-require-secure-transfer.md).
>
> Použití vlastních domén přes HTTPS vyžaduje v tuto chvíli použití Azure CDN.

## <a name="custom-domain-names"></a>Vlastní názvy domén

Svůj statický Web můžete zpřístupnit prostřednictvím vlastní domény. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro účet Azure Storage](storage-custom-domain-name.md).

Podrobný pohled na hostování vaší domény v Azure najdete v tématu [hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Ceny

Můžete povolit hostování statických webů zdarma. Účtuje se vám jenom úložiště objektů blob, které vaše lokalita využívá, a provozní náklady. Další podrobnosti o cenách pro Azure Blob Storage najdete na [stránce s cenami azure BLOB Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriky

Metriky můžete povolit na statických stránkách webu. Po povolení metrik se na řídicím panelu metriky nahlásí Statistiky provozu souborů v kontejneru **$Web** .

Pokud chcete povolit metriky na stránkách statického webu, přečtěte si téma [Povolení metrik na statických stránkách webu](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Další kroky

* [Hostování statického webu v Azure Storage](storage-blob-static-website-how-to.md)
* [Použití Azure CDN pro přístup k objektům blob s vlastními doménami přes HTTPS](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro objekt BLOB nebo webový koncový bod](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Sestavení první webové aplikace bez serveru](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování vaší domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
