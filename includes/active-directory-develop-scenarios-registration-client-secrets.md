---
title: zahrnout soubor
description: zahrnutý soubor pro cílové stránky scénáře klienta (démon, Webová aplikace, webové rozhraní API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995998"
---
## <a name="add-a-client-secret-or-certificate"></a>Přidání tajného klíče nebo certifikátu klienta

Stejně jako u libovolné důvěrné klientské aplikace musíte přidat tajný klíč nebo certifikát, který bude fungovat jako *pověření* této aplikace, aby se mohl ověřit jako sám, bez zásahu uživatele.

Přihlašovací údaje můžete do registrace klientské aplikace přidat pomocí [Azure Portal](#add-client-credentials-by-using-the-azure-portal) nebo pomocí nástroje příkazového řádku, jako je [PowerShell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Přidání přihlašovacích údajů klienta pomocí Azure Portal

Chcete-li přidat pověření k registraci aplikace důvěrné klientské aplikace, postupujte podle kroků v části [rychlý Start: registrace aplikace s platformou Microsoft Identity](../articles/active-directory/develop/quickstart-register-app.md) pro typ přihlašovacích údajů, které chcete přidat:

* [Přidat tajný klíč klienta](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Přidat certifikát](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Přidání přihlašovacích údajů klienta pomocí PowerShellu

Případně můžete přihlašovací údaje přidat při registraci aplikace pomocí platformy Microsoft Identity Platform pomocí prostředí PowerShell.

Ukázka kódu [Active-Directory-dotnetcore-démon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) na GitHubu ukazuje, jak přidat tajný klíč nebo certifikát aplikace při registraci aplikace:

- Podrobnosti o tom, jak přidat **tajný klíč klienta** pomocí PowerShellu, najdete v tématu [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190).
- Podrobnosti o tom, jak přidat **certifikát** pomocí PowerShellu, najdete v tématu [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178).
