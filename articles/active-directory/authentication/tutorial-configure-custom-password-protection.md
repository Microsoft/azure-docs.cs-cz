---
title: Konfigurace vlastních seznamů ochrany heslem služby Azure Active Directory
description: V tomto kurzu se dozvíte, jak nakonfigurovat vlastní zakázané seznamy ochrany heslem pro Azure Active Directory omezit běžná slova ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4abb15462689470c87e9cf5ba8d5be8af2e45bfd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78252842"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Kurz: Konfigurace vlastních zakázaných hesel pro ochranu heslem služby Azure Active Directory

Uživatelé často vytvářejí hesla, která používají běžná místní slova, například školu, sportovní tým nebo slavnou osobu. Tato hesla jsou snadno uhodnout, a slabé proti slovníku-založené útoky. Chcete-li vynutit silná hesla ve vaší organizaci, Azure Active Directory (Azure AD) vlastní seznam zakázaných hesel umožňují přidat konkrétní řetězce k vyhodnocení a blokování. Požadavek na změnu hesla se nezdaří, pokud je shoda ve vlastním seznamu zakázaných hesel.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Povolení vlastních zakázaných hesel
> * Přidání položek do seznamu vlastních zakázaných hesel
> * Testování změn hesla pomocí zakázaného hesla

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
    * V případě potřeby [si jej vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Účet s oprávněními *globálního správce.*
* Uživatel bez oprávnění správce s heslem, které znáte, například *testuser*. V tomto kurzu otestujete událost změny hesla pomocí tohoto účtu.
    * Pokud potřebujete vytvořit uživatele, přečtěte [si úvodní příručku: Přidání nových uživatelů do služby Azure Active Directory](../add-users-azure-active-directory.md).
    * Chcete-li otestovat operaci změny hesla pomocí zakázaného hesla, musí být klient Azure AD [nakonfigurován pro samoobslužné resetování hesla](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Co jsou seznamy zakázaných hesel?

Azure AD obsahuje globální seznam zakázaných hesel. Obsah globálního seznamu zakázaných hesel není založen na žádném externím zdroji dat. Místo toho globální seznam zakázaných hesel je založen na průběžné výsledky telemetrie zabezpečení Azure AD a analýzy. Pokud se uživatel nebo správce pokusí změnit nebo obnovit svá pověření, požadované heslo se zkontroluje podle seznamu zakázaných hesel. Požadavek na změnu hesla se nezdaří, pokud je shoda v seznamu globálních zakázaných hesel.

Chcete-li získat flexibilitu v tom, jaká hesla jsou povolena, můžete také definovat vlastní seznam zakázaných hesel. Vlastní seznam zakázaných hesel funguje společně s globálním seznamem zakázaných hesel a vynucuje silná hesla ve vaší organizaci. Termíny specifické pro organizaci lze přidat do seznamu vlastních zakázaných hesel, například v následujících příkladech:

* Značky
* Názvy produktů
* Umístění, například sídlo společnosti
* Interní podmínky specifické pro společnost
* Zkratky, které mají specifický význam společnosti

Když se uživatel pokusí obnovit heslo na něco, co je na globálním nebo vlastním seznamu zakázaných hesel, zobrazí se mu jedna z následujících chybových zpráv:

* *Bohužel, vaše heslo obsahuje slovo, frázi nebo vzor, který umožňuje heslo snadno uhodnout. Zkuste to prosím znovu s jiným heslem.*
* *Toto heslo bohužel nelze použít, protože obsahuje slova nebo znaky, které správce zablokoval. Zkuste to prosím znovu s jiným heslem.*

Seznam vlastních zakázaných hesel je omezen na maximálně 1000 termínů. Není určen pro blokování velkých seznamů hesel. Chcete-li maximalizovat výhody vlastního seznamu zakázaných hesel, projděte [si vlastní koncepty seznamu zakázaných hesel](concept-password-ban-bad.md#custom-banned-password-list) a [přehled algoritmu hodnocení hesel](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Konfigurace vlastních zakázaných hesel

Povolte vlastní seznam zakázaných hesel a přidejte některé položky. Do seznamu vlastních zakázaných hesel můžete kdykoli přidat další položky.

Chcete-li povolit vlastní seznam zakázaných hesel a přidat do něj položky, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce.*
1. Vyhledejte a vyberte **službu Azure Active Directory**a v nabídce na levé straně zvolte **Zabezpečení.**
1. V záhlaví nabídky **Spravovat** vyberte **metody ověřování**a potom **ochranu heslem**.
1. Nastavte možnost **Vynutit vlastní seznam** na *Ano*.
1. Přidejte řetězce do **seznamu Vlastní zakázané heslo**, jeden řetězec na řádek. Následující aspekty a omezení platí pro vlastní seznam zakázaných hesel:

    * Vlastní seznam zakázaných hesel může obsahovat až 1000 termínů.
    * Vlastní seznam zakázaných hesel nerozlišuje malá a velká písmena.
    * Vlastní seznam zakázaných hesel bere v úvahu běžné nahrazení znaků, například "o" a "0", nebo "a" a "@".
    * Minimální délka řetězce je čtyři znaky a maximální počet je 16 znaků.

    Zadejte vlastní hesla k zákazu, jak je znázorněno v následujícím příkladu

    [![](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png "Modify the custom banned password list under Authentication Methods in the Azure portal")](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Ponechte možnost **Povolit ochranu heslem ve službě Windows Server Active Directory** na *ne*.
1. Chcete-li povolit vlastní zakázaná hesla a položky, vyberte **uložit**.

Může trvat několik hodin, než budou aktualizace seznamu vlastních zakázaných hesel použity.

Pro hybridní prostředí můžete také [nasadit ochranu heslem Azure AD do místního prostředí](howto-password-ban-bad-on-premises-deploy.md). Stejné globální a vlastní zakázané seznamy hesel se používají pro cloud i on-prem žádosti o změnu hesla.

## <a name="test-custom-banned-password-list"></a>Testování seznamu vlastních zakázaných hesel

Chcete-li zobrazit vlastní seznam zakázaných hesel v akci, zkuste změnit heslo na variantu, kterou jste přidali v předchozí části. Když Se pokusí Azure AD zpracovat změnu hesla, heslo je porovnán s položkou v seznamu vlastní zakázané heslo. Uživateli se pak zobrazí chyba.

> [!NOTE]
> Než bude uživatel moci resetovat své heslo na webovém portálu, musí být klient Azure AD [nakonfigurován pro samoobslužné resetování hesla](tutorial-enable-sspr.md).

1. Přejděte na stránku [https://myapps.microsoft.com](https://myapps.microsoft.com)Moje **aplikace** na adrese .
1. V pravém horním rohu vyberte své jméno a v rozevírací nabídce zvolte **Profil.**

    ![Výběr profilu](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. Na stránce **Profil** vyberte **Změnit heslo**.
1. Na stránce **Změnit heslo** zadejte existující (staré) heslo. Zadejte a potvrďte nové heslo, které je uvedeno v seznamu vlastních zakázaných hesel, který jste definovali v předchozí části, a pak vyberte **Odeslat**.
1. Je vrácena chybová zpráva, která informuje o tom, že heslo bylo zablokováno správcem, jak je znázorněno v následujícím příkladu:

    ![Při pokusu o použití hesla, které je součástí vlastního seznamu zakázaných hesel, se zobrazí chybová zpráva](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nechcete používat vlastní seznam zakázaných hesel, který jste nakonfigurovali v rámci tohoto kurzu, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyhledejte a vyberte **službu Azure Active Directory**a v nabídce na levé straně zvolte **Zabezpečení.**
1. V záhlaví nabídky **Spravovat** vyberte **metody ověřování**a potom **ochranu heslem**.
1. Nastavte možnost **Vynutit vlastní seznam** na *ne*.
1. Chcete-li aktualizovat vlastní konfiguraci zakázaného hesla, vyberte **uložit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili a nakonfigurovali vlastní seznamy ochrany heslem pro Azure AD. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Povolení vlastních zakázaných hesel
> * Přidání položek do seznamu vlastních zakázaných hesel
> * Testování změn hesla pomocí zakázaného hesla

> [!div class="nextstepaction"]
> [Zapnutí služby Azure Multi-Factor Authentication na základě rizikové události](tutorial-mfa-applications.md)
