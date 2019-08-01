---
title: Registrace aplikace – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Podrobný návod, jak zaregistrovat novou aplikaci s využitím Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ROBOTS: NOINDEX
ms.openlocfilehash: 4a8e4e91e768e3ad28ea9ff88c18515db4a40323
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704554"
---
# <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

Pokud chcete pro svou aplikaci použít Custom Decision Service, zaregistrujte ji na portálu. Tento článek vysvětluje, jak.

1. Přejít na [úvodní stránku](https://portal.ds.microsoft.com/) Custom Decision Service. Na pásu karet klikněte na možnost **Můj portál**, jak je zvýrazněno na obrázku:

    ![Můj portál](./media/portal.png)

    Pokud ještě nejste přihlášení, na portálu se zobrazí výzva, abyste se přihlásili pomocí [účet Microsoft](https://account.microsoft.com/account). Po přihlášení se na portálu zobrazí účet Microsoft v pravém horním rohu stránky.

2. Aplikaci zaregistrujete kliknutím na tlačítko **Nová aplikace** .

3. V dialogovém okně vyberte ID aplikace pro vaši aplikaci. Custom Decision Service vyžaduje pro každou aplikaci jedinečné ID. Pokud již toto ID zabral někdo jiný, systém vás vyzve k výběru jiného typu.

4. Zadejte rozhraní API sady akcí. Toto nastavení je informační kanál RSS nebo Atom, který komunikuje dostupný obsah vaší aplikace až po Custom Decision Service. Zadejte název informačního kanálu a zadejte adresu URL, ze které se bude obsluhovat. Pokud chcete tento krok provést později, klikněte na tlačítko **informační kanály** a pak klikněte na tlačítko **Nový informační kanál** . Příklad, který vytváří informační kanál RSS, je popsán později.

5. Pokud chcete aplikaci zaregistrovat, zaškrtněte v levém dolním rohu políčko **vlastní aplikace** . Zadejte [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) pro účet služby Azure Storage, ve kterém jsou data aplikace protokolována. Další informace o tom, jak vytvořit účet úložiště, najdete v tématu [jak vytvořit, spravovat nebo odstranit účet úložiště](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Další postup

* Začněte optimalizovat [webovou stránku](custom-decision-service-get-started-browser.md) nebo [aplikaci pro smartphone](custom-decision-service-get-started-app.md).
* Podrobnější příklad najdete v [kurzu](custom-decision-service-tutorial-news.md) .
* Více informací o podporovaných funkcích se dozvíte v [referenční dokumentaci k rozhraní API](custom-decision-service-api-reference.md).
