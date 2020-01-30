---
title: Nastavení registrace a přihlášení pomocí účtu LinkedIn
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům LinkedIn ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edd6bc2bbad218a1ed0a25bdcb763bde656a589d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847716"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu LinkedIn pomocí Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Pokud chcete použít účet LinkedIn jako [poskytovatele identity](authorization-code-flow.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet LinkedIn, můžete se zaregistrovat na [https://www.linkedin.com/](https://www.linkedin.com/).

1. Přihlaste se na [Web vývojářům LinkedIn](https://www.developer.linkedin.com/) pomocí svých přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Moje aplikace**a pak klikněte na **vytvořit aplikaci**.
1. Zadejte **název společnosti**, **název aplikace**, **Popis aplikace**, **logo aplikace**, **použití aplikace**, **adresu URL webu**, **obchodní e-mail**a **firemní telefon**.
1. Odsouhlaste **podmínky použití rozhraní API LinkedIn** a klikněte na **Odeslat**.
1. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Můžete je najít v části **ověřovací klíče**. Obě tyto služby budete potřebovat ke konfiguraci LinkedInu jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **autorizovaných adresách URL pro přesměrování**. Nahraďte `your-tenant-name` názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C. Vyberte **Přidat**a potom klikněte na **aktualizovat**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurace účtu LinkedIn jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **LinkedIn**.
1. Zadejte **název**. Například *LinkedIn*.
1. Pro **ID klienta**zadejte ID klienta aplikace LinkedIn, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="migration-from-v10-to-v20"></a>Migrace z verze 1.0 do verze 2.0

LinkedIn nedávno [aktualizoval svá rozhraní API z verze 1.0 do verze 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). V rámci migrace je Azure AD B2C při registraci možné získat pouze úplné jméno uživatele LinkedIn. Pokud je e-mailová adresa jedním z atributů, které se shromažďují při registraci, uživatel musí ručně zadat e-mailovou adresu a ověřit ji.
