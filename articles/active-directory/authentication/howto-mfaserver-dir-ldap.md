---
title: Ověřování LDAP a server Azure MFA – Azure Active Directory
description: Nasazení ověřování LDAP a serveru Azure Pro vícefaktorové ověřování.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7a5400fce2fce1b75cbd579830f929fcc0d0dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454461"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Ověřování LDAP a server Azure pro vícefaktorové ověřování

Ve výchozím nastavení je Azure Multi-Factor Authentication Server nakonfigurovaný pro import nebo synchronizaci uživatelů ze služby Active Directory. Můžete ho však navázat na různé adresáře LDAP, například adresář ADAM nebo konkrétní řadič domény služby Active Directory. Při připojení k adresáři přes LDAP může server Azure Multi-Factor Authentication Server fungovat jako proxy server LDAP pro ověřování. Umožňuje také použití vazby protokolu LDAP jako cíle protokolu RADIUS pro předběžné ověřování uživatelů pomocí ověření IIS, nebo pro primární ověřování na portálu Azure MFA User Portal.

Chcete-li azure multifaktorové ověřování používat jako proxy server LDAP, vložte server Azure Multi-Factor Authentication Server mezi klienta LDAP (například zařízení VPN, aplikace) a adresářový server LDAP. Azure Multi-Factor Authentication Server musí být nakonfigurován tak, aby komunikoval s klientskými servery i s adresářem LDAP. V této konfiguraci server Azure Multi-Factor Authentication přijímá požadavky LDAP od klientských serverů a aplikací a předává je cílovému adresářovému serveru LDAP pro ověření primárních pověření. Pokud adresář LDAP ověří primární pověření, Azure Multi-Factor Authentication provede druhé ověření identity a odešle odpověď zpět klientovi LDAP. Celkové ověření proběhne úspěšně pouze pokud je úspěšné ověření pomocí serveru LDAP i druhý krok ověření.

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet server MFA pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali server MFA před 1.

## <a name="configure-ldap-authentication"></a>Konfigurace ověřování LDAP

Pro konfiguraci ověřování pomocí protokolu LDAP nainstalujte server Azure Multi-Factor Authentication na server Windows. Použijte následující postup:

### <a name="add-an-ldap-client"></a>Přidání klienta protokolu LDAP

1. Na serveru Azure Multi-Factor Authentication Server vyberte ikonu ověřování LDAP v levé nabídce.
2. Zaškrtněte políčko **Povolit ověřování LDAP.**

   ![Ověřování LDAP na serveru MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na kartě Klienti změňte port TCP a port SSL (TLS), pokud by se služba LDAP azure s vícefaktorovým ověřováním měla vázat na nestandardní porty, aby naslouchala požadavkům LDAP.
4. Pokud plánujete použít protokol LDAPS z klienta na server Azure Multi-Factor Authentication, musí být na stejném serveru jako server MFA nainstalován certifikát TLS/SSL. Klikněte na **Procházet** vedle pole certifikátu SSL (TLS) a vyberte certifikát, který chcete použít pro zabezpečené připojení.
5. Klikněte na **Přidat**.
6. V dialogovém okně Přidat klienta LDAP zadejte ADRESU IP zařízení, serveru nebo aplikace, která se ověřuje na serveru, a název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
7. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud významný počet uživatelů ještě nebyl importován na server a/nebo jsou osvobozeni od dvoustupňového ověření, ponechte políčko nezaškrtnuté. Další informace o této funkci naleznete v souboru nápovědy serveru MFA.

Opakujte tyto kroky pro přidání dalších klientů protokolu LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurace připojení k adresáři LDAP

Pokud je Azure Multi-Factor Authentication nakonfigurováno pro příjem ověřování LDAP, musí směrovat proxy těchto ověřování do adresáře protokolu LDAP. Proto karta Cíl pouze zobrazí jednu zašedlou možnost použití cíle LDAP.

> [!NOTE]
> Není zaručeno, že integrace adresářů bude pracovat s jinými adresáři než se službou Active Directory Domain Services.

1. Chcete-li nakonfigurovat připojení adresáře LDAP, klepněte na ikonu **Integrace adresáře.**
2. Na kartě Nastavení vyberte **přepínač Použít konkrétní konfiguraci LDAP.**
3. Vybrat **Upravit...**
4. V dialogovém okně Upravit konfiguraci LDAP vyplňte pole pomocí informací požadovaných pro připojení k adresáři protokolu LDAP. Popisy těchto polí jsou uvedeny v souboru nápovědy Azure Multi-Factor Authentication Serveru.

    ![Konfigurace ldap integrace adresářů](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Otestujte připojení LDAP klepnutím na tlačítko **Testovat.**
6. Pokud byl test připojení LDAP úspěšný, klepněte na tlačítko **OK.**
7. Klikněte na kartu **Filtry.** Server je předem nakonfigurován tak, aby načítaný pro načtení kontejnerů, skupin zabezpečení a uživatelů ze služby Active Directory. Pokud provádíte navázání na jiný adresář LDAP, budete pravděpodobně muset upravit zobrazené filtry. Další informace o filtrech získáte klepnutím na odkaz **Nápověda.**
8. Klikněte na kartu **Atributy.** Server je předem nakonfigurován tak, aby mapoval atributy ze služby Active Directory.
9. Pokud provádíte navázání na jiný adresář LDAP nebo chcete změnit předem nakonfigurované mapování atributů, klikněte na **Upravit...**
10. V dialogovém okně Upravit atributy upravte mapování atributů protokolu LDAP pro váš adresář. Názvy atributů lze zadat nebo vybrat kliknutím na **...** na tlačítko vedle každého pole. Další informace o atributech získáte klepnutím na odkaz **Nápověda.**
11. Klikněte na tlačítko **OK.**
12. Klikněte na ikonu **Nastavení společnosti** a vyberte kartu **Rozlišení uživatelského jména.**
13. Pokud se připojujete ke službě Active Directory ze serveru připojeného k doméně, ponechejte vybraný přepínač **Pro porovnání uživatelských jmen použít identifikátory zabezpečení systému Windows (SID)**. V opačném případě vyberte **atribut Použít jedinečný identifikátor LDAP pro odpovídající přepínací tlačítko uživatelských jmen.**

Když je vybraný přepínač **Pro porovnávání uživatelských jmen použít atribut jedinečného identifikátoru LDAP**, Azure Multi-Factor Authentication Server se pokusí každé uživatelské jméno přeložit na jedinečný identifikátor v adresáři LDAP. Vyhledávání LDAP se provádí na atributy Uživatelské jméno definované na kartě Directory Integration -> Atributy. Když se uživatel ověří, uživatelské jméno je přeloženo na jedinečný identifikátor v adresáři LDAP. Jedinečný identifikátor se používá pro párování uživatele v datovém souboru Azure Multi-Factor Authentication. To umožňuje porovnání bez rozlišování velkých a malých písmen a dlouhé a krátké formáty uživatelského jména.

Po dokončení těchto kroků server MFA naslouchá na konfigurovaných portech pro požadavky na přístup LDAP od nakonfigurovaných klientů a funguje jako proxy server pro tyto požadavky do adresáře LDAP pro ověřování.

## <a name="configure-ldap-client"></a>Konfigurace klienta LDAP

Chcete-li nakonfigurovat klienta LDAP, postupujte podle pokynů:

* Nakonfigurujte zařízení, server nebo aplikaci k ověřování prostřednictvím protokolu LDAP na Azure Multi-Factor Authentication Serveru, jako by šlo o váš adresář LDAP. Použijte stejné nastavení, které běžně používáte pro připojení přímo k adresáři LDAP, s výjimkou názvu serveru nebo IP adresy, u kterých použijte hodnoty pro Azure Multi-Factor Authentication Server.
* Nakonfigurujte časový rozsah LDAP na 30 až 60 sekund tak, aby byl čas ověřit pověření uživatele pomocí adresáře LDAP, provést ověření ve druhém kroku, přijmout jejich odpověď a odpovědět na žádost o přístup LDAP.
* Pokud používáte ldaps, zařízení nebo server, který provádí dotazy LDAP, musí důvěřovat certifikátu TLS/SSL nainstalovanému na serveru Azure Multi-Factor Authentication Server.
