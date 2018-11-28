---
title: Odebrání aplikace z Azure Active Directory
description: Zjistěte, jak odebrat aplikaci z Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: ddc04d32f76515cb93d2cde3fe4eccaab7e4c291
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245354"
---
# <a name="quickstart-remove-an-application-from-azure-active-directory"></a>Rychlý start: Odebrání aplikace z Azure Active Directory

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří mají zaregistrované aplikace v Azure Active Directory (Azure AD), mohou potřebovat odebrat registraci aplikace z tenanta Azure AD.

V tomto rychlém startu se naučíte:

* [Odebrat aplikaci vytvořenou vaší organizací](#removing-an-application-authored-by-your-organization)
* [Odebrat aplikaci s více tenanty vytvořenou vaší organizací](#removing-a-multi-tenant-application-authorized-by-another-organization)

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat tenanta služby Azure AD, ve kterém jsou aplikace zaregistrované.

* Pokud ještě tenanta nemáte, [zjistěte, jak ho získat](quickstart-create-new-tenant.md).
* Pokud chcete zjistit, jak do tenanta přidat a zaregistrovat aplikace, přejděte na článek o [přidání aplikace do Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="removing-an-application-authored-by-your-organization"></a>Odebrání aplikace vytvořené vaší organizací

K zobrazení aplikací zaregistrovaných vaší organizací můžete použít filtr **Moje aplikace** na hlavní stránce tenanta **Registrace aplikací**. Tyto aplikace jste zaregistrovali ručně prostřednictvím webu Azure Portal nebo programově prostřednictvím PowerShellu či rozhraní API pro Microsoft Graph. Konkrétněji jsou tyto aplikace v tenantovi reprezentované jak objektem aplikace, tak i instančním objektem. Další informace o těchto objektech najdete v tématu o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

### <a name="to-remove-a-single-tenant-application-from-your-directory"></a>Postup odebrání aplikace s jedním tenantem z adresáře

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud váš účet umožňuje přístup k více účtům, vyberte požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory**, vyberte **Registrace aplikací** a potom najděte a vyberte aplikaci, kterou chcete nakonfigurovat.
    Tím přejdete na hlavní stránku registrace aplikace, která se otevře na stránce **Nastavení**.
1. Na hlavní stránce registrace aplikace vyberte **Odstranit**.
1. Vybráním **Ano** potvrďte, že chcete aplikaci odstranit.

### <a name="to-remove-a-multi-tenant-application-from-its-home-directory"></a>Postup odebrání aplikace s více tenanty z jejího domovského adresáře

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Pokud váš účet umožňuje přístup k více účtům, vyberte požadovaný účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory**, vyberte **Registrace aplikací** a potom najděte a vyberte aplikaci, kterou chcete nakonfigurovat.
    Tím přejdete na hlavní stránku registrace aplikace, která se otevře na stránce **Nastavení**.
1. Na stránce **Nastavení** nejprve vyberete **Vlastnosti** a možnost **Více tenantů** přepnete na **Ne**, abyste aplikaci změnili na aplikaci s jedním tenantem. Potom vyberte **Uložit**.
    Instanční objekty aplikace zůstanou v tenantech všech organizací, které s tím už vyjádřily souhlas.
1. Na hlavní stránce registrace aplikace vyberte **Odstranit**.
1. Vybráním **Ano** potvrďte, že chcete aplikaci odstranit.

## <a name="removing-a-multi-tenant-application-authorized-by-another-organization"></a>Odebrání aplikace s více tenanty vytvořené vaší organizací

Podmnožina aplikací, které se zobrazují ve filtru **Všechny aplikace** (s výjimkou registrací z filtru **Moje aplikace**) na hlavní stránce tenanta **Registrace aplikací**, je aplikacemi s více tenanty.

Z technického hlediska jsou tyto aplikace s více tenanty z jiného tenanta a do vašeho tenanta se zaregistrovaly po vyjádření souhlasu. Konkrétněji je reprezentuje pouze instanční objekt ve vašem tenantovi bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi objekty aplikací a instančními objekty najdete v tématu o [objektech aplikací a instančních objektech v Azure AD](app-objects-and-service-principals.md).

Pokud chcete odebrat přístup aplikace s více tenanty k vašemu adresáři (po udělení souhlasu), musí správce společnosti odebrat instanční objekt aplikace. Tento správce musí mít přístup globálního správce a instanční objekt může odebrat pouze prostřednictvím webu Azure Portal nebo použitím [rutin Azure AD PowerShellu](https://go.microsoft.com/fwlink/?LinkId=294151).

## <a name="next-steps"></a>Další kroky

Získejte další informace o těchto rychlých startech souvisejících se správou aplikací používajících koncový bod Azure AD v1.0:

- [Přidání aplikace do Azure AD](quickstart-v1-integrate-apps-with-azure-ad.md)
- [Aktualizace aplikace v Azure AD](quickstart-v1-update-azure-ad-app.md)
