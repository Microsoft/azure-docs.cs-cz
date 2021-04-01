---
title: Kurz – import a publikování prvního rozhraní API v Azure API Management
description: V tomto kurzu naimportujete rozhraní OpenAPI Specification API do Azure API Management a potom své rozhraní API otestujete v Azure Portal.
author: mikebudzynski
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 9ff64f57e61002101b4e2c560bdcd91863cc461e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91626943"
---
# <a name="tutorial-import-and-publish-your-first-api"></a>Kurz: Import a publikování vašeho prvního rozhraní API

V tomto kurzu se dozvíte, jak do API Management Azure importovat rozhraní API back-endu OpenAPI Specification ve formátu JSON. Microsoft poskytuje rozhraní back-end používané v tomto příkladu a hostuje ho v Azure na adrese [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json) .

Po importu back-endu rozhraní API do API Management se vaše API Management API staly fasádou pro back-endové rozhraní API. Můžete přizpůsobit fasádu vašim potřebám v API Management bez zásahu do back-endu API. Další informace najdete v tématu [Transformace a ochrana vašeho rozhraní API](transform-api.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import rozhraní API do API Management
> * Testovat rozhraní API na portálu Azure Portal

Po importu můžete rozhraní API spravovat v Azure Portal.

:::image type="content" source="media/import-and-publish/created-api.png" alt-text="Nové rozhraní API v API Management":::

## <a name="prerequisites"></a>Požadavky

- Vysvětlení [terminologie Azure API Management](api-management-terminology.md).
- [Vytvořte instanci služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a>Import a publikování back-endového rozhraní API

V této části se dozvíte, jak importovat a publikovat rozhraní API pro back-end OpenAPI Specification.

1. V levém navigačním panelu instance API Management vyberte **rozhraní API**.
1. Vyberte dlaždici **openapi** .
1. V okně **vytvořit z openapi specifikace** vyberte možnost **Full (úplné**).
1. Zadejte hodnoty z následující tabulky. Pak vyberte **vytvořit** a vytvořte své rozhraní API.

   Hodnoty rozhraní API můžete nastavit během vytváření nebo později tak, že na kartu **Nastavení** kliknete.

   :::image type="content" source="media/import-and-publish/create-api.png" alt-text="Vytvoření rozhraní API":::


   |Nastavení|Hodnota|Popis|
   |-------|-----|-----------|
   |**Specifikace OpenAPI**|*https: \/ /conferenceapi.azurewebsites.NET? Format = JSON*|Služba implementující rozhraní API. API Management přepošle požadavky na tuto adresu.|
   |**Zobrazovaný název**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Název zobrazený na [portálu pro vývojáře](api-management-howto-developer-portal.md)|
   |**Název**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Jedinečný název rozhraní API.|
   |**Popis**|Po zadání předchozí adresy URL služby API Management vyplní toto pole na základě formátu JSON.|Volitelný popis rozhraní API.|
   |**Schéma URL**|**HTTPS**|Které protokoly mají přístup k rozhraní API.|
   |**Přípona adresy URL rozhraní API**|*conference*|Přípona připojena k základní adrese URL služby API Management. API Management odlišuje rozhraní API jejich příponou, takže přípona musí být jedinečná pro každé rozhraní API pro daného vydavatele.|
   |**Značky**| |Značky pro uspořádání rozhraní API pro vyhledávání, seskupování nebo filtrování.|
   |**Produkty**|**Unlimited**|Přidružení jednoho nebo více rozhraní API. Každá instance API Management obsahuje dva ukázkové produkty: **Start** a **Unlimited**. Rozhraní API publikujete pomocí přidružení rozhraní API k produktu, **neomezeně** v tomto příkladu.<br/><br/> Do produktu můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Pokud chcete toto rozhraní API přidat k jinému produktu, zadejte nebo vyberte název produktu. Opakováním tohoto kroku přidejte rozhraní API do více produktů. Rozhraní API můžete do produktů přidat také později ze stránky **Nastavení** .<br/><br/>  Další informace o produktech najdete v tématu [Vytvoření a publikování produktu](api-management-howto-add-products.md).|
   |**Brány**|**Spravované**|Brány rozhraní API, které zpřístupňují rozhraní API. Toto pole je dostupné jenom pro **vývojáře** a služby úrovně **Premium** .<br/><br/>**Spravované** znamená, že brána integrovaná do služby API Management a hostovaná Microsoftem v Azure. [Samoobslužné brány](self-hosted-gateway-overview.md) jsou k dispozici pouze v úrovních služeb Premium a Developer. Můžete je nasadit místně nebo v jiných cloudech.<br/><br/> Pokud nejsou vybrané žádné brány, rozhraní API nebude dostupné a požadavky rozhraní API nebudou úspěšné.|
   |**Chcete vytvořit verzi tohoto rozhraní API?**|Vybrat nebo zrušit výběr|Další informace najdete v tématu [publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md).|

   > [!NOTE]
   > Pokud chcete publikovat rozhraní API pro příjemce rozhraní API, musíte ho přidružit k produktu.

2. Vyberte **Vytvořit**.

Pokud máte problémy s importem definice rozhraní API, přečtěte si [seznam známých problémů a omezení](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Otestujte nové rozhraní API v Azure Portal

Můžete volat operace rozhraní API přímo z Azure Portal, což poskytuje pohodlný způsob, jak zobrazit a otestovat operace.

1. V levém navigačním panelu instance API Management vyberte **rozhraní** API  >  **ukázková konferenční konference API**.
1. Vyberte kartu **test** a pak vyberte **getspeakers**. Stránka zobrazuje **parametry dotazu** a **záhlaví**, pokud existují. Klíč **OCP-APIM-Subscription-Key** se vyplní automaticky pro klíč předplatného přidružený k tomuto rozhraní API.
1. Vyberte **Odeslat**.

   :::image type="content" source="media/import-and-publish/01-import-first-api-01.png" alt-text="Test API v Azure Portal":::

   Back-end odpoví s **200 OK** a dalšími daty.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Importovat vaše první rozhraní API
> * Testovat rozhraní API na portálu Azure Portal

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit a publikovat produkt:

> [!div class="nextstepaction"]
> [Vytvoření a publikování produktu](api-management-howto-add-products.md)
