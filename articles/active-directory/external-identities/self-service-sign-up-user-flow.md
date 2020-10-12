---
title: Přidání uživatelského toku samoobslužné registrace – Azure AD
description: Vytváření uživatelských toků pro aplikace sestavené vaší organizací. Uživatelé, kteří navštíví tuto aplikaci, mohou získat účet Guest pomocí možností nakonfigurovaných v toku uživatele.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffb1d5eb756c7bc316a6d8e1de30a95eb1ac5194
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91287045"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app-preview"></a>Přidání uživatelského toku samoobslužné registrace do aplikace (Preview)
> [!NOTE]
> Samoobslužná registrace je funkce veřejné verze Preview služby Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

Můžete vytvářet toky uživatelů pro aplikace, které jsou sestavené vaší organizací. Přidružení toku uživatele k aplikaci vám umožní povolit registraci této aplikace. Můžete zvolit více než jednu aplikaci, kterou chcete přidružit k toku uživatele. Po přidružení toku uživatele k jedné nebo více aplikacím se uživatelé, kteří navštíví tuto aplikaci, budou moci zaregistrovat a získat účet Guest pomocí možností nakonfigurovaných v toku uživatele.

> [!NOTE]
> Můžete přidružit toky uživatelů k aplikacím vytvořeným vaší organizací. Toky uživatelů se nedají použít pro aplikace Microsoftu, jako je SharePoint nebo Teams.

## <a name="before-you-begin"></a>Než začnete

### <a name="add-social-identity-providers-optional"></a>Přidat zprostředkovatele sociálních identit (volitelné)

Služba Azure AD je výchozím zprostředkovatelem identity pro samoobslužné registrace. To znamená, že se uživatelé můžou k účtu Azure AD zaregistrovat ve výchozím nastavení. Poskytovatelé sociálních identit taky můžou být součástí těchto toků registrace pro podporu účtů Google a Facebook.

- [Přidat Facebook do seznamu poskytovatelů sociálních identit](facebook-federation.md)
- [Přidat Google do seznamu poskytovatelů sociálních identit](google-federation.md)

> [!NOTE]
> Pokud je v aktuální verzi Preview přidružený uživatelský tok samoobslužné registrace k aplikaci a odešlete uživateli pozvánku k této aplikaci, uživatel nebude moct použít účet Gmail k uplatnění pozvánky. Alternativním řešením je, že uživatel může projít samoobslužným procesem registrace. Nebo můžou uplatnit pozvánku pomocí přístupu k jiné aplikaci nebo pomocí portálu moje aplikace na https://myapps.microsoft.com .

### <a name="define-custom-attributes-optional"></a>Definovat vlastní atributy (volitelné)

Atributy uživatele jsou hodnoty shromážděné uživatelem během samoobslužného přihlášení. Azure AD obsahuje integrovanou sadu atributů, ale můžete vytvořit vlastní atributy pro použití v toku uživatele. Tyto atributy můžete také číst a zapisovat pomocí rozhraní Microsoft Graph API. Viz [definování vlastních atributů pro toky uživatelů](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Povolit samoobslužnou registraci pro vašeho tenanta

Předtím, než budete moci do svých aplikací přidat samoobslužný uživatelský tok, je nutné povolit funkci pro vašeho tenanta. Po jeho povolení budou ovládací prvky k dispozici v uživatelském toku, který umožňuje přidružit tok uživatele k aplikaci.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. Vyberte **nastavení uživatele**a potom v části **externí uživatelé**vyberte **Spravovat nastavení externí spolupráce**.
4. Nastavte možnost **Povolit samoobslužnou registraci hosta prostřednictvím uživatelských toků (Preview)** na **Ano**.

   ![Povolit samoobslužnou registraci hosta](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)

## <a name="create-the-user-flow-for-self-service-sign-up"></a>Vytvoření toku uživatele pro samoobslužnou registraci

V dalším kroku vytvoříte uživatelský tok pro samoobslužnou registraci a přidáte ho do aplikace.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **toky uživatelů (Preview)** a pak vyberte **Nový tok uživatele**.

   ![Tlačítko pro přidání nového uživatelského toku](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na stránce **vytvořit** zadejte **název** toku uživatele. Všimněte si, že název je automaticky předponou **B2X_1_**.
6. V seznamu **Zprostředkovatelé identity** vyberte jednoho nebo víc zprostředkovatelů identity, které můžou externí uživatelé použít k přihlášení do vaší aplikace. Ve výchozím nastavení je vybraná možnost **registrace Azure Active Directory** . (Další informace o tom, jak přidat zprostředkovatele identity, najdete v části dřív, co [jste začali](#before-you-begin) v tomto článku.)
7. V části **atributy uživatele**vyberte atributy, které mají být od uživatele shromažďovány. U dalších atributů vyberte **Zobrazit další**. Vyberte například možnost **Zobrazit více**a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno**a **poštovní směrovací číslo**. Vyberte **OK**.

   ![Vytvoří novou stránku toku uživatele.](media/self-service-sign-up-user-flow/create-user-flow.png)

8. Vyberte **Vytvořit**.
9. Nový tok uživatele se zobrazí v seznamu **toky uživatelů (Preview)** . V případě potřeby aktualizujte stránku.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Vyberte rozložení formuláře kolekce atributů.

Můžete zvolit pořadí, ve kterém se atributy zobrazí na stránce pro registraci. 

1. Na portálu [Azure Portal](https://portal.azure.com) vyberte **Azure Active Directory**.
2. Vyberte **externí identity**, vyberte **toky uživatelů (Preview)**.
3. V seznamu vyberte tok uživatele samoobslužné registrace.
4. V části **přizpůsobit**vyberte **rozložení stránky**.
5. Zobrazí se atributy, které jste si zvolili ke shromáždění. Chcete-li změnit pořadí zobrazení, vyberte atribut a pak vyberte možnost **Přesunout nahoru**, **Přesunout dolů**, **přesunout do horní části**nebo **Přejít na konec**.
6. Vyberte **Uložit**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Přidání aplikací do uživatelského toku samoobslužné registrace

Nyní můžete přidružit aplikace k toku uživatele.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com) jako správce.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. V části **samoobslužné registrace**vyberte **toky uživatelů (Preview)**.
5. V seznamu vyberte tok uživatele samoobslužné registrace.
6. V nabídce vlevo v části **použít**vyberte **aplikace**.
7. Vyberte **Přidat aplikaci**.

   ![Přiřazení aplikace k toku uživatele](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Vyberte aplikaci ze seznamu. Nebo použijte vyhledávací pole k vyhledání aplikace a pak ji vyberte.
9. Klikněte na **Vybrat**.

## <a name="next-steps"></a>Další kroky

- [Přidat Google do seznamu poskytovatelů sociálních identit](google-federation.md)
- [Přidat Facebook do seznamu poskytovatelů sociálních identit](facebook-federation.md)
- [Použití konektorů rozhraní API k přizpůsobení a rozšíření toků uživatelů přes webová rozhraní API](api-connectors-overview.md)
- [Přidání vlastního pracovního postupu schválení do toku uživatele](self-service-sign-up-add-approvals.md)
