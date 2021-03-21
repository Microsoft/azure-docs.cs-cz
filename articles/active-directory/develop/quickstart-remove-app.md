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
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199794"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Jak odebrat aplikaci registrovanou na platformě Microsoft identity

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří zaregistrovali aplikace s platformou Microsoft Identity Platform, můžou potřebovat registraci aplikace odebrat.

V následujících částech se dozvíte, jak:

* Odebrání aplikace vytvořené vámi nebo vaší organizací
* Odebrání aplikace vytvořené jinou organizací

## <a name="prerequisites"></a>Předpoklady

* [Aplikace zaregistrovaná v Tenantovi Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Odebrání aplikace vytvořené vámi nebo vaší organizací

Aplikace, které jste zaregistrovali vy nebo vaše organizace, jsou reprezentované objektem aplikace a instančním objektem ve vašem tenantovi. Další informace najdete v tématu [Objekty aplikací a instanční objekty](./app-objects-and-service-principals.md).

> [!NOTE]
> Odstranění aplikace odstraní také jeho instanční objekt v domovském adresáři aplikace. Pro víceklientské aplikace se objekty instančních objektů v jiných adresářích neodstraní.

Chcete-li odstranit aplikaci, je uvedena jako vlastník aplikace nebo má oprávnění správce.

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Máte-li přístup k více klientům, vyberte v horní nabídce možnost **adresář a filtr předplatného** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: a vyberte klienta, ve kterém je aplikace zaregistrovaná.
1. Vyhledejte a vyberte **Azure Active Directory**. 
1. V části **Spravovat** vyberte **Registrace aplikací**  a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** dané aplikace.
1. Na stránce **Přehled** vyberte **Odstranit**.
1. Přečtěte si důsledky odstranění.  Zaškrtněte políčko, pokud je zobrazeno v dolní části podokna.
1. Vyberte **Odstranit** a potvrďte, že chcete aplikaci odstranit.

## <a name="remove-an-application-authored-by-another-organization"></a>Odebrání aplikace vytvořené jinou organizací

Pokud se díváte na **Registrace aplikací** v kontextu tenanta, na kartě **Všechny aplikace** se zobrazí podmnožina aplikací, které jsou z jiného tenanta a do vašeho tenanta se zaregistrovaly po vyjádření souhlasu. Konkrétněji je reprezentuje pouze instanční objekt ve vašem tenantovi bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi objekty aplikací a instančními objekty najdete v tématu [Objekty aplikací a instanční objekty v Azure AD](./app-objects-and-service-principals.md).

Pokud chcete odebrat přístup aplikace k vašemu adresáři (po udělení souhlasu), musí správce společnosti odebrat instanční objekt aplikace. Správce musí mít globální přístup k správce a může aplikaci odebrat pomocí Azure Portal nebo použít [rutiny Azure AD PowerShell](/previous-versions/azure/jj151815(v=azure.100)) k odebrání přístupu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [aplikacích a instančních objektech](app-objects-and-service-principals.md) v platformě Microsoft identity.
