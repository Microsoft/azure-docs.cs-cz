---
title: 'Rychlý Start: odebrání registrované aplikace z platformy Microsoft Identity Platform | Azure'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak odebrat aplikaci registrovanou na platformě Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: aa0662ae0b1caa63938f5e07aea10b9af60661d3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173256"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Rychlý Start: odebrání aplikace registrované s platformou Microsoft identity

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS), kteří mají zaregistrované aplikace na platformě Microsoft Identity Platform, můžou potřebovat odebrat registraci aplikace.

V tomto rychlém startu se naučíte:

* Odebrání aplikace vytvořené vámi nebo vaší organizací
* Odebrání aplikace vytvořené jinou organizací

## <a name="prerequisites"></a>Předpoklady

* Dokončení [rychlého startu: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Odebrání aplikace vytvořené vámi nebo vaší organizací

Aplikace, které jste zaregistrovali vy nebo vaše organizace, jsou reprezentované objektem aplikace a instančním objektem ve vašem tenantovi. Další informace najdete v tématu [Objekty aplikací a instanční objekty](./app-objects-and-service-principals.md).

Abyste mohli odstranit aplikaci, musíte být uvedeni jako vlastník aplikace nebo mít oprávnění správce.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak vyberte **Registrace aplikací**. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** dané aplikace.
1. Na stránce **Přehled** vyberte **Odstranit**.
1. Vybráním **Ano** potvrďte, že chcete aplikaci odstranit.

## <a name="remove-an-application-authored-by-another-organization"></a>Odebrání aplikace vytvořené jinou organizací

Pokud se díváte na **Registrace aplikací** v kontextu tenanta, na kartě **Všechny aplikace** se zobrazí podmnožina aplikací, které jsou z jiného tenanta a do vašeho tenanta se zaregistrovaly po vyjádření souhlasu. Konkrétněji je reprezentuje pouze instanční objekt ve vašem tenantovi bez odpovídajícího objektu aplikace. Další informace o rozdílech mezi objekty aplikací a instančními objekty najdete v tématu [Objekty aplikací a instanční objekty v Azure AD](./app-objects-and-service-principals.md).

Pokud chcete odebrat přístup aplikace k vašemu adresáři (po udělení souhlasu), musí správce společnosti odebrat instanční objekt aplikace. Tento správce musí mít přístup globálního správce a instanční objekt může odebrat prostřednictvím webu Azure Portal nebo použít [rutiny Azure AD PowerShellu](/previous-versions/azure/jj151815(v=azure.100)) k odebrání přístupu.

## <a name="next-steps"></a>Další kroky

Další informace o aplikačních a instančních objektech na platformě Microsoft identity:

> [!div class="nextstepaction"]
> [Instanční objekty aplikace a služby v Azure Active Directory](app-objects-and-service-principals.md)