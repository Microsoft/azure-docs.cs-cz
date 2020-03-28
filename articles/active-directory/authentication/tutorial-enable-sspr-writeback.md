---
title: Povolení zpětného zápisu hesla služby Azure Active Directory
description: V tomto kurzu se dozvíte, jak povolit samoobslužné obnovení hesla Azure AD zpětný zápis pomocí Azure AD Connect pro synchronizaci změn zpět do místního prostředí služby Active Directory Domain Services.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3578cb1326ebd701c3f00618c19a501a1476372
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80332125"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Kurz: Povolení samoobslužného hesla Azure Active Directory resetovat zpětný zápis do místního prostředí

Pomocí samoobslužného resetování hesla azure active directory (Azure AD) mohou uživatelé aktualizovat své heslo nebo odemknout svůj účet pomocí webového prohlížeče. V hybridním prostředí, kde je Azure AD připojenkmístnímu prostředí služby Active Directory Domain Services (AD DS), může tento scénář způsobit, že se hesla mezi dvěma adresáři liší.

Zpětný zápis hesla lze použít k synchronizaci změn hesla ve službě Azure AD zpět do místního prostředí služby AD DS. Azure AD Connect poskytuje zabezpečený mechanismus pro odesílání těchto změn hesla zpět do existujícího místního adresáře z Azure AD.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace požadovaných oprávnění pro zpětný zápis hesla
> * Povolení možnosti zpětného zápisu hesla ve službě Azure AD Connect
> * Povolení zpětného zápisu hesla v sspr služby Azure AD

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující prostředky a oprávnění:

* Funkční tenant Azure AD, který má přiřazenou alespoň zkušební licenci.
    * V případě potřeby [si jej vytvořte zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * Další informace najdete [v tématu Licenční požadavky na azure ad sspr](concept-sspr-licensing.md).
* Účet s oprávněními *globálního správce.*
* Azure AD nakonfigurované pro samoobslužné resetování hesla.
    * V případě potřeby [dokončete předchozí kurz a povolte sspr služby Azure AD](tutorial-enable-sspr.md).
* Existující místní prostředí služby AD DS nakonfigurované s aktuální verzí služby Azure AD Connect.
    * V případě potřeby nakonfigurujte Azure AD Connect pomocí [nastavení Express](../hybrid/how-to-connect-install-express.md) nebo [Custom.](../hybrid/how-to-connect-install-custom.md)
    * Chcete-li použít zpětný zápis hesla, musí být řadiče domény Windows Server 2008 R2 nebo novější.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Konfigurace oprávnění účtu pro Azure AD Connect

Azure AD Connect umožňuje synchronizovat uživatele, skupiny a přihlašovací údaje mezi místním prostředím Služby AD DS a službou Azure AD. Azure AD Connect se obvykle instaluje do počítače se systémem Windows Server 2012 nebo novějším, který je spojený s místní doménou služby AD DS.

Chcete-li správně pracovat s zpětným zápisem programu SSPR, musí mít účet zadaný ve službě Azure AD Connect příslušná oprávnění a možnosti. Pokud si nejste jistí, který účet se aktuálně používá, otevřete Azure AD Connect a vyberte možnost **Zobrazit aktuální konfiguraci.** Účet, ke kterému je třeba přidat oprávnění, je uveden v části **Synchronizované adresáře**. V účtu musí být nastavena následující oprávnění a možnosti:

* **Resetování hesla**
* **Napište oprávnění**`lockoutTime`
* **Napište oprávnění**`pwdLastSet`
* **Rozšířená práva** pro "Unexpire Password" na obou:
   * Kořenový objekt *každé domény* v této doménové struktuře
   * Organizační jednotky uživatele, které chcete mít v oboru pro oblast sspr

Pokud tato oprávnění nepřiřadíte, zdá se, že zpětný zápis je nakonfigurován správně, ale uživatelé se při správě místních hesel z cloudu setkávají s chybami.

Chcete-li nastavit příslušná oprávnění pro zpětné zápis hesel, proveďte následující kroky:

1. V místním prostředí služby AD DS otevřete **službu Uživatelé a počítače služby Active Directory** s účtem, který má příslušná oprávnění *správce domény.*
1. V nabídce **Zobrazení** zkontrolujte, zda jsou **zapnuté rozšířené funkce.**
1. V levém panelu vyberte objekt, který představuje kořen domény, a vyberte **vlastnosti** > **zabezpečení** > **upřesnit**.
1. Na kartě **Oprávnění** vyberte **Přidat**.
1. Pro **hlavní**položky vyberte účet, na který by měla být použita oprávnění (účet používaný službou Azure AD Connect).
1. V rozevíracím seznamu **Platí pro** vyberte **objekty potomka uživatele**.
1. V části *Oprávnění*zaškrtněte políčka pro následující možnosti:
    * **Resetování hesla**
1. V části *Vlastnosti*vyberte pole pro následující možnosti. Chcete-li najít tyto možnosti, které již mohou být nastaveny ve výchozím nastavení, je třeba procházet seznamem:
    * **Zápis lockoutTime**
    * **Zapsat pwdLastSet**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Až budete připraveni, vyberte **Použít / OK,** chcete-li použít změny a ukončit všechna otevřená dialogová okna.

Při aktualizaci oprávnění může trvat až hodinu nebo více, než se tato oprávnění replikují na všechny objekty v adresáři.

Zásady hesel v místním prostředí služby AD DS mohou bránit správnému zpracování resetování hesla. Aby zpětný zápis hesla fungoval co nejefektivněji, musí být zásady skupiny pro *minimální stáří hesla* nastaveny na hodnotu 0. Toto nastavení naleznete v části **Zásady konfigurace počítače > > Nastavení systému Windows > Nastavení zabezpečení > Zásady účtu** v aplikaci `gpedit.msc`. 

Pokud aktualizujete zásady skupiny, počkejte na replikaci aktualizované zásady nebo použijte `gpupdate /force` příkaz.

> [!Note]
> Aby byla hesla okamžitě změněna, musí být zpětný zápis hesla nastaven na 0. Pokud však uživatelé dodržují místní zásady a *minimální stáří hesla* je nastaveno na hodnotu větší než nula, bude zpětný zápis hesla fungovat i po vyhodnocení místních zásad. 

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Povolení zpětného zápisu hesla ve službě Azure AD Connect

Jednou z možností konfigurace ve službě Azure AD Connect je pro zpětný zápis hesla. Pokud je tato možnost povolena, události změny hesla způsobí, že Azure AD Connect synchronizuje aktualizované přihlašovací údaje zpět do místního prostředí služby AD DS.

Chcete-li povolit samoobslužné obnovení hesla zpětný zápis, nejprve povolte možnost zpětného zápisu v Azure AD Connect. Na serveru Azure AD Connect proveďte následující kroky:

1. Přihlaste se k serveru Azure AD Connect a spusťte Průvodce konfigurací **Azure AD Connect.**
1. Na **úvodní** stránce vyberte **Konfigurovat**.
1. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce pro svého klienta Azure a pak vyberte **Další**.
1. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
1. Na stránce **Volitelné funkce** vyberte políčko vedle **Zpětný zápis hesla** a vyberte **Další**.

    ![Konfigurace služby Azure AD Connect pro zpětný zápis hesla](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
1. Až se konfigurace dokončí, vyberte **Ukončit**.

## <a name="enable-password-writeback-for-sspr"></a>Povolení zpětného zápisu hesla pro sspr

Když je ve službě Azure AD Connect povoleno zpětné zápisování hesel, teď nakonfiguruj azure ad sspr pro zpětný zápis. Pokud povolíte funkci Samoobslužné resetování hesla k použití zpětného zápisu hesla, uživatelé, kteří změní nebo resetují své heslo, mají toto aktualizované heslo také synchronizované zpět do místního prostředí služby AD DS.

Chcete-li povolit zpětný zápis hesla v sspr, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu globálního správce.
1. Vyhledejte a vyberte **Službu Azure Active Directory**, vyberte **Resetování hesla**a pak zvolte Místní **integrace**.
1. Nastavte možnost **Zápis hesel do místního adresáře?** *Yes*
1. Nastavit možnost **Povolit uživatelům odemknutí účtů bez resetování hesla?** *Yes*

    ![Povolení samoobslužného resetování hesla služby Azure AD pro zpětný zápis hesla](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Až budete připraveni, vyberte **Uložit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již nechcete používat funkci zpětného zápisu programu SSPR, kterou jste nakonfigurovali v rámci tohoto kurzu, proveďte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyhledejte a vyberte **Službu Azure Active Directory**, vyberte **Resetování hesla**a pak zvolte Místní **integrace**.
1. Nastavte možnost **Zápis hesel do místního adresáře?** *No*
1. Nastavit možnost **Povolit uživatelům odemknout účty bez resetování hesla?** *No*

Pokud už nechcete používat žádné funkce hesla, proveďte následující kroky ze serveru Azure AD Connect:

1. Přihlaste se k serveru Azure AD Connect a spusťte Průvodce konfigurací **Azure AD Connect.**
1. Na **úvodní** stránce vyberte **Konfigurovat**.
1. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
1. Na stránce **Připojit k Azure AD** zadejte přihlašovací údaje globálního správce pro svého klienta Azure a pak vyberte **Další**.
1. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
1. Na stránce **Volitelné funkce** odznačte políčko vedle **položky Zpětný zápis hesla** a vyberte **další**.
1. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
1. Až se konfigurace dokončí, vyberte **Ukončit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili zpětný zápis programu Azure AD SSPR do místního prostředí služby AD DS. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Konfigurace požadovaných oprávnění pro zpětný zápis hesla
> * Povolení možnosti zpětného zápisu hesla ve službě Azure AD Connect
> * Povolení zpětného zápisu hesla v sspr služby Azure AD

> [!div class="nextstepaction"]
> [Hodnocení rizika při přihlášení](tutorial-risk-based-sspr-mfa.md)
