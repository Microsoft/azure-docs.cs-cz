---
title: Přidání vlastní domény – Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přidat vlastní doménu pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: de91bd7e1e4c5f9909213f663dd3ede0f979d4de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262149"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Přidání vlastního názvu domény pomocí portálu Azure Active Directory

Každý nový klient Azure AD je dodáván s názvem počáteční domény, * \<název domény>.onmicrosoft.com*. Počáteční název domény nemůžete změnit ani odstranit, ale můžete přidat názvy vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která jsou uživatelům známá, například *alain\@contoso.com*.

## <a name="before-you-begin"></a>Než začnete

Před přidáním vlastního názvu domény vytvořte název domény pomocí doménového registrátora. Informace o akreditovaném registrátorovi domén viz [Registrátoři akreditovaní ICANN](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Vytvoření adresáře ve službě Azure AD

Po získání názvu domény můžete vytvořit svůj první adresář Azure AD. Přihlaste se k portálu Azure pro svůj adresář pomocí účtu s rolí **vlastníka** pro předplatné.

Vytvořte nový adresář podle kroků v části [Vytvoření nového klienta pro vaši organizaci](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Osoba, která vytvoří klienta je automaticky globální správce pro tohoto klienta. Globální správce můžete přidat další správce do klienta.

Další informace o rolích předplatného najdete v [tématu Role Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Pokud máte v plánu federate vaše místní Windows Server AD s Azure AD, pak je potřeba vybrat **Mám v plánu nakonfigurovat tuto doménu pro jednotné přihlašování s místní službou Active Directory** při spuštění nástroje Azure AD Connect pro synchronizaci adresářů.
>
> V průvodci v kroku **Doména služby Azure AD** musíte také zaregistrovat stejný název domény, který vyberete, pro federaci pomocí místního adresáře. Informace o tom, jak toto nastavení vypadá, najdete [v tématu Ověření domény Azure AD vybrané pro federaci](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Pokud nástroj Azure AD Connect nemáte, můžete [si ho stáhnout tady](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Přidání vlastního názvu domény do Služby Azure AD

Po vytvoření adresáře můžete přidat vlastní název domény.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *službu Azure Active Directory* na libovolné stránce. Pak vyberte **Vlastní názvy** > domén**Přidat vlastní doménu**.

    ![Stránka Vlastní názvy domén se zobrazenou doplňkem Přidat vlastní doménu](media/add-custom-domain/add-custom-domain.png)

1. Do **pole Vlastní název domény**zadejte nový název organizace, v tomto příkladu *contoso.com*. Vyberte **Add domain** (Přidat doménu).

    ![Stránka Vlastní názvy domén s vlastní stránkou Přidat vlastní doménu](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >Chcete-li, aby to fungovalo správně, je nutné zahrnout *rozhraní .com*, *.net*nebo jakékoli jiné rozšíření nejvyšší úrovně.

    Neověřená doména je přidána. Zobrazí se **stránka contoso.com** zobrazující informace dns. Uložte tyto informace. K vytvoření záznamu TXT pro konfiguraci DNS jej později potřebujete.

    ![Stránka Contoso s informacemi o položce DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Přidání informací DNS do doménového registrátora

Po přidání vlastního názvu domény do Služby Azure AD se musíte vrátit k doménovému registrátorovi a přidat informace dns Azure AD z kopírovaného souboru TXT. Vytvoření tohoto záznamu TXT pro vaši doménu ověří vlastnictví názvu domény.

Vraťte se k doménovým registrátorům a vytvořte nový txt záznam pro vaši doménu na základě zkopírovaných informací DNS. Nastavte čas k životu (TTL) na 3600 sekund (60 minut) a uložte záznam.

>[!IMPORTANT]
>Můžete zaregistrovat libovolný počet doménových jmen. Každá doména však získá svůj vlastní txt záznam z Azure AD. Buďte opatrní při zadávání informací o souboru TXT v doménovém registrátorovi. Pokud omylem zadáte nesprávné nebo duplicitní informace, budete muset počkat, až časový výběh TTL (60 minut), než to budete moci zkusit znovu.

## <a name="verify-your-custom-domain-name"></a>Ověření vlastního názvu domény

Po registraci vlastního názvu domény se ujistěte, že je platný ve službě Azure AD. Šíření z vašeho doménového registrátora do Azure AD může být okamžité nebo může trvat několik dní, v závislosti na vašem doménovém registrátorovi.

Chcete-li ověřit vlastní název domény, postupujte takto:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *službu Azure Active Directory* na libovolné stránce a vyberte **vlastní názvy domén**.

1. V **části Vlastní názvy domén**vyberte vlastní název domény. V tomto příkladu vyberte **contoso.com**.

    ![Fabrikam – stránka vlastních názvů domén se zvýrazněnou položkou contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na **stránce contoso.com** vyberte **Ověřit,** abyste se ujistili, že je vaše vlastní doména správně zaregistrována a je platná pro Azure AD.

    ![Stránka Contoso s informacemi o položce DNS a tlačítkem Ověřit](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Po ověření vlastního názvu domény můžete odstranit ověřovací soubor TXT nebo MX.

## <a name="common-verification-issues"></a>Běžné problémy s ověřováním

Pokud Azure AD nemůže ověřit vlastní název domény, vyzkoušejte následující návrhy:

- **Počkejte alespoň hodinu a akci opakujte**. Záznamy DNS se musí nejprve rozšířit a teprve potom může služba Azure AD doménu ověřit. Tento proces může trvat hodinu i déle.

- **Zkontrolujte správnou hodnotu záznamu DNS.** Vraťte se na web registrátora doménových jmen. Ujistěte se, že položka je tam a že odpovídá informace o zadávání DNS poskytované Azure AD.

  Pokud nemůžete aktualizovat záznam na webu registrátora, sdílejte položku s někým, kdo má oprávnění k přidání položky, a ověřte, zda je správná.

- **Ujistěte se, že název domény ještě není používán v jiném adresáři.** Název domény lze ověřit pouze v jednom adresáři. Pokud je název domény aktuálně ověřen v jiném adresáři, nelze jej ověřit ani v novém adresáři. Chcete-li tento problém s duplikací vyřešit, je nutné odstranit název domény ze starého adresáře. Další informace o odstranění názvů domén naleznete v tématu [Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).

- **Ujistěte se, že nemáte žádné nespravované klienty Power BI.** Pokud vaši uživatelé aktivovali Power BI prostřednictvím samoobslužné registrace a vytvořili pro vaši organizaci nespravovaného klienta, musíte převzít správu jako interní nebo externí správce pomocí PowerShellu. Další informace najdete v tématu o [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Další kroky

- Přidejte do adresáře dalšího globálního správce. Další informace naleznete v tématu [Jak přiřadit role a správce](active-directory-users-assign-role-azure-portal.md).

- Přidejte uživatele do domény. Další informace naleznete v tématu [Jak přidat nebo odstranit uživatele](add-users-azure-active-directory.md).

- Správa informací o názvu domény ve službě Azure AD. Další informace naleznete v [tématu Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).

- Pokud máte místní verze Windows Serveru, které chcete používat společně s Azure Active Directory, přečtěte si článek [Integrace místních adresářů pomocí Služby Azure Active Directory](../connect/active-directory-aadconnect.md).
