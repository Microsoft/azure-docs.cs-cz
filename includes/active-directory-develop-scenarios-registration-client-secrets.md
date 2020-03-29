---
title: zahrnout soubor
description: zahrnout soubor pro důvěrné klientský scénář vstupní stránky (daemon, webová aplikace, webové ROZHRANÍ API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773388"
---
## <a name="register-secrets-or-certificates"></a>Registrace tajných kódů nebo certifikátů

Stejně jako u jakékoli důvěrné klientské aplikace musíte zaregistrovat tajný nebo certifikát. Tajné klíče aplikací můžete zaregistrovat buď prostřednictvím interaktivního prostředí na [webu Azure Portal,](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) nebo pomocí nástrojů příkazového řádku (například PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrace tajných kódů klientů pomocí portálu pro registraci aplikací

Správa pověření klienta probíhá na stránce **Certifikáty & tajných kódů** pro aplikaci:

![Stránka Certifikáty & tajných kódů](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Tajný klíč aplikace (také s názvem tajný klíč klienta) je generován službou Azure AD během registrace důvěrné klientské aplikace. K této generaci dojde, když vyberete **nový tajný klíč klienta**. V tomto okamžiku musíte zkopírovat tajný řetězec do schránky pro použití v aplikaci, než vyberete **Uložit**. Tento řetězec již nebude prezentován.
- Během registrace aplikace použijete k nahrání certifikátu tlačítko **Nahrát certifikát.** Azure AD podporuje pouze certifikáty, které jsou přímo registrované v aplikaci a nesledují řetězy certifikátů.

Podrobnosti naleznete [v tématu Úvodní příručka: Konfigurace klientské aplikace pro přístup k webovým rozhraním API | Přidejte do aplikace přihlašovací údaje](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Registrace tajných klíčů klientů pomocí Prostředí PowerShell

Případně můžete zaregistrovat aplikaci s Azure AD pomocí nástrojů příkazového řádku. Ukázka [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak zaregistrovat tajný klíč aplikace nebo certifikát s aplikací Azure AD:

- Podrobnosti o tom, jak zaregistrovat tajný klíč aplikace, naleznete v [tématu AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Podrobnosti o registraci certifikátu v aplikaci naleznete v tématu [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
