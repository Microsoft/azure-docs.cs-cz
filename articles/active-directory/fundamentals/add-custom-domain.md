---
title: Přidání vlastní domény – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat vlastní doménu s využitím Azure Active Directory.
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
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073536"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Přidání vlastního názvu domény pomocí portálu Azure Active Directory

Každý nový tenant služby Azure AD obsahuje počáteční název domény, *\<domainname >. The Microsoft. com*. Nemůžete změnit nebo odstranit počáteční název domény, ale můžete přidat názvy vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která se uživatelům znají, například *alain\@contoso.com*.

## <a name="before-you-begin"></a>Než začnete

Než budete moct přidat vlastní název domény, vytvořte název domény s doménovým registrátorem. Akreditované doménový registrátor, naleznete v tématu [ICANN-Accredited registrátorů](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Vytvoření adresáře ve službě Azure AD

Po získání názvu domény, můžete vytvořit první adresář Azure AD. Přihlaste se k Azure Portal pro svůj adresář pomocí účtu s rolí **vlastníka** pro předplatné.

Vytvořit nový adresář pomocí následujících kroků v [vytvořit nového tenanta pro vaši organizaci](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Osoba, která vytvoří klienta je automaticky globálního správce pro tohoto tenanta. Globální správce můžete přidat další správce pro tenanta.

Další informace o rolích předplatného najdete v tématu [role Azure RBAC](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles).

>[!TIP]
> Pokud máte v úmyslu federovat místní službu Windows Server AD pomocí služby Azure AD, musíte při spuštění nástroje Azure AD Connect k synchronizaci adresářů vybrat **plán pro konfiguraci této domény pro jednotné přihlašování s místní službou Active Directory** .
>
> V průvodci v kroku **Doména služby Azure AD** musíte také zaregistrovat stejný název domény, který vyberete, pro federaci pomocí místního adresáře. Pokud se chcete podívat, jak to vypadá nastavení, přečtěte si téma [ověření domény Azure AD vybrané pro federaci](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Pokud nástroj Azure AD Connect nemáte, můžete [si ho stáhnout tady](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Přidání vlastního názvu domény do Azure AD

Po vytvoření adresáře, můžete přidat vlastní název domény.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *Azure Active Directory* z libovolné stránky. Pak vyberte **vlastní názvy domén** > **Přidat vlastní doménu**.

    ![Stránka vlastní názvy domén s zobrazenou součástí přidat vlastní doménu](media/add-custom-domain/add-custom-domain.png)

1. Do **vlastního názvu domény**zadejte nový název vaší organizace, v tomto příkladu *contoso.com*. Vyberte **Add domain** (Přidat doménu).

    ![Stránka vlastní názvy domén pomocí stránky přidat vlastní doménu](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >K tomu, aby to fungovalo správně, musíte zahrnout *. com*, *.NET*nebo jakékoli jiné rozšíření nejvyšší úrovně.

    Je přidána neověřená doména. Zobrazí se stránka **contoso.com** se zobrazením informací o DNS. Uložte tyto informace. Později ho budete potřebovat k vytvoření záznamu TXT pro konfiguraci DNS.

    ![Contoso stránku s informacemi o záznam DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Přidejte své informace o DNS pro registrátora domény

Po přidání vlastního názvu domény do Azure AD, musíte vrátit vašeho registrátora domény a přidejte informace o Azure AD DNS ze zkopírovaného souboru TXT. Vytvořením tohoto záznamu TXT pro vaši doménu ověříte vlastnictví vašeho názvu domény.

Vraťte se do svého registrátora domény a vytvořte nový záznam TXT pro vaši doménu na základě kopírovaných informací DNS. Nastavte hodnotu TTL (Time to Live) na 3600 sekund (60 minut) a pak záznam uložte.

>[!IMPORTANT]
>Můžete zaregistrovat libovolný počet názvů domén. Každá doména však získá svůj vlastní záznam TXT ze služby Azure AD. Při zadávání informací o souboru TXT v registrátoru domény buďte opatrní. Pokud omylem zadáte chybné nebo duplicitní informace, budete muset počkat na vypršení časového limitu TTL (60 minut), než to zkusíte znovu.

## <a name="verify-your-custom-domain-name"></a>Ověřte název vaší vlastní domény

Po registraci vlastního názvu domény se ujistěte, že je platný ve službě Azure AD. Šíření z vašeho registrátora domény do Azure AD může být okamžité nebo může trvat několik dní v závislosti na vašem registrátoru domény.

Pokud chcete ověřit vlastní název domény, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *Azure Active Directory* na libovolné stránce a pak vyberte **vlastní názvy domén**.

1. V části **vlastní názvy domén**vyberte vlastní název domény. V tomto příkladu vyberte **contoso.com**.

    ![Společnost Fabrikam - vlastní stránku názvy domény, se zvýrazněným společnosti contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na stránce **contoso.com** vyberte **ověřit** a ujistěte se, že je vaše vlastní doména správně zaregistrovaná a platná pro Azure AD.

    ![Stránka Contoso s informace o DNS záznam a na tlačítko Ověřit](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Po ověření vlastního názvu domény můžete odstranit ověřovací soubor TXT nebo MX.

## <a name="common-verification-issues"></a>Běžné problémy ověření

Pokud Azure AD nemůže ověřit vlastní název domény, vyzkoušejte následující návrhy:

- **Počkejte aspoň hodinu a zkuste to znovu**. Záznamy DNS se musí nejprve rozšířit a teprve potom může služba Azure AD doménu ověřit. Tento proces může trvat hodinu i déle.

- **Ujistěte se, že záznam DNS je správná.** Vraťte se do lokality registrátora názvu domény. Ujistěte se, že položka je tam a že odpovídá informacím DNS, které poskytuje Azure AD.

  Pokud nemůžete aktualizovat záznam v lokalitě registrátora, sdílejte tuto položku s někým, kdo má oprávnění k přidání položky a ověření správnosti.

- **Ujistěte se, že název domény se už používá v jiném adresáři.** Název domény se dá ověřit jenom v jednom adresáři. Pokud je název vaší domény aktuálně ověřený v jiném adresáři, nelze ho také ověřit v novém adresáři. Chcete-li vyřešit tento problém duplikace, je nutné odstranit název domény z původního adresáře. Další informace o odstraňování názvů domén najdete v tématu [Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).

- **Ujistěte se, že nemáte žádné nespravované tenantů Power BI.** Pokud vaši uživatelé si aktivovali Powerbi prostřednictvím samoobslužné registrace a vytvořili nespravovaného tenanta pro vaši organizaci, je nutné provést nad správou jako interní nebo externí správu, pomocí Powershellu. Další informace najdete v tématu o [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../users-groups-roles/domains-admin-takeover.md).

## <a name="next-steps"></a>Další kroky

- Přidáte další globální správce adresáře. Další informace najdete v tématu [přiřazení rolí a správců](active-directory-users-assign-role-azure-portal.md).

- Přidejte uživatele do domény. Další informace najdete v tématu [jak přidat nebo odstranit uživatele](add-users-azure-active-directory.md).

- Spravujte vaše informace o názvu domény ve službě Azure AD. Další informace najdete v tématu [Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).

- Pokud máte místní verze systému Windows Server, který chcete použít společně s Azure Active Directory, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../connect/active-directory-aadconnect.md).
