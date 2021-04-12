---
title: Ověřování pomocí protokolu LDAP a Azure MFA Server – Azure Active Directory
description: Nasazení ověřování pomocí protokolu LDAP a Azure Multi-Factor Authentication Server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742217"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Ověřování pomocí protokolu LDAP a Azure Multi-Factor Authentication Server

Ve výchozím nastavení je Azure Multi-Factor Authentication Server nakonfigurovaný pro import nebo synchronizaci uživatelů ze služby Active Directory. Můžete ho však navázat na různé adresáře LDAP, například adresář ADAM nebo konkrétní řadič domény služby Active Directory. Když se připojíte k adresáři přes LDAP, může Azure Multi-Factor Authentication Server fungovat jako proxy server LDAP a provádět ověřování. Umožňuje také použití vazby protokolu LDAP jako cíle protokolu RADIUS pro předběžné ověřování uživatelů pomocí ověření IIS, nebo pro primární ověřování na portálu Azure MFA User Portal.

Pokud chcete použít Azure Multi-Factor Authentication jako proxy LDAP, vložte Multi-Factor Authentication Server Azure mezi klienta LDAP (například zařízení VPN, aplikace) a adresářový server LDAP. Azure Multi-Factor Authentication Server musí být nakonfigurován tak, aby komunikoval s klientskými servery i s adresářem LDAP. V této konfiguraci server Azure Multi-Factor Authentication přijímá požadavky LDAP od klientských serverů a aplikací a předává je cílovému adresářovému serveru LDAP pro ověření primárních pověření. Pokud adresář LDAP ověří primární přihlašovací údaje, Azure Multi-Factor Authentication provede druhé ověření identity a pošle odpověď zpátky klientovi LDAP. Celkové ověření proběhne úspěšně pouze pokud je úspěšné ověření pomocí serveru LDAP i druhý krok ověření.

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

## <a name="configure-ldap-authentication"></a>Konfigurace ověřování pomocí protokolu LDAP

Pro konfiguraci ověřování pomocí protokolu LDAP nainstalujte server Azure Multi-Factor Authentication na server Windows. Použijte následující postup:

### <a name="add-an-ldap-client"></a>Přidání klienta protokolu LDAP

1. V Multi-Factor Authentication Server Azure vyberte v levé nabídce ikonu ověřování LDAP.
2. Zaškrtněte políčko **Povolit ověřování pomocí protokolu LDAP** .

   ![Ověřování protokolu LDAP na serveru MFA](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Na kartě Klienti změňte port TCP a SSL (TLS), pokud má služba Azure Multi-Factor Authentication LDAP vytvořit vazby na nestandardní porty pro naslouchání požadavkům LDAP.
4. Pokud plánujete použít LDAPs z klienta nástroje na Multi-Factor Authentication Server Azure, musí být certifikát TLS/SSL nainstalovaný na stejném serveru jako server MFA. Klikněte na tlačítko **Procházet** vedle pole certifikát SSL (TLS) a vyberte certifikát, který chcete použít pro zabezpečené připojení.
5. Klikněte na **Přidat**.
6. V dialogovém okně Přidat klienta LDAP zadejte IP adresu zařízení, serveru nebo aplikace, které se ověřují pro server a název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
7. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud se na server ještě neimportoval velký počet uživatelů a/nebo se nejedná o dvoustupňové ověření, ponechejte políčko nezaškrtnuté. Další informace o této funkci najdete v souboru s nápovědu k serveru MFA.

Opakujte tyto kroky pro přidání dalších klientů protokolu LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurace připojení k adresáři LDAP

Pokud je Azure Multi-Factor Authentication nakonfigurováno pro příjem ověřování LDAP, musí směrovat proxy těchto ověřování do adresáře protokolu LDAP. Proto karta Cíl pouze zobrazí jednu zašedlou možnost použití cíle LDAP.

> [!NOTE]
> Integrace adresáře není zaručena pracovat s jinými adresáři než Active Directory Domain Services.

1. Chcete-li konfigurovat připojení adresáře LDAP, klikněte na ikonu **integrace adresáře** .
2. Na kartě nastavení vyberte přepínač **použít konkrétní konfiguraci LDAP** .
3. Vybrat **Upravit...**
4. V dialogovém okně Upravit konfiguraci LDAP vyplňte pole pomocí informací požadovaných pro připojení k adresáři protokolu LDAP. Popisy těchto polí jsou uvedeny v souboru nápovědy Azure Multi-Factor Authentication Serveru.

    ![Konfigurace LDAP pro integraci adresáře](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Otestujte připojení LDAP kliknutím na tlačítko **test** .
6. Pokud byl test připojení LDAP úspěšný, klikněte na tlačítko **OK** .
7. Klikněte na kartu **filtry** . Server je předem nakonfigurovaný k načítání kontejnerů, skupin zabezpečení a uživatelů ze služby Active Directory. Pokud provádíte navázání na jiný adresář LDAP, budete pravděpodobně muset upravit zobrazené filtry. Kliknutím na odkaz **nápovědu** zobrazíte další informace o filtrech.
8. Klikněte na kartu **atributy** . Server je předem nakonfigurovaný pro mapování atributů ze služby Active Directory.
9. Pokud provádíte navázání na jiný adresář LDAP nebo chcete změnit předem nakonfigurované mapování atributů, klikněte na **Upravit...**
10. V dialogovém okně Upravit atributy upravte mapování atributů protokolu LDAP pro váš adresář. Názvy atributů můžete zadat nebo vybrat kliknutím na **...** na tlačítko vedle každého pole. Další informace o atributech získáte kliknutím na odkaz **help** .
11. Klikněte na tlačítko **OK** .
12. Klikněte na ikonu **nastavení společnosti** a vyberte kartu **překlad uživatelského jména** .
13. Pokud se připojujete ke službě Active Directory ze serveru připojeného k doméně, ponechejte vybraný přepínač **Pro porovnání uživatelských jmen použít identifikátory zabezpečení systému Windows (SID)**. V opačném případě zaškrtněte přepínač **pro porovnání uživatelských jmen použít atribut jedinečného identifikátoru LDAP** .

Když je vybraný přepínač **Pro porovnávání uživatelských jmen použít atribut jedinečného identifikátoru LDAP**, Azure Multi-Factor Authentication Server se pokusí každé uživatelské jméno přeložit na jedinečný identifikátor v adresáři LDAP. Hledání LDAP se provádí na atributech uživatelského jména definovaných v integraci adresářů – > atributy. Po ověření uživatele se uživatelské jméno přeloží na jedinečný identifikátor v adresáři LDAP. Jedinečný identifikátor se používá pro porovnání uživatele v datovém souboru Azure Multi-Factor Authentication. To umožňuje porovnávání bez rozlišení velkých a malých písmen a dlouhé a krátké formáty uživatelského jména.

Po dokončení těchto kroků server MFA naslouchá na konfigurovaných portech pro požadavky na přístup LDAP od konfigurovaných klientů a slouží jako proxy pro tyto požadavky na adresář LDAP pro ověřování.

## <a name="configure-ldap-client"></a>Konfigurace klienta LDAP

Chcete-li nakonfigurovat klienta LDAP, postupujte podle pokynů:

* Nakonfigurujte zařízení, server nebo aplikaci k ověřování prostřednictvím protokolu LDAP na Azure Multi-Factor Authentication Serveru, jako by šlo o váš adresář LDAP. Použijte stejné nastavení, které běžně používáte pro připojení přímo k adresáři LDAP, s výjimkou názvu serveru nebo IP adresy, u kterých použijte hodnoty pro Azure Multi-Factor Authentication Server.
* Nastavte časový limit protokolu LDAP na 30-60 sekund, aby měl čas ověřit přihlašovací údaje uživatele v adresáři LDAP, provést ověření v druhém kroku, přijmout odpověď a odpovědět na žádost o přístup k protokolu LDAP.
* Pokud používáte protokol LDAPs, musí zařízení nebo server, který provádí dotazy protokolu LDAP, důvěřovat certifikátu TLS/SSL nainstalovanému na Azure Multi-Factor Authentication Server.
