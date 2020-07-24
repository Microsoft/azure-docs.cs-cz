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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102559"
---
## <a name="register-secrets-or-certificates"></a>Registrace tajných klíčů nebo certifikátů

Stejně jako u jakýchkoli důvěrných klientských aplikací je nutné zaregistrovat tajný klíč nebo certifikát. Tajná klíč aplikace můžete zaregistrovat buď pomocí interaktivního prostředí v [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) nebo pomocí nástrojů příkazového řádku (například PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Registrace klientských tajných klíčů pomocí portálu pro registraci aplikací

Správa přihlašovacích údajů klienta se stane na stránce **certifikáty & tajných** kódů pro aplikaci:

![Stránka & tajných klíčů certifikátů](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Pokud chcete vytvořit *tajný klíč klienta* , vyberte **nový tajný klíč klienta** v registraci aplikace v Azure Portal. Když vytváříte tajný klíč klienta, je _nutné_ před navigací z podokna **certifikáty & tajných** kódů nahrát řetězec tajného kódu. Řetězec tajného klíče se už nezobrazuje znovu.
- Během registrace aplikace použijete tlačítko **Odeslat certifikát** k nahrání certifikátu. Azure AD podporuje jenom certifikáty, které jsou v aplikaci registrované přímo, a nedodržují řetězy certifikátů.

Podrobnosti najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API | Přidejte do své aplikace přihlašovací údaje](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Registrace klientských tajných klíčů pomocí PowerShellu

Případně můžete svou aplikaci zaregistrovat ve službě Azure AD pomocí nástrojů příkazového řádku. Ukázka [Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) ukazuje, jak zaregistrovat tajný klíč nebo certifikát aplikace pomocí aplikace Azure AD:

- Podrobnosti o tom, jak zaregistrovat tajný klíč aplikace, najdete v tématu [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Podrobnosti o tom, jak zaregistrovat certifikát s aplikací, najdete v tématu [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
