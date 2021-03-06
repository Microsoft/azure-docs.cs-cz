---
title: Začínáme s Azure MFA serverem – Azure Active Directory
description: Podrobný návod, jak začít s místním Azure MFA Serverem
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7bd9913cccbe077a4deed9a7c5bfdc601f3dd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742336"
---
# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Začínáme s Azure Multi-Factor Authentication Serverem

<center>

![Začínáme s místním MFA serverem](./media/howto-mfaserver-deploy/server2.png)</center>

Tato stránka popisuje novou instalaci serveru a jeho nastavení pro spolupráci s místní službou Active Directory. Pokud už máte nainstalovaný server MFA a chcete provést upgrade, přečtěte si téma [Upgrade na nejnovější verzi Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-upgrade.md). Pokud hledáte informace pouze o instalaci webové služby, přečtěte si téma [Nasazení webové služby mobilní aplikace Azure Multi-Factor Authentication Serveru](howto-mfaserver-deploy-mobileapp.md).

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) během přihlašovacích událostí, by měli používat cloudovou Multi-Factor Authentication Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem.

## <a name="plan-your-deployment"></a>Plánování nasazení

Před stažením Azure Multi-Factor Authentication Serveru se zamyslete nad tím, jaké jsou vaše požadavky na zatížení a vysokou dostupnost. Tyto informace použijte k rozhodnutí, jak a kde provést nasazení.

Dobrým vodítkem pro velikost potřebné paměti je počet uživatelů, u kterých očekáváte pravidelné ověřování.

| Uživatelé | Paměť RAM |
| ----- | --- |
| 1-10,000 | 4 GB |
| 10,001-50,000 | 8 GB |
| 50,001-100,000 | 12 GB |
| 100,000-200,001 | 16 GB |
| 200,001+ | 32 GB |

Potřebujete nastavit několik serverů pro zajištění vysoké dostupnosti nebo vyrovnávání zatížení? Existuje řada způsobů, jak tuto konfiguraci s Azure MFA Serverem nastavit. Z prvního nainstalovaného Azure MFA Serveru se stane hlavní server. Všechny další servery jsou podřízené a automaticky synchronizují uživatele a konfiguraci s hlavním serverem. Potom můžete nakonfigurovat jeden primární server a využívat ostatní jako zálohu, nebo můžete nastavit vyrovnávání zatížení mezi všemi servery.

Když hlavní Azure MFA Server přejde do offline režimu, podřízené servery mohou nadále zpracovávat žádosti o dvoustupňové ověření. Nemůžete ale přidávat nové uživatele a stávající uživatelé nemohou aktualizovat svoje nastavení, dokud se hlavní server nevrátí do online režimu nebo dokud nedojde ke zvýšení úrovně podřízeného severu.

### <a name="prepare-your-environment"></a>Příprava prostředí

Ujistěte se, že server, který používáte pro Azure Multi-Factor Authentication, splňuje následující požadavky:

| Požadavky pro Azure Multi-Factor Authentication Server | Description |
|:--- |:--- |
| Hardware |<li>200 MB volného místa na pevném disku</li><li>Procesor kompatibilní s x32 nebo x64</li><li>1 GB RAM nebo víc</li> |
| Software |<li>Windows Server 2016</li><li>Windows Server 2012 R2</li><li>Windows Server 2012</li><li>Windows Server 2008/R2 (jenom s [EVJ](/lifecycle/faq/extended-security-updates) )</li><li>Windows 10</li><li>Windows 8.1, všechny edice</li><li>Windows 8, všechny edice</li><li>Windows 7, všechny edice (jenom s [EVJ](/lifecycle/faq/extended-security-updates) )</li><li>Microsoft .NET 4.0 Framework</li><li>IIS 7.0 nebo novější, pokud instalujete uživatelský portál nebo sadu SDK webové služby</li> |
| Oprávnění | Účet správce domény nebo účet správce rozlehlé sítě pro registraci ve službě Active Directory |

### <a name="azure-mfa-server-components"></a>Komponenty Azure MFA Serveru

Azure MFA Server se skládá ze tří webových komponent:

* Sada SDK webové služby – Umožňuje komunikaci s dalšími komponentami a je nainstalovaná na aplikačním serveru Azure MFA.
* User Portal – Web služby IIS, který uživatelům umožňuje se zaregistrovat do služby Azure Multi-Factor Authentication (MFA) a spravovat své účty.
* Webová služba mobilní aplikace – Umožňuje používání mobilní aplikace, jako je aplikace Microsoft Authenticator, pro dvoustupňové ověřování.

Všechny tři komponenty můžou být nainstalované na stejném serveru, pokud má přístup k internetu. V případě rozdělení komponent je sada SDK webové služby nainstalovaná na aplikačním serveru Azure MFA a portál User Portal a webová služba mobilní aplikace jsou nainstalované na serveru s přístupem k internetu.

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Požadavky na firewall pro Azure Multi-Factor Authentication Server

Každý server MFA musí být schopný komunikovat na odchozím portu 443 s těmito adresami:

* https://pfd.phonefactor.net
* https://pfd2.phonefactor.net
* https://css.phonefactor.net

Pokud brána firewall omezuje odchozí port 443, je nutné otevřít tyto rozsahy IP adres:

| Podsíť IP | Síťová maska | Rozsah IP adres |
|:---: |:---: |:---: |
| 134.170.116.0/25 |255.255.255.128 |134.170.116.1 – 134.170.116.126 |
| 134.170.165.0/25 |255.255.255.128 |134.170.165.1 – 134.170.165.126 |
| 70.37.154.128/25 |255.255.255.128 |70.37.154.129 – 70.37.154.254   |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

Pokud nepoužíváte funkci Potvrzení události a vaši uživatelé nepoužívají k ověřování ze zařízení v podnikové síti mobilní aplikace, potřebujete pouze následující rozsahy:

| Podsíť IP | Síťová maska | Rozsah IP adres |
|:---: |:---: |:---: |
| 134.170.116.72/29 |255.255.255.248 |134.170.116.72 – 134.170.116.79|
| 134.170.165.72/29 |255.255.255.248 |134.170.165.72 – 134.170.165.79|
| 70.37.154.200/29 |255.255.255.248  |70.37.154.201 – 70.37.154.206  |
| 52.251.8.48/28   | 255.255.255.240 | 52.251.8.48 - 52.251.8.63     |
| 52.247.73.160/28 | 255.255.255.240 | 52.247.73.160 - 52.247.73.175 |
| 52.159.5.240/28  | 255.255.255.240 | 52.159.5.240 - 52.159.5.255   |
| 52.159.7.16/28   | 255.255.255.240 | 52.159.7.16 - 52.159.7.31     |
| 52.250.84.176/28 | 255.255.255.240 | 52.250.84.176 - 52.250.84.191 |
| 52.250.85.96/28  | 255.255.255.240 | 52.250.85.96 - 52.250.85.111  |

## <a name="download-the-mfa-server"></a>Stažení MFA Serveru

Postupujte podle těchto kroků a stáhněte Azure Multi-Factor Authentication Server z webu Azure Portal:

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí MFA Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování (MFA) od uživatelů, by měli používat cloudovou Multi-Factor Authentication služby Azure AD.
>
> Pokud chcete začít s cloudovým ověřováním MFA, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Stávající zákazníci, kteří si vyaktivovali MFA Server dřív než 1. července 2019, můžou stáhnout nejnovější verzi, budoucí aktualizace a vygenerovat přihlašovací údaje pro aktivaci obvyklým způsobem. Následující postup funguje jenom v případě, že jste byli stávajícím zákazníkem MFA serveru.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte *Azure Active Directory*. Vyberte **zabezpečení**  >  **MFA**.
3. V části **správce MFA serveru** vyberte **nastavení serveru**.
4. Vyberte **Stáhnout** a podle pokynů na stránce pro stažení uložte instalační program. 

   ![Stažení MFA serveru z Azure Portal](./media/howto-mfaserver-deploy/downloadportal.png)

5. Ponechte tuto stránku otevřenou, protože se k ní vrátíme po spuštění instalačního programu.

## <a name="install-and-configure-the-mfa-server"></a>Instalace a konfigurace MFA Serveru

Teď, když jste server stáhli, ho můžete nainstalovat a nastavit. Ujistěte se, že server, na kterém ho chcete nainstalovat, splňuje požadavky uvedené v části věnované plánování.

1. Dvakrát klikněte na spustitelný soubor.
2. Na obrazovce vybrat instalační složku se ujistěte, že je složka správná, a klikněte na **Další**.
3. Až instalace skončí, klikněte na **Dokončit**. Spustí se průvodce konfigurací.
4. Na úvodní obrazovce průvodce konfigurací zaškrtněte políčko **Vynechat použití průvodce konfigurací ověřování** a klikněte na **Další**. Průvodce se zavře a spustí se server.

   ![Přeskočit pomocí Průvodce konfigurací ověřování](./media/howto-mfaserver-deploy/skip2.png)

5. Zpátky na stránce, odkud jste server stáhli, klikněte na tlačítko **Vytvoření přihlašovacích údajů pro aktivaci**. Tyto údaje zkopírujte do příslušných polí v Azure MFA Serveru a klikněte na **Aktivovat**.

> [!NOTE]
> Přihlašovací údaje pro aktivaci v Azure Portal můžou generovat jenom globální správci.

## <a name="send-users-an-email"></a>Zaslání e-mailu uživatelům

Pro usnadnění uvedení povolte MFA Serveru komunikaci s vašimi uživateli. MFA Server jim může odeslat e-mail s informací, že byli zaregistrování k dvoustupňovému ověřování.

Jaký e-mail odešlete byste měli určit podle toho, jak jste pro uživatele nakonfigurovali dvoustupňové ověřování. Například pokud máte možnost naimportovat telefonní čísla z adresáře společnosti, e-mail by měl obsahovat výchozí telefonní čísla, aby uživatelé věděli, co mají očekávat. Pokud telefonní čísla nenaimportujete nebo pokud budou uživatelé používat mobilní aplikaci, odešlete jim e-mail, který je nasměruje k dokončení registrace účtu. Do e-mailu přidejte hypertextový odkaz na portál Azure Multi-Factor Authentication User Portal.

Obsah e-mailu se liší také podle metody ověřování nastavené pro konkrétního uživatele (telefonní hovor, zpráva SMS nebo mobilní aplikace). Pokud například uživatel musí při ověřování zadat PIN, v e-mailu se dozví, jaký počáteční PIN je pro něj nastavený. Uživatelé musí při prvním ověření svůj PIN změnit.

### <a name="configure-email-and-email-templates"></a>Konfigurace e-mailu a šablon e-mailu

Kliknutím vlevo na ikonu e-mailu můžete změnit nastavení odesílání těchto e-mailů. Na této stránce můžete zadat informace o vašem poštovním serveru SMTP a odesílat e-maily zaškrtnutím políčka **Odesílat uživatelům e-maily**.

![Konfigurace e-mailu MFA Serveru](./media/howto-mfaserver-deploy/email1.png)

Na kartě Obsah e-mailu uvidíte šablony e-mailů, ze kterých si můžete vybrat. V závislosti na tom, jak jste uživatelům nakonfigurovali dvoustupňové ověřování, vyberte nejvhodnější šablonu.

![Šablony e-mailu MFA serveru v konzole](./media/howto-mfaserver-deploy/email2.png)

## <a name="import-users-from-active-directory"></a>Import uživatelů ze služby Active Directory

Teď, když je server nainstalovaný, budete chtít přidat uživatele. Můžete je vytvořit ručně, naimportovat uživatele ze služby Active Directory nebo nakonfigurovat automatizovanou synchronizaci se službou Active Directory.

### <a name="manual-import-from-active-directory"></a>Ruční import ze služby Active Directory

1. V Azure MFA Serveru klikněte vlevo na **Uživatelé**.
2. Dole vyberte **Importovat ze služby Active Directory**.
3. Teď můžete hledat jednotlivé uživatele nebo v adresáři AD vyhledat organizační jednotky, ve kterých jsou uživatelé. V tomto případě vyhledáme OJ uživatele.
4. Vpravo označte všechny uživatele a klikněte na **Importovat**. Mělo by se zobrazit vyskakovací okno s informací, že akce proběhla úspěšně. Zavřete okno importu.

   ![Import uživatele MFA serveru ze služby Active Directory](./media/howto-mfaserver-deploy/import2.png)

### <a name="automated-synchronization-with-active-directory"></a>Automatizovaná synchronizace se službou Active Directory

1. V Azure MFA Serveru klikněte na levé straně na **Integrace adresáře**.
2. Přejděte na kartu **Synchronizace**.
3. V dolní části zvolte **Přidat**.
4. V zobrazeném okně **Přidat položku synchronizace** zvolte pro tuto úlohu synchronizace doménu, organizační jednotku **nebo** skupinu zabezpečení, nastavení, výchozí hodnoty metod a výchozí hodnoty jazyka a klikněte na **Přidat**.
5. Zaškrtněte políčko s popiskem **Povolit synchronizaci se službou Active Directory** a zvolte **Interval synchronizace** mezi jednou minutou a 24 hodinami.

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Jak Azure Multi-Factor Authentication Server nakládá s uživatelskými daty

Když použijete místní server Multi-Factor Authentication (MFA), budou se data uživatele ukládat na místní servery. V cloudu se neukládají žádná trvalá data. Když uživatel provádí dvoustupňové ověření, MFA Server odešle data do cloudové služby Azure MFA, a tam se provede ověření. Když se tyto požadavky na ověření pošlou do cloudové služby, odešlou se v žádosti a následující pole a protokoly, aby byly dostupné pro sestavy o používání/ověřování zákazníka. Některá tato pole jsou volitelná a můžou se v Multi-Factor Authentication Serveru zapnout nebo vypnout. Komunikace z MFA Serveru do cloudové služby MFA probíhá přes SSL/TLS na odchozím portu 443. Tato pole jsou:

* Jedinečné ID - uživatelské jméno nebo interní ID serveru MFA
* Jméno a příjmení (volitelné)
* E-mailová adresa (volitelné)
* Telefonní číslo – při ověření přes telefonní hovor nebo zprávu SMS
* Token zařízení - při ověření přes mobilní aplikaci
* Režim ověřování
* Výsledek ověřování
* Název MFA Serveru
* IP adresa serveru MFA
* IP adresa klienta – pokud je dostupná

Vedle těchto polí se s ověřovacími údaji uloží taky výsledek ověření (úspěch/zamítnuto) a případně důvod zamítnutí, které jsou dostupné v sestavách o ověřování/používání.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům MFA serveru v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Telefonní hovor bude dál k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv jenom na bezplatné nebo zkušební klienty Azure AD.

## <a name="back-up-and-restore-azure-mfa-server"></a>Zálohování a obnovení Azure MFA Serveru

Zajištění dobrého zálohování je důležitý krok, který byste měli provést u každého systému.

Pokud chcete zálohovat Azure MFA Server, ujistěte se, že máte kopii složky **C:\Program Files\Multi-Factor Authentication Server\Data** včetně souboru **PhoneFactor.pfdata**. 

Pokud potřebujete provést obnovení, proveďte následující kroky:

1. Přeinstalujte Azure MFA Server na nový server.
2. Aktivujte nový Azure MFA Server.
3. Zastavte službu **MultiFactorAuth**.
4. Přepište soubor **PhoneFactor.pfdata** zálohovanou kopií.
5. Spusťte službu **MultiFactorAuth**.

Nový server je teď zprovozněný s původní zálohovanou konfigurací a uživatelskými daty.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Správa protokolů TLS a SSL a šifrovacích sad

Po upgradu na MFA Server verze 8.x nebo novější nebo jeho instalaci se doporučuje zakázat nebo odebrat starší a slabší šifrovací sady, pokud je vaše organizace nevyžaduje. Informace o tom, jak to provést, najdete v článku [Správa protokolů SSL a TLS a šifrovacích sad pro AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs).

## <a name="next-steps"></a>Další kroky

- Nastavení a konfigurace portálu [User Portal](howto-mfaserver-deploy-userportal.md) pro uživatelskou samoobsluhu.
- Nastavení a konfigurace Azure MFA Serveru pomocí [služby Active Directory Federation Service](multi-factor-authentication-get-started-adfs.md), [ověřování pomocí protokolu RADIUS](howto-mfaserver-dir-radius.md) nebo [ověřování pomocí protokolu LDAP](howto-mfaserver-dir-ldap.md).
- Nastavení a konfigurace [Brány vzdálené plochy a Azure Multi-Factor Authentication Serveru používajícího protokol RADIUS](howto-mfaserver-nps-rdg.md).
- [Nasaďte službu Azure Multi-Factor Authentication Server Mobile App Web Service](howto-mfaserver-deploy-mobileapp.md).
- [Pokročilé scénáře se službou Azure Multi-Factor Authentication a sítěmi VPN třetích stran](howto-mfaserver-nps-vpn.md).