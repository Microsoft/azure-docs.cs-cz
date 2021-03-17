---
title: Přidání vlastní domény – Azure Active Directory | Microsoft Docs
description: Pokyny k přidání vlastní domény pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: elkuzmen
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28847ece3c08e93d14d381b35fc93fb16dad95a1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973894"
---
# <a name="add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Přidání vlastního názvu domény pomocí portálu Azure Active Directory

Každý nový tenant Azure AD obsahuje počáteční název domény, *\<domainname> . onmicrosoft.com*. Nemůžete změnit nebo odstranit počáteční název domény, ale můžete přidat názvy vaší organizace. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, která jsou pro vaše uživatele známá, například *alain \@ contoso.com*.

## <a name="before-you-begin"></a>Než začnete

Než budete moct přidat vlastní název domény, vytvořte název domény s doménovým registrátorem. V případě schváleného registrátora domény si přečtěte téma [ICANN registrátori](https://www.icann.org/registrar-reports/accredited-list.html).

## <a name="create-your-directory-in-azure-ad"></a>Vytvoření adresáře ve službě Azure AD

Po získání názvu domény můžete vytvořit svůj první adresář služby Azure AD. Přihlaste se k Azure Portal pro svůj adresář pomocí účtu s rolí **vlastníka** pro předplatné.

Vytvořte nový adresář podle kroků uvedených v části [Vytvoření nového tenanta pro vaši organizaci](active-directory-access-create-new-tenant.md#create-a-new-tenant-for-your-organization).

>[!IMPORTANT]
>Osoba, která vytvořila tenanta, je automaticky globálním správcem tohoto tenanta. Globální správce může do tenanta přidat další správce.

Další informace o rolích předplatného najdete v tématu [role Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

>[!TIP]
> Pokud máte v úmyslu federovat místní službu Windows Server AD pomocí služby Azure AD, musíte při spuštění nástroje Azure AD Connect k synchronizaci adresářů vybrat **plán pro konfiguraci této domény pro jednotné přihlašování s místní službou Active Directory** .
>
> V průvodci v kroku **Doména služby Azure AD** musíte také zaregistrovat stejný název domény, který vyberete, pro federaci pomocí místního adresáře. Pokud se chcete podívat, jak to vypadá nastavení, přečtěte si téma [ověření domény Azure AD vybrané pro federaci](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation). Pokud nástroj Azure AD Connect nemáte, můžete [si ho stáhnout tady](https://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-your-custom-domain-name-to-azure-ad"></a>Přidání vlastního názvu domény do Azure AD

Po vytvoření adresáře můžete přidat vlastní název domény.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *Azure Active Directory* z libovolné stránky. Pak vyberte **vlastní názvy domén**  >  **Přidat vlastní doménu**.

    ![Stránka vlastní názvy domén s zobrazenou součástí přidat vlastní doménu](media/add-custom-domain/add-custom-domain.png)

1. Do **vlastního názvu domény** zadejte nový název vaší organizace, v tomto příkladu *contoso.com*. Vyberte **Přidat doménu**.

    ![Stránka vlastní názvy domén pomocí stránky přidat vlastní doménu](media/add-custom-domain/add-custom-domain-blade.png)

    >[!IMPORTANT]
    >K tomu, aby to fungovalo správně, musíte zahrnout *. com*, *.NET* nebo jakékoli jiné rozšíření nejvyšší úrovně.

    Je přidána neověřená doména. Zobrazí se stránka **contoso.com** se zobrazením informací o DNS. Uložte tyto informace. Později ho budete potřebovat k vytvoření záznamu TXT pro konfiguraci DNS.

    ![Stránka contoso s informacemi o položkách DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-dns-information-to-the-domain-registrar"></a>Přidání údajů DNS u doménového registrátora

Po přidání vlastního názvu domény do Azure AD se musíte vrátit do svého registrátora domény a přidat informace DNS služby Azure AD z zkopírovaného souboru TXT. Vytvořením tohoto záznamu TXT pro vaši doménu ověříte vlastnictví vašeho názvu domény.

Vraťte se do svého registrátora domény a vytvořte nový záznam TXT pro vaši doménu na základě kopírovaných informací DNS. Nastavte hodnotu TTL (Time to Live) na 3600 sekund (60 minut) a pak záznam uložte.

>[!IMPORTANT]
>Můžete zaregistrovat libovolný počet názvů domén, kolik chcete. Každá doména ale získá vlastní záznam TXT ze služby Azure AD. Při zadávání informací o souboru TXT v registrátoru domény buďte opatrní. Pokud omylem zadáte chybné nebo duplicitní informace, budete muset počkat na vypršení časového limitu TTL (60 minut), než to zkusíte znovu.

## <a name="verify-your-custom-domain-name"></a>Ověření vlastního názvu domény

Po registraci vlastního názvu domény se ujistěte, že je platný ve službě Azure AD. Šíření z vašeho registrátora domény do Azure AD může být okamžité nebo může trvat několik dní v závislosti na vašem registrátoru domény.

Pokud chcete ověřit vlastní název domény, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce daného adresáře.

1. Vyhledejte a vyberte *Azure Active Directory* na libovolné stránce a pak vyberte **vlastní názvy domén**.

1. V části **vlastní názvy domén** vyberte vlastní název domény. V tomto příkladu vyberte **contoso.com**.

    ![Fabrikam – stránka vlastní názvy domén se zvýrazněnou společností contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

1. Na stránce **contoso.com** vyberte **ověřit** a ujistěte se, že je vaše vlastní doména správně zaregistrovaná a platná pro Azure AD.

    ![Stránka contoso s informacemi o položkách DNS a tlačítkem ověřit](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

Po ověření vlastního názvu domény můžete odstranit ověřovací soubor TXT nebo MX.

## <a name="common-verification-issues"></a>Běžné problémy s ověřováním

Pokud Azure AD nemůže ověřit vlastní název domény, zkuste následující návrhy:

- **Počkejte alespoň hodinu a zkuste to znovu**. Záznamy DNS se musí nejprve rozšířit a teprve potom může služba Azure AD doménu ověřit. Tento proces může trvat hodinu i déle.

- **Ujistěte se, že je záznam DNS správný.** Vraťte se do lokality registrátora názvu domény. Ujistěte se, že položka je tam a že odpovídá informacím DNS, které poskytuje Azure AD.

  Pokud nemůžete aktualizovat záznam v lokalitě registrátora, sdílejte tuto položku s někým, kdo má oprávnění k přidání položky a ověření správnosti.

- **Ujistěte se, že se název domény už nepoužívá v jiném adresáři.** Název domény se dá ověřit jenom v jednom adresáři. Pokud je název vaší domény aktuálně ověřený v jiném adresáři, nelze ho také ověřit v novém adresáři. Chcete-li vyřešit tento problém duplikace, je nutné odstranit název domény ze starého adresáře. Další informace o odstraňování názvů domén najdete v tématu [Správa vlastních názvů domén](../enterprise-users/domains-manage.md).

- **Ujistěte se, že nemáte žádné nespravované klienty Power BI.** Pokud vaši uživatelé aktivovali Power BI prostřednictvím samoobslužné registrace a vytvořili nespravovaného tenanta pro vaši organizaci, musíte převzít správu jako interní nebo externí správce pomocí prostředí PowerShell. Další informace najdete v tématu o [převzetí nespravovaného adresáře jako správce v Azure Active Directory](../enterprise-users/domains-admin-takeover.md).

## <a name="next-steps"></a>Další kroky

- Přidejte do svého adresáře dalšího globálního správce. Další informace najdete v tématu [přiřazení rolí a správců](active-directory-users-assign-role-azure-portal.md).

- Přidejte uživatele do domény. Další informace najdete v tématu [jak přidat nebo odstranit uživatele](add-users-azure-active-directory.md).

- Spravujte informace o názvu domény v Azure AD. Další informace najdete v tématu [Správa vlastních názvů domén](../enterprise-users/domains-manage.md).

- Pokud máte místní verze Windows serveru, které chcete používat společně s Azure Active Directory, přečtěte si téma [Integrace místních adresářů s Azure Active Directory](../hybrid/whatis-hybrid-identity.md).