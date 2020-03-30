---
title: Definování vlastních atributů ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Ve službě Azure Active Directory B2C definujte vlastní atributy pro vaši aplikaci a shromažďujte informace o zákaznících.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186045"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definování vlastních atributů ve službě Azure Active Directory B2C

 Každá aplikace orientovaná na zákazníka má jedinečné požadavky na informace, které je třeba shromáždit. Váš klient Azure Active Directory B2C (Azure AD B2C) je dodáván s integrovanou sadou informací uložených v atributech, jako je křestní jméno, příjmení, město a PSČ. S Azure AD B2C můžete rozšířit sadu atributů uložených na každém účtu zákazníka.

 Vlastní atributy můžete vytvořit na [webu Azure Portal](https://portal.azure.com/) a použít je ve svých uživatelských tocích registrace, tocích uživatelů registrace nebo přihlášení nebo při úpravách profilu uživatelských toků. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**.

    ![Přepnutí na tenanta Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Zvýrazněný klient B2C ve filtru Adresář a Odběr](./media/user-flow-custom-attributes/select-directory.PNG)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **Atributy uživatele**a pak vyberte **Přidat**.
5. Zadejte **název** pro vlastní atribut (například "ShoeSize")
6. Zvolte **datový typ**. K dispozici jsou pouze **řetězce**, **logické**a **int.**
7. Volitelně zadejte **popis** pro informační účely.
8. Klikněte na **Vytvořit**.

Vlastní atribut je nyní k dispozici v seznamu **atributů uživatele** a pro použití v tocích uživatelů. Vlastní atribut je vytvořen pouze při prvním použití v libovolném toku uživatele, a nikoli při jeho přidání do seznamu **atributů Uživatele**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Použití vlastního atributu v toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **Toky uživatelů**.
1. Chcete-li ji otevřít, vyberte zásadu (například "B2C_1_SignupSignin").
1. Vyberte **Atributy uživatele** a pak vyberte vlastní atribut (například "ShoeSize"). Klikněte na **Uložit**.
1. Vyberte **Deklarace aplikace** a pak vyberte vlastní atribut.
1. Klikněte na **Uložit**.

Po vytvoření nového uživatele pomocí toku uživatele, který používá nově vytvořený vlastní atribut, může být objekt dotazován v [aplikaci Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Případně můžete použít [spustit tok uživatele](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) na tok uživatele k ověření zkušenosti zákazníka. Nyní byste měli vidět **ShoeSize** v seznamu atributů shromážděných během cesty registrace a vidět v tokenu odeslané zpět do vaší aplikace.
