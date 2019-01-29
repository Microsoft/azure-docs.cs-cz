---
title: Přepnutí na tenanta B2C v Azure Active Directory B2C | Dokumentace Microsoftu
description: Postup přepnutí do kontextu tenanta Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/13/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9522e921b59ddc57a6039c625bf6b0af4c4b4af2
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181805"
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
   
    ![Snímek obrazovky s přechodem do podokna s funkcemi B2C](./media/active-directory-b2c-get-started/b2c-browse.png)

> [!IMPORTANT]
> Pro přístup k podoknu s funkcemi B2C musíte být globálním správcem tenanta B2C. Globální správce jiného klienta ani uživatel jakéhokoli klienta nemají k oknu přístup.  Můžete přepnout na svého klienta B2C pomocí přepínače klienta v pravém horním rohu webu Azure Portal.
