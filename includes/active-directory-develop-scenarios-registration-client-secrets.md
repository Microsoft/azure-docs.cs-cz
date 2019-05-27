---
title: zahrnout soubor
description: zahrňte soubor pro scénář důvěrnému klientovi cílových stránkách (démon, webové aplikace, webové rozhraní API)
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
ms.openlocfilehash: 9ee7422b372993d60c629524eb036b9678e5776c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121853"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrace tajných kódů a certifikátů

Například pro každou aplikaci důvěrnému klientovi musíte zaregistrovat certifikát a tajný klíč. Vaše tajné kódy aplikace můžete zaregistrovat prostřednictvím interaktivního prostředí v [webu Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), nebo pomocí nástrojů příkazového řádku (jako je PowerShell)

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrace klienta tajných kódů pomocí portálu pro registraci aplikace

Probíhá Správa přihlašovacích údajů klienta **certifikáty a tajné kódy** stránky pro aplikaci:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- Azure AD, při registraci aplikace důvěrnému klientovi vygeneruje tajný klíč aplikace (také tajný kód klienta s názvem). Tato generace se stane, když vyberete **nový tajný kód klienta**. V tomto okamžiku musíte zkopírovat tajný řetězec do schránky pro použití ve vaší aplikaci, před výběrem **Uložit**. Tento řetězec už nebude zobrazovat.
- Nahrání certifikátu v aplikaci pomocí registrace **nahrát certifikát** tlačítko

Podrobnosti najdete v tématu [rychlý start: Konfigurovat klientskou aplikaci pro přístup k webovým rozhraním API | Přidání přihlašovacích údajů pro vaši aplikaci](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)

### <a name="registering-client-secrets-using-powershell"></a>Registrace klienta tajných kódů pomocí Powershellu

Alternativně můžete registrace aplikace pomocí Azure AD pomocí nástroje příkazového řádku. [Active-directory-dotnetcore – démon – v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) příklad ukazuje, jak zaregistrovat aplikaci Azure AD tajný klíč aplikace nebo certifikát:

- Podrobnosti o tom, jak zaregistrovat tajný klíč aplikace najdete v tématu [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Podrobnosti o tom, jak zaregistrovat certifikát s aplikací najdete v tématu [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)