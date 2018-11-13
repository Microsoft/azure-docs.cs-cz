---
title: Opětovná aktivace deaktivovaných oborů názvů Azure Access Control Service (ACS)
description: Zjistěte, jak najít a povolit obory názvů Azure Access Control Service (ACS) a požádali o prodloužení Novoroční povolená až do 4. února 2019.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: celested
ms.reviewer: jlu
ms.custom: aaddev
ms.openlocfilehash: 0f0de122dc3dbd770e91a8412430423bee222b30
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577941"
---
# <a name="how-to-reactivate-disabled-access-control-service-namespaces"></a>Postupy: nová aktivace deaktivovaného Access Control Service obory názvů

V listopadu 2017 jsme oznámili, že Microsoft Azure Access Control Service (ACS), služba Azure Active Directory (Azure AD), se vyřazuje k 7. listopadu 2018.

Protože potom odeslali jsme vám e-mailů k e-mailu správce že ACS předplatná informace o vyřazení ACS 12 měsíců, 9 měsíců, 6 měsíců, 3 měsíce, 1 měsíc, 2 týdny, 1 týden, a 1 den před vyřazením 7. listopadu 2018.

3. října 2018 jsme oznámili (prostřednictvím e-mailu a [blogový příspěvek](https://azure.microsoft.com/blog/one-month-retirement-notice-access-control-service/)) rozšíření nabídky zákazníkům, kteří nejde dokončit, jejich migraci před 7. listopadu 2018. Oznámení bylo také pokyny, jak požaduje rozšíření.

## <a name="why-your-namespace-is-disabled"></a>Proč je neaktivní váš obor názvů

Pokud rozšíření ještě výslovného souhlasu, začneme zakazovat obory názvů ACS od 7. listopadu 2018. Pokud se vynechalo komunikací a přesto chcete vyjádřit výslovný souhlas pro rozšíření do 4. února 2019, postupujte podle pokynů v následujících částech.

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

        ```
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

1. Přejděte na portál pro správu vašeho oboru názvů služby ACS tak, že přejdete do `https://{your-namespace}.accesscontrol.windows.net`.
1. Vyberte **čtení podmínky** tlačítko ke čtení [podmínky použití aktualizovat](https://azure.microsoft.com/support/legal/access-control/), který nasměruje na stránku s aktualizované podmínky použití.

    [![Klikněte na tlačítko podmínky pro čtení](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/read-terms-button-expanded.png#lightbox)

1. Vyberte **žádost o rozšíření** na banner v horní části stránky. Tlačítko bude povoleno pouze po přečtení [podmínky použití aktualizovat](https://azure.microsoft.com/support/legal/access-control/).

    [![Klikněte na tlačítko požádat o rozšíření](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/request-extension-button-expanded.png#lightbox)

1. Po registraci rozšíření požadavek na stránce se aktualizuje s novým bannerem v horní části stránky.

    [![Aktualizovaná stránka s aktualizovat banner](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png)](./media/howto-reactivate-disabled-acs-namespaces/updated-banner-expanded.png#lightbox)

## <a name="help-and-support"></a>Nápověda a podpora

- Pokud narazíte na problémy při dodržení tento návod, obraťte se na [podpory Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
- Pokud máte dotazy nebo připomínky o vyřazení z provozu služby ACS, kontaktujte nás na adrese acsfeedback@microsoft.com.

## <a name="next-steps"></a>Další postup

- Přečtěte si informace o vyřazení z provozu služby ACS v [postupy: Migrace z Azure Access Control Service](active-directory-acs-migration.md).
