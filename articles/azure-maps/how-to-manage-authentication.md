---
title: Správa ověřování
titleSuffix: Azure Maps
description: Seznámení s ověřováním Azure Maps. Podívejte se, který přístup funguje nejlépe v tomto scénáři. Naučte se používat portál k zobrazení nastavení ověřování.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57e847116febcea66e1e3ac4ba131617463b6c94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895762"
---
# <a name="manage-authentication-in-azure-maps"></a>Správa ověřování v Azure Maps

Po vytvoření účtu Azure Maps se vytvoří ID klienta a klíče pro podporu ověřování Azure Active Directory (Azure AD) a ověřování pomocí sdíleného klíče.

## <a name="view-authentication-details"></a>Zobrazit podrobnosti o ověřování

Po vytvoření účtu Azure Maps se vygenerují primární a sekundární klíče. Pokud [ke volání Azure Maps použijete ověřování pomocí sdíleného klíče](./azure-maps-authentication.md#shared-key-authentication), doporučujeme použít primární klíč jako klíč předplatného. Sekundární klíč můžete použít ve scénářích, jako je například vracení klíčových změn. Další informace najdete v tématu [ověřování v Azure Maps](./azure-maps-authentication.md).

Podrobnosti o ověřování můžete zobrazit v Azure Portal. Ve svém účtu v nabídce **Nastavení** vyberte **ověřování**.

> [!div class="mx-imgBorder"]
> ![Podrobnosti o ověřování](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Vyhledat kategorii a scénář

V závislosti na potřebách aplikací existují konkrétní cesty k zabezpečení aplikace. Azure AD definuje kategorie pro podporu široké škály ověřovacích toků. Podívejte se na kategorie [aplikací](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) a zjistěte, kterou kategorii aplikace vyhovuje.

> [!NOTE]
> I když používáte ověřování pomocí sdíleného klíče, porozumět kategoriím a scénářům, které vám pomůžou zajistit zabezpečení aplikace.

## <a name="determine-authentication-and-authorization"></a>Určení ověřování a autorizace

Následující tabulka popisuje běžné scénáře ověřování a autorizace v Azure Maps. Tabulka poskytuje srovnání typů ochrany, které každý scénář nabízí.

> [!IMPORTANT]
> Microsoft doporučuje implementovat Azure Active Directory (Azure AD) s řízením přístupu na základě role Azure (Azure RBAC) pro produkční aplikace.

| Scenario                                                                                    | Authentication | Autorizace | Úsilí při vývoji | Provozní úsilí |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Důvěryhodná klientská aplikace typu démon/neinteraktivní](./how-to-secure-daemon-app.md)        | Sdílený klíč     | –           | Střední             | Vysoká               |
| [Důvěryhodná klientská aplikace typu démon/neinteraktivní](./how-to-secure-daemon-app.md)        | Azure AD       | Vysoká          | Nízká                | Střední             |
| [Aplikace webové stránky s interaktivním jedním přihlašováním](./how-to-secure-spa-users.md) | Azure AD       | Vysoká          | Střední             | Střední             |
| [Aplikace webové stránky s neinteraktivním přihlašováním](./how-to-secure-spa-app.md)      | Azure AD       | Vysoká          | Střední             | Střední             |
| [Webová aplikace s interaktivním jednotným přihlašováním](./how-to-secure-webapp-users.md)          | Azure AD       | Vysoká          | Vysoká               | Střední             |
| [Zařízení nebo vstupní omezené zařízení IoT](./how-to-secure-device-code.md)                     | Azure AD       | Vysoká          | Střední             | Střední             |

Odkazy v tabulce odkazují na podrobné informace o konfiguraci jednotlivých scénářů.

## <a name="view-role-definitions"></a>Zobrazit definice rolí

Pokud chcete zobrazit role Azure, které jsou k dispozici pro Azure Maps, přejděte na **řízení přístupu (IAM)**. Vyberte **role** a potom vyhledejte role, které začínají na *Azure Maps*. Tyto role Azure Maps jsou role, kterým můžete udělit přístup.

> [!div class="mx-imgBorder"]
> ![Zobrazit dostupné role](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Zobrazit přiřazení rolí

Chcete-li zobrazit uživatele a aplikace, kterým byl udělen přístup pro Azure Maps, přejděte na **Access Control (IAM)**. Vyberte možnost **přiřazení rolí** a potom filtrovat podle **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Zobrazit uživatele a aplikace, kterým byl udělen přístup](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Žádosti o tokeny pro Azure Maps

Požádat o token z koncového bodu tokenu služby Azure AD. V žádosti o Azure AD použijte následující podrobnosti:

| Prostředí Azure      | Koncový bod tokenu Azure AD             | ID prostředku Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Veřejný cloud Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Cloud Azure Government | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Další informace o vyžádání přístupových tokenů z Azure AD pro uživatele a instanční objekty najdete v tématu [scénáře ověřování pro Azure AD](../active-directory/develop/authentication-vs-authorization.md) a zobrazení konkrétních scénářů v tabulce [scénářů](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Azure AD a Azure Maps Web SDK](./how-to-use-map-control.md).

Najděte metriky využití API pro váš účet Azure Maps:
> [!div class="nextstepaction"]
> [Zobrazení metrik využití](how-to-view-api-usage.md)

Prozkoumejte ukázky, které ukazují, jak integrovat Azure AD s Azure Maps:

> [!div class="nextstepaction"]
> [Ukázky ověřování Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)