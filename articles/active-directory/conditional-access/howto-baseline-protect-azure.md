---
title: Základní zásady vyžadovat vícefaktorové ověřování pro správu service (preview) – Azure Active Directory
description: Zásady podmíněného přístupu k vynucení vícefaktorového ověřování pro Azure Resource Manageru
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
ms.openlocfilehash: 00ed40bef3f3cbe59825f546ffa39c3ebfb2e41f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003173"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Základní zásady: Vyžadovat vícefaktorové ověřování pro službu správy (preview)

Může používat širokou škálu služeb Azure ve vaší organizaci. Tyto služby můžete spravovat pomocí rozhraní API Azure Resource Manageru:

* portál Azure
* Azure PowerShell
* Azure CLI

Pomocí Azure Resource Manageru pro správu svých služeb je vysoce privilegované akce. Azure Resource Manageru můžete změnit celého tenanta konfigurace, například nastavení služby a fakturace předplatného. Jednomu faktoru ověřování je citlivé na celou řadu útoků, jako jsou útoky phishing a heslo zařízení. Proto je důležité k ověření identity uživatelů, kteří chtějí přístup k Azure Resource Manageru a aktualizace konfigurací s vyžadováním ověřování službou Multi-Factor Authentication před povolením přístupu.

**Vyžadovat vícefaktorové ověřování pro správu služeb** je [základní zásady](concept-baseline-protection.md) , který bude vyžadovat vícefaktorové ověřování pro všechny uživatele, kteří používají Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. Tyto zásady platí pro všechny uživatele přístup k Azure Resource Manageru, bez ohledu na to, pokud jsou správci.

Jakmile se povolí tyto zásady v tenantovi, všichni uživatelé přihlásí prostředky správy Azure se samy službou Multi-Factor authentication. Pokud uživatel není zaregistrovaný pro MFA, uživatel bude muset zaregistrovat, chcete-li pokračovat pomocí aplikace Microsoft Authenticator.

![Vyžadovat vícefaktorové ověřování pro Azure Resource Manageru](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

Provádět interaktivní přihlášení pomocí [prostředí Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), použijte [připojit AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) rutiny.

```PowerShell
Connect-AzAccount
```

Po spuštění této rutiny se zobrazí řetězec tokenu. Pro přihlášení, zkopírujte tento řetězec a vložte ho do [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) v prohlížeči. Vaše relace PowerShellu se ověří pro připojení k Azure.

K provádění interaktivního přihlášení pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), spusťte [az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) příkazu.

```azurecli
az login
```

Pokud rozhraní příkazového řádku může spustit výchozí prohlížeč, udělá to a načte přihlašovací stránku. V opačném případě budete muset otevřít stránku prohlížeče a postupujte podle pokynů v příkazovém řádku zadejte autorizační kód po přejití do [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) v prohlížeči. Potom se přihlaste se pomocí přihlašovacích údajů k účtu v prohlížeči.

## <a name="deployment-considerations"></a>Aspekty nasazování

Vzhledem k tomu, **vyžadovat vícefaktorové ověřování pro správu služeb** zásady platí pro všechny uživatele Azure Resource Manageru, třeba mít na paměti, které jsou potřeba provést zajistit hladký průběh nasazení. Mezi tyto aspekty patří identifikace uživatelů a zásad služby Azure AD, který nelze nebo by neměly provádět vícefaktorové ověřování, jakož i aplikacím a klientům ve své organizaci, které nepodporují moderní ověřování.

### <a name="user-exclusions"></a>Vyloučení uživatele

Tyto zásady na směrný plán poskytuje možnost vyloučit uživatele. Než povolíte zásady pro vašeho tenanta, doporučujeme, abyste kromě následující účty:

* **Nouzový přístup** nebo **skleněné** účty, aby se zabránilo uzamčení účtu celého tenanta. V nepravděpodobném scénáři, které jsou všichni správci zamknutí mimo vašeho tenanta je možné přihlásit tenanta kroky zkuste obnovit přístup k účtu pro správu přístupu nouze.
   * Další informace najdete v článku, [spravovat účty pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Účty služeb** a **služby Principy**, jako je například účet Azure AD Connect Sync. Účty služeb jsou neinteraktivní účty, které nejsou vázané na konkrétního uživatele. Tyto jsou obvykle používány back endové služby a povolit programový přístup k aplikacím. Účty služby by měl vyloučen, protože nelze dokončit vícefaktorové ověřování, prostřednictvím kódu programu.
   * Pokud má vaše organizace tyto účty používané v skripty a kód, zvažte nahrazení pomocí [spravovaných identit](../managed-identities-azure-resources/overview.md). Jako dočasné řešení můžete vyloučit tyto konkrétní účty ze základní zásady.
* Uživatelé, kteří nemají nebo nebudete moci používat Smartphone.
   * Tato zásada vyžaduje, aby uživatelé registrace pro vícefaktorové ověřování pomocí aplikace Microsoft Authenticator.

## <a name="enable-the-baseline-policy"></a>Povolit zásady směrný plán

Zásady **směrný plán zásad: Vyžadovat vícefaktorové ověřování pro službu správy (preview)** vybavená předem nakonfigurovaným a se zobrazí v horní části, když přejdete do okna podmíněného přístupu na webu Azure portal.

Tuto zásadu povolit a chránit vaše správce:

1. Přihlaste se k **webu Azure portal** jako globální správce, správce zabezpečení nebo správce podmíněného přístupu.
1. Přejděte do **Azure Active Directory** > **podmíněného přístupu**.
1. V seznamu zásad, vyberte **směrný plán zásad: Vyžadovat vícefaktorové ověřování pro službu správy (preview)**.
1. Nastavte **povolit zásady** k **použít zásady okamžitě**.
1. Kliknutím na Přidat všechny uživatele vyloučení **uživatelé** > **vybrat vyloučené uživatele** a výběru uživatelů, které je třeba vyloučit. Klikněte na tlačítko **vyberte** pak **provádí**.
1. Klikněte na tlačítko **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace naleznete v tématu:

* [Zásady podmíněného přístupu směrný plán ochrany](concept-baseline-protection.md)
* [Zabezpečení vaší infrastruktury identit v pěti krocích](../../security/azure-ad-secure-steps.md)
* [Co je podmíněný přístup v Azure Active Directory?](overview.md)