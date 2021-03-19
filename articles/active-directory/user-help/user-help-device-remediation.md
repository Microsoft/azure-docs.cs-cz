---
title: Opravy pro "nemůžete získat z tohoto místa" – Azure AD
description: Najděte si možné opravy, proč se vám zobrazí chybová zpráva "nemůžete získat odsud".
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88799413"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>Možné opravy pro chybovou zprávu "nemůžete se dostat sem"

Při přístupu k interním webovým aplikacím nebo službám vaší organizace se může zobrazit chybová zpráva s informacemi o tom, že se **tam nemůžete dostat**. Tato zpráva znamená, že vaše organizace má zavedené zásady, které brání vašemu zařízení v přístupu k prostředkům vaší organizace. I když se může stát, že se budete muset obrátit na helpdesk, abyste mohli tento problém vyřešit, můžete si nejdřív vyzkoušet několik věcí.

## <a name="make-sure-youre-using-a-supported-browser"></a>Ujistěte se, že používáte podporovaný prohlížeč.
Pokud se vám zobrazí zpráva, že se **tam nedaří získat** přístup k webům vaší organizace z nepodporovaného prohlížeče, podívejte se, jestli je v něm spuštěný prohlížeč.

![Chybová zpráva týkající se podpory prohlížeče](media/user-help-device-remediation/browser-version.png)

Chcete-li tento problém vyřešit, je nutné nainstalovat a spustit podporovaný prohlížeč v závislosti na vašem operačním systému. Pokud používáte Windows 10, podporované prohlížeče zahrnují Microsoft Edge, Internet Explorer a Google Chrome. Pokud používáte jiný operační systém, můžete se podívat na úplný seznam [podporovaných prohlížečů](../conditional-access/concept-conditional-access-conditions.md#supported-browsers).

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Ujistěte se, že používáte podporovaný operační systém.
Ujistěte se, že používáte podporovanou verzi operačního systému, včetně:

- **Klient systému Windows.** Windows 7 nebo novější.

- **Systém Windows Server.** Windows Server 2008 R2 nebo novější.

- **MacOS.** macOS X nebo novější

- **Android a iOS.** Nejnovější verze mobilních operačních systémů Android a iOS

Chcete-li tento problém vyřešit, je nutné nainstalovat a spustit podporovaný operační systém.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Ujistěte se, že je zařízení připojené k síti.
Pokud se vám zobrazí zpráva z tohoto seznamu **nemůžete získat** zprávu, že vaše zařízení je nekompatibilní se zásadami přístupu vaší organizace, ujistěte se, že jste zařízení připojili k síti vaší organizace.

![Chybová zpráva týkající se toho, jestli jste v síti](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Ověření, jestli je zařízení připojené k síti
1. Přihlaste se k Windows pomocí svého pracovního nebo školního účtu. Například, alain@contoso.com.

2. Připojte se k síti vaší organizace pomocí virtuální privátní sítě (VPN) nebo technologie DirectAccess.

3. Po připojení stiskněte klávesu s **logem Windows + L** pro uzamčení zařízení.

4. Odemkněte zařízení pomocí svého pracovního nebo školního účtu a zkuste znovu získat přístup k problematické aplikaci nebo službě.

    Pokud se vám zobrazí chybová zpráva **z tohoto pole nemůžete získat** znovu, vyberte odkaz **Další podrobnosti** a požádejte o pomoc helpdesk.

### <a name="to-join-your-device-to-your-network"></a>Připojení zařízení k síti
Pokud zařízení není připojené k síti vaší organizace, můžete provést jednu z následujících akcí:

- **Připojte se k pracovnímu zařízení.** Připojte zařízení s Windows 10 vlastněné do sítě vaší organizace, abyste měli přístup k potenciálně omezeným prostředkům. Další informace a podrobné pokyny najdete v tématu [připojení pracovního zařízení k síti vaší organizace](user-help-join-device-on-network.md).

- **Zaregistrujte svoje osobní zařízení pro práci.** Zaregistrujte své osobní zařízení, obvykle telefon nebo tablet, do sítě vaší organizace. Po zaregistrování zařízení bude mít přístup k prostředkům s omezeným přístupem vaší organizace. Další informace a podrobné pokyny najdete v tématu [registrace osobního zařízení v síti vaší organizace](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Další kroky
- [Co je to portál MyApp?](./my-apps-portal-end-user-access.md)

- [Přihlášení pomocí telefonu, ne pomocí hesla](user-help-auth-app-sign-in.md)