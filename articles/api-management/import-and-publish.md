---
title: Import a publikování prvního rozhraní API ve správě rozhraní Azure API
description: Zjistěte, jak importovat rozhraní API OpenAPI do Azure API Management a otestovat rozhraní API na webu Azure Portal.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78163476"
---
# <a name="import-and-publish-your-first-api"></a>Import a publikování vašeho prvního rozhraní API 

Tento kurz ukazuje, jak importovat rozhraní back-endové rozhraní OpenAPI Specification ve formátu JSON do správy rozhraní Azure API. Microsoft poskytuje rozhraní API back-endu [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)a hostuje ho v Azure na adrese .

Po importu back-endového rozhraní API do správy rozhraní API se vaše rozhraní API pro správu rozhraní API stane fasádou pro rozhraní API back-endu. Fasádu můžete přizpůsobit vašim potřebám ve správě rozhraní API, aniž byste se museli dotýkat rozhraní API back-endu. Další informace najdete v tématu [Transformace a ochrana vašeho rozhraní API](transform-api.md). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import rozhraní API do správy rozhraní API
> * Testovat rozhraní API na portálu Azure Portal

![Nové rozhraní API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Požadavky

- Principy [terminologie správy rozhraní API Azure](api-management-terminology.md).
- [Vytvořte instanci Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Import a publikování back-endového rozhraní API

Tato část ukazuje, jak importovat a publikovat rozhraní back-endapi specifikace OpenAPI.
 
1. V levém navigačním panelu instance Správy rozhraní API vyberte **rozhraní API** v části Správa **rozhraní API.**
1. Vyberte dlaždici **OpenAPI** a pak na rozbalovací obrazovce vyberte **Úplné.**
1. Na obrazovce **Specifikace vytvořit z OpenAPI** vytvořte rozhraní API pomocí hodnot z následující tabulky.
   
   Červená hvězdička vedle pole ve formuláři označuje, že pole je povinné. Hodnoty rozhraní API můžete nastavit během vytváření nebo později na kartě **Nastavení.** 
   
   ![Vytvoření rozhraní API](./media/api-management-import-and-publish/create-api.png)
   
   |Nastavení|Hodnota|Popis|
   |-------|-----|-----------|
   |**Specifikace OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|Služba implementující rozhraní API. Služba API Management na tuto adresu směruje požadavky.|
   |**Zobrazované jméno**|Po zadání předchozí adresy URL služby vyplní správa rozhraní API toto pole na základě pole JSON.|Název zobrazený na portálu pro vývojáře.|
   |**Název**|Po zadání předchozí adresy URL služby vyplní správa rozhraní API toto pole na základě pole JSON.|Jedinečný název rozhraní API.|
   |**Popis**|Po zadání předchozí adresy URL služby vyplní správa rozhraní API toto pole na základě pole JSON.|Volitelný popis rozhraní API.|
   |**Schéma URL**|**Protokol HTTPS**|Protokoly, které lze použít pro přístup k rozhraní API.|
   |**Přípona adresy URL rozhraní API**|*conference*|Přípona připojená k základní adrese URL služby API Management. Správa rozhraní API rozlišuje rozhraní API podle jejich přípony, takže přípona musí být jedinečná pro každé rozhraní API pro daného vydavatele.|
   |**Produkty**|**Neomezené**|Přidružení jednoho nebo více api. Každá instance správy rozhraní API je dodávána se dvěma ukázkovými produkty: **Starter** a **Unlimited**. Rozhraní API publikujete tak, že přisuzujete rozhraní API k produktu **Neomezený** v tomto příkladu.<br/>Můžete zahrnout několik rozhraní API v produktu a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Chcete-li přidat toto rozhraní API do jiného produktu, zadejte nebo vyberte název produktu. Tento krok opakujte a přidejte rozhraní API do více produktů. Api můžete také přidat k produktům později na stránce **Nastavení.**<br/>Přístup k rozhraní API získají vývojáři až po přihlášení k odběru produktu. Když se přihlásí, získají klíč předplatného, který je vyhodnocovaný pro jakékoli rozhraní API v tomto produktu. <br/>Pokud jste vytvořili instanci Api Management, jste již správcem, takže jste přihlášeni ke každému produktu v instanci.|
   |**Značky**| |Značky pro uspořádání api pro vyhledávání, seskupování nebo filtrování.|
   |**Chcete vytvořit verzi tohoto rozhraní API?**|Výběr nebo zrušení výběru|Další informace o správu verzí naleznete v [tématu Publikování více verzí rozhraní API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Pokud chcete rozhraní API publikovat, musíte ho přidružit k produktu. Můžete to udělat na stránce **Nastavení.**
   
1. Vyberte **Vytvořit**.

Pokud máte problémy s importem definice rozhraní API, podívejte se na [seznam známých problémů a omezení](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testování nového rozhraní API na webu Azure Portal

Operace rozhraní API můžete volat přímo z portálu Azure, který poskytuje pohodlný způsob zobrazení a testování operací.

1. V levém navigačním panelu instance Api Management vyberte **rozhraní API** v části Správa **rozhraní API** a pak vyberte Rozhraní **API ukázkové konference**.
1. Vyberte kartu **Test** a pak vyberte **GetSpeakers**. Na stránce jsou **zobrazeny parametry dotazu** a **záhlaví**, pokud existuje. **Klíč Ocp-Apim-Subscription-Key** je vyplněn automaticky pro klíč předplatného přidružený k tomuto rozhraní API.
1. Vyberte **Poslat**.
   
   ![Mapa testu rozhraní API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Back-end předá odpověď **200 OK** a nějaká data.

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Importovat vaše první rozhraní API
> * Testovat rozhraní API na portálu Azure Portal

Přejdete k dalšímu kurzu, kde se dozvíte, jak produkt vytvořit a publikovat:

> [!div class="nextstepaction"]
> [Vytvoření a publikování produktu](api-management-howto-add-products.md)
