---
title: Konfigurace vlastních seznamů Azure Active Directory ochrany heslem
description: V tomto kurzu se dozvíte, jak nakonfigurovat vlastní seznamy zakázané ochrany hesel pro Azure Active Directory k omezení běžných slov ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741180"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Kurz: Konfigurace vlastního zakázaného hesla pro Azure Active Directory ochranu heslem

Uživatelé často vytvářejí hesla, která používají běžná místní slova, jako je škola, sportovní tým nebo slavných osoba. Tato hesla se dají snadno uhodnout a slabá na základě slovníkových útoků. K vynucení silných hesel ve vaší organizaci vám Azure Active Directory (Azure AD) vlastní seznam zakázaných hesel umožňuje přidat konkrétní řetězce pro vyhodnocení a blokování. Pokud se v seznamu vlastní zakázané heslo nachází shoda, žádost o změnu hesla se nezdařila.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolit vlastní zakázaná hesla
> * Přidat položky do seznamu vlastních zakázaných hesel
> * Testování změn hesel pomocí zakázaného hesla

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD s povolenou aspoň Azure AD Premium P1 nebo zkušební licencí.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce* .
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. Pomocí tohoto účtu v tomto kurzu otestujete událost změny hesla.
    * Pokud potřebujete vytvořit uživatele, přečtěte si téma [rychlý Start: přidání nových uživatelů do Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
    * K otestování operace změny hesla pomocí zakázaného hesla musí být tenant služby Azure AD [nakonfigurovaný pro Samoobslužné resetování hesla](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Co jsou seznamy zakázaných hesel?

Azure AD zahrnuje globální seznam zakázaných hesel. Obsah seznamu globálních zakázaných hesel není založený na žádném externím zdroji dat. Místo toho je seznam globálních zakázaných hesel založený na průběžných výsledcích telemetrie a analýzy zabezpečení Azure AD. Když se uživatel nebo správce pokusí změnit nebo resetovat své přihlašovací údaje, bude požadované heslo zkontrolováno na seznam zakázaných hesel. V případě, že je v seznamu globální seznam zakázaných hesel shoda, žádost o změnu hesla se nezdařila. Nemůžete upravit výchozí seznam zakázaných hesel.

Pokud chcete mít flexibilitu v tom, jaká hesla jsou povolená, můžete také definovat vlastní seznam zakázaných hesel. Vlastní seznam zakázaných hesel funguje společně se seznamem globálních zakázaných hesel pro vynucení silných hesel ve vaší organizaci. Do vlastního seznamu zakázaných hesel můžete přidat výrazy specifické pro danou organizaci, například následující příklady:

* Názvy značek
* Názvy produktů
* Umístění, jako je například ústředí společnosti
* Interní výrazy specifické pro společnost
* Zkratky, které mají konkrétní význam společnosti

Když se uživatel pokusí resetovat heslo na něco, co je v seznamu globální nebo vlastní zakázané heslo, zobrazí se některá z následujících chybových zpráv:

* *Vaše heslo bohužel obsahuje slovo, frázi nebo vzor, který umožňuje snadnou možnost uhodnout heslo. Zkuste to prosím znovu s jiným heslem.*
* *Toto heslo bohužel nemůžete použít, protože obsahuje slova nebo znaky blokované vaším správcem. Zkuste to prosím znovu s jiným heslem.*

Vlastní seznam zakázaných hesel je omezený na maximum 1000 podmínek. Není navržený pro blokování rozsáhlých seznamů hesel. Pokud chcete maximalizovat výhody vlastního seznamu zakázaných hesel, přečtěte si téma vlastní informace o [principech seznamu zakázaných](concept-password-ban-bad.md#custom-banned-password-list) hesel a [hodnocení algoritmu pro vyhodnocování hesel](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Konfigurace vlastních zakázaných hesel

Pojďme povolit vlastní seznam zakázaných hesel a přidat nějaké položky. Můžete kdykoli přidat další položky do seznamu vlastních zakázaných hesel.

Pokud chcete povolit seznam vlastních zakázaných hesel a přidávat do něj položky, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce* .
1. Vyhledejte a vyberte **Azure Active Directory** a pak v nabídce na levé straně zvolte **zabezpečení** .
1. V záhlaví nabídky **Spravovat** vyberte **metody ověřování** a pak **ochrana heslem**.
1. Nastavte možnost pro **vysazení vlastního seznamu** na *Ano*.
1. Přidejte řetězce do **seznamu vlastních zakázaných hesel**, jeden řetězec na řádek. Následující hlediska a omezení se vztahují na vlastní seznam zakázaných hesel:

    * Vlastní seznam zakázaných hesel může obsahovat až 1000 podmínek.
    * Vlastní seznam zakázaných hesel rozlišuje velká a malá písmena.
    * Vlastní seznam zakázaných hesel je považován za běžné nahrazení znaků, například "o" a "0" nebo "a" \@ .
    * Minimální délka řetězce je 4 znaky a maximální délka je 16 znaků.

    Určete vlastní hesla, která chcete zakázat, jak je znázorněno v následujícím příkladu.

    [![V Azure Portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png) upravte vlastní seznam zakázaných hesel v části metody ověřování.](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Ponechte možnost **Povolit ochranu heslem ve službě Windows Server Active Directory** na *ne*.
1. Pokud chcete povolit vlastní zakázaná hesla a vaše položky, vyberte **Uložit**.

Použití aktualizací vlastního seznamu zakázaných hesel může trvat několik hodin.

V hybridním prostředí můžete také [nasadit ochranu heslem Azure AD do místního prostředí](howto-password-ban-bad-on-premises-deploy.md). Stejné globální a vlastní seznamy zakázaných hesel se používají pro žádosti o změnu hesla cloudu i na Prem.

## <a name="test-custom-banned-password-list"></a>Test vlastního seznamu zakázaných hesel

Chcete-li zobrazit vlastní seznam zakázaných hesel v akci, zkuste změnit heslo na variantu, kterou jste přidali v předchozí části. Když se služba Azure AD pokusí zpracovat změnu hesla, heslo se bude shodovat s položkou v seznamu vlastní zakázané heslo. Uživateli se pak zobrazí chyba.

> [!NOTE]
> Než může uživatel resetovat heslo ve webovém portálu, musí být tenant služby Azure AD [nakonfigurovaný pro Samoobslužné resetování hesla](tutorial-enable-sspr.md). V případě potřeby si uživatel může [zaregistrovat pro SSPR na adrese https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup).

1. Přejít na stránku **Moje aplikace** na adrese [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. V pravém horním rohu vyberte své jméno a pak v rozevírací nabídce vyberte možnost **profil** .

    ![Výběr profilu](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na stránce **profil** vyberte **změnit heslo**.
1. Na stránce **změnit heslo** zadejte stávající (staré) heslo. Zadejte a potvrďte nové heslo, které se nachází na seznamu vlastního zakázaného hesla, které jste definovali v předchozí části, a pak vyberte **Odeslat**.
1. Vrátí se chybová zpráva oznamující, že Správce zablokoval heslo, jak je znázorněno v následujícím příkladu:

    ![Chybová zpráva, která se zobrazí při pokusu o použití hesla, které je součástí vlastního seznamu zakázaných hesel](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nechcete používat vlastní seznam zakázaných hesel, který jste nakonfigurovali v rámci tohoto kurzu, proveďte následující kroky:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte **Azure Active Directory** a pak v nabídce na levé straně zvolte **zabezpečení** .
1. V záhlaví nabídky **Spravovat** vyberte **metody ověřování** a pak **ochrana heslem**.
1. Nastavte možnost **vymáhat vlastní seznam** na *ne*.
1. Pokud chcete aktualizovat vlastní konfiguraci zakázaného hesla, vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili a nakonfigurovali vlastní seznamy ochrany heslem pro Azure AD. Naučili jste se:

> [!div class="checklist"]
> * Povolit vlastní zakázaná hesla
> * Přidat položky do seznamu vlastních zakázaných hesel
> * Testování změn hesel pomocí zakázaného hesla

> [!div class="nextstepaction"]
> [Zapnutí vícefaktorového ověřování Azure AD na základě rizikové události](./tutorial-enable-azure-mfa.md)