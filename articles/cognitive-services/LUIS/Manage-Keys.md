---
title: Spravovat klíče v LEOŠ | Microsoft Docs
description: Principy jazyka (LEOŠ) použijte ke správě vašeho programovací rozhraní API, koncový bod a externí klíče.
titleSuffix: Azure
services: cognitive-services
author: v-geberr
manager: Kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/21/2018
ms.author: v-geberr
ms.openlocfilehash: 8e6e363649a0bdab5525de7b8e7abe9a53d14573
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266018"
---
# <a name="manage-your-luis-keys"></a>Spravovat klíče LEOŠ
Klíč umožňuje vytvářet a publikovat aplikace LEOŠ nebo dotaz na koncový bod. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
## <a name="key-concepts"></a>Klíčové koncepty
V tématu [klíče v LEOŠ](luis-concept-keys.md) pochopit LEOŠ vytváření obsahu a koncový bod klíčové koncepty.

<a name="create-and-use-an-endpoint-key"></a>
## <a name="assign-endpoint-key"></a>Přiřadit klíč koncového bodu
Na **publikovat aplikace** stránky, je již klíč v **prostředky a klíče** tabulky. Toto je pro tvorbu klíč (starter). 

1. Vytvořte klíč LEOŠ na [portál Azure](https://portal.azure.com). Další pokyny najdete v tématu [vytváření předplatného klíče pomocí Azure](luis-how-to-azure-subscription.md).
 
2. Chcete-li přidat klíč LEOŠ vytvořili v předchozím kroku, klikněte na tlačítko **přidat klíč** tlačítko Otevřít **přiřadit klíč do vaší aplikace** dialogové okno. 

    ![Přiřazení klíče do vaší aplikace](./media/luis-manage-keys/assign-key.png)
3. V dialogovém okně vyberte klienta. 
 
    > [!Note]
    > V Azure představuje klienta Azure Active Directory ID klienta nebo organizace, které jsou spojené se službou. Pokud jste dříve zaregistrovali pro předplatné služby Azure pomocí individuálního Account Microsoft, máte klienta! Při přihlášení k portálu Azure můžete automaticky přihlášeni k [výchozího klienta](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant). Můžete použít tohoto klienta, ale můžete chtít vytvořit účet správce organizace.

4. Zvolte předplatné Azure spojené s Azure LEOŠ klíče, který chcete přidat.

5. Vyberte účet LEOŠ Azure. Oblast účtu se zobrazí v závorkách. 

    ![Vyberte klíč](./media/luis-manage-keys/assign-key-filled-out.png)

6. Po přiřazení tento klíč koncového bodu, můžete jej použijte v všechny dotazy na koncový bod. 

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

## <a name="publishing-regions"></a>Publikování oblastí
Další informace o publikování [oblasti](luis-reference-regions.md) včetně publikování v [Evropa](luis-reference-regions.md#publishing-to-europe), a [Austrálie](luis-reference-regions.md#publishing-to-australia). Publikování oblastí se liší od vytváření oblastí. Ujistěte se, že vytvoříte aplikaci v vytváření oblast odpovídající k publikování oblast, kterou chcete.

## <a name="unassign-key"></a>Zrušení přiřazení klíče

* V **seznam prostředků a klíče**, klikněte na ikonu přihrádky koši vedle entity, kterou chcete zrušit přiřazení. Potom klikněte na **OK** v potvrzovací zprávě potvrďte odstranění.
 
    ![Zrušení přiřazení Entity](./media/luis-manage-keys/unassign-key.png)

> [!NOTE]
> Zrušení přiřazení klíč LEOŠ neodstraní z vašeho předplatného Azure.

## <a name="next-steps"></a>Další postup

K publikování aplikace v používání vašeho klíče **publikovat aplikace** stránky. Pokyny k publikování, najdete v části [publikovat aplikace](PublishApp.md).

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website