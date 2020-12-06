---
title: RDG a Azure MFA Server pomocí protokolu RADIUS – Azure Active Directory
description: Toto je stránka ověřování Azure Multi-Factor Authentication, která vám pomůže při nasazení brány vzdálené plochy (RD) a serveru Azure Multi-Factor Authentication využívajícím protokol RADIUS.
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
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742064"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Brána vzdálené plochy Azure Multi-Factor Authentication Server pomocí protokolu RADIUS

Brána vzdálené plochy (RD) často používá k ověřování uživatelů [službu NPS (Network Policy Services)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures) . Tento článek popisuje směrování požadavků protokolu RADIUS ze služby Brána vzdálené plochy (prostřednictvím místního serveru NPS) na Multi-Factor Authentication Server. Kombinace Azure MFA a služby Brána VP znamená, že při provádění silného ověřování můžou uživatelé ke svým pracovním prostředím přistupovat odkudkoli.

Vzhledem k tomu, že ověřování systému Windows pro terminálové služby není podporováno pro Server 2012 R2, použijte pro integraci s MFA Serverem službu Brána VP a protokol RADIUS.

Nainstalujte Multi-Factor Authentication Server na samostatném serveru, který bude směrovat požadavky protokolu RADIUS přes proxy server zpět na server NPS na serveru služby Brána vzdálené plochy. Když NPS ověří uživatelské jméno a heslo, vrátí odpověď Multi-Factor Authentication Serveru. Potom MFA Server provádí druhý faktor ověřování a vrátí výsledek bráně.

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Pokud používáte cloudové VÍCEFAKTOROVÉ ověřování, přečtěte si téma [integrace s ověřováním protokolu RADIUS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

## <a name="prerequisites"></a>Předpoklady

- Azure MFA Server připojený k doméně. Pokud jej ještě nemáte nainstalovaný, postupujte podle pokynů v tématu [Začínáme s Azure Multi-Factor Authentication Serverem](howto-mfaserver-deploy.md).
- Existující nakonfigurovaný server NPS.
- Brána vzdálené plochy, která provádí ověřování pomocí serveru NPS.

> [!NOTE]
> Tento článek by se měl používat jenom pro nasazení MFA serveru, ne pro Azure MFA (cloudové).

## <a name="configure-the-remote-desktop-gateway"></a>Konfigurace služby Brána vzdálené plochy

Nakonfigurujte službu Brána VP na odesílání ověřování RADIUS na Azure Multi-Factor Authentication Server.

1. V okně Správce brány VP klikněte pravým tlačítkem na název serveru a vyberte **Vlastnosti**.
2. Přejděte na kartu **Úložiště zásad CAP ke Vzdálené ploše** a vyberte **Centrální server NPS**.
3. Přidejte jeden nebo více Azure Multi-Factor Authentication Serverů jako servery RADIUS tak, že pro každý server zadáte název nebo IP adresu.
4. Vytvořte pro každý server sdílený tajný klíč.

## <a name="configure-nps"></a>Konfigurace NPS

Brána VP používá server NPS k odeslání požadavku protokolu RADIUS do Azure Multi-Factor Authentication. Server NPS nakonfigurujete tak, že nejprve změníte nastavení časového limitu, abyste zabránili vypršení časového limitu služby Brána VP před dokončením dvoustupňového ověření. Potom server NPS aktualizujete pro příjem ověření protokolu RADIUS z MFA Serveru. Ke konfiguraci serveru NPS použijte následující postup:

### <a name="modify-the-timeout-policy"></a>Změna zásad vypršení časového limitu

1. Na serveru NPS otevřete nabídku **Klienti a server RADIUS** v levém sloupci a vyberte **Skupiny vzdálených serverů RADIUS**.
2. Vyberte **TS GATEWAY SERVER GROUP**.
3. Přejděte na kartu **Vyrovnávání zatížení**.
4. Změňte **Počet sekund bez odpovědi, než je žádost považována za zrušenou** a **Počet sekund mezi požadavky, když je server identifikován jako nedostupný** na 30–60 sekund. (Pokud zjistíte, že na serveru stále dochází k vypršení časového limitu během ověřování, můžete se vrátit a zvýšit počet sekund.)
5. Přejděte na kartu **Ověřování/účet** a zkontrolujte, že zadané porty protokolu RADIUS odpovídají portům, na kterých naslouchá Multi-Factor Authentication Server.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Příprava serveru NPS na příjem ověření z MFA Serveru

1. V části Klienti a servery RADIUS v levém sloupci klikněte pravým tlačítkem na **Klienti RADIUS** a vyberte **Nový**.
2. Přidejte server Azure Multi-Factor Authentication jako klienta protokolu RADIUS. Zvolte popisný název a určete sdílený tajný klíč.
3. Otevřete nabídku **Zásady** v levém sloupci a vyberte **Zásady vyžádání nového připojení**. Měli byste vidět zásadu TS GATEWAY AUTHORIZATION POLICY, která byla vytvořena při konfiguraci služby Brána VP. Tato zásada předá požadavky protokolu RADIUS serveru Multi-Factor Authentication.
4. Klikněte pravým tlačítkem na **TS GATEWAY AUTHORIZATION POLICY** a vyberte **Duplikovat zásadu**.
5. Otevřete novou zásadu a přejděte na kartu **Podmínky**.
6. Přidejte podmínku, která odpovídá popisnému názvu klienta s popisným názvem nastaveným v kroku 2 pro klienta RADIUS Azure Multi-Factor Authentication Serveru.
7. Přejděte na kartu **Nastavení** a vyberte **Ověřování**.
8. Změňte poskytovatele ověřování na **Ověřit požadavky tímto serverem**. Tato zásada zajistí, že při přijetí požadavku protokolu RADIUS serverem NPS z Azure MFA Serveru dojde k místnímu ověření místo odesílání požadavku protokolu RADIUS zpět na Azure Multi-Factor Authentication Server, což by vytvořilo smyčku.
9. Chcete-li zabránit vzniku smyčky, ujistěte se, že v podokně **Zásady vyžádání nového připojení** je nová zásada v pořadí NAD původní zásadou.

## <a name="configure-azure-multi-factor-authentication"></a>Konfigurace Azure Multi-Factor Authentication

Server Azure Multi-Factor Authentication je nakonfigurován jako proxy server protokolu RADIUS mezi Bránou VP a serverem NPS.  Musí být nainstalován na serveru připojeném k doméně, který je oddělený od serveru Brána VP. Pomocí následujícího postupu nakonfigurujte server Azure Multi-Factor Authentication.

1. Otevřete Azure Multi-Factor Authentication Server a vyberte ikonu ověření RADIUS.
2. Zaškrtněte políčko **Povolit ověřování pomocí protokolu RADIUS**.
3. Na kartě Klienti ověřte, že se porty shodují s tím, co je nakonfigurováno na serveru NPS, a potom vyberte **Přidat**.
4. Přidejte IP adresu serveru služby Brána VP, název aplikace (volitelné) a sdílený tajný klíč. Sdílený tajný klíč musí být stejný jak na Azure Multi-Factor Authentication Serveru, tak i ve službě Brána VP.
3. Přejděte na kartu **Cíl** a vyberte přepínač **Server(y) RADIUS**.
4. Vyberte **Přidat** a zadejte IP adresu, sdílený tajný klíč a porty serveru NPS. Pokud nepoužíváte centrální server NPS, klient RADIUS a cíl RADIUS budou stejné. Sdílený tajný klíč musí odpovídat jednomu nastavení v oddílu klienta protokolu RADIUS serveru NPS.

![Ověřování pomocí protokolu RADIUS na serveru MFA](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Další kroky

- Integrace Azure MFA a [webových aplikací IIS](howto-mfaserver-iis.md)

- Získání odpovědí v části [Nejčastější dotazy k Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)