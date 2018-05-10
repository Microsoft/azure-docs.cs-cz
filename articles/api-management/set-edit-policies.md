---
title: Jak nastavit nebo upravit zásady služby Azure API Management | Microsoft Docs
description: Toto téma ukazuje, jak nastavit nebo upravit zásady služby Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: aaf86a440328e27c8c47b809536951eeaf2104b9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Jak nastavit nebo upravit zásady služby Azure API Management

Definice zásady je dokument XML, který popisuje posloupnost příchozí a odchozí příkazy. Soubor XML můžete upravovat přímo v okně definice. Předdefinované zásady můžete také vybrat ze seznamu, který je k dispozici napravo od okna zásady. Příkazy pro aktuální obor jsou povolené a zvýrazní. Kliknutím na povoleno příkaz přidá odpovídající XML na umístění kurzoru v definici zobrazení. 

Podrobné informace o zásadách najdete v tématu [zásady ve službě Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Nastavit nebo upravit zásadu

Nastavit nebo upravit zásadu, postupujte podle následujících kroků:

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Přejděte k vaší instanci APIM.
3. Klikněte **rozhraní API** kartě.
4. Vyberte jednu z rozhraní API, která jste předtím naimportovali.
5. Vyberte **návrhu** kartě.
6. Vyberte operaci, do které chcete zásadu použít. Pokud chcete aplikovat zásadu na všechny operace, vyberte **všechny operace**.
7. Klepněte na trojúhelník vedle **příchozí** nebo **odchozí** tužky.
8. Vyberte **editor kódu** položky.

    ![Upravení zásady](./media/set-edit-policies/set-edit-policies01.png)

9. Vložte kód požadovanou zásadu do jedné odpovídající bloků.
         
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
 
## <a name="configure-scope"></a>Konfiguraci rozsahu

Zásady můžete nakonfigurovat, globálně nebo v oboru produktu, rozhraní API nebo operace. Pokud chcete začít, nakonfigurujete zásady, musíte nejdřív vybrat oboru, ve kterém mají zásady platit.

Zásady obory jsou vyhodnocovány v následujícím pořadí:

1. Globální obor
2. Rozsah produktu
3. Rozhraní API oboru
4. Operace oboru

Příkazy v rámci zásad vyhodnotí podle umístění `base` elementu, pokud je k dispozici. Globální zásady nemá nadřazený zásady a pomocí `<base>` element v ní nemá žádný vliv.

Chcete-li zobrazit zásady v aktuálním oboru v editoru zásad, klikněte na tlačítko **přepočítat efektivní zásady pro vybraný obor**.

### <a name="global-scope"></a>Globální obor

Globální obor je nakonfigurován pro **všechna rozhraní API** v instanci APIM.

1. Přihlaste se k [portál Azure](https://portal.azure.com/) a přejděte k vaší instanci APIM.
2. Klikněte na tlačítko **všechna rozhraní API**.

    ![Globální obor](./media/api-management-howto-policies/global-scope.png)

3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte klávesu **Uložit**. 

    Změny jsou okamžitě předány k bráně správy rozhraní API.

### <a name="product-scope"></a>Rozsah produktu

Rozsah produktu je nakonfigurován pro vybrané produktu.

1. Klikněte na tlačítko **produkty**.

    ![Rozsah produktu](./media/api-management-howto-policies/product-scope.png)

2. Vyberte produkt, do které chcete použít zásady.
3. Klikněte na tlačítko **zásady**.
4. Přidání nebo úprava zásad.
5. Stiskněte klávesu **Uložit**. 

### <a name="api-scope"></a>Rozhraní API oboru

Rozhraní API obor je nakonfigurován pro **všechny operace** vybrané rozhraní API.

1. Vyberte **rozhraní API** chcete použít zásady.

    ![Rozhraní API oboru](./media/api-management-howto-policies/api-scope.png)

2. Vyberte **všechny operace**
3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte klávesu **Uložit**. 

### <a name="operation-scope"></a>Operace oboru 

Operace oboru je nakonfigurován pro vybranou operaci.

1. Vyberte **rozhraní API**.
2. Vyberte operaci, kterou chcete použít zásady.

    ![Operace oboru](./media/api-management-howto-policies/operation-scope.png)

3. Klikněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte klávesu **Uložit**. 

## <a name="next-steps"></a>Další postup

Najdete v následujících tématech souvisejících:

+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)