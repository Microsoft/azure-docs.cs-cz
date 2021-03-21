---
title: Přidání vlastních atributů do samoobslužných registračních toků – Azure AD
description: Přečtěte si o přizpůsobení atributů pro uživatelské toky samoobslužné registrace.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46b498f8b8512d0202f47dd31ba25cc851ca71e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644098"
---
# <a name="define-custom-attributes-for-user-flows"></a>Definování vlastních atributů pro toky uživatelů

Pro každou aplikaci můžete mít různé požadavky na informace, které chcete shromažďovat během registrace. Azure AD obsahuje integrovanou sadu informací uložených v atributech, jako je křestní jméno, příjmení, město a poštovní směrovací číslo. Pomocí Azure AD můžete roztáhnout sadu atributů uložených v účtu Guest, když se externí uživatel přihlásí pomocí toku uživatele.

V Azure Portal můžete vytvořit vlastní atributy a použít je v uživatelských tocích samoobslužné registrace. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](../../active-directory-b2c/microsoft-graph-operations.md). Rozhraní Microsoft Graph API podporuje vytváření a aktualizaci uživatele s atributy rozšíření. Atributy rozšíření v Graph API jsou pojmenovány pomocí konvence `extension_<extensions-app-id>_attributename` . Například:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

`<extensions-app-id>`Je specifický pro vašeho tenanta. Chcete-li zjistit tento identifikátor, přejděte na Azure Active Directory > Registrace aplikací > všechny aplikace. Vyhledejte aplikaci, která začíná na "AAD-Extensions-App" a vyberte ji. Na stránce Přehled aplikace si poznamenejte ID aplikace (klienta).

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure** vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **vlastní atributy uživatele**. Zobrazí se seznam dostupných atributů uživatele.

   ![Vyberte atributy uživatele pro registraci](media/user-flow-add-custom-attributes/user-attributes.png)

5. Chcete-li přidat atribut, vyberte možnost **Přidat**.
6. V podokně **Přidat atribut** zadejte následující hodnoty:

   - **Název** – zadejte název vlastního atributu (například "Shoesize").
   - **Datový typ** – vyberte datový typ (**řetězec**, **logická hodnota** nebo **celé číslo**).
   - **Popis** – volitelně zadejte popis vlastního atributu pro interní použití. Tento popis není pro uživatele viditelný.

   ![Přidat atribut](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Vyberte **Vytvořit**.

Vlastní atribut je teď k dispozici v seznamu atributů uživatele a pro použití ve vašich uživatelských tocích. Vlastní atribut je vytvořen pouze při prvním použití v uživatelském toku, a ne při jeho přidání do seznamu atributů uživatele.

Jakmile vytvoříte nového uživatele pomocí toku uživatele, který používá nově vytvořený vlastní atribut, může se objekt dotazovat v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer). Nyní byste měli vidět **ShoeSize** v seznamu atributů shromážděných během cesty pro registraci objektu uživatele. Můžete volat Graph API z aplikace a získat data z tohoto atributu po jeho přidání do objektu uživatele.

## <a name="next-steps"></a>Další kroky

[Přidání uživatelského toku samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md)