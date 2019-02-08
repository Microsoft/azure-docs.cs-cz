---
title: Registrace vaší aplikace – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: Krok za krokem provede postup registrace nové aplikace pomocí služby Azure Custom Decision Service.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo
ms.openlocfilehash: 0771c417a526c17d1bef7b0d40405efd94333929
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55869989"
---
# <a name="register-your-application"></a>Registrace vaší aplikace

Použití služby Custom Decision Service pro vaši aplikaci, zaregistrujte ho na portálu. Tento článek vysvětluje, jak.

1. Přejděte [přední stránce](https://ds.microsoft.com/) ze služby Custom Decision Service. Na pásu karet klikněte na tlačítko **Moje portál**, jak již bylo zdůrazněno na obrázku:

    ![Můj portál](./media/portal.png)

    Pokud už nejste přihlášení, na portálu zobrazí výzvu se přihlásit pomocí vašeho [účtu Microsoft](https://account.microsoft.com/account). Po přihlášení, na portálu se zobrazí váš účet Microsoft v pravém horním rohu stránky.

2. Chcete-li zaregistrovat aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko.

3. V dialogovém okně vyberte ID aplikace pro vaši aplikaci. Custom Decision Service vyžaduje jedinečný ID pro každou aplikaci. Když se někdo jiný se už používá toto ID, systém vás požádá o vyberte jiné.

4. Určete skupinu akcí rozhraní API. Toto nastavení je RSS nebo informační kanál Atom, který komunikuje dostupný obsah pro vaši aplikaci do služby Custom Decision Service. Zadejte název pro kanál a zadejte adresu URL, ze kterého je obsloužen. Chcete-li tento krok udělat později, klikněte na tlačítko **informační kanály** tlačítko a pak klikněte na tlačítko **nový informační kanál** tlačítko. Příklad, který vytvoří kanál RSS je popsaný níže.

5. Chcete-li zaregistrovat aplikaci, vyberte **vlastní aplikace** zaškrtávací políčko v levém dolním rohu. Zadejte [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) pro účet úložiště Azure, ve kterém se protokolují tato data vaší aplikace. Další informace o tom, jak vytvořit účet úložiště najdete v tématu [jak vytvořit, spravovat nebo odstranit účet úložiště](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Další postup

* Začněte optimalizovat [webovou stránku](custom-decision-service-get-started-browser.md) nebo [aplikaci pro smartphone](custom-decision-service-get-started-app.md).
* Seznámení se základními [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Více informací o podporovaných funkcích se dozvíte v [referenční dokumentaci k rozhraní API](custom-decision-service-api-reference.md).
