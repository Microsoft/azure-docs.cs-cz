---
title: Přidání vlastních atributů pro toky uživatelů Azure AD
description: Přečtěte si o přizpůsobení atributů pro uživatelské toky samoobslužné registrace.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f187cc47d9c64c8257cc097734fa41e10629f1c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597447"
---
# <a name="define-custom-attributes-for-user-flows-preview"></a>Definování vlastních atributů toků uživatelů (Preview)
|     |
| --- |
| Vlastní funkce atributů uživatele je funkce Public Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.|
|     |

Pro každou aplikaci můžete mít různé požadavky na informace, které chcete shromažďovat během registrace. Azure AD obsahuje integrovanou sadu informací uložených v atributech, jako je křestní jméno, příjmení, město a poštovní směrovací číslo. Pomocí Azure AD můžete roztáhnout sadu atributů uložených v účtu Guest, když se externí uživatel přihlásí pomocí toku uživatele.

V Azure Portal můžete vytvořit vlastní atributy a použít je v uživatelských tocích samoobslužné registrace. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](https://docs.microsoft.com/azure/active-directory-b2c/manage-user-accounts-graph-api). Rozhraní Microsoft Graph API podporuje vytváření a aktualizaci uživatele s atributy rozšíření. Atributy rozšíření v Graph API jsou pojmenovány pomocí konvence `extension_<Application-client-id>_attributename` . Například:

```JSON
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Najdete ho na `<Application-client-id>` stránce **Registrace aplikací** vedle **ID aplikace (klienta)**. Toto ID je specifické pro vašeho tenanta.

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **vlastní atributy uživatele (Preview)**. Zobrazí se seznam dostupných atributů uživatele.

   ![Vyberte atributy uživatele pro registraci](media/user-flow-add-custom-attributes/user-attributes.png)

5. Chcete-li přidat atribut, vyberte možnost **Přidat**.
6. V podokně **Přidat atribut** zadejte následující hodnoty:

   - **Název** – zadejte název vlastního atributu (například "Shoesize").
   - **Datový typ** – vyberte datový typ (**řetězec**, **logická hodnota**nebo **celé číslo**).
   - **Popis** – volitelně zadejte popis vlastního atributu pro interní použití. Tento popis není pro uživatele viditelný.

   ![Přidat atribut](media/user-flow-add-custom-attributes/add-an-attribute.png)

7. Vyberte **Vytvořit**.

Vlastní atribut je teď k dispozici v seznamu atributů uživatele a pro použití ve vašich uživatelských tocích. Vlastní atribut je vytvořen pouze při prvním použití v uživatelském toku, a ne při jeho přidání do seznamu atributů uživatele.

Jakmile vytvoříte nového uživatele pomocí toku uživatele, který používá nově vytvořený vlastní atribut, může se objekt dotazovat v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer). Teď byste měli vidět **ShoeSize** v seznamu atributů shromážděných během cesty pro registraci a zobrazit je v tokenu, který se pošle zpátky do vaší aplikace. Informace o tom, jak zahrnout tyto deklarace do tokenu odeslaného zpátky do vaší aplikace, najdete v tématu [konfigurace volitelných deklarací rozšíření adresáře](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims#configuring-directory-extension-optional-claims) .

## <a name="next-steps"></a>Další kroky

[Přidání uživatelského toku samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md)