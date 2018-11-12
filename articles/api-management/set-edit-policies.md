---
title: Jak nastavit nebo upravit zásady služby Azure API Management | Dokumentace Microsoftu
description: Toto téma ukazuje, jak nastavit nebo upravit zásady služby Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cflower
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: apimpm
ms.openlocfilehash: 3d1847b6001ef8e32f00a4e1cd9728d5ca0662f8
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51008262"
---
# <a name="how-to-set-or-edit-azure-api-management-policies"></a>Jak nastavit nebo upravit zásady služby Azure API Management

Definice zásad není dokument XML, který popisuje pořadí příkazů příchozí a odchozí. Soubor XML můžete upravit přímo v okně definice. Můžete zvolit také předdefinovaných zásad ze seznamu, který je k dispozici k pravému okraji okna zásady. Příkazy aktuálního oboru jsou povolené a zvýrazněn. Kliknutím na příkaz povoleno přidá odpovídající kód XML v umístění kurzoru v definici zobrazení. 

Podrobné informace o zásadách najdete v tématu [zásady ve službě Azure API Management](api-management-howto-policies.md).

## <a name="set-or-edit-a-policy"></a>Nastavení nebo úprava zásad

Nastavení nebo úprava zásad, postupujte podle následujících kroků:

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. Přejděte k vaší instanci APIM.
3. Klikněte na kartu **Rozhraní API**.

    ![Upravení zásady](./media/set-edit-policies/code-editor.png)

4. Vyberte některé z rozhraní API, která jste předtím naimportovali.
5. Vyberte kartu **Návrh**.
6. Vyberte operaci, do které chcete zásadu použít. Pokud chcete zásadu použít pro všechny operace, vyberte **všechny operace**.
7. Vyberte **</>** ikonu (editor kódu) **zpracování příchozích** nebo **zpracování odchozích požadavků** oddílu.
8. Vložte kód požadovanou zásadu do jedné z příslušné bloky.
         
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
 
## <a name="configure-scope"></a>Konfigurace oboru

Zásady lze konfigurovat, globálně, nebo v rámci produktu, rozhraní API nebo operace. Pokud chcete začít konfigurovat zásady, musíte nejprve vybrat rozsah, ve kterém mají zásady platit.

Zásady obory jsou vyhodnocovány v následujícím pořadí:

1. Globální obor
2. Obor produktu
3. Rozhraní API oboru
4. Rozsah operace

Příkazy v rámci zásad vyhodnotí podle umístění `base` element, pokud je k dispozici. Globální zásady nemá žádný nadřazený objekt zásad a použití `<base>` element v sobě nemá žádný vliv.

Zobrazit zásady v aktuálním oboru v editoru zásad, klikněte na tlačítko **přepočítat platné zásady pro vybraný obor**.

### <a name="global-scope"></a>Globální obor

Globální obor se nakonfiguruje pro **všechna rozhraní API** ve vaší instanci APIM.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) a přejděte k vaší instanci APIM.
2. Klikněte na tlačítko **všechna rozhraní API**.

    ![Globální obor](./media/api-management-howto-policies/global-scope.png)

3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte **Uložit**. 

    Změny se rozšíří do brány API Management okamžitě.

### <a name="product-scope"></a>Obor produktu

Pro vybraný produkt není nakonfigurovaná obor produktu.

1. Klikněte na tlačítko **produkty**.

    ![Obor produktu](./media/api-management-howto-policies/product-scope.png)

2. Vyberte produkt, na který chcete použít zásady.
3. Klikněte na tlačítko **zásady**.
4. Přidání nebo úprava zásad.
5. Stiskněte **Uložit**. 

### <a name="api-scope"></a>Rozhraní API oboru

Rozhraní API obor se nakonfiguruje pro **všechny operace** vybrané rozhraní API.

1. Vyberte **API** chcete použít zásady.

    ![Rozhraní API oboru](./media/api-management-howto-policies/api-scope.png)

2. Vyberte **Všechny operace**.
3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte **Uložit**. 

### <a name="operation-scope"></a>Rozsah operace 

Operace obor se nakonfiguruje pro vybranou operaci.

1. Vyberte **API**.
2. Vyberte operaci, která chcete použít zásady.

    ![Rozsah operace](./media/api-management-howto-policies/operation-scope.png)

3. Klepněte na ikonu trojúhelníku.
4. Vyberte **Editor kódu**.
5. Přidání nebo úprava zásad.
6. Stiskněte **Uložit**. 

## <a name="next-steps"></a>Další postup

V následujících tématech souvisejících:

+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)