---
title: Použití serveru Azure MFA se službou AD FS 2.0 – Azure Active Directory
description: Toto je stránka vícefaktorového ověřování Azure, která popisuje, jak začít s vícefaktorovým ověřováním Azure a službami AD FS 2.0.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c79a42bbd60d7a1857649cffc97ed7f0103fa16
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653517"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Konfigurace serveru Azure Multi-Factor Authentication pro práci se službou AD FS 2.0

Tento článek je určený pro organizace, které jsou federované se službou Azure Active Directory a chtějí zabezpečit prostředky, které mají uložené místně nebo v cloudu. Chraňte své prostředky pomocí Azure Multi-Factor Authentication Serveru a jeho nakonfigurováním tak, aby fungoval s AD FS a bylo možné spouštět dvoustupňové ověření pro koncové body vysoké hodnoty.

Tato dokumentace popisuje používání Azure Multi-Factor Authentication Serveru s AD FS 2.0 Další informace o AD FS najdete v tématu [Zabezpečení cloudových a místních prostředků pomocí Azure Multi-Factor Authentication Serveru s Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Zabezpečení AD FS 2.0 pomocí serveru proxy

Pro zabezpečení AD FS 2.0 pomocí proxy serveru nainstalujte Azure Multi-Factor Authentication Server na proxy server služby AD FS.

### <a name="configure-iis-authentication"></a>Konfigurace ověřování IIS

1. V rámci Azure Multi-Factor Authentication Serveru klikněte na ikonu **Ověřování IIS** v levé nabídce.
2. Klikněte na kartu **Založené na formulářích**.
3. Klikněte na **Přidat**.

   ![Okno Ověřování služby IIS serveru MFA](./media/howto-mfaserver-adfs-2/setup1.png)

4. Chcete-li automaticky rozpoznat uživatelské jméno, heslo a proměnné `https://sso.contoso.com/adfs/ls`domény, zadejte přihlašovací adresu URL (například) do dialogového okna Automaticky konfigurovat web založený na formuláři a klepněte na tlačítko **OK**.
5. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud ještě na server nebyl importován velký počet uživatelů nebo budou uživatelé vyloučení z dvoustupňového ověření, nechte toto políčko nezaškrtnuté.
6. Pokud proměnné stránky nejde rozpoznat automaticky, klikněte na **Zadat ručně...** v dialogovém okně Automatická konfigurace webu na základě formuláře.
7. V dialogovém okně Přidat web založený na formuláři zadejte adresu URL přihlašovací stránky `https://sso.contoso.com/adfs/ls`služby AD FS do pole Odeslat adresu URL (například) a zadejte název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
8. Formát požadavku nastavte na **POST nebo GET**.
9. Zadejte Proměnnou uživatelského jména (ctl00$ContentPlaceHolder1$UsernameTextBox) a Proměnnou hesla (ctl00$ContentPlaceHolder1$PasswordTextBox). Pokud vaše formulářová přihlašovací stránka zobrazí pole pro doménu, zadejte taky Proměnnou domény. Pokud chcete vyhledat názvy vstupních polí na přihlašovací stránce, přejděte ve webovém prohlížeči na přihlašovací stránku, klikněte na stránku pravým tlačítkem myši a vyberte **Zobrazit zdrojový kód**.
10. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud ještě na server nebyl importován velký počet uživatelů nebo budou uživatelé vyloučení z dvoustupňového ověření, nechte toto políčko nezaškrtnuté.

    ![Přidání webu založeného na formulářích na server MFA](./media/howto-mfaserver-adfs-2/manual.png)

11. Klikněte na **Upřesnit...** a zkontrolujte upřesňující nastavení. Mezi nastavení, která můžete konfigurovat, patří:

    - Výběr vlastního souboru odmítnutí stránky
    - Ukládání úspěšných ověření na webu do mezipaměti
    - Výběr způsobu ověření primárních přihlašovacích údajů

12. Protože se proxy server služby AD FS pravděpodobně nepřipojí k doméně, můžete použít protokol LDAP pro připojení k řadiči domény pro předběžné ověření a import uživatelů. V dialogovém okně Rozšířená webová stránka s formuláři klikněte na kartu **Primární ověření** a pro typ ověření předběžného ověření vyberte **Vázání protokolu LDAP**.
13. Po dokončení se kliknutím na **OK** vraťte do dialogového okna Přidat webovou stránku s formuláři.
14. Kliknutím na **OK** zavřete dialogové okno.
15. Po zjištění nebo zadání adresy URL a proměnných hodnot stránek se data webové stránky zobrazí v panelu založeném na formulářích.
16. Klikněte na kartu **Nativní modul** a vyberte server, web, pod kterým je spuštěn proxy server služby AD FS (například "Výchozí web") nebo proxy aplikaci služby AD FS (například "ls" v části "adfs") a povolte modul plug-in služby IIS na požadované úrovni.
17. Zaškrtněte políčko **Povolit ověřování IIS** v horní části obrazovky.

Teď je povolené IIS ověřování

### <a name="configure-directory-integration"></a>Konfigurace integrace adresáře

Povolili jste ověřování IIS, ale abyste mohli provádět předběžné ověřování Active Directory (AD) přes LDAP, musíte připojení LDAP nakonfigurovat na řadič domény.

1. Klikněte na ikonu **Integrace adresáře**.
2. Na kartě Nastavení vyberte **přepínač Použít konkrétní konfiguraci LDAP.**

   ![Konfigurace nastavení protokolu LDAP pro konkrétní nastavení protokolu LDAP](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Klikněte na **Upravit**.
4. V dialogovém okně Upravit konfiguraci LDAP vyplňte pole pomocí informací požadovaných pro připojení k řadiči domény AD. Popisy těchto polí jsou uvedeny v souboru nápovědy Azure Multi-Factor Authentication Serveru.
5. Otestujte připojení LDAP klepnutím na tlačítko **Testovat.**

   ![Testování konfigurace protokolu LDAP na serveru MFA](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Pokud byl test připojení LDAP úspěšný, klikněte na **OK**.

### <a name="configure-company-settings"></a>Konfigurace nastavení společnosti

1. Dále klepněte na ikonu **Nastavení společnosti** a vyberte kartu **Rozlišení uživatelského jména.**
2. Vyberte **atribut Použít jedinečný identifikátor LDAP pro odpovídající přepínací tlačítko uživatelských jmen.**
3. Pokud uživatelé zadají své uživatelské jméno ve formátu "doména\uživatelské jméno", server musí být schopen odstranit doménu z uživatelského jména při vytváření dotazu LDAP. To můžete udělat nastavením registrů.
4. Na 64bitovém serveru otevřete editor registrů a přejděte na HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor. Pokud na 32bitovém serveru, vyveďte "Wow6432Node" z cesty. Vytvořte klíč registru DWORD s názvem "UsernameCxz_stripPrefixDomain" a nastavte hodnotu na hodnotu 1. Azure Multi-Factor Authentication teď zabezpečuje proxy server služby AD FS.

Ověřte, že se uživatelé naimportovali z Active Directory do Serveru. Pokud [Trusted IPs section](#trusted-ips) chcete povolit interní IP adresy, takže při přihlašování k webu z těchto míst není nutné dvoustupňové ověření.

![Editor registru pro konfiguraci nastavení společnosti](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct bez serveru proxy

AD FS můžete zabezpečit, i když se server proxy AD FS nepoužívá. Nainstalujte Azure Multi-Factor Authentication Server na server služby ADFS a nakonfigurujte ho podle následujícího postupu:

1. V levém panelu azure multifaktorového ověřovacího serveru klikněte na ikonu **Ověřování služby IIS** v levé nabídce.
2. Klikněte na kartu **HTTP**.
3. Klikněte na **Přidat**.
4. Do dialogového okna Přidat základní adresu URL zadejte adresu URL webu služby AD FS, kde se provádí ověřování HTTP (například `https://sso.domain.com/adfs/ls/auth/integrated`) do pole Základní adresa URL. Potom zadejte název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
5. Podle potřeby upravte časový limit nečinnosti a maximální dobu trvání relace.
6. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud ještě na server nebyl importován velký počet uživatelů nebo budou uživatelé vyloučení z dvoustupňového ověření, nechte toto políčko nezaškrtnuté.
7. V případě potřeby zaškrtněte políčko mezipaměti souborů cookie.

   ![AD FS 2.0 Direct bez serveru proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Klikněte na tlačítko **OK**.
9. Klikněte na kartu **Nativní modul** a vyberte server, web (například "Výchozí web") nebo aplikaci služby AD FS (například "ls" v části "adfs") a povolte modul plug-in služby IIS na požadované úrovni.
10. Zaškrtněte políčko **Povolit ověřování IIS** v horní části obrazovky.

Azure Multi-Factor Authentication teď zabezpečuje službu AD FS.

Ověřte, že se uživatelé naimportovali z Active Directory do Serveru. Pokud chcete povolit interní IP adresy, takže při přihlašování k webu z těchto míst není nutné dvoustupňové ověření.

## <a name="trusted-ips"></a>Důvěryhodné IP adresy

Důvěryhodné IP adresy umožňují uživatelům obejít ověřování Azure Multi-Factor Authentication u požadavků webů pocházejících z konkrétní IP adresy nebo podsítě. Můžete třeba chtít vyloučit uživatele z dvoustupňového ověření, když se přihlásí z kanceláře. V takovém případě zadáte podsíť kanceláře jako položku důvěryhodných IP adres.

### <a name="to-configure-trusted-ips"></a>Konfigurace důvěryhodných adres IP

1. V části Ověřování služby IIS klikněte na kartu **Důvěryhodné adresy IP.**
2. Klikněte na tlačítko **Přidat...** .
3. Jakmile se zobrazí dialogové okno Přidat důvěryhodné IP adresy, vyberte přepínač **Jedna IP adresa**, **Rozsah IP adres** nebo **Podsíť**.
4. Zadejte adresu IP, rozsah adres IP nebo podsíť, která by měla být povolena. Pokud zadáváte podsíť, vyberte příslušnou síťovou masku a klikněte na tlačítko **OK**.

![Konfigurace důvěryhodných ip serverů na serveru MFA](./media/howto-mfaserver-adfs-2/trusted.png)
