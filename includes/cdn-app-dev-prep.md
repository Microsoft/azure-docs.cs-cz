---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 4967991b0edaa854acbf6b308596859d662311fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993379"
---
## <a name="prerequisites"></a>Požadavky
Než začnete psát kód pro správu CDN, musíte provést nějakou přípravu, aby kód mohl pracovat s Azure Resource Manager. K provedení této přípravy potřebujete:

* Vytvořte skupinu prostředků, která bude obsahovat profil CDN vytvořený v tomto kurzu.
* Konfigurace Azure Active Directory pro zajištění ověřování pro aplikaci
* Umožňuje použít oprávnění pro skupinu prostředků, aby mohli pracovat jenom autorizovaní uživatelé z vašeho tenanta Azure AD s profilem CDN.

### <a name="creating-the-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Klikněte na **vytvořit prostředek**.
3. Vyhledejte **skupinu prostředků** a v podokně skupina prostředků klikněte na **vytvořit**.

    ![Vytváří se nová skupina prostředků.](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Pojmenujte skupinu prostředků *CdnConsoleTutorial*.  Vyberte své předplatné a zvolte umístění poblíž.  Pokud chcete, můžete kliknutím na zaškrtávací políčko **Připnout na řídicí panel** připnout skupinu prostředků na řídicí panel na portálu.  Připnutí usnadňuje vyhledání později.  Po provedení výběru klikněte na **vytvořit**.

    ![Snímek obrazovky dialogového okna skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Pokud jste po vytvoření skupiny prostředků ji nepřidali do řídicího panelu, můžete ji najít kliknutím na **Procházet** a pak na **skupiny prostředků**.  Pokud ho chcete otevřít, klikněte na skupinu prostředků.  Poznamenejte si **ID předplatného**. Budeme ho potřebovat později.

    ![Snímek obrazovky s oddílem kurzu konzoly C D N.](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Vytvoření aplikace Azure AD a používání oprávnění
Existují dva přístupy k ověřování aplikací pomocí Azure Active Directory: jednotliví uživatelé nebo instančního objektu. Instanční objekt je podobný účtu služby v systému Windows.  Místo udělení konkrétních uživatelských oprávnění k interakci s profily CDN se jim místo toho udělí oprávnění instančnímu objektu.  Instanční objekty se obvykle používají pro automatizované procesy, které nejsou interaktivní.  I když v tomto kurzu píšete interaktivní konzolovou aplikaci, zaměřte se na přístup instančního objektu.

Vytvoření instančního objektu se skládá z několika kroků, včetně vytvoření Azure Active Directory aplikace.  Pokud ho chcete vytvořit, budeme [postupovat podle tohoto kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Nezapomeňte postupovat podle všech kroků v [propojeném kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Je *důležité* , abyste ho dokončili přesně tak, jak je popsáno.  Nezapomeňte si poznamenat **ID tenanta**, **název domény tenanta** (obvykle doménu *. onmicrosoft.com* , pokud jste nezadali vlastní doménu), **ID klienta** a **ověřovací klíč klienta**, protože tyto informace budeme potřebovat později.  Buďte opatrní a zabezpečte **ID klienta** a **ověřovací klíč klienta**, protože tyto přihlašovací údaje můžou použít kdokoli k provádění operací jako instančního objektu.
>
> Až se dostanete ke kroku s názvem konfigurace víceklientské aplikace, vyberte **ne**.
>
> Když se dostanete k kroku [přiřazení aplikace k roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), použijte skupinu prostředků vytvořenou dříve,  *CdnConsoleTutorial*, ale místo role **Čtenář** , přiřaďte roli **Přispěvatel profilu CDN** .  Po přiřazení aplikace k roli **Přispěvatel profilu CDN** ve vaší skupině prostředků se vraťte k tomuto kurzu. 
>
>

Po vytvoření instančního objektu a přiřazení role **Přispěvatel profilu CDN** by okno **Uživatelé** pro skupinu prostředků mělo vypadat podobně jako na následujícím obrázku.

![Okno Uživatelé](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktivní ověřování uživatelů
Pokud místo instančního objektu potřebujete interaktivní ověřování individuálních uživatelů, je tento proces podobný jako u instančního objektu.  Ve skutečnosti je nutné postupovat stejným způsobem, ale proveďte několik menších změn.

> [!IMPORTANT]
> Pokud se rozhodnete použít individuální ověřování uživatele místo instančního objektu, postupujte pouze podle těchto dalších kroků.
>
>

1. Při vytváření aplikace namísto **webové aplikace** vyberte možnost **nativní aplikace**.

    ![Nativní aplikace](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na další stránce se zobrazí výzva k zadání **identifikátoru URI přesměrování**.  Identifikátor URI se neověří, ale zapamatujte si, co jste zadali. Budete ho potřebovat později.
3. Není nutné vytvářet **ověřovací klíč klienta**.
4. Místo přiřazení instančního objektu k roli **Přispěvatel profilů CDN** přiřadíme jednotlivé uživatele nebo skupiny.  V tomto příkladu vidíte, že byl přiřazen  *ukázkový uživatel CDN* k roli **Přispěvatel profilu CDN** .  

    ![Přístup jednotlivých uživatelů](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
