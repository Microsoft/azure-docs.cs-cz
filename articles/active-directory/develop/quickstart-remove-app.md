---
title: 'Postupy: odebrání registrované aplikace z platformy Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: V tomto postupu se naučíte, jak odebrat aplikaci registrovanou na platformě Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 12074b6dc5d27c0d28ceea5ab6d266d1f02ecbe2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938187"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Jak odebrat aplikaci registrovanou na platformě Microsoft identity

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří zaregistrovali aplikace s platformou Microsoft Identity Platform, můžou potřebovat registraci aplikace odebrat.

V následujících částech se dozvíte, jak:

* Odebrání aplikace vytvořené vámi nebo vaší organizací
* Odebrání aplikace vytvořené jinou organizací

## <a name="prerequisites"></a>Požadavky

* [Aplikace zaregistrovaná v Tenantovi Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Odebrání aplikace vytvořené vámi nebo vaší organizací

Aplikace, které jste zaregistrovali vy nebo vaše organizace, jsou reprezentované objektem aplikace a instančním objektem ve vašem tenantovi. Další informace najdete v tématu [Objekty aplikací a instanční objekty](./app-objects-and-service-principals.md).

Chcete-li odstranit aplikaci, je uvedena jako vlastník aplikace nebo má oprávnění správce.

1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a> pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** dané aplikace.
1. Na stránce **Přehled** vyberte **Odstranit**.
1. Vybráním **Ano** potvrďte, že chcete aplikaci odstranit.

## <a name="remove-an-application-authored-by-another-organization"></a>Odebrání aplikace vytvořené jinou organizací

Pokud se díváte na **Registrace aplikací** v kontextu tenanta, na kartě **Všechny aplikace** se zobrazí podmnožina aplikací, které jsou z jiného tenanta a do vašeho tenanta se zaregistrovaly po vyjádření souhlasu. Konkrétněji je reprezentuje pouze instanční objekt ve vašem tenantovi bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi objekty aplikací a instančními objekty najdete v tématu [Objekty aplikací a instanční objekty v Azure AD](./app-objects-and-service-principals.md).

Pokud chcete odebrat přístup aplikace k vašemu adresáři (po udělení souhlasu), musí správce společnosti odebrat instanční objekt aplikace. Tento správce musí mít přístup globálního správce a instanční objekt může odebrat prostřednictvím webu Azure Portal nebo použít [rutiny Azure AD PowerShellu](/previous-versions/azure/jj151815(v=azure.100)) k odebrání přístupu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [aplikacích a instančních objektech](app-objects-and-service-principals.md) v platformě Microsoft identity.
