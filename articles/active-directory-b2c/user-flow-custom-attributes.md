---
title: Definování vlastních atributů v Azure Active Directory B2C | Microsoft Docs
description: Definujte vlastní atributy vaší aplikace v Azure Active Directory B2C, abyste mohli shromažďovat informace o vašich zákaznících.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1d631c47225fac7d8a95541313593333a1399e07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87115955"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definování vlastních atributů v Azure Active Directory B2C

 Každá aplikace směřující od zákazníka má jedinečné požadavky na informace, které je třeba shromáždit. Tenant Azure Active Directory B2C (Azure AD B2C) obsahuje integrovanou sadu informací uložených v atributech, jako je křestní jméno, příjmení, město a poštovní směrovací číslo. Pomocí Azure AD B2C můžete roztáhnout sadu atributů uložených na každém účtu zákazníka.

 Můžete vytvořit vlastní atributy v [Azure Portal](https://portal.azure.com/) a použít je v uživatelských tokůch registrace, registraci nebo přihlašování uživatelů nebo v profilech pro úpravu uživatelských toků. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**.

    ![Přepnutí na tenanta Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Tenant B2C se ve filtru adresářů a předplatných zvýrazní.](./media/user-flow-custom-attributes/select-directory.PNG)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **atributy uživatele**a pak vyberte **Přidat**.
5. Zadejte **název** vlastního atributu (například "ShoeSize").
6. Vyberte **datový typ**. K dispozici je pouze **řetězec**, **logická hodnota**a **int** .
7. Volitelně můžete zadat **Popis** pro informativní účely.
8. Klikněte na **Vytvořit**.

Vlastní atribut je teď k dispozici v seznamu **atributů uživatele** a pro použití ve vašich uživatelských tocích. Vlastní atribut je vytvořen pouze při prvním použití v uživatelském toku, a ne při jeho přidání do seznamu **atributů uživatele**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Použití vlastního atributu v toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Vyberte zásadu (například "B2C_1_SignupSignin") a otevřete ji.
1. Vyberte **atributy uživatele** a pak vyberte vlastní atribut (například "ShoeSize"). Klikněte na **Uložit**.
1. Vyberte **deklarace identity aplikace** a pak vyberte vlastní atribut.
1. Klikněte na **Uložit**.

Jakmile vytvoříte nového uživatele pomocí toku uživatele, který používá nově vytvořený vlastní atribut, může se objekt dotazovat v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer). Případně můžete pomocí funkce [tok](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) uživatele v toku uživatelů ověřit činnost zákazníka. Teď byste měli vidět **ShoeSize** v seznamu atributů shromážděných během cesty pro registraci a zobrazit je v tokenu, který se pošle zpátky do vaší aplikace.
