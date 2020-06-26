---
title: Změna kontaktních informací pro účet Azure
description: Článek vysvětluje, jak změnit kontaktní informace pro účet správce Azure.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d1c38057a32f8abc5fa289bc5ab96b8f65ddfb06
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2020
ms.locfileid: "85209915"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>Změna kontaktních informací pro účet Azure

Tento článek vám pomůže aktualizovat kontaktní informace pro váš účet na webu Azure Portal. Pokyny pro aktualizaci kontaktních údajů se liší podle typu fakturačního účtu. Další informace o fakturačních účtech a určení typu fakturačního účtu najdete v tématu [Zobrazení fakturačních účtů na webu Azure Portal](view-all-accounts.md).

*Adresa kupujícího:* Adresa kupujícího jsou adresa a kontaktní informace organizace nebo jednotlivce, který zodpovídá za fakturační účet. Je uvedená na všech fakturách vygenerovaných pro konkrétní fakturační účet.

*Fakturační adresa:* Fakturační adresa jsou adresa a kontaktní informace organizace nebo jednotlivce, který zodpovídá faktury vygenerované pro fakturační účet. Pro fakturační účet MOSP (Microsoft Online Service Program, MOSP) existuje jedna fakturační adresa, která je uvedená na všech fakturách vygenerovaných pro tento účet. Pro fakturační účet MCA (Microsoft Customer Agreement, Smlouva se zákazníkem Microsoftu) existuje fakturační adresa pro každý fakturační profil, která je uvedená na všech fakturách vygenerovaných pro tento profil.

*Kontaktní e-mailová adresa pro oznámení služeb a marketingové e.maily:* Můžete zadat e-mailovou adresu (která se liší od e-mailové adresy, pomocí které jste se přihlásili) pro získání důležitých oznámení týkajících se fakturace, služeb a doporučení v souvislosti s vaším účtem Azure.  

## <a name="update-an-mosp-billing-account-address"></a>Aktualizace adresy fakturačního účtu MOSP

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí e-mailové adresy, která má k danému účtu oprávnění správce účtu.
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/change-azure-account-profile/search-cmb.png)
1. Na levé straně vyberte **Vlastnosti**.  
    ![Screenshot zobrazující stránku pro aktualizaci adresy](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. Pokud chcete aktualizovat adresu kupujícího a fakturační adresu, vyberte **Aktualizovat fakturační adresu**. Zadejte novou adresu a potom vyberte **Uložit**.  
    ![Screenshot zobrazující stránku pro aktualizaci adresy](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>Aktualizace adresy kupujícího pro fakturační účet MCA

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí e-mailové adresy, která má ve fakturačním účtu pro Smlouvu se zákazníkem Microsoftu roli vlastníka nebo přispěvatele.
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/change-azure-account-profile/search-cmb.png)
1. Na levé straně vyberte **Vlastnosti** a potom vyberte **Aktualizovat kupujícího**.  
    ![Screenshot znázorňující výběr položky Aktualizovat kupujícího](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. Zadejte novou adresu a potom vyberte **Uložit**.  
    ![Screenshot zobrazující aktualizaci adresy](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > Některé účty při aktualizaci kupujícího vyžadují dodatečné ověření. Pokud váš účet vyžaduje manuální schválení, budete požádáni, abyste kontaktovali podporu Azure.

## <a name="update-an-mca-billing-account-address"></a>Aktualizace adresy fakturačního účtu MCA

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) pomocí e-mailové adresy, která má ve fakturačním účtu nebo profilu pro Smlouvu se zákazníkem Microsoftu roli vlastníka nebo přispěvatele.
1. Vyhledejte **Cost Management a fakturace**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/change-azure-account-profile/search-cmb.png)
1. Na levé straně vyberte **Fakturační profily**.
1. Vyberte fakturační profil pro aktualizaci fakturační adresy.  
    ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. Na levé straně vyberte **Vlastnosti**.
1. Vyberte **Aktualizovat adresu**.  
    ![Snímek obrazovky zachycující hledání položky Cost Management a fakturace na portálu](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. Zadejte novou adresu a potom vyberte **Uložit**.  
    ![Screenshot zobrazující aktualizaci adresy](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>Oznámení služeb a marketingové e-maily

Na webu [Azure Portal](https://portal.azure.com) se vám každých 90 dní zobrazí výzva k ověření nebo aktualizaci e-mailové adresy. Microsoft na tuto e-mailovou adresu odesílá následující informace týkající se účtu Azure:

- Oznámení služby
- Výstrahy zabezpečení
- Zprávy o fakturaci
- Podpora
- Marketingová sdělení
- Doporučené osvědčené postupy na základě vašeho používání Azure

Zadejte e-mailovou adresu, na kterou chcete dostávat zprávy o vašem účtu. Zadáním e-mailové adresy se přihlašujete k zasílání zpráv od Microsoftu.

![Příklad výzvy k aktualizaci kontaktních údajů](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>Změna kontaktní e-mailové adresy

Kontaktní e-mailovou adresu můžete změnit jednou z následujících metod. Aktualizace kontaktní e-mailové adresy neznamená aktualizaci e-mailové adresy, pomocí které se přihlašujete.

* Pokud jste správce účtu MOSP, postupujte podle pokynů v tématu [Aktualizace adresy fakturačního účtu MOSP](#update-an-mosp-billing-account-address) a v posledním kroku vyberte **Aktualizovat kontaktní údaje**. Potom zadejte novou e-mailovou adresu.

* Na webu Azure Portal přejděte do části [Kontaktní údaje](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) a zadejte novou e-mailovou adresu. 

* Na webu [Azure Portal](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) vyberte ikonu s vašimi iniciálami nebo obrázkem. Pak vyberte místní nabídku ( **…** ). Potom v nabídce vyberte **Moje kontaktní údaje** a zadejte novou e-mailovou adresu.

![Příklad aktualizace e-mailové adresy v Azure](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>Odhlášení od zasílání marketingových e-mailů

Pokud se chcete odhlásit od zasílání marketingových e-mailů, postupujte takto:

1. Přejděte na [formulář požadavku](https://account.microsoft.com/profile/permissions-link-request) a zadejte požadavek, ve kterém uvedete e-mailovou adresu ve svém profilu. Dostanete e-mail s odkazem pro aktualizaci předvoleb.
1. Výběrem tohoto odkazu otevřete stránku **Správa oprávnění ke komunikaci**. Tato stránka obsahuje typy marketingových zpráv, k jejichž zasílání je daná e-mailová adresa přihlášená. Zrušte zaškrtnutí všech možností, z jejichž zasílání se chcete odhlásit, a pak zvolte **Uložit**.  
    ![Příklad stránky pro správu oprávnění ke komunikaci](./media/change-azure-account-profile/manage-communication-permissions.png)

Po odhlášení ze zasílání marketingových zpráv budete dál dostávat oznámení služeb pro váš účet.

## <a name="update-the-email-address-that-you-sign-in-with"></a>Aktualizace e-mailové adresy, pomocí které se přihlašujete

E-mailovou adresu, kterou používáte pro přístup k vašemu účtu, nemůžete aktualizovat. Pokud ale máte fakturační účet pro MOSP, můžete si zaregistrovat jiný účet pomocí nové e-mailové adresy a potom převést vlastnictví vašich předplatných na tento nový účet. V případě fakturačního účtu MCA [můžete nové e-mailové adrese poskytnout oprávnění k vašemu účtu](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal).

## <a name="update-your-credit-card"></a>Aktualizace platební karty

Informace o aktualizaci platební karty najdete v tématu [Změna platební karty použité k úhradě předplatného Azure](change-credit-card.md).

## <a name="update-your-country-or-region"></a>Aktualizace země nebo oblasti

Změna země nebo oblasti pro už existující účet se nepodporuje. Můžete ale vytvořit nový účet v jiné zemi nebo oblasti a potom kontaktovat podporu Azure a přenést vaše předplatné do nového účtu.

## <a name="change-the-subscription-name"></a>Změna názvu předplatného

1. Přihlaste se na web [Azure Portal](https://portal.azure.com), v levém podokně vyberte **Předplatné** a pak zvolte předplatné, které chcete přejmenovat.
1. Vyberte **Přehled** a na panelu příkazů vyberte **Přejmenovat**.  
    ![Příklad přejmenování předplatného Azure](./media/change-azure-account-profile/rename-sub.png)
1. Po dokončení změny názvu vyberte **Uložit**.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás.

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Zobrazení fakturačních účtů](view-all-accounts.md)
