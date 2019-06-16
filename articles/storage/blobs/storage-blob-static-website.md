---
title: Hostování statického webu ve službě Azure Storage
description: Azure Storage statický web hostovat, poskytuje nákladově efektivní, škálovatelné řešení pro hostování moderních webových aplikací.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427980"
---
# <a name="static-website-hosting-in-azure-storage"></a>Hostování statického webu ve službě Azure Storage

Můžete poskytovat statický obsah (HTML, CSS, JavaScript a soubory obrázků) přímo z kontejneru úložiště s názvem *$web*. Hostování obsahu ve službě Azure Storage umožňuje používat architektur bez serveru, které zahrnují [Azure Functions](/azure/azure-functions/functions-overview) a jiné platformy jako služby (PaaS).

> [!NOTE]
> Pokud váš web závisí na kódu na straně serveru, použijte [služby Azure App Service](/azure/app-service/overview) místo.

## <a name="setting-up-a-static-website"></a>Nastavení statického webu

Hostoval statický web je funkce, které chcete povolit v účtu úložiště.

Pokud chcete povolit statický web, vyberte název výchozího souboru a volitelně zadejte cestu k vlastní stránka 404. Pokud se kontejner úložiště objektů blob s názvem **$web** ještě neexistuje v účtu, vytvoří se pro vás. Přidání souborů vašeho webu do tohoto kontejneru.

Podrobné pokyny najdete v tématu [hostování statického webu ve službě Azure Storage](storage-blob-static-website-how-to.md).

![Azure Storage statických webů metriky metrika](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

Soubory **$web** kontejneru jsou malá a velká písmena, obsluhovat požadavky na anonymní přístup a jsou k dispozici pouze prostřednictvím operace čtení.

## <a name="uploading-content"></a>Nahrává se obsah

Některé z těchto nástrojů můžete nahrát obsah tak, aby **$web** kontejneru:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Modul Azure PowerShell](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Kanály Azure](https://azure.microsoft.com/services/devops/pipelines/)
> * [Rozšíření sady Visual Studio Code](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Zobrazení obsahu

Uživatelé mohou zobrazit obsah webu z prohlížeče pomocí veřejné adresy URL webu. Adresu URL můžete najít pomocí webu Azure portal, Azure CLI nebo Powershellu. Tuto tabulku použijte jako vodítko.

|Nástroj| Doprovodné materiály |
|----|----|
|**Azure Portal** | [Vyhledat adresu URL webu pomocí webu Azure portal](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [Vyhledat adresu URL webu pomocí příkazového řádku Azure](storage-blob-static-website-how-to.md#cli-find-url) |
|**Modul Azure PowerShell** | [Vyhledat adresu URL webu s použitím prostředí PowerShell](storage-blob-static-website-how-to.md#powershell-find-url) |

Adresa URL vašeho webu obsahuje 8bitový kód. Například adresa URL `https://contosoblobaccount.z22.web.core.windows.net/` obsahuje představujících 8bitový kód `z22`.

Adresa URL musí i nadále, že kód, je určena jen pro interní použití a není nutné použít tento kód jiným způsobem.

Dokumentu indexu, který zadáte při povolení hostování statického webu se zobrazí, když uživatelé otevřete web a nezadávejte konkrétního souboru (například: `https://contosoblobaccount.z22.web.core.windows.net`).  

Pokud server vrátí chybu 404 a nezadali jste dokument Chyba při povolování web, je výchozí 404 – Stránka vrátí uživateli.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>Dopad nastavení úroveň veřejného přístupu webový kontejner

Úroveň veřejného přístupu můžete upravit **$web** kontejneru, ale nemá na žádný vliv na koncový bod statického webu primární vzhledem k tomu, že tyto soubory jsou poskytovány prostřednictvím žádosti o anonymní přístup. To znamená veřejné (přístup jen pro čtení) pro všechny soubory.

Následující snímek obrazovky ukazuje nastavení úroveň veřejného přístupu na webu Azure Portal:

![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Zatímco koncový bod statického webu primární nemá vliv, ovlivní změna úroveň veřejného přístupu koncového bodu služby objektů blob v primární.

Například, pokud změníte úroveň veřejného přístupu **$web** kontejneru z **privátní (bez anonymního přístupu)** k **objektů Blob (anonymní přístup pro čtení pro objekty BLOB pouze)** , pak bude úroveň veřejného přístupu ke koncovému bodu primární statického webu `https://contosoblobaccount.z22.web.core.windows.net/index.html` nedojde ke změně.

Ale blob veřejný přístup k primární koncový bod služby `https://contosoblobaccount.blob.core.windows.net/$web/index.html` změnit ze soukromé na veřejnou. Uživatelé teď mohou otevřít tento soubor pomocí některé z těchto dvou koncových bodů.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) a vrstva SSL (Secure Socket) podpory

Soubory statického webu zpřístupnit prostřednictvím vaší vlastní domény a HTTPS, naleznete v tématu [přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md). Jako součást tohoto procesu musíte odkazovat na primární vaší sítě CDN *statického webu* koncový bod na rozdíl od primární *službu blob service* koncového bodu. Můžete potřebovat Počkejte několik minut, než váš obsah je viditelné, protože konfigurace CDN není spuštěna ihned.

Při aktualizaci statického webu, je potřeba vymazat obsah uložený v mezipaměti v krajních serverů CDN tím, že vyprázdnění koncového bodu CDN. Další informace najdete v tématu [Vyprázdnění koncového bodu Azure CDN](../../cdn/cdn-purge-endpoint.md).

> [!NOTE]
> HTTPS je nativně podporována přes webový koncový bod účtu, aby webový koncový bod je přístupný prostřednictvím HTTP i HTTPS. Nicméně pokud účet úložiště je nakonfigurovaný tak, aby vyžadovala zabezpečený přenos přes protokol HTTPS, pak musí uživatelé používat koncový bod HTTPS. Další informace najdete v tématu [vyžadovat zabezpečený přenos ve službě Azure Storage](../common/storage-require-secure-transfer.md).
>
> Použití vlastních domén přes protokol HTTPS vyžaduje použití Azure CDN v tuto chvíli.

## <a name="custom-domain-names"></a>Vlastní názvy domén

Váš statický web můžete zpřístupnit prostřednictvím vlastní domény. Další informace najdete v tématu [konfigurace vlastního názvu domény pro váš účet Azure Storage](storage-custom-domain-name.md).

Podrobný rozbor toho hostovat svoji doménu v Azure, najdete v části [hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Ceny

Můžete povolit hostoval statický web zdarma. Bude se vám účtovat jenom za úložiště objektů blob, který používá váš web a náklady na provoz. Další podrobnosti o cenách pro službu Azure Blob Storage, podívejte se [Azure Blob Storage stránce s cenami](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Metriky

Metriky na stránkách statického webu můžete povolit. Po povolení metrik, statistiky na soubory v provozu **$web** kontejneru jsou hlášeny v řídicím panelu metrik.

K zapnutí metrik na stránkách statického webu, naleznete v tématu [zapnutí metrik na stránkách statického webu](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>Další postup

* [Hostování statického webu ve službě Azure Storage](storage-blob-static-website-how-to.md)
* [Přístup k objektům BLOB s použitím vlastních domén přes protokol HTTPS pomocí Azure CDN](storage-https-custom-domain-cdn.md)
* [Konfigurace vlastního názvu domény pro koncový bod služby blob nebo web](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Vytvořte svoji první aplikaci bez serveru web](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Kurz: Hostování domény v Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
