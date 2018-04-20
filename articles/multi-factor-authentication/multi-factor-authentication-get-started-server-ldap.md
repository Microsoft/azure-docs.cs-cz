---
title: "Ověřování pomocí protokolu LDAP a Azure MFA Server | Dokumentace Microsoftu"
description: "Toto je stránka Azure Multi-Factor Authentication, která vám pomůže při nasazení ověření LDAP a serveru Azure Multi-Factor Authentication."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: joflore
ms.openlocfilehash: 37f96afab67680b3140a6f4da6bff0c23a1f328f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>Ověření služby LDAP a Server Azure Multi-Factor Authentication
Ve výchozím nastavení je server Azure Multi-Factor Authentication nakonfigurován pro import nebo synchronizaci uživatelů ze služby Active Directory. Můžete ho však navázat na různé adresáře LDAP, například adresář ADAM nebo konkrétní řadič domény služby Active Directory. Při připojení k adresáři prostřednictvím protokolu LDAP, Azure Multi-Factor Authentication Server může fungovat jako server proxy protokolu LDAP k provedení ověření. Umožňuje také použití vazby protokolu LDAP jako cíle protokolu RADIUS pro předběžné ověřování uživatelů pomocí ověření IIS, nebo pro primární ověřování na portálu Azure MFA User Portal.

Chcete-li použít Azure Multi-Factor Authentication jako proxy server LDAP, vložte Azure Multi-Factor Authentication Server mezi klientem LDAP (například zařízení VPN, aplikace) a adresářový server LDAP. Azure Multi-Factor Authentication Server musí být nakonfigurován tak, aby komunikoval s klientskými servery i s adresářem LDAP. V této konfiguraci server Azure Multi-Factor Authentication přijímá požadavky LDAP od klientských serverů a aplikací a předává je cílovému adresářovému serveru LDAP pro ověření primárních pověření. Pokud adresář LDAP ověří primární pověření, Azure Multi-Factor Authentication provede druhé ověření identity a odešle odpověď zpět klientovi LDAP. Celkové ověření proběhne úspěšně pouze pokud je úspěšné ověření pomocí serveru LDAP i druhý krok ověření.

## <a name="configure-ldap-authentication"></a>Konfigurace ověřování pomocí protokolu LDAP
Pro konfiguraci ověřování pomocí protokolu LDAP nainstalujte server Azure Multi-Factor Authentication na server Windows. Použijte následující postup:

### <a name="add-an-ldap-client"></a>Přidání klienta protokolu LDAP

1. V Azure Multi-Factor Authentication Server vyberte v levé nabídce na ikonu ověřování LDAP.
2. Zaškrtněte políčko **Povolit ověřování pomocí protokolu LDAP**.

   ![Ověřování pomocí protokolu LDAP](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. Na kartě Klienti změňte port TCP a SSL, pokud má služba Azure Multi-Factor Authentication LDAP vytvořit vazbu na nestandardní porty pro naslouchání požadavkům protokolu LDAP.
4. Pokud plánujete používat LDAPS z klienta na serveru Azure Multi-Factor Authentication, musí být na stejném serveru jako Server vícefaktorového ověřování nainstalovaný certifikát SSL. Klikněte na tlačítko **Procházet** vedle SSL certifikát pole a vyberte certifikát, který chcete použít pro zabezpečené připojení.
5. Klikněte na tlačítko **Přidat**.
6. V dialogovém okně Přidat klienta LDAP zadejte IP adresu zařízení, server nebo aplikaci, která ověřuje na Server a název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
7. Zaškrtněte políčko **Vyžadovat porovnání uživatele Azure Multi-Factor Authentication**, pokud byli nebo budou všichni uživatelé importováni na server a podstoupí dvoustupňové ověření. Pokud velký počet uživatelů dosud nebyl importován do serveru a/nebo se vyloučí z dvoustupňové ověřování, nechte pole nezaškrtnuté. Server MFA soubor pro další informace o této funkci nápovědy zobrazit.

Opakujte tyto kroky pro přidání dalších klientů protokolu LDAP.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurace připojení k adresáři LDAP

Pokud je Azure Multi-Factor Authentication nakonfigurováno pro příjem ověřování LDAP, musí směrovat proxy těchto ověřování do adresáře protokolu LDAP. Proto karta Cíl pouze zobrazí jednu zašedlou možnost použití cíle LDAP.

1. Pro konfiguraci připojení k adresáři LDAP klikněte na ikonu **Integrace adresáře**.
2. Na kartě Nastavení vyberte přepínač **Použít specifickou konfiguraci LDAP**.
3. Vyberte **Upravit...**
4. V dialogovém okně Upravit konfiguraci LDAP vyplňte pole pomocí informací požadovaných pro připojení k adresáři protokolu LDAP. Popisy těchto polí jsou uvedeny v souboru nápovědy Azure Multi-Factor Authentication Serveru.

    ![Integrace adresáře](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Otestujte připojení LDAP kliknutím na tlačítko **Test**.
6. Pokud byl test připojení LDAP úspěšný, klikněte na tlačítko **OK**.
7. Klikněte na kartu **Filtry**. Server je předem nakonfigurovaný k načítání kontejnerů, skupin zabezpečení a uživatelů ze služby Active Directory. Pokud provádíte navázání na jiný adresář LDAP, budete pravděpodobně muset upravit zobrazené filtry. Kliknutím na odkaz **Nápověda** zobrazíte další informace o filtrech.
8. Klikněte na kartu **Atributy**. Server je předem nakonfigurovaný k mapování atributů ze služby Active Directory.
9. Pokud provádíte navázání na jiný adresář LDAP nebo chcete změnit předem nakonfigurované mapování atributů, klikněte na **Upravit...**
10. V dialogovém okně Upravit atributy upravte mapování atributů protokolu LDAP pro váš adresář. Názvy atributů můžete zadat nebo vybrat kliknutím na **...** na tlačítko vedle každého pole. Kliknutím na odkaz **Nápověda** zobrazíte další informace o atributech.
11. Klikněte na tlačítko **OK**.
12. Klikněte na ikonu **Nastavení společnosti** a vyberte kartu **Překlad uživatelského jména**.
13. Pokud se připojujete ke službě Active Directory ze serveru připojeného k doméně, ponechejte vybraný přepínač **Pro porovnání uživatelských jmen použít identifikátory zabezpečení systému Windows (SID)**. Jinak vyberte přepínač **Pro porovnávání uživatelských jmen použít atribut jedinečného identifikátoru LDAP**. 

Když je vybraný přepínač **Pro porovnávání uživatelských jmen použít atribut jedinečného identifikátoru LDAP**, Azure Multi-Factor Authentication Server se pokusí každé uživatelské jméno přeložit na jedinečný identifikátor v adresáři LDAP. Provede se vyhledávání protokolu LDAP u atributů uživatelského jména definovaných v části Integrace adresáře na kartě Atributy. Při ověření uživatele je uživatelské jméno přeložit na jedinečný identifikátor v adresáři LDAP. Jedinečný identifikátor se používá k porovnání s uživatelem v datovém souboru Azure Multi-Factor Authentication. To umožňuje porovnávání s rozlišování velikosti písmen a dlouhé a krátké formáty uživatelských jmen.

Po dokončení těchto kroků, MFA Server naslouchá na nakonfigurovaných portech požadavkům přístupu LDAP z konfigurovaných klientů a funguje jako proxy pro tyto požadavky na adresář LDAP pro ověřování.

## <a name="configure-ldap-client"></a>Konfigurace klienta LDAP
Chcete-li nakonfigurovat klienta LDAP, postupujte podle pokynů:

* Nakonfigurujte zařízení, server nebo aplikaci k ověřování prostřednictvím protokolu LDAP na Azure Multi-Factor Authentication Serveru, jako by šlo o váš adresář LDAP. Použijte stejné nastavení, které běžně používáte pro připojení přímo k adresáři LDAP, s výjimkou názvu serveru nebo IP adresy, u kterých použijte hodnoty pro Azure Multi-Factor Authentication Server.
* Nakonfigurujte na 30 – 60 sekund vypršel časový limit LDAP, tak, aby bylo času na ověření pověření uživatele v adresáři LDAP, druhý krok ověření, obdržení odpovědi a reagovat na žádosti o přístup protokolu LDAP.
* V případě použití LDAPS musí zařízení nebo server provádějící žádosti LDAP důvěřovat certifikátu SSL nainstalovaném na serveru Azure Multi-Factor Authentication.

