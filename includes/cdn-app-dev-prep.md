---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608713"
---
## <a name="prerequisites"></a>Požadavky
Před zápisem kódu správy CDN, musíte provést nějakou přípravu povolit kód pro interakci s Azure Resource Manager. Chcete-li provést tuto přípravu, musíte:

* Vytvoření skupiny prostředků obsahující profil CDN vytvořený v tomto kurzu
* Konfigurace služby Azure Active Directory pro poskytování ověřování pro aplikaci
* Použití oprávnění pro skupinu prostředků tak, aby s profilem CDN mohli pracovat jenom oprávnění uživatelé z vašeho klienta Azure AD.

### <a name="creating-the-resource-group"></a>Vytvoření skupiny prostředků
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek**.
3. Vyhledejte **skupinu prostředků** a v podokně skupiny prostředků klepněte na tlačítko **Vytvořit**.

    ![Vytvoření nové skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Pojmenujte skupinu prostředků *CdnConsoleTutorial*.  Vyberte předplatné a zvolte umístění ve vašem okolí.  Pokud chcete, můžete kliknout na zaškrtávací **políčko Připnout na řídicí panel** a připnout skupinu prostředků k řídicímu panelu na portálu.  Připnutí usnadňuje pozdější vyhledání.  Po výběru klikněte na **Vytvořit**.

    ![Pojmenování skupiny prostředků](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Pokud jste ji po vytvoření skupiny prostředků nepřipisují k řídicímu panelu, najdete ji tak, že kliknete na **Procházet**a potom na **Skupiny prostředků**.  Chcete-li ji otevřít, klepněte na skupinu prostředků.  Poznamenejte si **ID předplatného**. Potřebujeme to později.

    ![Pojmenování skupiny prostředků](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Vytvoření aplikace Azure AD a použití oprávnění
Existují dva přístupy k ověřování aplikací pomocí Služby Azure Active Directory: Jednotliví uživatelé nebo instanční objekt. Instanční objekt je podobný účtu služby v systému Windows.  Namísto udělení oprávnění určitého uživatele k interakci s profily CDN jsou oprávnění místo toho udělena instančnímu objektu.  Instanční objekty se obvykle používají pro automatizované, neinteraktivní procesy.  I když tento kurz je psaní interaktivní konzolové aplikace, zaměříme se na přístup instančního objektu.

Vytvoření instančního objektu se skládá z několika kroků, včetně vytvoření aplikace Služby Azure Active Directory.  Chcete-li jej vytvořit, budeme [postupovat podle tohoto kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).

> [!IMPORTANT]
> Ujistěte se, že postupujte podle všech kroků v [propojeném kurzu](../articles/active-directory/develop/howto-create-service-principal-portal.md).  Je *důležité,* abyste jej dokončili přesně tak, jak je popsáno.  Nezapomeňte si poznamenat **ID klienta**, **název domény klienta** (obvykle doména *.onmicrosoft.com,* pokud jste nezadali vlastní doménu), **ID klienta**a **ověřovací klíč klienta**, protože tyto informace potřebujeme později.  Dávejte pozor, abyste chránili **ID klienta** a **ověřovací klíč klienta**, protože tato pověření mohou být použita kýmkoli ke spuštění operací jako instančního objektu.
>
> Až se dostanete ke kroku s názvem Konfigurovat víceklientské aplikace, vyberte **Ne**.
>
> Když se dostanete ke kroku [Přiřadit aplikaci k roli](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application), použijte skupinu prostředků vytvořenou dříve *CdnConsoleTutorial*, ale místo role **Čtečka** přiřaďte roli **přispěvatele profilu CDN.**  Po přiřazení aplikace role **přispěvatele profilu CDN** ve skupině prostředků se vraťte do tohoto kurzu. 
>
>

Po vytvoření instančního objektu a přiřazení role **přispěvatele profilu CDN** by okno **Uživatelé** pro vaši skupinu prostředků mělo vypadat podobně jako na následujícím obrázku.

![Okno uživatelů](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interaktivní ověřování uživatelů
Pokud místo instančního objektu chcete mít interaktivní individuální ověřování uživatele, je proces podobný procesu pro instanční objekt.  Ve skutečnosti musíte postupovat stejným způsobem, ale provést několik drobných změn.

> [!IMPORTANT]
> Postupujte pouze tyto další kroky, pokud se rozhodnete použít individuální ověřování uživatele namísto instančního objektu služby.
>
>

1. Při vytváření aplikace zvolte místo **webové aplikace** **nativní aplikaci**.

    ![Nativní aplikace](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Na další stránce se zobrazí výzva k **zadání identifikátoru URI přesměrování**.  Identifikátor URI nebude ověřen, ale zapamatujte si, co jste zadali. Budeš to potřebovat později.
3. Není nutné vytvářet **ověřovací klíč klienta**.
4. Místo přiřazení instančního objektu roli **přispěvatele profilu CDN** přiřadíme jednotlivé uživatele nebo skupiny.  V tomto příkladu uvidíte, že jsem přiřadil *cdn demo uživatele* do role **přispěvatele profilu CDN.**  

    ![Přístup jednotlivých uživatelů](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
