---
title: Import a publikování vašeho prvního rozhraní API v Azure API Management
description: Naučte se importovat rozhraní OpenAPI Specification API do Azure API Management a potom otestovat rozhraní API v Azure Portal.
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
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 71a239393c61dccf39ed505aa2b08d7612b7e370
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905546"
---
# <a name="import-and-publish-your-first-api"></a>Import a publikování vašeho prvního rozhraní API

V tomto kurzu se dozvíte, jak do API Management Azure importovat rozhraní API back-endu OpenAPI Specification ve formátu JSON. Microsoft poskytuje rozhraní back-end API a hostuje ho v Azure na adrese [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Po importu back-endu rozhraní API do API Management se vaše API Management API staly fasádou pro back-endové rozhraní API. Můžete přizpůsobit fasádu vašim potřebám v API Management bez zásahu do back-endu API. Další informace najdete v tématu [Transformace a ochrana vašeho rozhraní API](transform-api.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import rozhraní API do API Management
> * Testovat rozhraní API na portálu Azure Portal

![Nové rozhraní API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Požadavky

- Vysvětlení [terminologie Azure API Management](api-management-terminology.md).
- [Vytvořte instanci služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Import a publikování back-endového rozhraní API

V této části se dozvíte, jak importovat a publikovat rozhraní API pro back-end OpenAPI Specification.

1. V levém navigačním panelu instance API Management v části **API Management** vyberte **rozhraní API** .
1. Vyberte dlaždici **openapi** a pak v místní obrazovce vyberte **plná** .
1. Na obrazovce **vytvořit z openapi specifikace** použijte k vytvoření rozhraní API hodnoty z následující tabulky.

   Červená hvězdička vedle pole ve formuláři označuje, že pole je povinné. Hodnoty rozhraní API můžete nastavit během vytváření nebo později tak, že na kartu **Nastavení** kliknete.

   ![Vytvoření rozhraní API](./media/api-management-import-and-publish/create-api.png)

   |Nastavení|Hodnota|Popis|
   |-------|-----|-----------|
   |**Specifikace OpenAPI**|*https: \/ /conferenceapi.azurewebsites.NET? Format = JSON*|Služba implementující rozhraní API. Služba API Management na tuto adresu směruje požadavky.|
   |**Zobrazovaný název**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Název zobrazený na portálu pro vývojáře|
   |**Název**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Jedinečný název rozhraní API.|
   |**Popis**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Volitelný popis rozhraní API.|
   |**Schéma URL**|**HTTPS**|Které protokoly je možné použít pro přístup k rozhraní API.|
   |**Přípona adresy URL rozhraní API**|*conference*|Přípona připojena k základní adrese URL služby API Management. API Management odlišuje rozhraní API jejich příponou, takže přípona musí být jedinečná pro každé rozhraní API pro daného vydavatele.|
   |**Značky**| |Značky pro uspořádání rozhraní API pro vyhledávání, seskupování nebo filtrování.|
   |**Produktech**|**Unlimited**|Přidružení jednoho nebo více rozhraní API. Každá instance API Management obsahuje dva ukázkové produkty: **Start** a **Unlimited**. Rozhraní API publikujete pomocí přidružení rozhraní API k produktu, **neomezeně** v tomto příkladu.<br/>Do produktu můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Pokud chcete toto rozhraní API přidat k jinému produktu, zadejte nebo vyberte název produktu. Opakováním tohoto kroku přidejte rozhraní API do více produktů. Rozhraní API můžete do produktů přidat také později ze stránky **Nastavení** .<br/>Přístup k rozhraní API získají vývojáři až po přihlášení k odběru produktu. Když se přihlásí k odběru, získá klíč předplatného, který je vhodný pro jakékoli rozhraní API v tomto produktu. <br/>Pokud jste vytvořili instanci API Management, jste už správce, takže jste přihlášeni k odběru všech produktů v instanci.|
   |**Brány**|**Spravované**|Brány rozhraní API, které zpřístupňují rozhraní API. Toto pole je dostupné jenom pro **vývojáře** a služby úrovně **Premium** .<br/>**Spravovaná** brána indikuje bránu, která je integrovaná do služby API Management a je hostovaná Microsoftem v Azure. Další brány jsou [samoobslužné brány](self-hosted-gateway-overview.md) a jsou dostupné jenom v úrovních služeb Premium a Developer. Můžete je nasadit místně nebo v jiných cloudech.<br/>Pokud nejsou vybrané žádné brány, rozhraní API nebude dostupné a požadavky rozhraní API nebudou úspěšné.|
   |**Chcete vytvořit verzi tohoto rozhraní API?**|Vybrat nebo zrušit výběr|Další informace o tom, jak se správou verzí, najdete v tématu [publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Pokud chcete publikovat rozhraní API pro příjemce rozhraní API, musíte ho přidružit k produktu.

2. Vyberte **Vytvořit**.

Pokud máte problémy s importem definice rozhraní API, přečtěte si [seznam známých problémů a omezení](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Otestujte nové rozhraní API v Azure Portal

Můžete volat operace rozhraní API přímo z Azure Portal, což poskytuje pohodlný způsob, jak zobrazit a otestovat operace.

1. V levém navigačním panelu instance API Management v části **API Management** vyberte **rozhraní API** a pak vyberte **ukázková konferenční konference API**.
1. Vyberte kartu **test** a pak vyberte **getspeakers**. Stránka zobrazuje **parametry dotazu** a **záhlaví**, pokud existují. Klíč **OCP-APIM-Subscription-Key** se vyplní automaticky pro klíč předplatného přidružený k tomuto rozhraní API.
1. Vyberte **Poslat**.

   ![Mapa testu rozhraní API](./media/api-management-import-and-publish/01-import-first-api-01.png)

   Back-end předá odpověď **200 OK** a nějaká data.

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Importovat vaše první rozhraní API
> * Testovat rozhraní API na portálu Azure Portal

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit a publikovat produkt:

> [!div class="nextstepaction"]
> [Vytvoření a publikování produktu](api-management-howto-add-products.md)
