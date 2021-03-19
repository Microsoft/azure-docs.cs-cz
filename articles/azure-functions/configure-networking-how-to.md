---
title: Postup konfigurace Azure Functions s virtuální sítí
description: Článek, který ukazuje, jak provádět určité úlohy virtuální sítě pro Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609075"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Postup konfigurace Azure Functions s virtuální sítí

V tomto článku se dozvíte, jak provádět úlohy související s konfigurací aplikace Function App pro připojení a spuštění ve virtuální síti. Další informace o Azure Functions a sítích najdete v tématu [možnosti Azure Functions sítě](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Omezení účtu úložiště na virtuální síť 

Když vytváříte aplikaci Function App, musíte vytvořit nebo propojit s účtem Azure Storage pro obecné účely, který podporuje objekty blob, Queue a Table Storage. Tento účet úložiště můžete nahradit takovým, který je zabezpečený pomocí koncových bodů služby nebo privátního koncového bodu. 

> [!NOTE]  
> Tato funkce v současné době funguje pro všechny SKU podporované virtuální sítě Windows ve vyhrazeném (App Service) plánu a pro plány Premium. Plán spotřeby se nepodporuje. 

Pokud chcete nastavit funkci s účtem úložiště omezeným na soukromou síť:

1. Vytvořte funkci s účtem úložiště bez povolených koncových bodů služby.

1. Nakonfigurujte funkci pro připojení k vaší virtuální síti.

1. Vytvořte nebo nakonfigurujte jiný účet úložiště.  Toto je účet úložiště, který se zabezpečuje s koncovými body služby a spojíme naši funkci.

1. [Vytvořte sdílenou složku](../storage/files/storage-how-to-create-file-share.md#create-file-share) v účtu zabezpečeného úložiště.

1. Povolte koncové body služby nebo privátní koncový bod pro účet úložiště.  
    * Pokud používáte připojení privátního koncového bodu, bude účet úložiště potřebovat privátní koncový bod `file` pro `blob` subprostředky a.  Pokud používáte některé možnosti, jako je Durable Functions, budete potřebovat `queue` a `table` přistupovat přes připojení privátního koncového bodu.
    * Pokud používáte koncové body služby, povolte pro účty úložiště podsíť vyhrazenou pro vaše aplikace Function App.

1. Zkopírujte soubor a obsah objektu BLOB z účtu úložiště Function App do zabezpečeného účtu úložiště a sdílené složky.

1. Zkopírujte připojovací řetězec pro tento účet úložiště.

1. Aktualizujte **nastavení aplikace** v části **Konfigurace** pro aplikaci Function App na následující:

    | Název nastavení | Hodnota | Komentář |
    |----|----|----|
    | `AzureWebJobsStorage`| Připojovací řetězec úložiště | Toto je připojovací řetězec pro zabezpečený účet úložiště. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Připojovací řetězec úložiště | Toto je připojovací řetězec pro zabezpečený účet úložiště. |
    | `WEBSITE_CONTENTSHARE` | Sdílená složka | Název sdílené složky vytvořené v účtu zabezpečeného úložiště, kde se nacházejí soubory nasazení projektu. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Nové nastavení |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Vynutí veškerý odchozí provoz přes virtuální síť. Vyžaduje se, když účet úložiště používá připojení privátního koncového bodu. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Server DNS používaný aplikací Vyžaduje se, když účet úložiště používá připojení privátního koncového bodu. |

1. Vyberte **Uložit** a uložte nastavení aplikace. Změna nastavení aplikace způsobí, že se aplikace restartuje.  

Po restartování aplikace Function App je nyní připojen k zabezpečenému účtu úložiště.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Možnosti sítí Azure Functions](functions-networking-options.md)

