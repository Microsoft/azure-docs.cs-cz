---
title: Spravovat klíče v LUIS | Dokumentace Microsoftu
description: Language Understanding (LUIS) použijte ke správě programových rozhraní API, koncový bod a externí klíče.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 80064584468c109d0bad49f1a22c485fa9cce846
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344514"
---
# <a name="manage-your-luis-keys"></a>Spravovat klíče pro služby LUIS
Klíč umožňuje vytvářet a publikovat aplikace LUIS nebo dotazu váš koncový bod. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Klíčové koncepty
V tématu [klíče v LUIS](luis-concept-keys.md) koncepce LUIS vytváření obsahu a koncový bod klíče.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Přiřazení klíče koncového bodu
Na **publikovat aplikaci** stránky, je už klíč v **prostředky a klíče** tabulky. Jedná se o vytváření klíč (starter). 

1. Vytvoření klíče služby LUIS na [webu Azure portal](https://portal.azure.com). Další pokyny najdete v tématu [vytvořit klíč koncového bodu pomocí Azure](luis-how-to-azure-subscription.md).
 
2. Chcete-li přidat LUIS klíč vytvořený v předchozím kroku, klikněte na tlačítko **přidat klíč** tlačítko Otevřít **přiřadit klíč k vaší aplikaci** dialogového okna. 

    ![Přiřazení klíče do vaší aplikace](./media/luis-manage-keys/assign-key.png)
3. V dialogovém okně vyberte Tenanta. 
 
    > [!Note]
    > V Azure představuje tenanta Azure Active Directory ID klient nebo organizace, které jsou spojené se službou. Pokud jste již dříve zaregistrovali předplatné Azure pomocí svého individuálního Account Microsoft, již klienta máte! Po přihlášení k webu Azure portal budete automaticky přihlášeni k [svému výchozímu tenantu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Můžete libovolně udělovat tohoto klienta použít, ale můžete chtít vytvořit účet správce organizace.

4. Zvolte předplatné Azure spojené s klíčem služby LUIS Azure, které chcete přidat.

5. Vyberte účet Azure LUIS. Oblast účtu se zobrazí v závorkách. 

    ![Zvolte klávesu](./media/luis-manage-keys/assign-key-filled-out.png)

6. Po přiřazení tohoto klíče koncového bodu, můžete jej použijte v všechny dotazy na koncový bod. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Publikování oblastí
Další informace o publikování [oblastech](luis-reference-regions.md) včetně publikování v [Evropa](luis-reference-regions.md#publishing-to-europe), a [Austrálie](luis-reference-regions.md#publishing-to-australia). Publikování oblastech se liší od vytváření oblastí. Ujistěte se, že vytvoříte aplikaci pro vytváření oblasti odpovídajících k publikování oblast, kterou chcete.

## <a name="unassign-key"></a>Zrušit přiřazení klíč

* V **seznam prostředků a klíče**, klikněte na ikony koše vedle entity, které chcete zrušit přiřazení. Potom klikněte na **OK** v potvrzovací zprávě potvrďte odstranění.
 
    ![Zrušit přiřazení Entity](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Zrušení přiřazení klíč LUIS neodstraní z vašeho předplatného Azure.

## <a name="next-steps"></a>Další kroky

Publikování aplikace v pomocí svého klíče **publikovat aplikaci** stránky. Pokyny k publikování najdete v tématu [publikovat aplikaci](luis-how-to-publish-app.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website