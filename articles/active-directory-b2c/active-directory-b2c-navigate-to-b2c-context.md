---
title: Přepnutí na tenanta B2C v Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup přepnutí do kontextu tenanta Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: ee6d85788211315412b7cbd3c82532dcd45162f7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697711"
---
# <a name="switching-to-your-azure-ad-b2c-tenant"></a>Přepnutí na tenanta Azure AD B2C

Abyste mohli konfigurovat Azure AD B2C, musíte být v kontextu tenanta Azure AD B2C.

## <a name="log-into-azure-ad-b2c-tenant"></a>Přihlášení k tenantovi Azure AD B2C

Abyste mohli přejít do tenanta Azure AD B2C, musíte být přihlášeni na webu Azure Portal jako globální správce tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Tenanty můžete přepínat kliknutím na vaši e-mailovou adresu nebo váš obrázek v horním pravém rohu.
1. V seznamu `Directory`, který se zobrazí, vyberte tenanta Azure AD B2C, kterého chcete spravovat.

Azure Portal se aktualizuje.  Nyní jste na webu Azure Portal přihlášeni v kontextu vašeho tenanta Azure AD B2C.

## <a name="navigate-to-the-b2c-features-pane"></a>Přechod do podokna s funkcemi B2C

1. Klikněte na **Procházet** v levém navigačním panelu.
1. Klikněte na **Všechny služby** a pak v levém navigačním panelu vyhledejte `Azure AD B2C`.  (Pro připnutí na Úvodní panel vlevo klikněte na hvězdičku nalevo od Azure AD B2C)
1. Kliknutím na **Azure AD B2C** otevřete podokno s funkcemi B2C.
   
    ![Snímek obrazovky přejděte do podokna s funkcemi B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Pro přístup k podoknu s funkcemi B2C musíte být globálním správcem tenanta B2C. Globální správce jiného klienta ani uživatel jakéhokoli klienta nemají k oknu přístup.  Můžete přepnout na svého klienta B2C pomocí přepínače klienta v pravém horním rohu webu Azure Portal.
