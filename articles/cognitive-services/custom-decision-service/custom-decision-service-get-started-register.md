---
title: Registrace vaší aplikace - kognitivní služeb Azure | Microsoft Docs
description: Jak registrovat nové aplikace vlastní rozhodnutí službou Azure podrobný Průvodce
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins
ms.reviewer: marcozo;alekh;marossi
ms.openlocfilehash: 2aa8fbe77c11df4434eefa4c92d8529d5ca1d885
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343409"
---
# <a name="register-your-application"></a>Registrace vaší aplikace

Pomocí vlastního rozhodnutí služby pro vaši aplikaci, zaregistrujte ji na portálu. Tento článek vysvětluje, jak.

1. Přejděte na [titulní stránky](https://ds.microsoft.com/) služby vlastního rozhodnutí. Na pásu karet klikněte na tlačítko **Moje portál**, jako zvýrazněných v bitové kopii:

    ![Můj portál](./media/portal.png)

    Pokud ještě nejste přihlášeni, portálu vás vyzve, abyste Přihlaste se pomocí vaší [účtu Microsoft](https://account.microsoft.com/account). Po přihlášení, zobrazí na portálu účtu Microsoft v pravém horním rohu stránky.

2. Chcete-li zaregistrovat aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko.

3. V dialogovém okně vyberte ID aplikace pro vaši aplikaci. Vlastní rozhodnutí služba vyžaduje jedinečné ID pro každou aplikaci. Když se někdo se už používá toto ID, systému se žádostí o vyberte jiné.

4. Zadejte sadu akce rozhraní API. Toto nastavení je informačního kanálu RSS nebo Atom kanálu, který komunikuje dostupný obsah pro vaši aplikaci službě vlastní rozhodnutí. Zadejte název pro informační kanál a zadejte adresu URL, ze kterého je zpracovat. Chcete-li provést tento krok později, klikněte na tlačítko **kanály** tlačítko a pak klikněte na tlačítko **nový kanál** tlačítko. Příklad, který vytvoří informačního kanálu RSS je popsán později.

5. Chcete-li zaregistrovat aplikaci, vyberte **vlastní aplikace** políčko v levém dolním rohu. Zadejte [připojovací řetězec](../../storage/common/storage-configure-connection-string.md) pro účet úložiště Azure, kde je zaznamenané data aplikací. Další informace o tom, jak vytvořit účet úložiště najdete v tématu [jak vytvořit, spravovat nebo odstranit účet úložiště](../../storage/common/storage-create-storage-account.md).

### <a name="next-steps"></a>Další postup

* Začínáme za účelem optimalizace [webové stránky](custom-decision-service-get-started-browser.md) nebo [aplikace pro smartphone](custom-decision-service-get-started-app.md).
* Na spolupracovat [kurzu](custom-decision-service-tutorial-news.md) podrobnější příklad.
* Obrátit [referenční dokumentace rozhraní API](custom-decision-service-api-reference.md) Další informace o funkci zadané.