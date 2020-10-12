---
title: Znovu aktivovat zakázané obory názvů Azure Access Control Service (ACS)
description: Najděte a povolte své obory názvů Azure Access Control Service (ACS) a požádejte o rozšíření, aby se povolilo až do 4. února 2019.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2019
ms.author: ryanwi
ms.reviewer: jlu
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7bb572e9e22519491290e54417f5ca350d6c0b5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383713"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Postupy: opětovná aktivace zakázaných oborů názvů Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Od listopadu 2017 jsme oznámili, že Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), je využívána 7. listopadu 2018.

Od té doby jsme poslali e-maily e-mailem pro správce předplatných ACS o 12 měsících, 9 měsíců, 6 měsíců, 3 měsíce, 1 měsíc, 2 týdny, 1 týden a 1 den před datem vyřazení 7. listopadu 2018.

3. října 2018 jsme oznámili rozšíření nabídky zákazníkům, kteří [a blog post](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)nemůžou dokončit migraci do 7. listopadu 2018. Oznámení má také pokyny pro vyžádání rozšíření.

## <a name="why-your-namespace-is-disabled"></a>Proč je váš obor názvů zakázaný

Pokud jste nezvolili pro rozšíření, začneme zablokovat obory názvů ACS od 7. listopadu 2018. Musíte si vyžádat rozšíření na 4. února 2019 již dříve. v opačném případě nebudete moci povolit obory názvů prostřednictvím PowerShellu.

> [!NOTE]
> Aby bylo možné spustit příkazy prostředí PowerShell a požádat o rozšíření, musíte být správcem služby nebo spolusprávcem předplatného.

## <a name="find-and-enable-your-acs-namespaces"></a>Vyhledání a povolení oborů názvů služby ACS

Pomocí PowerShellu služby ACS můžete vypsat všechny obory názvů ACS a znovu aktivovat zakázané.

1. Stáhněte a nainstalujte PowerShell služby ACS:
    1. Přejít na Galerie prostředí PowerShell a stáhnout službu [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Nainstalujte modul:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Získat seznam všech možných příkazů:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Chcete-li získat nápovědu ke konkrétnímu příkazu, spusťte příkaz:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        kde `[Command-Name]` je název příkazu ACS.
1. Připojte se k ACS pomocí rutiny **Connect-AcsAccount** . 

    Než budete moct spustit příkaz, možná budete muset změnit zásady spouštění spuštěním rutiny **Set-ExecutionPolicy** .
1. Seznam dostupných předplatných Azure získáte pomocí rutiny **Get-AcsSubscription** .
1. Uveďte obory názvů služby ACS pomocí rutiny **Get-AcsNamespace** .
1. Potvrďte, že jsou obory názvů zakázané potvrzením, že `State` je `Disabled` .

    [![Potvrďte, že jsou obory názvů zakázané.](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Můžete také použít `nslookup {your-namespace}.accesscontrol.windows.net` k potvrzení, zda je doména stále aktivní.

1. Povolte obory názvů ACS pomocí rutiny **Enable-AcsNamespace** .

    Jakmile povolíte obory názvů, můžete požádat o rozšíření, aby se obory názvů znovu zakázaly před 4. února 2019. Po tomto datu nebudou všechny požadavky na službu ACS úspěšné.

## <a name="request-an-extension"></a>Požádat o rozšíření

Připravujeme nové požadavky na rozšíření od 21. ledna 2019.

Začneme zakazovat obory názvů pro zákazníky, kteří požadovali rozšíření od 4. února 2019. Obory názvů můžete i nadále znovu povolit prostřednictvím PowerShellu, ale obory názvů budou po 48 hodinách zakázané.

Po 4. březnu 2019 už zákazníci nebudou moct znovu povolit žádné obory názvů prostřednictvím PowerShellu.

Další rozšíření již nebudou automaticky schválena. Pokud k migraci potřebujete další čas, kontaktujte [podporu Azure](https://portal.azure.com/#create/Microsoft.Support) a poskytněte vám podrobnou časovou osu migrace.

### <a name="to-request-an-extension"></a>Vyžádání rozšíření

1. Přihlaste se k Azure Portal a vytvořte [novou žádost o podporu](https://portal.azure.com/#create/Microsoft.Support).
1. Vyplňte formulář nové žádosti o podporu, jak je znázorněno v následujícím příkladu.

    | Support request pole | Hodnota |
    |-----------------------|--------------------|
    | **Typ problému** | `Technical` |
    | **Předplatné** | Nastavit na vaše předplatné |
    | **Služba** | `All services` |
    | **Prostředek** | `General question/Resource not available` |
    | **Typ problému** | `ACS to SAS Migration` |
    | **Předmět** | Popište problém. |

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

- Pokud narazíte na problémy po následujícím postupu, obraťte se na [podporu Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Pokud máte dotazy nebo připomínky k vyřazení služby ACS, kontaktujte nás na adrese acsfeedback@microsoft.com .

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o vyřazení služby ACS v tématu [Postupy: migrace z Access Control Service Azure](active-directory-acs-migration.md).
