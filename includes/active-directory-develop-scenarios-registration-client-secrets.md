---
title: zahrnout soubor
description: zahrnutý soubor pro cílové stránky scénáře klienta (démon, Webová aplikace, webové rozhraní API)
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
ms.openlocfilehash: cd37880be6d518105e880b93a0bd748f7c729d88
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286247"
---
## <a name="registration-of-secrets-or-certificates"></a>Registrace tajných klíčů nebo certifikátů

Podobně jako u libovolné důvěrné klientské aplikace musíte zaregistrovat tajný klíč nebo certifikát. Tajná klíč aplikace můžete zaregistrovat buď pomocí interaktivního prostředí v [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview), nebo pomocí nástrojů příkazového řádku (například PowerShell).

### <a name="registering-client-secrets-using-the-application-registration-portal"></a>Registrace klientských tajných klíčů pomocí portálu pro registraci aplikací

Správa přihlašovacích údajů klienta se stane na stránce **certifikáty & tajných** kódů pro aplikaci:

![image](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)

- Při registraci důvěrné klientské aplikace vygeneruje služba Azure AD tajný klíč aplikace (také nazvaný tajný klíč klienta). K této generaci dojde, když vyberete **nový tajný klíč klienta**. V tomto okamžiku musíte před výběrem možnosti **Uložit**zkopírovat tajný řetězec ve schránce pro použití ve vaší aplikaci. Tento řetězec se už nebude prezentovat.
- certifikát se nahraje v registraci aplikace pomocí tlačítka **Odeslat certifikát** . Azure AD podporuje jenom certifikáty, které jsou v aplikaci registrované přímo, a nedodržují řetězy certifikátů.

Podrobnosti najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API | Přidání přihlašovacích údajů do aplikace](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="registering-client-secrets-using-powershell"></a>Registrace klientských tajných klíčů pomocí PowerShellu

Alternativně můžete svoji aplikaci zaregistrovat ve službě Azure AD pomocí nástrojů příkazového řádku. Ukázka [Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak zaregistrovat tajný klíč aplikace nebo certifikát s aplikací Azure AD:

- Podrobnosti o registraci tajného klíče aplikace najdete v tématu [AppCreationScripts/configure. ps1.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)
- Podrobnosti o tom, jak zaregistrovat certifikát u aplikace, najdete v tématu [AppCreationScripts-withCert/configure. ps1.](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)
