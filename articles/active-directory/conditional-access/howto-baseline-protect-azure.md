---
title: Základní zásady vyžadují MFA pro správu služeb (Preview) – Azure Active Directory
description: Zásada podmíněného přístupu, která vyžaduje MFA pro Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b3a3f12b2955ae97c43b0caf6a4ac13c9b770a
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086732"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Základní zásady: Vyžadovat MFA pro správu služby (Preview)

Ve vaší organizaci možná používáte nejrůznější služby Azure. Tyto služby je možné spravovat prostřednictvím rozhraní Azure Resource Manager API:

* portál Azure
* Azure PowerShell
* Azure CLI

Použití Azure Resource Manager ke správě služeb je vysoce privilegovaná akce. Azure Resource Manager může měnit konfigurace v rámci tenanta, jako je například nastavení služby a fakturace předplatného. Ověřování jedním faktorem je zranitelné vůči nejrůznějším útokům, jako je útok phishing a heslo. Proto je důležité ověřit identitu uživatelů, kteří chtějí přistupovat k konfiguracím Azure Resource Manager a aktualizace, a to tak, že před povolením přístupu povolíte vícefaktorové ověřování.

**Vyžadovat vícefaktorové ověřování pro správu služeb** je [základní zásadou](concept-baseline-protection.md) , která bude vyžadovat MFA pro všechny uživatele, kteří přistupují k Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Tato zásada platí pro všechny uživatele, kteří přistupují k Azure Resource Manager bez ohledu na to, jestli se jedná o správce.

Po povolení této zásady v tenantovi budou mít všichni uživatelé přihlášení k prostředkům správy Azure služby Multi-Factor Authentication. Pokud uživatel není zaregistrován pro vícefaktorové ověřování, uživatel bude muset zaregistrovat pomocí aplikace Microsoft Authenticator, aby bylo možné pokračovat.

Pokud chcete provádět interaktivní přihlašování pomocí [Azure PowerShellu](https://docs.microsoft.com/powershell/azure/authenticate-azureps), použijte rutinu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .

```PowerShell
Connect-AzAccount
```

Po spuštění této rutiny se zobrazí řetězec tokenu. Pokud se chcete přihlásit, zkopírujte tento řetězec a vložte ho [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) do prohlížeče v prohlížeči. Vaše relace PowerShellu se ověří pro připojení k Azure.

Pokud chcete provádět interaktivní přihlašování pomocí [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), spusťte příkaz [AZ Login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě je nutné otevřít stránku prohlížeče a podle pokynů v příkazovém řádku zadat autorizační kód po přechodu do [https://aka.ms/devicelogin](https://aka.ms/devicelogin) prohlížeče. Pak se přihlaste pomocí přihlašovacích údajů k účtu v prohlížeči.

## <a name="deployment-considerations"></a>Aspekty nasazování

Zásada **vyžadovat MFA pro správu služby** se vztahuje na všechny Azure Resource Manager uživatele.

## <a name="enable-the-baseline-policy"></a>Povolit základní zásady

Zásady standardních **hodnot zásad: Vyžadovat vícefaktorové ověřování pro správu služeb (Preview** ) je předem nakonfigurované a při přechodu do okna podmíněný přístup v Azure Portal se zobrazí v horní části.

Chcete-li povolit tuto zásadu a chránit správce:

1. Přihlaste se k **Azure Portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte na **Azure Active Directory** > **podmíněný přístup**.
1. V seznamu zásad vyberte **základní zásady: Vyžadovat vícefaktorové ověřování pro správu služeb (Preview**).
1. Nastavte **Povolit zásadu** pro **okamžité použití zásad**.
1. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu:

* [Zásady ochrany základní úrovně přístupu pro podmíněný přístup](concept-baseline-protection.md)
* [Pět kroků pro zabezpečení infrastruktury identity](../../security/fundamentals/steps-secure-identity.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)
