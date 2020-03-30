---
title: Jak nastavit nebo upravit zásady správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak nastavit nebo upravit zásady správy rozhraní API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 2df57477ae5270405a1774b7a4f04ed185fea396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70071707"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Postup při nastavení nebo úpravě zásad služby Azure API Management

Definice zásady je dokument XML, který popisuje posloupnost příchozích a odchozích příkazů. XML lze upravovat přímo v definičním okně. Můžete také vybrat předdefinovanou zásadu ze seznamu, který je k dispozici vpravo od okna zásady. Příkazy platné pro aktuální obor jsou povoleny a zvýrazněny. Klepnutí na povolený příkaz přidá příslušný xml v umístění kurzoru v zobrazení definice. 

Podrobné informace o zásadách najdete [v tématu Zásady ve správě rozhraní Azure API](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Nastavení nebo úprava zásady

Chcete-li nastavit nebo upravit zásadu, postupujte takto:

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
2. Přejděte k vaší instanci APIM.
3. Klikněte na kartu **Rozhraní API**.

    ![Upravení zásady](./media/set-edit-policies/code-editor.png)

4. Vyberte některé z rozhraní API, která jste předtím naimportovali.
5. Vyberte kartu **Návrh**.
6. Vyberte operaci, na kterou chcete zásadu použít. Pokud chcete zásadu použít pro všechny operace, vyberte **možnost Všechny operace**.
7. V **</>** části **Příchozí zpracování** nebo **Odchozí zpracování** vyberte ikonu (editor kódu).
8. Vložte požadovaný kód zásad do jednoho z příslušných bloků.

    ```XML
    <policies>
        <inbound>
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```
 
## <a name="configure-scope"></a>Konfigurace oboru

Zásady lze konfigurovat globálně nebo v rozsahu produktu, rozhraní API nebo operace. Chcete-li začít konfigurovat zásadu, musíte nejprve vybrat obor, ve kterém by měla zásada použít.

Obory zásad jsou vyhodnocovány v následujícím pořadí:

1. Globální rozsah
2. Rozsah produktu
3. Obor rozhraní API
4. Rozsah operace

Příkazy v rámci zásad jsou hodnoceny podle umístění `base` prvku, pokud je k dispozici. Globální zásady nemá žádné `<base>` nadřazené zásady a použití prvku v něm nemá žádný vliv.

Chcete-li zobrazit zásady v aktuálním oboru v editoru zásad, klepněte na tlačítko **Přepočítat efektivní zásady pro vybraný obor**.

### <a name="global-scope"></a>Globální rozsah

Globální obor je nakonfigurován pro **všechna rozhraní API** v instanci APIM.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) a přejděte na instanci APIM.
2. Klepněte na **položku Všechna přístupová a vynaliště .**

    ![Globální rozsah](./media/api-management-howto-policies/global-scope.png)

3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidejte nebo upravte zásady.
6. Stiskněte **Uložit**. 

    Změny jsou šířeny do brány pro správu rozhraní API okamžitě.

### <a name="product-scope"></a>Rozsah produktu

Obor produktu je konfigurován pro vybraný produkt.

1. Klepněte na **položku Produkty**.

    ![Rozsah produktu](./media/api-management-howto-policies/product-scope.png)

2. Vyberte produkt, na který chcete použít zásady.
3. Klepněte na **položku Zásady**.
4. Přidejte nebo upravte zásady.
5. Stiskněte **Uložit**. 

### <a name="api-scope"></a>Obor rozhraní API

Obor rozhraní API je nakonfigurován pro **všechny operace** vybraného rozhraní API.

1. Vyberte **rozhraní API,** na které chcete použít zásady.

    ![Obor rozhraní API](./media/api-management-howto-policies/api-scope.png)

2. Vyberte **Všechny operace**.
3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidejte nebo upravte zásady.
6. Stiskněte **Uložit**. 

### <a name="operation-scope"></a>Rozsah operace 

Obor operace je konfigurován pro vybranou operaci.

1. Vyberte **rozhraní API**.
2. Vyberte operaci, na kterou chcete použít zásady.

    ![Rozsah operace](./media/api-management-howto-policies/operation-scope.png)

3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidejte nebo upravte zásady.
6. Stiskněte **Uložit**. 

## <a name="next-steps"></a>Další kroky

Podívejte se na následující související témata:

+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
