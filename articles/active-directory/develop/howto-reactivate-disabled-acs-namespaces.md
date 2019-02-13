---
title: Opětovná aktivace deaktivovaných oborů názvů Azure Access Control Service (ACS)
description: Zjistěte, jak najít a povolit obory názvů Azure Access Control Service (ACS) a požádali o prodloužení Novoroční povolená až do 4. února 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0078c260e09ec9fd31ecf2124bc4bf7ad7f92b0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173738"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Postup: Opětovná aktivace zakázaných oborů názvů služby Access Control Service

V listopadu 2017 jsme oznámili, že Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), se vyřazuje k 7. listopadu 2018.

Protože potom odeslali jsme vám e-mailů k e-mailu správce že ACS předplatná informace o vyřazení ACS 12 měsíců, 9 měsíců, 6 měsíců, 3 měsíce, 1 měsíc, 2 týdny, 1 týden, a 1 den před vyřazením 7. listopadu 2018.

3. října 2018 jsme oznámili (prostřednictvím e-mailu a [blogový příspěvek](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) rozšíření nabídky zákazníkům, kteří nejde dokončit, jejich migraci před 7. listopadu 2018. Oznámení bylo také pokyny, jak požaduje rozšíření.

## <a name="why-your-namespace-is-disabled"></a>Proč je neaktivní váš obor názvů

Pokud rozšíření ještě výslovného souhlasu, začneme zakazovat obory názvů ACS od 7. listopadu 2018. Požádali jste o musí rozšíření do 4. února 2019 již; v opačném případě nebudete moct povolit oborů názvů pomocí prostředí PowerShell.

> [!NOTE]
> Musíte být správce služeb nebo spolusprávcem předplatného, který chcete spustit příkazy Powershellu a požádali o prodloužení.

## <a name="find-and-enable-your-acs-namespaces"></a>Najít a povolit obory názvů ACS

ACS Powershellu můžete použít k zobrazení seznamu všech oborů názvů ACS a znovu aktivovat, které byly zakázány.

1. Stáhněte a nainstalujte prostředí PowerShell služby ACS:
    1. Přejít do Galerie prostředí PowerShell a stáhnout [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
    1. Instalace modulu:

        ```powershell
        Install-Module -Name Acs.Namespaces
        ```

    1. Získání seznamu všech možných příkazů:

        ```powershell
        Get-Command -Module Acs.Namespaces
        ```

        Pokud chcete zobrazit nápovědu ke konkrétnímu příkazu, spusťte:

        ```powershell
        Get-Help [Command-Name] -Full
        ```
    
        kde `[Command-Name]` je název příkazu ACS.
1. Připojte se k používání služby ACS **AcsAccount připojit** rutiny. 

    Budete muset změnit vaše zásady spouštění spuštěním **Set-ExecutionPolicy** před spuštěním příkazu.
1. Dostupná předplatná Azure pomocí seznamu **Get-AcsSubscription** rutiny.
1. Seznam pomocí oborů názvů ACS **Get-AcsNamespace** rutiny.
1. Potvrďte, že tím, že jsou zakázané obory názvů `State` je `Disabled`.

    [![Potvrďte, že jsou zakázané obory názvů](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png)](./media/howto-reactivate-disabled-acs-namespaces/confirm-disabled-namespace.png#lightbox)

    Můžete také použít `nslookup {your-namespace}.accesscontrol.windows.net` pro potvrzení, pokud doména je pořád aktivní.

1. Povolení s využitím ACS namespace (s) **povolit AcsNamespace** rutiny.

    Po povolení vaše namespace (s), můžete požádat o rozšíření tak, aby namespace (s) nebude znovu před 4. února 2019 zakázáno. Po tomto datu selžou všechny žádosti o služby ACS.

## <a name="request-an-extension"></a>Žádost o rozšíření

Jsme se rozhodli nové žádosti o rozšíření od 21. ledna 2019.

Začneme zakázání obory názvů pro zákazníky, kteří odeslali žádost o rozšíření do 4. února 2019. Můžete stále znovu povolit oborů názvů pomocí Powershellu, ale obory názvů bude zakázáno znovu po 48 hodinách.

Po 4 března 2019 zákazníci už budou moct znovu povolit všechny obory názvů pomocí prostředí PowerShell.

Další rozšíření se už být automaticky schvalovat. Pokud budete potřebovat další čas na migraci, obraťte se na [podpory Azure](https://portal.azure.com/#create/Microsoft.Support) stanovit časovou osu migrace podrobné.

### <a name="to-request-an-extension"></a>Chcete-li požádat o prodloužení

1. Připojte se k webu Azure portal a vytvoření [novou žádost o podporu](https://portal.azure.com/#create/Microsoft.Support).
1. Vyplňte formulář nové žádosti o podporu, jak je znázorněno v následujícím příkladu.

    | Pole žádosti o podporu | Hodnota |
    |-----------------------|--------------------|
    | **Typ problému** | `Technical` |
    | **Předplatné** | Nastavte na předplatné |
    | **Služba** | `All services` |
    | **Prostředek** | `General question/Resource not available` |
    | **Typ problému** | `ACS to SAS Migration` |
    | **Předmět** | Popište problém |

  ![Nová žádost o technickou podporu](./media/howto-reactivate-disabled-acs-namespaces/new-technical-support-request.png)

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

- Pokud narazíte na problémy při dodržení tento návod, obraťte se na [podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Pokud máte dotazy nebo připomínky o vyřazení z provozu služby ACS, kontaktujte nás na adrese acsfeedback@microsoft.com.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o vyřazení z provozu služby ACS v [jak: Migrace z Azure Access Control Service](active-directory-acs-migration.md).
