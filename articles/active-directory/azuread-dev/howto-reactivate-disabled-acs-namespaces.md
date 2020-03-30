---
title: Opětovná aktivace zakázaných oborů názvů služby Azure Access Control Service (ACS)
description: Vyhledejte a povolte obory názvů služby Azure Access Control Service (ACS) a požádejte o rozšíření, které jim umožní jejich povolení až do 4.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: ff0ce05b13fea8409475e3415c5d810d7c79769a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154861"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Postup: Opětovná aktivace zakázaných oborů názvů služby Řízení přístupu

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

V listopadu 2017 jsme oznámili, že Služba Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), je vyřazena v listopadu 7, 2018.

Od té doby jsme odeslali e-maily s administrátorem aslužebných odběrů ACS o odchodu do důchodu ACS 12 měsíců, 9 měsíců, 6 měsíců, 3 měsíce, 1 měsíce, 2 týdny, 1 týden a 1 den před datem odchodu do důchodu v listopadu 7, 2018.

3. října 2018 jsme oznámili (prostřednictvím e-mailu a [blogového příspěvku)](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)nabídku rozšíření pro zákazníky, kteří nemohou dokončit migraci před listopadem 7, 2018. Oznámení mělo také pokyny pro žádost o prodloužení.

## <a name="why-your-namespace-is-disabled"></a>Proč je obor názvů zakázán

Pokud jste se k rozšíření nepřihlásili, začneme zakazovat obory názvů služby ACS od 7. O prodloužení jste již museli požádat do 4. února 2019; v opačném případě nebude možné povolit obory názvů prostřednictvím prostředí PowerShell.

> [!NOTE]
> Chcete-li spouštět příkazy prostředí PowerShell a požadovat rozšíření, musíte být správcem služby nebo spolusprávcem předplatného.

## <a name="find-and-enable-your-acs-namespaces"></a>Vyhledání a povolení oborů názvů acs

Pomocí prostředí ACS PowerShell můžete vypsat všechny obory názvů služby ACS a znovu aktivovat ty, které byly zakázány.

1. Stažení a instalace prostředí ACS PowerShell:
    1. Přejděte do Galerie prostředí PowerShell a stáhněte si [položku Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Nainstalujte modul:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Získejte seznam všech možných příkazů:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Chcete-li získat nápovědu k určitému příkazu, spusťte:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        kde `[Command-Name]` je název příkazu ACS.
1. Připojte se ke službě ACS pomocí rutiny **Connect-AcsAccount.** 

    Před spuštěním příkazu může být nutné změnit zásady provádění spuštěním **zásad y Set-ExecutionPolicy.**
1. Seznam dostupných předplatných Azure pomocí rutiny **Get-AcsSubscription.**
1. Seznam jmenných prostorů služby ACS pomocí rutiny **Get-AcsNamespace.**
1. Potvrďte, že obory názvů `State` `Disabled`jsou zakázány potvrzením, že je .

    [![Zkontrolujte, zda jsou obory názvů zakázány.](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Můžete také `nslookup {your-namespace}.accesscontrol.windows.net` potvrdit, zda je doména stále aktivní.

1. Povolte obornázvů služby ACS pomocí rutiny **Povolit acsnamespace.**

    Jakmile poaktivujete obory názvů, můžete požádat o rozšíření, aby obory názvů nebyly znovu zakázány před 4. Po tomto datu se nezdaří všechny požadavky na službu ACS.

## <a name="request-an-extension"></a>Žádost o prodloužení

21. ledna 2019 přijímáme nové žádosti o prodloužení.

Začneme zastavovat obory názvů pro zákazníky, kteří požádali o rozšíření do února 4, 2019. Stále můžete znovu povolit obory názvů prostřednictvím prostředí PowerShell, ale obory názvů budou po 48 hodinách opět zakázány.

března 2019 už zákazníci nebudou moct znovu povolit žádné obory názvů prostřednictvím prostředí PowerShell.

Další rozšíření již nebudou automaticky schválena. Pokud potřebujete další čas na migraci, obraťte se na [podporu Azure](https://portal.azure.com/#create/Microsoft.Support) a poskytněte podrobnou časovou osu migrace.

### <a name="to-request-an-extension"></a>Chcete-li požádat o prodloužení

1. Přihlaste se k portálu Azure a vytvořte [novou žádost o podporu](https://portal.azure.com/#create/Microsoft.Support).
1. Vyplňte nový formulář žádosti o podporu, jak je znázorněno v následujícím příkladu.

    | Pole žádosti o podporu | Hodnota |
    |-----------------------|--------------------|
    | **Typ problému** | `Technical` |
    | **Předplatné** | Nastavení předplatného |
    | **Služby** | `All services` |
    | **Zdrojů** | `General question/Resource not available` |
    | **Typ problému** | `ACS to SAS Migration` |
    | **Subjekt** | Popište problém |

   ![Ukazuje příklad nové žádosti o technickou podporu.](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

<!--

1. Navigate to your ACS namespace's management portal by going to `https://{your-namespace}.accesscontrol.windows.net`.
1. Select the **Read Terms** button to read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/), which will direct you to a page with the updated Terms of Use.

    [![Select the Read Terms button](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Select **Request Extension** on the banner at the top of the page. The button will only be enabled after you read the [updated Terms of Use](https://azure.microsoft.com/support/legal/access-control/).

    [![Select the Request Extension button](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. After the extension request is registered, the page will refresh with a new banner at the top of the page.

    [![Updated page with refreshed banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)
-->

## <a name="help-and-support"></a>Nápověda a podpora

- Pokud narazíte na nějaké problémy po provedení tohoto návodu, obraťte se na [podporu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Máte-li dotazy nebo zpětnou vazbu acsfeedback@microsoft.comohledně vyřazení acs, kontaktujte nás na adrese .

## <a name="next-steps"></a>Další kroky

- Projděte si informace o vyřazení služby ACS v [části Postup: Migrace ze služby Řízení přístupu Azure](active-directory-acs-migration.md).
