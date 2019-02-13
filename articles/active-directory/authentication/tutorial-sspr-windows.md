---
title: Samoobslužné resetování hesla Azure AD z přihlašovací obrazovky Windows 10
description: V tomto kurzu povolíte resetování hesla na přihlašovací obrazovce Windows 10, aby se snížil počet telefonátů na helpdesk.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0463a2ad3fa74f33a52e15a246dfd4ffd63107a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200866"
---
# <a name="tutorial-azure-ad-password-reset-from-the-login-screen"></a>Kurz: Resetování hesla Azure AD z přihlašovací obrazovky

V tomto kurzu povolíte uživatelům resetovat svá hesla z přihlašovací obrazovky Windows 10. V nové aktualizaci Windows 10 z dubna 2018 se uživatelům se zařízeními **připojenými k Azure AD** nebo **k hybridní službě Azure AD** zobrazí na přihlašovací obrazovce odkaz pro resetování hesla. Když uživatelé na tento odkaz kliknou, přesměruje je to do stejného prostředí pro samoobslužné resetování hesla, které už znají.

> [!div class="checklist"]
> * Konfigurace odkazu na resetování hesla pomocí Intune
> * Volitelná konfigurace pomocí registru Windows
> * Pochopení toho, co uvidí vaši uživatelé

## <a name="prerequisites"></a>Požadavky

* Musíte spuštěné aspoň Windows 10. dubna 2018 verze aktualizací a zařízení musí být buď:
   * [Azure AD připojený](../device-management-azure-portal.md) nebo
   * [Hybridní Azure připojené k AD](../device-management-hybrid-azuread-joined-devices-setup.md), s připojením k řadiči domény.
* Je nutné povolit Azure AD samoobslužné resetování hesla.
* Pokud vaše zařízení s Windows 10 jsou za proxy server nebo brána firewall, je nutné přidat adresy URL, `passwordreset.microsoftonline.com` a `ajax.aspnetcdn.com` do seznamu povolené adresy URL HTTPS provoz (port 443).
* Kontrola omezení pod před pokusem o tuto funkci ve vašem prostředí.

## <a name="configure-reset-password-link-using-intune"></a>Konfigurace odkazu na resetování hesla pomocí Intune

Nasazení změny konfigurace, která umožní resetování hesla z přihlašovací obrazovky, pomocí Intune je nejflexibilnější metodou. Intune vám umožňuje nasadit změnu konfigurace pro vámi zvolenou skupinu počítačů. Tato metoda vyžaduje registraci zařízení v Intune.

### <a name="create-a-device-configuration-policy-in-intune"></a>Vytvoření zásad konfigurace zařízení v Intune

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Intune**.
2. Vytvořte nový profil konfigurace zařízení tak, že přejdete do **Konfigurace zařízení** > **Profily** > **Vytvořit profil**.
   * Zadejte výstižný název profilu.
   * Volitelně zadejte výstižný popis profilu.
   * Jako platformu vyberte **Windows 10 a novější**.
   * Jako typ profilu vyberte **Vlastní**.

3. Nakonfigurujte **Nastavení**.
   * **Přidejte** následující Nastavení identifikátoru OMA-URI, které povolí odkaz na resetování hesla.
      * Zadejte výstižný název vysvětlující, co toto nastavení dělá.
      * Volitelně zadejte výstižný popis nastavení.
      * Identifikátor **OMA-URI** nastavte na `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`.
      * **Datový typ** nastavte na **Integer**.
      * Jako **Hodnota** nastavte **1**.
      * Klikněte na tlačítko **OK**.
   * Klikněte na tlačítko **OK**.
4. Klikněte na **Vytvořit**

### <a name="assign-a-device-configuration-policy-in-intune"></a>Přiřazení zásad konfigurace zařízení v Intune

#### <a name="create-a-group-to-apply-device-configuration-policy-to"></a>Vytvoření skupiny, na kterou se použijí zásady konfigurace zařízení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Azure Active Directory**.
2. Přejděte do **Uživatelé a skupiny** > **Všechny skupiny** > **Nová skupina**.
3. Zadejte název skupiny a v části **Typ členství** zvolte **Přiřazený**.
   * V části **Členové** zvolte zařízení s Windows 10 připojená k Azure AD, pro která chcete zásady použít.
   * Klikněte na **Vybrat**.
4. Klikněte na **Vytvořit**

Další informace o vytváření skupin najdete v článku [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md).

#### <a name="assign-device-configuration-policy-to-device-group"></a>Přiřazení zásad konfigurace zařízení ke skupině zařízení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) a klikněte na **Intune**.
2. Přejděte do **Konfigurace zařízení** > **Profily**, vyhledejte dříve vytvořený profil konfigurace zařízení a klikněte na něj.
3. Přiřazení profilu ke skupině zařízení 
   * V části **Zahrnout** > **Vybrat skupiny, které se zahrnou** klikněte na **Přiřazení**.
   * Vyberte dříve vytvořenou skupinu a klikněte na **Vybrat**.
   * Klikněte na **Uložit**.

   ![Přiřazení][Assignment]

Právě jste vytvořili a přiřadili zásady konfigurace zařízení a povolili jste odkaz na resetování hesla na přihlašovací obrazovce pomocí Intune.

## <a name="configure-reset-password-link-using-the-registry"></a>Konfigurace odkazu na resetování hesla pomocí registru

1. Přihlaste se k počítači Windows pomocí přihlašovacích údajů pro správu
2. Spusťte program **regedit** jako správce.
3. Nastavte následující klíč registru:
   * `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`
      * `"AllowPasswordReset"=dword:00000001`

## <a name="what-do-users-see"></a>Co vidí uživatelé

Co se po konfiguraci a přiřazení zásad změnilo pro uživatele? Jak se dozví, že své heslo můžou resetovat na přihlašovací obrazovce?

![Přihlašovací obrazovka][LoginScreen]

Když uživatel se pokusí přihlásit, uvidí teď resetování prostředí na přihlašovací obrazovce odkaz resetovat heslo, které se otevře hesla pomocí samoobslužné služby. Tato funkce umožňuje uživatelům resetovat své heslo, aniž by museli použít jiné zařízení pro přístup k webovému prohlížeči.

Vaši uživatelé najdou pokyny k použití této funkce v tématu popisujícím [resetování hesla k pracovnímu nebo školnímu účtu](../user-help/active-directory-passwords-update-your-own-password.md#reset-password-at-sign-in).

Protokol auditu služby Azure AD bude obsahovat informace o IP adrese a typu klienta, kde došlo k resetování hesla.

![Příklad resetování hesla na přihlašovací obrazovce v protokolu auditu služby Azure AD](media/tutorial-sspr-windows/windows-sspr-azure-ad-audit-log.png)

Když uživatelům resetovat své heslo z přihlašovací obrazovky zařízení s Windows 10, se vytvoří nízkou úrovní oprávnění dočasné účet s názvem "defaultuser1". Tento účet slouží k zabezpečení procesu resetování hesla. Účet samotný má náhodně generované heslo, nezobrazí se pro přihlášení k zařízení a bude automaticky odstraněna po uživatel může resetovat své heslo. Může existovat více profilů "defaultuser", ale můžete bezpečně ignorovat.

## <a name="limitations"></a>Omezení

Při testování této funkce s použitím Hyper-V se odkaz Resetovat heslo nezobrazí.

* Přejděte do virtuálního počítače, který používáte k testování, klikněte na **Zobrazit** a pak zrušte zaškrtnutí políčka **Rozšířená relace**.

Při testování této funkce s použitím vzdálené plochy nebo relaci rozšířeného virtuálního počítače, "Resetovat heslo" odkaz se nezobrazí.

* Resetování hesla u Vzdálené plochy se v současné době nepodporuje.

Pokud před 1809, vyžaduje zásady v systému Windows 10 ve verzi Ctrl + Alt + Del **resetovat heslo** nebude fungovat.

Pokud jsou oznámení pro uzamčenou obrazovku vypne, **resetovat heslo** nebude fungovat.

Je známo, že následující nastavení zásad v konfliktu s možností k resetování hesel

   * HideFastUserSwitching je nastaveno na povoleno nebo 1
   * DontDisplayLastUserName je nastaveno na povoleno nebo 1
   * NoLockScreen je nastaveno na povoleno nebo 1
   * EnableLostMode nastavená na zařízení
   * Explorer.exe nahradí za vlastní prostředí

Tato funkce nefunguje pro sítí pomocí protokolu 802. 1 x ověřování sítě nasazené a možnost "Provádět bezprostředně před přihlášením uživatele". Sítí pomocí protokolu 802. 1 x ověřování sítě nasadit se doporučuje použití ověřování počítače pro tuto funkci povolil.

U scénářů s hybridní k doméně se úspěšně dokončí pracovního postupu samoobslužné resetování HESLA bez nutnosti řadič domény služby Active Directory. Pokud uživatel dokončí proces resetování hesla při komunikaci s řadičem domény služby Active Directory není k dispozici, stejně jako při práci vzdáleně, uživatel nebude moct přihlásit k zařízení, dokud se zařízení může komunikovat s řadičem domény a aktualizace mezipaměti přihlašovacích údajů. **Připojení k řadiči domény je potřeba pomocí nového hesla poprvé**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se rozhodnete, že už funkci nakonfigurovanou jako součást tohoto kurzu používat nechcete, odstraňte profil konfigurace zařízení Intune, který jste vytvořili, nebo klíč registru.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste umožnili uživatelům resetovat svá hesla z přihlašovací obrazovky Windows 10. Pokračujte k dalšímu kurzu, ve kterém se dozvíte, jak můžete integrovat Azure Identity Protection do prostředí pro samoobslužné resetování hesla a vícefaktorové ověřování.

> [!div class="nextstepaction"]
> [Hodnocení rizika při přihlášení](tutorial-risk-based-sspr-mfa.md)

[Assignment]: ./media/tutorial-sspr-windows/profile-assignment.png "Přiřazení zásad konfigurace zařízení Intune ke skupině zařízení s Windows 10"
[LoginScreen]: ./media/tutorial-sspr-windows/logon-reset-password.png "Odkaz na resetování hesla na přihlašovací obrazovce Windows 10"
