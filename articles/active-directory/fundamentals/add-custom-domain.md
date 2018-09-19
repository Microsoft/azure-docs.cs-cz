---
title: Přidání vlastní domény do Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak přidat vlastní doménu na portálu Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: lizross
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.openlocfilehash: e49e362528f5fcd00a13a9fc1b233e62a569fe5a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297942"
---
# <a name="how-to-add-your-custom-domain-name-using-the-azure-active-directory-portal"></a>Postupy: Přidání vlastního názvu domény pomocí portálu Azure Active Directory
Každý nový Azure AD tenanta se dodává s počáteční název domény, *domainname*. onmicrosoft.com. Nelze změnit ani odstranit název domény, ale vaše organizace názvy můžete přidat do seznamu. Přidání vlastních názvů domén vám pomůže vytvořit uživatelská jména, které jsou vaši uživatelé znají, jako je například _alain@contoso.com_.

>[!Note]
>Je nutné zopakovat celý tento proces, od začátku do konce pro každý z vašich vlastních názvů domén.

## <a name="add-a-custom-domain-name"></a>Přidání vlastního názvu domény
Vlastní název domény musíte nejprve přidat do tenanta služby Azure AD.

### <a name="to-add-a-custom-domain-name"></a>Přidání vlastního názvu domény
1. Přihlaste se k [portálu Azure AD](https://portal.azure.com/) pomocí účtu globálního správce adresáře.

<<<<<<< HEAD
> [!TIP]
> Pokud máte v úmyslu federovat místní službu Windows Server AD pomocí služby Azure AD, musíte při spuštění nástroje Azure AD Connect pro synchronizaci adresářů zaškrtnout políčko **Mám v plánu nakonfigurovat pro tuto doménu jednotné přihlašování se svým místním adresářem Active Directory**. V průvodci v kroku **Doména služby Azure AD** musíte také zaregistrovat stejný název domény, který vyberete, pro federaci pomocí místního adresáře. [V těchto pokynech](../hybrid/how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation) uvidíte, jak daný krok v průvodci vypadá. Pokud nástroj Azure AD Connect nemáte, můžete [ho stáhnout tady](http://go.microsoft.com/fwlink/?LinkId=615771).
=======
2. Vyberte **Azure Active Directory**vyberte **vlastní názvy domén**a pak vyberte **přidat vlastní doménu**.
>>>>>>> 73f447b1499ba1f189efb6832ad759f1230e55a2

    ![Fabrikam - Custom domain names blade, with Add custom domain option highlighted](media/add-custom-domain/add-custom-domain.png)

3. Zadejte název nového podnikové doméně do **vlastní název domény** pole (například _contoso.com_) a pak vyberte **přidáním domény**.

    >[!Important]
    >Musí zahrnovat .com, .net nebo jakékoli jinou příponu nejvyšší úrovně pro to správně fungovat.

    ![Společnost Fabrikam - vlastní okna názvy domény, se zvýrazněným tlačítkem Přidat doménu](media/add-custom-domain/add-custom-domain-blade.png)

4. Zkopírujte informace DNS z **Contoso** okno.

    ![Contoso okno s informacemi o záznam DNS](media/add-custom-domain/contoso-blade-with-dns-info.png)

## <a name="add-your-domain-name-with-a-domain-name-registrar"></a>Přidání názvu domény u registrátora názvu domény
V dalším kroku je nutné aktualizovat soubor zóny DNS pro novou vlastní doménu. Můžete použít [zóny DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pro Azure, Office 365 nebo externích záznamů DNS nebo můžete přidat váš nový záznam DNS pomocí různých registrátora DNS (například [správce](https://go.microsoft.com/fwlink/p/?LinkId=402770)).

### <a name="to-add-your-domain-name"></a>Chcete-li přidat název domény 
1. Přihlaste se k registrátora názvu domény pro vaši vlastní doménu. Pokud nemáte správná oprávnění k aktualizaci vašeho položky, budete muset kontaktovat někoho s těmito oprávněními.

2. Po aktualizaci položky DNS u registrátora, je nutné aktualizovat soubor zóny DNS s informacemi, které poskytuje Azure AD.

    >[!Note]
    >Položku DNS nedojde ke změně fungování směrování pošty nebo webhosting.

## <a name="verify-your-custom-domain-name"></a>Ověřte název vaší vlastní domény
Až dokončíte registraci vlastního názvu domény, může trvat několik sekund do několika hodin, než se informace o DNS rozšíří na kam Azure AD je můžou zobrazit jako platný.

### <a name="to-verify-your-custom-domain-name"></a>Chcete-li ověřit vlastní název domény
1. Přihlaste se k [portálu Azure AD](https://portal.azure.com/) pomocí účtu globálního správce adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **vlastní názvy domén**.

3. Na **Fabrikam - vlastní názvy domén** okno, vyberte název vlastní domény **Contoso**.

    ![Společnost Fabrikam - vlastní okna názvy domény, se zvýrazněnou společnosti contoso](media/add-custom-domain/custom-blade-with-contoso-highlighted.png)

4. Na **Contoso** okně vyberte **ověřte** zajistit vaší vlastní domény správně zaregistrován a je platný pro službu Azure AD.

    ![Contoso okno s informacemi o položku DNS a na tlačítko Ověřit](media/add-custom-domain/contoso-blade-with-dns-info-verify.png)

### <a name="common-verification-issues"></a>Běžné problémy ověření
Pokud Azure AD nemůže ověřit vlastní název domény, vyzkoušejte následující návrhy:
- **Počkejte aspoň hodinu a zkuste to znovu**. Záznamy DNS musí rozšířit a teprve potom Azure AD můžete ověřit, že domény a tento proces může trvat hodinu i déle.

- **Ujistěte se, že záznam DNS je správná.** Přejděte zpět na web registrátora názvu domény a ujistěte se, že položka existuje a zda odpovídá názvu DSN položky na základě informací poskytnutých Azure AD.

    Pokud nemůžete aktualizovat record na webu registrátora, musejí sdílet ji s někým, který má správná oprávnění k přidání položky a ověřte, zda že je přesné.

- **Ujistěte se, že název domény už není v jiném adresáři.** Název domény můžete ověřit jenom v jednom adresáři, což znamená, že pokud je název vaší domény aktuálně ověřený v jiném adresáři, není také možné ho ověřit v novém adresáři. Chcete-li vyřešit tento problém duplikace, je nutné odstranit název domény z původního adresáře. Další informace o odstraňování názvů domén najdete v tématu [Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).    

## <a name="next-steps"></a>Další postup
- Přidání uživatelů do domény, najdete v článku [Správa vlastních názvů domén](../users-groups-roles/domains-manage.md).

- Pokud máte místní verze systému Windows Server, který chcete použít společně s Azure Active Directory, naleznete v tématu [integrace místních adresářů se službou Azure Active Directory](../connect/active-directory-aadconnect.md).