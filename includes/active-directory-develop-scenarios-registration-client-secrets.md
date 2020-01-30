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
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773388"
---
## <a name="register-secrets-or-certificates"></a>Registrace tajných klíčů nebo certifikátů

Stejně jako u jakýchkoli důvěrných klientských aplikací je nutné zaregistrovat tajný klíč nebo certifikát. Tajná klíč aplikace můžete zaregistrovat buď pomocí interaktivního prostředí v [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) nebo pomocí nástrojů příkazového řádku (například PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrace klientských tajných klíčů pomocí portálu pro registraci aplikací

Správa přihlašovacích údajů klienta se stane na stránce **certifikáty & tajných** kódů pro aplikaci:

![Stránka & tajných klíčů certifikátů](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Při registraci důvěrné klientské aplikace vygeneruje služba Azure AD tajný klíč aplikace (také nazvaný tajný klíč klienta). K této generaci dojde, když vyberete **nový tajný klíč klienta**. V tomto okamžiku je nutné zkopírovat tajný řetězec do schránky pro použití ve vaší aplikaci, než vyberete **Uložit**. Tento řetězec se už nebude prezentovat.
- Během registrace aplikace použijete tlačítko **Odeslat certifikát** k nahrání certifikátu. Azure AD podporuje jenom certifikáty, které jsou v aplikaci registrované přímo, a nedodržují řetězy certifikátů.

Podrobnosti najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API | Přidejte do své aplikace přihlašovací údaje](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Registrace klientských tajných klíčů pomocí PowerShellu

Případně můžete svou aplikaci zaregistrovat ve službě Azure AD pomocí nástrojů příkazového řádku. Ukázka [Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak zaregistrovat tajný klíč nebo certifikát aplikace pomocí aplikace Azure AD:

- Podrobnosti o tom, jak zaregistrovat tajný klíč aplikace, najdete v tématu [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Podrobnosti o tom, jak zaregistrovat certifikát s aplikací, najdete v tématu [AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
