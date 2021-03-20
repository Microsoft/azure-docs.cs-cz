---
title: Nastavení nebo úprava zásad Azure API Management | Microsoft Docs
description: Naučte se, jak nastavit nebo upravit zásady Azure API Management. Tyto zásady jsou dokumenty XML, které popisují posloupnost příchozích a odchozích příkazů.
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
ms.openlocfilehash: a737fada3e019029967bc752aaa0dedc354fa880
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078319"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Postup při nastavení nebo úpravě zásad služby Azure API Management

Definice zásady je dokument XML, který popisuje sekvenci příchozích a odchozích příkazů. KÓD XML lze upravit přímo v okně definice. Můžete také vybrat předdefinované zásady ze seznamu, který je k dispozici na pravé straně okna zásad. Příkazy použitelné pro aktuální obor jsou povoleny a zvýrazněny. Kliknutím na příkaz Enabled přidáte příslušný kód XML do umístění kurzoru v zobrazení definice. 

Podrobné informace o zásadách najdete v tématu [zásady v Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Nastavení nebo úprava zásad

Chcete-li nastavit nebo upravit zásadu, postupujte podle následujících kroků:

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Přejděte k vaší instanci APIM.
3. Klikněte na kartu **Rozhraní API**.

    ![Upravení zásady](./media/set-edit-policies/code-editor.png)

4. Vyberte některé z rozhraní API, která jste předtím naimportovali.
5. Vyberte kartu **Návrh**.
6. Vyberte operaci, na kterou chcete zásadu použít. Pokud chcete zásadu použít pro všechny operace, vyberte **všechny operace**.
7. **</>** V části **příchozí zpracování** nebo **zpracování odchozího zpracování** vyberte ikonu (Editor kódu).
8. Vložte požadovaný kód zásad do některého z příslušných bloků.

    ```xml
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

Zásady se dají nakonfigurovat globálně nebo v oboru produktu, rozhraní API nebo operace. Pokud chcete začít konfigurovat zásadu, musíte nejdřív vybrat rozsah, na kterém by se měla zásada vztahovat.

Obory zásad jsou vyhodnocovány v následujícím pořadí:

1. Globální rozsah
2. Rozsah produktu
3. Rozsah rozhraní API
4. Rozsah operace

Příkazy v rámci zásad jsou vyhodnocovány podle umístění `base` elementu, pokud je k dispozici. Globální zásady nemají žádné nadřazené zásady a použití `<base>` elementu v něm nemá žádný vliv.

Pokud chcete zobrazit zásady v aktuálním oboru v editoru zásad, klikněte na **Přepočítat efektivní zásady pro vybraný obor**.

### <a name="global-scope"></a>Globální rozsah

Globální rozsah je nakonfigurovaný pro **všechna rozhraní API** v instanci APIM.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) a přejděte k instanci APIM.
2. Klikněte na **všechna rozhraní API**.

    ![Globální rozsah](./media/api-management-howto-policies/global-scope.png)

3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidat nebo upravit zásady.
6. Stiskněte **Uložit**. 

    Změny se rozšíří přímo na bránu API Management.

### <a name="product-scope"></a>Rozsah produktu

Pro vybraný produkt je nakonfigurovaný obor produktu.

1. Klikněte na **produkty**.

    ![Rozsah produktu](./media/api-management-howto-policies/product-scope.png)

2. Vyberte produkt, pro který chcete použít zásady.
3. Klikněte na **zásady**.
4. Přidat nebo upravit zásady.
5. Stiskněte **Uložit**. 

### <a name="api-scope"></a>Rozsah rozhraní API

Obor rozhraní API je nakonfigurovaný pro **všechny operace** vybraného rozhraní API.

1. Vyberte **rozhraní API** , na které chcete zásady použít.

    ![Rozsah rozhraní API](./media/api-management-howto-policies/api-scope.png)

2. Vyberte **Všechny operace**.
3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidat nebo upravit zásady.
6. Stiskněte **Uložit**. 

### <a name="operation-scope"></a>Rozsah operace 

Pro vybranou operaci je nakonfigurován obor operace.

1. Vyberte **rozhraní API**.
2. Vyberte operaci, na kterou chcete zásady použít.

    ![Rozsah operace](./media/api-management-howto-policies/operation-scope.png)

3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidat nebo upravit zásady.
6. Stiskněte **Uložit**. 

## <a name="next-steps"></a>Další kroky

Podívejte se na následující témata týkající se:

+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)