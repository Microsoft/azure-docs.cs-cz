---
title: Opravy chyby "Odtud se k tomu nemůžete dostat" – Azure AD
description: Najděte potenciální opravy, proč se vám zobrazuje chybová zpráva "Odtud se k ní nemůžete dostat".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 2484de4e554d16ba049d206981a44654ede28a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190026"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Možné opravy chybové zprávy "Odtud se tam nemůžete dostat"

Při přístupu k interním webovým aplikacím nebo službám vaší organizace se může zobrazit chybová zpráva, že **se k vám odtud nedostanete**. Tato zpráva znamená, že vaše organizace zavedla zásady, které zařízení brání v přístupu k prostředkům vaší organizace. I když budete muset kontaktovat helpdesk k vyřešení tohoto problému, zde je několik věcí, které můžete vyzkoušet jako první.

## <a name="make-sure-youre-using-a-supported-browser"></a>Ujistěte se, že používáte podporovaný prohlížeč
Pokud se vám zobrazí zpráva **Nedostanete se k nim a** zobrazí se zpráva, že se pokoušíte získat přístup k webům vaší organizace z nepodporovaného prohlížeče, zkontrolujte, který prohlížeč používáte.

![Chybová zpráva související s podporou prohlížeče](media/user-help-device-remediation/browser-version.png)

Chcete-li tento problém vyřešit, je nutné nainstalovat a spustit podporovaný prohlížeč založený na operačním systému. Pokud používáte Windows 10, podporované prohlížeče zahrnují Microsoft Edge, Internet Explorer a Google Chrome. Pokud používáte jiný operační systém, můžete zkontrolovat úplný seznam [podporovaných prohlížečů](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Ujistěte se, že používáte podporovaný operační systém
Ujistěte se, že používáte podporovanou verzi operačního systému, včetně:

- **Klient systému Windows.** Windows 7 nebo novější.

- **Windows Server.** Systém Windows Server 2008 R2 nebo novější.

- **Macos.** macOS X nebo novější

- **Android a iOS.** Nejnovější verze mobilních operačních systémů Android a iOS

Chcete-li tento problém vyřešit, je nutné nainstalovat a spustit podporovaný operační systém.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Zkontrolujte, zda je zařízení připojeno k síti.
Pokud se vám zobrazí zpráva **Nedostanete se** k němu, že vaše zařízení nesplňuje podmínky pro přístup vaší organizace, ujistěte se, že jste se připojili k síti vaší organizace.

![Chybová zpráva související s tím, zda se v síti nejste](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Kontrola, zda je zařízení připojeno k síti
1. Přihlaste se k Windows pomocí pracovního nebo školního účtu. Například, alain@contoso.com.

2. Připojte se k síti vaší organizace prostřednictvím virtuální privátní sítě (VPN) nebo technologie DirectAccess.

3. Po připojení zařízení uzamkněte stisknutím **klávesy s logem Windows+L.**

4. Odemkněte zařízení pomocí pracovního nebo školního účtu a zkuste znovu získat přístup k problematické aplikaci nebo službě.

    Pokud se zobrazí chybová zpráva **Nemůžete se k ní dostat** znovu, vyberte odkaz Další **podrobnosti** a poté se obraťte na helpdesk s podrobnostmi.

### <a name="to-join-your-device-to-your-network"></a>Připojení zařízení k síti
Pokud vaše zařízení není připojeno k síti vaší organizace, můžete udělat jednu ze dvou věcí:

- **Připojte se k pracovnímu zařízení.** Připojte se k zařízení s Windows 10 vlastněné prací do sítě vaší organizace, abyste měli přístup k potenciálně omezeným prostředkům. Další informace a podrobné pokyny najdete v tématu [Připojení pracovního zařízení k síti vaší organizace](user-help-join-device-on-network.md).

- **Zaregistrujte své osobní zařízení pro práci.** Zaregistrujte své osobní zařízení, obvykle telefon nebo tablet, v síti vaší organizace. Po registraci má zařízení přístup k omezeným prostředkům vaší organizace. Další informace a podrobné pokyny naleznete v tématu [Registrace osobního zařízení v síti organizace](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Další kroky
- [Co je portál MyApps?](active-directory-saas-access-panel-introduction.md)

- [Přihlášení pomocí telefonu, ne pomocí hesla](user-help-auth-app-sign-in.md)
