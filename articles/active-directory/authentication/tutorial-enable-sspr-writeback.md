---
title: Povolit Azure Active Directory zpětný zápis hesla
description: V tomto kurzu se dozvíte, jak povolit zpětný zápis pomocí samoobslužného resetování hesla služby Azure AD pomocí Azure AD Connect k synchronizaci změn zpátky do místního prostředí Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccc64fb8dd8bd8abc198d9bfc9d643ef618188ea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967781"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Kurz: povolení zpětného zápisu pro Samoobslužné resetování hesla Azure Active Directory do místního prostředí

Pomocí služby Azure Active Directory (Azure AD) samoobslužného resetování hesla (SSPR) mohou uživatelé aktualizovat heslo nebo odemknout účet pomocí webového prohlížeče. V hybridním prostředí, kde je služba Azure AD připojená k místnímu prostředí Active Directory Domain Services (služba AD DS), může tento scénář způsobit, že se hesla mezi těmito dvěma adresáři liší.

Zpětný zápis hesla se dá použít k synchronizaci změn hesel v Azure AD zpátky do místního prostředí služba AD DS. Azure AD Connect poskytuje zabezpečený mechanismus, pomocí kterého se tyto změny hesla odesílají zpátky do stávajícího místního adresáře ze služby Azure AD.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurovat požadovaná oprávnění pro zpětný zápis hesla
> * Povolit možnost zpětného zápisu hesla v Azure AD Connect
> * Povolení zpětného zápisu hesla ve službě Azure AD SSPR

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
    * V případě potřeby [ho vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Další informace najdete v tématu [licenční požadavky pro Azure AD SSPR](concept-sspr-licensing.md).
* Účet s oprávněními *globálního správce* .
* Služba Azure AD konfigurovaná pro Samoobslužné resetování hesla.
    * V případě potřeby [pro povolení služby Azure AD SSPR dokončete předchozí kurz](tutorial-enable-sspr.md).
* Existující místní služba AD DS prostředí nakonfigurované s aktuální verzí Azure AD Connect.
    * V případě potřeby nakonfigurujte Azure AD Connect pomocí [expresního](../hybrid/how-to-connect-install-express.md) nebo [vlastního](../hybrid/how-to-connect-install-custom.md) nastavení.
    * Pokud chcete použít zpětný zápis hesla, musí být řadiče domény Windows Server 2008 R2 nebo novější.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurace oprávnění účtu pro Azure AD Connect

Azure AD Connect umožňuje synchronizaci uživatelů, skupin a přihlašovacích údajů mezi místním prostředím služba AD DS a službou Azure AD. Obvykle nainstalujete Azure AD Connect na počítač s Windows Serverem 2012 nebo novějším, který je připojený k místní doméně služba AD DS.

Aby bylo možné správně pracovat se zpětným zápisem SSPR, musí mít účet zadaný v Azure AD Connect příslušná oprávnění a nastavené možnosti. Pokud si nejste jistí, který účet se aktuálně používá, otevřete Azure AD Connect a vyberte možnost **Zobrazit aktuální konfiguraci** . Účet, do kterého potřebujete přidat oprávnění, je uveden v části **synchronizované adresáře**. Na účtu musí být nastavená následující oprávnění a možnosti:

* **Resetování hesla**
* **Změnit heslo**
* **Oprávnění k zápisu** na `lockoutTime`
* **Oprávnění k zápisu** na `pwdLastSet`
* **Rozšířená práva** na jednom z těchto:
   * Kořenový objekt *každé domény* v této doménové struktuře
   * Organizační jednotky (OU) uživatele, které chcete mít v oboru pro SSPR

Pokud tato oprávnění nepřiřadíte, zdá se, že se zpětný zápis správně nakonfiguroval, ale uživatelé narazí na chyby při správě místních hesel z cloudu.

Chcete-li nastavit příslušná oprávnění ke zpětnému zápisu hesla, proveďte následující kroky:

1. V místním prostředí služba AD DS otevřete modul **Uživatelé a počítače služby Active Directory** pomocí účtu, který má příslušná oprávnění *správce domény* .
1. V nabídce **zobrazení** se ujistěte, že jsou zapnuté **Rozšířené funkce** .
1. Na levém panelu klikněte pravým tlačítkem myši na objekt, který představuje kořen domény, a vyberte možnost **vlastnosti** > **zabezpečení** > **Upřesnit**.
1. Na kartě **oprávnění** vyberte **Přidat**.
1. V části **objekt zabezpečení**vyberte účet, na který se mají oprávnění použít (účet používaný Azure AD Connect).
1. V rozevíracím seznamu **platí pro** vyberte **podřízené objekty uživatele**.
1. V části *oprávnění*zaškrtněte políčka pro následující možnosti:
    * **Změnit heslo**
    * **Resetování hesla**
1. V části *vlastnosti*zaškrtněte políčka pro následující možnosti. Chcete-li najít tyto možnosti, které mohou být ve výchozím nastavení již nastaveny, procházejte seznamem:
    * **Zápis lockoutTime**
    * **Zápis pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Až budete připraveni, použijte možnost **použít/ok** , aby se změny projevily, a zavřete všechna otevřená dialogová okna.

Když aktualizujete oprávnění, může trvat až hodinu, než se tato oprávnění replikují do všech objektů ve vašem adresáři.

Zásady hesel v místním prostředí služba AD DS můžou zabránit správnému zpracování resetování hesla. Aby zpětný zápis hesla fungoval správně, musí být zásady skupiny pro *Minimální stáří hesla* nastavené na 0. Toto nastavení najdete v části **zásady > konfigurace počítače > nastavení systému Windows > nastavení zabezpečení > zásadách účtů** v `gpedit.msc`.

Pokud aktualizujete zásady skupiny, počkejte na replikaci aktualizované zásady nebo použijte příkaz `gpupdate /force`.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Povolení zpětného zápisu hesla v Azure AD Connect

Jednou z možností konfigurace v Azure AD Connect je pro zpětný zápis hesla. Když je tato možnost povolená, události změny hesla způsobí, že Azure AD Connect synchronizovat aktualizované přihlašovací údaje zpátky do místního prostředí služba AD DS.

Pokud chcete povolit zpětný zápis resetování hesla, nejdřív v Azure AD Connect povolte možnost zpětného zápisu. Z Azure AD Connect serveru proveďte následující kroky:

1. Přihlaste se k serveru Azure AD Connect a spusťte Průvodce konfigurací **Azure AD Connect** .
1. Na **úvodní** stránce vyberte **Konfigurovat**.
1. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
1. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce pro vašeho tenanta Azure a pak vyberte **Další**.
1. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
1. Na stránce **Volitelné funkce** vyberte políčko vedle **Zpětný zápis hesla** a vyberte **Další**.

    ![Konfigurace Azure AD Connect pro zpětný zápis hesla](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
1. Až se konfigurace dokončí, vyberte **Ukončit**.

## <a name="enable-password-writeback-for-sspr"></a>Povolení zpětného zápisu hesla pro SSPR

Se zapnutým zpětným zápisem hesla v Azure AD Connect teď nakonfigurujte Azure AD SSPR pro zpětný zápis. Když povolíte, aby SSPR používal zpětný zápis hesla, uživatelé, kteří změnili nebo resetují heslo, mají taky aktualizované heslo synchronizované zpátky do místního služba AD DS prostředí.

Pokud chcete povolit zpětný zápis hesla v SSPR, proveďte následující kroky:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce.
1. Vyhledejte a vyberte **Azure Active Directory**, vyberte **resetování hesla**a pak zvolte místní **integrace**.
1. Nastavte možnost pro **zápis hesel zpátky do místního adresáře?** na *Ano*.
1. Nastavte možnost, aby **Uživatelé odemkli účty bez resetování hesla?** na *Ano*.

    ![Povolení samoobslužného resetování hesla služby Azure AD pro zpětný zápis hesla](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Až budete připraveni, vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nechcete používat funkci zpětného zápisu SSPR, kterou jste nakonfigurovali v rámci tohoto kurzu, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyhledejte a vyberte **Azure Active Directory**, vyberte **resetování hesla**a pak zvolte místní **integrace**.
1. Nastavte možnost pro **zápis hesel zpátky do místního adresáře?** na *ne*.
1. Nastavte možnost *,* aby **Uživatelé odemkli účty bez resetování hesla?**

Pokud už nechcete používat žádné funkce hesla, proveďte následující kroky z Azure AD Connect serveru:

1. Přihlaste se k serveru Azure AD Connect a spusťte Průvodce konfigurací **Azure AD Connect** .
1. Na **úvodní** stránce vyberte **Konfigurovat**.
1. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
1. Na stránce **připojit ke službě Azure AD** zadejte přihlašovací údaje globálního správce pro vašeho tenanta Azure a pak vyberte **Další**.
1. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
1. Na stránce **volitelné funkce** zrušte zaškrtnutí políčka vedle **zpětného zápisu hesla** a vyberte **Další**.
1. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
1. Až se konfigurace dokončí, vyberte **Ukončit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili zpětný zápis ve službě Azure AD SSPR do místního prostředí služba AD DS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Nakonfigurovat požadovaná oprávnění pro zpětný zápis hesla
> * Povolit možnost zpětného zápisu hesla v Azure AD Connect
> * Povolení zpětného zápisu hesla ve službě Azure AD SSPR

> [!div class="nextstepaction"]
> [Hodnocení rizika při přihlášení](tutorial-risk-based-sspr-mfa.md)
