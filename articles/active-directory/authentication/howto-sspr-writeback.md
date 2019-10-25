---
title: Jak nakonfigurovat zpětný zápis hesla pro Azure AD SSPR-Azure Active Directory
description: Použití Azure AD a Azure AD Connect ke zpětnému zápisu hesel do místního adresáře
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71a16ad3c571086a73a2aae192fb2d00bce4d5f9
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808152"
---
# <a name="how-to-configure-password-writeback"></a>Postupy: Konfigurace zpětného zápisu hesla

Následující postup předpokládá, že jste už ve svém prostředí nakonfigurovali Azure AD Connect pomocí [expresního](../hybrid/how-to-connect-install-express.md) nebo [vlastního](../hybrid/how-to-connect-install-custom.md) nastavení.

1. Pokud chcete zpětný zápis hesla nakonfigurovat a zapnout, přihlaste se ke svému serveru Azure AD Connect a spusťte průvodce konfigurací služby **Azure AD Connect**.
2. Na **úvodní** stránce vyberte **Konfigurovat**.
3. Na stránce **Další úlohy** vyberte **Přizpůsobit možnosti synchronizace** a potom vyberte **Další**.
4. Na stránce **Připojení k Azure AD** zadejte přihlašovací údaje globálního správce a pak vyberte **Další**.
5. Na stránkách filtrování **Připojení adresářů** a **Doména či organizační jednotka** vyberte **Další**.
6. Na stránce **Volitelné funkce** vyberte políčko vedle **Zpětný zápis hesla** a vyberte **Další**.
   ![Povolení zpětného zápisu hesla v Azure AD Connect][Writeback]
7. Na stránce **Připraveno ke konfiguraci** vyberte **Konfigurovat** a počkejte na dokončení procesu.
8. Až se konfigurace dokončí, vyberte **Ukončit**.

Běžné úlohy odstraňování potíží související se zpětným zápisem hesla najdete v části [řešení potíží se zpětným zápisem hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) v našem článku o řešení potíží.

> [!WARNING]
> Zpětný zápis hesla přestane fungovat pro zákazníky, kteří používají Azure AD Connect verze 1.0.8641.0 a starší, pokud [je služba Azure Access Control Service (ACS) vyřazena 7. listopadu 2018](../develop/active-directory-acs-migration.md). Azure AD Connect verze 1.0.8641.0 a starší již v tuto chvíli neumožňují zpětný zápis hesla, protože pro tuto funkci závisí na službě ACS.
>
> Chcete-li se vyhnout výpadkům služby, upgradujte z předchozí verze Azure AD Connect na novější verzi, přečtěte si článek [Azure AD Connect: upgrade z předchozí verze na nejnovější](../hybrid/how-to-upgrade-previous-version.md)
>

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky pro zpětný zápis hesla

**Samoobslužné resetování hesla/změna/odemknutí pomocí místního zpětného zápisu je funkce Premium služby Azure AD**. Další informace o licencování najdete na [webu Azure Active Directory Price](https://azure.microsoft.com/pricing/details/active-directory/).

Pokud chcete použít zpětný zápis hesla, musíte mít přiřazenou jednu z následujících licencí ve vašem tenantovi:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 nebo a3
* Enterprise Mobility + Security E5 nebo a5
* Microsoft 365 E3 nebo a3
* Microsoft 365 E5 nebo a5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Samostatné plány licencování Office 365 *nepodporují Samoobslužné resetování hesla, změny/odemknutí pomocí místního zpětného zápisu* a vyžadují, abyste měli k dispozici jeden z předchozích plánů, aby tato funkce fungovala.
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Oprávnění služby Active Directory a místní zásady složitosti hesla 

Účet zadaný v nástroji Azure AD Connect musí mít nastavené následující položky, pokud chcete být v oboru pro SSPR:

* **Resetování hesla** 
* **Změnit heslo** 
* **Oprávnění k zápisu** na `lockoutTime`
* **Oprávnění k zápisu** na `pwdLastSet`
* **Rozšířená práva** na jednom z těchto:
   * Kořenový objekt *každé domény* v této doménové struktuře
   * Organizační jednotky (OU) uživatele, které chcete mít v oboru pro SSPR

Pokud si nejste jistí, ke kterému účtu se odkazuje účet, otevřete uživatelské rozhraní Azure Active Directory Connect konfigurace a vyberte možnost **Zobrazit aktuální konfiguraci** . Účet, do kterého je nutné přidat oprávnění, je uveden v části **synchronizované adresáře**.

Pokud tato oprávnění nastavíte, účet služby MA pro každou doménovou strukturu může spravovat hesla jménem uživatelských účtů v rámci této doménové struktury. 

> [!IMPORTANT]
> Pokud tato oprávnění přiřadíte, a to i v případě, že se zpětný zápis zdá být správně nakonfigurovaný, uživatelé budou při pokusu o správu místních hesel z cloudu zacházet s chybami.
>

> [!NOTE]
> Může trvat až hodinu, než se tato oprávnění replikují do všech objektů ve vašem adresáři.
>

Chcete-li nastavit příslušná oprávnění ke zpětnému zápisu hesla, proveďte následující kroky:

1. Otevřete modul Uživatelé a počítače služby Active Directory pomocí účtu, který má příslušná oprávnění pro správu domény.
2. V nabídce **zobrazení** se ujistěte, že jsou zapnuté **Rozšířené funkce** .
3. Na levém panelu klikněte pravým tlačítkem myši na objekt, který představuje kořen domény, a vyberte možnost **vlastnosti** > **zabezpečení** > **Upřesnit**.
4. Na kartě **oprávnění** vyberte **Přidat**.
5. Vyberte účet, ke kterému se vztahují oprávnění (z instalace Azure AD Connect).
6. V rozevíracím seznamu **platí pro** vyberte **podřízené objekty uživatele**.
7. V části **oprávnění**zaškrtněte políčka pro následující možnosti:
    * **Změnit heslo**
    * **Resetování hesla**
8. V části **vlastnosti**zaškrtněte políčka pro následující možnosti:
    * **Zápis lockoutTime**
    * **Zápis pwdLastSet**
9. Výběrem **použít/ok** použijte změny a zavřete všechna otevřená dialogová okna.

Vzhledem k tomu, že zdroj autority je místní, zásady složitosti hesla se použijí ze stejného připojeného zdroje dat. Ujistěte se, že jste změnili existující zásady skupiny pro minimální délku hesla. Zásady skupiny by neměly být nastavené na hodnotu 1, což znamená, že heslo musí být alespoň jeden den starý předtím, než bude možné ho aktualizovat. Musíte se ujistit, že je nastavené na 0. Tato nastavení najdete v části `gpmc.msc` v části **Konfigurace počítače zásady > > nastavení systému Windows > nastavení zabezpečení > zásady účtů**. Spusťte `gpupdate /force`, abyste zajistili, že se změna projeví. 

## <a name="next-steps"></a>Další kroky

[Co je zpětný zápis hesla?](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Povolení zpětného zápisu hesla v Azure AD Connect"
