---
title: Správa prostředků pomocí Microsoft Graphu
titleSuffix: Azure AD B2C
description: Připravte se na správu prostředků Azure AD B2C pomocí Microsoft Graphu registrací aplikace, která je udělena požadovaná oprávnění rozhraní GRAPH API.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 32117d4bfcf0c0af94eced095b94ab0c1b6f88af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184335"
---
# <a name="manage-azure-ad-b2c-with-microsoft-graph"></a>Správa Azure AD B2C pomocí Microsoft Graphu

[Microsoft Graph][ms-graph] umožňuje spravovat mnoho prostředků v rámci klienta Azure AD B2C, včetně uživatelských účtů zákazníků a vlastních zásad. Psaní skriptů nebo aplikací, které volají [rozhraní Microsoft Graph API][ms-graph-api], můžete automatizovat úlohy správy klienta, jako jsou:

* Migrace existujícího úložiště uživatelů do klienta Azure AD B2C
* Nasazení vlastních zásad pomocí Azure Pipeline v Azure DevOps a správa vlastních klíčů zásad
* Registrace hostitelských uživatelů na vlastní stránce a vytvoření uživatelských účtů v adresáři Azure AD B2C na pozadí
* Automatizace registrace aplikace
* Získat protokoly auditu

Následující části vám pomohou připravit se na použití rozhraní Microsoft Graph API k automatizaci správy prostředků v adresáři Azure AD B2C.

## <a name="microsoft-graph-api-interaction-modes"></a>Režimy interakce rozhraní API rozhraní Microsoft Graph

Existují dva způsoby komunikace, které můžete použít při práci s rozhraním Microsoft Graph API pro správu prostředků v tenantovi Azure AD B2C:

* **Interaktivní** – vhodné pro úlohy spouštění, můžete použít účet správce v tenantovi B2C k provádění úloh správy. Tento režim vyžaduje, aby se správce před voláním rozhraní MICROSOFT Graph API přihlásil pomocí svých přihlašovacích údajů.

* **Automatizovaná** – Pro naplánované nebo průběžně spouštěné úlohy používá tato metoda účet služby, který nakonfigurujete s oprávněními požadovanými k provádění úloh správy. Vytvoření "účet služby" v Azure AD B2C registrací aplikace, které vaše aplikace a skripty použít pro ověřování pomocí jeho *ID aplikace (klient)* a oauth 2.0 pověření klienta grant. V tomto případě aplikace funguje jako sama o sobě volat rozhraní Microsoft Graph API, nikoli správce uživatele jako v dříve popsané interaktivní metody.

Scénář **automatické** interakce povolíte vytvořením registrace aplikace uvedené v následujících částech.

## <a name="register-management-application"></a>Žádost o správu registru

Než vaše skripty a aplikace můžou pracovat s [rozhraním Microsoft Graph API][ms-graph-api] pro správu prostředků Azure AD B2C, musíte vytvořit registraci aplikace v tenantovi Azure AD B2C, která uděluje požadovaná oprávnění rozhraní API.

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="grant-api-access"></a>Udělení přístupu k rozhraní API

Dále udělte registrovaným oprávněním aplikací k manipulaci s prostředky tenanta prostřednictvím volání rozhraní Microsoft Graph API.

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="create-client-secret"></a>Vytvořit tajný klíč klienta

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

Nyní máte aplikaci, která má oprávnění k *vytváření*, *čtení*, *aktualizaci*a *odstranění* uživatelů v tenantovi Azure AD B2C. Pokračujte k další části a přidejte oprávnění k *aktualizaci hesla.*

## <a name="enable-user-delete-and-password-update"></a>Povolení odstranění a aktualizace hesla uživatele

Oprávnění *Číst a zapisovat data adresáře* **nezahrnuje** možnost odstranit uživatele nebo aktualizovat hesla uživatelských účtů.

Pokud vaše aplikace nebo skript potřebuje odstranit uživatele nebo aktualizovat jejich hesla, přiřaďte aplikaci roli *správce uživatele:*

1. Přihlaste se k [portálu Azure portal](https://portal.azure.com) a pomocí filtru **Directory + Subscription** přepněte do svého klienta Azure AD B2C.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **Správa**vyberte **Role a správce**.
1. Vyberte roli **správce uživatele.**
1. Vyberte **Přidat přiřazení**.
1. Do textového pole **Vybrat** zadejte název aplikace, kterou jste zaregistrovali dříve, například *managementapp1*. Vyberte aplikaci, jakmile se zobrazí ve výsledcích hledání.
1. Vyberte **Přidat**. Může trvat několik minut, než se oprávnění plně rozšíří.

## <a name="next-steps"></a>Další kroky

Teď, když jste zaregistrovali aplikaci pro správu a udělili jí požadovaná oprávnění, můžou vaše aplikace a služby (například Azure Pipelines) používat její přihlašovací údaje a oprávnění k interakci s rozhraním MICROSOFT Graph API.

* [Operace B2C podporované aplikací Microsoft Graph](microsoft-graph-operations.md)
* [Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md)
* [Získání protokolů auditování pomocí rozhraní API pro vytváření sestav Azure AD](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: https://docs.microsoft.com/graph/
[ms-graph-api]: https://docs.microsoft.com/graph/api/overview
