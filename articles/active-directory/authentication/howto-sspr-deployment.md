---
title: Samoobslužné resetování hesla plánu nasazení – Azure Active Directory
description: Resetovat strategie pro úspěšnou implementaci hesla pomocí samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440951"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Nasazení Azure AD samoobslužné resetování hesla

Samoobslužné resetování hesla (SSPR) je funkce Azure Active Directory, která umožňuje uživatelům resetovat svá hesla bez nutnosti kontaktovat pracovníky. Zaměstnanci musí zaregistrovat nebo zaregistrovat pro resetování před použitím služby hesla pomocí samoobslužné služby. Během registrace zaměstnanec zvolí jednu nebo více metod ověřování ve své organizaci povolené.

Samoobslužné resetování HESLA umožňující uživatelům rychle získat odblokuje a pokračovat v práci bez ohledu na to, kde jsou, nebo čas. Tím, že uživatele odblokujete sami, můžete snížit vaše organizace neproduktivní čas a náklady na vysokou podporu pro nejběžnější problémy související s hesly.

Pomoc uživatelům zaregistrovat rychlé nasazení samoobslužného resetování HESLA společně s jiné aplikace nebo služby ve vaší organizaci. Tato akce bude generovat značný přihlášení a registraci se bude řídit.

Před nasazením SSPR, organizace chtít určit, kolik resetování hesla související volání služby HelpDesk stane průběhu času a průměrné náklady na volání. Použitím této po nasazení dat. aby se zobrazila hodnota, že přináší samoobslužné resetování HESLA pro vaši organizaci.  

## <a name="how-sspr-works"></a>Fungování samoobslužného resetování HESLA

1. Když se uživatel pokusí o resetování hesla, musíte ověřit v jejich dříve zaregistrovaný metodu nebo metody ověřování k prokázání své identity.
1. Poté uživatel zadá nové heslo.
   1. Pro uživatele jenom pro cloud nové heslo je uloženo v Azure Active Directory. Další informace najdete v článku [jak samoobslužné resetování HESLA funguje](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Pro uživatele, hybridní je zpětný zápis hesla do místní služby Active Directory prostřednictvím služby Azure AD Connect. Další informace najdete v článku [co je zpětný zápis hesla](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Důležité informace o licencování

Azure Active Directory je význam uživatelských licencí všichni uživatelé musí mít příslušnou licenci pro funkce, které se, jak využívají.

- Samoobslužné resetování hesla pro cloudové uživatele je k dispozici s Azure AD Basic nebo vyšší.
- Samoobslužné resetování hesla se zpětným zápisem místní pro hybridní prostředí vyžaduje Azure AD Premium P1 nebo vyšší.

Další informace o licencování najdete na [Azure Active Directory, na stránce s cenami](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Povolení kombinovaná registrace pro samoobslužné resetování HESLA a vícefaktorové ověřování

Společnost Microsoft doporučuje, aby organizace povolit prostředí kombinovaná registrace pro samoobslužné resetování HESLA a vícefaktorové ověřování. Pokud povolíte toto kombinované registraci prostředí, uživatelé pouze vybráním své informace o registraci jednou obě funkce.

![Informace o registraci kombinované zabezpečení](./media/howto-sspr-deployment/combined-security-info.png)

Kombinovaná registrace prostředí nevyžaduje, aby organizacím, které chcete povolit samoobslužné resetování HESLA a ověřování Azure Multi-Factor Authentication použít. Kombinovaná registrace prostředí poskytuje organizacím lepší uživatelské prostředí v porovnání s tradičním jednotlivé komponenty. Další informace o kombinované registrace a jak povolit, najdete v článku [kombinovat informace o registraci zabezpečení (preview)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Plánování konfigurace

Následující nastavení jsou nutné k povolení samoobslužného resetování HESLA spolu se doporučené hodnoty.

| Oblast | Nastavení | Hodnota |
| --- | --- | --- |
| **Vlastnosti samoobslužné resetování HESLA** | Samoobslužné resetování hesla povoleno | **Vybraná** pro pilotní nasazení / **všechny** pro produkční prostředí |
| **Metody ověřování** | Metody ověřování požadovaných k registraci | Vždy 1 více, než se požaduje pro obnovení |
|   | Metody ověřování požadovaných k resetování | Jeden nebo dva |
| **Registrace** | Při přihlášení vyžadovat registraci uživatelů | Ano |
|   | Počet dní, než se uživatelům zobrazí výzva k potvrzení jejich ověřovacích informací | 90 – 180 dnů |
| **Oznámení** | Upozornit uživatele na resetování hesla | Ano |
|   | Upozornit všechny správce na resetování hesla jiného správce | Ano |
| **Vlastní nastavení** | Přizpůsobit odkaz na helpdesk | Ano |
|   | E-mailu vlastního helpdesku nebo adresy URL | Podpora lokality nebo e-mailovou adresu |
| **Místní integrace** | Zapisovat hesla zpět do místní AD | Ano |
|   | Povolit uživatelům odemčení účtů bez resetování hesla | Ano |

### <a name="sspr-properties-recommendations"></a>Doporučení vlastnosti samoobslužné resetování HESLA

Při povolení samoobslužného resetování hesla, vyberte skupinu zabezpečení se použije během pilotního programu.

Když budete chtít spustit službu širší, doporučujeme použít možnost vše k vynucení samoobslužné resetování HESLA pro všechny v organizaci. Pokud nelze nastavit pro všechny skupiny zabezpečení vyberte příslušné služby Azure AD nebo AD synchronizované do Azure AD.

### <a name="authentication-methods"></a>Metody ověřování

Nastavení ověřování metod požadovaných k registraci do alespoň jedné větší než počet požadovaných k resetování. Umožňuje více uživatelům umožňuje flexibilitu, kdy potřebují resetovat.

Nastavte **počet metod požadovaných k resetování** na úrovni vhodné pro vaši organizaci. Jeden vyžaduje nejméně případná problémová místa, ale dvě může zvýšit tak stav zabezpečení.

Zobrazit [jaké metody ověřování jsou](concept-authentication-methods.md) podrobné informace o ověřování metody jsou k dispozici pro samoobslužné resetování HESLA, předdefinované bezpečnostní otázky a jak vytvořit vlastní bezpečnostní otázky.

### <a name="registration-settings"></a>Nastavení registrace

Nastavte **vyžadovat od uživatelů registraci při přihlašování** k **Ano**. Toto nastavení znamená, že uživatelé budou ukončeny registraci při přihlašování, zajištění, že všichni uživatelé jsou chráněné.

Nastavte **počet dní, než se uživatelům zobrazí výzva k potvrzení jejich ověřovacích informací** k mezi **90** a **180** dnů, pokud má vaše organizace obchodní potřeby pro kratší časový rámec.

### <a name="notifications-settings"></a>Nastavení oznámení

Konfigurovat i **upozornit uživatele na resetování hesla** a **upozornit všechny správce na resetování hesla jiného správce** k **Ano**. Výběr **Ano** u obou zvyšuje zabezpečení tím, že zajišťuje, že uživatelé budou vědět při jejich heslo bylo resetováno. proto, že všichni Administrátoři jsou seznámení s Pokud správce změní heslo. Pokud uživatelé nebo správci přijímat taková oznámení a inicializuje se ještě změny, se okamžitě ohlásit na potenciální porušení zabezpečení.

### <a name="customization"></a>Přizpůsobení

Je velmi důležité pro přizpůsobení **technické podpory e-mailu nebo adresa URL** zajistit uživatelům, kteří docházet k potížím, můžete rychle získat nápovědu. Tuto možnost nastavte na společné technické podpory e-mailovou adresu nebo webové stránky, která vaši uživatelé znají.

### <a name="on-premises-integration"></a>Místní integrace

Pokud máte hybridní prostředí, ujistěte se, že **zapisovat hesla zpět do místní AD** je nastavena na **Ano**. Nastavte také povolit uživatelům odemčení účtu bez resetování hesla na hodnotu Ano, jak je to poskytuje větší flexibilitu.

### <a name="changingresetting-passwords-of-administrators"></a>Změna/resetování hesel správců

Účty správců jsou zvláštní účty se zvýšenými oprávněními. Pokud je Pokud chcete zabezpečit, platí následující omezení změna hesel správců:

- Enterprise administrators v místním nebo domain administrators, nelze resetování hesla prostřednictvím samoobslužného resetování HESLA. Lze změnit pouze své heslo ve svém místním prostředí. Proto doporučujeme není synchronizovat místní účty správce služby AD do služby Azure AD.
- Správce nelze použít tajných kódů otázky a odpovědi jako metoda pro resetování hesla.

### <a name="environments-with-multiple-identity-management-systems"></a>Prostředí s více systémů pro správu identit

Pokud existuje více identit systémy pro správu v rámci prostředí, jako jsou místní správci identity jako je Oracle AM, SiteMinder, nebo jiných systémů, a potom hesla zapsána do služby Active Directory může být nutné synchronizovat do jiných systémů pomocí nástroje, jako je Password Change Notification Service (služba PCNS) pomocí Microsoft Identity Manageru (MIM). Pokud chcete najít informace o tomto složitější scénář, najdete v článku [nasazení MIM Password Change Notification Service na řadiči domény](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Plánování nasazení a podporu pro samoobslužné resetování HESLA

### <a name="engage-the-right-stakeholders"></a>Zapojení zúčastněných vpravo

Když dojde k selhání technologie projekty, jsou obvykle to provést z důvodu neodpovídající požadavkům na dopad, výsledky a odpovědnosti. Chcete-li zabránit těmto nástrahy, zajistěte, aby jsou zapojení správné zúčastněných a, účastníka role v projektu jsou dobře pochopitelné Dokumentováním zúčastněné strany a jejich vstupu projektu a zodpovědnost.

### <a name="communications-plan"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikaci s vašimi uživateli, jak používat službu a co mohou udělat pro získání nápovědy, pokud něco nebude fungovat podle očekávání. Zkontrolujte [samoobslužné resetování hesla uvedení materiálů na webu Microsoft download center](https://www.microsoft.com/download/details.aspx?id=56768) nápady týkající se plánování strategie komunikaci s koncovým uživatelem.

### <a name="testing-plan"></a>Plán testování

Pokud chcete mít jistotu, že nasazení funguje podle očekávání, měli byste si sadu testovacích případů, které se použijí k ověření implementace. Následující tabulka obsahuje některé užitečné testovací scénáře, které můžete použít k dokumentu očekávané výsledky podle zásad vaší organizace.

| Obchodní zdůvodnění | Očekávaný výsledek |
| --- | --- |
| Samoobslužné resetování HESLA portál je přístupný z v rámci podnikové sítě | Určit vaší organizací |
| Portál pro samoobslužného resetování HESLA je dostupné z oblasti mimo podnikovou síť | Určit vaší organizací |
| Resetovat heslo uživatele z prohlížeče, když uživatel není povolen pro resetování hesla | Uživatel není přístup k procesu resetování hesla |
| Resetovat heslo uživatele z prohlížeče, pokud uživatel nemá zaregistrované pro resetování hesla | Uživatel není přístup k procesu resetování hesla |
| Přihlásí uživatele při registraci pro resetování hesla se nevynutí. | Uživatel je vyzván k registraci informací o zabezpečení |
| Přihlásí uživatele při registraci pro resetování hesla dokončil | Uživatel není vyzván k registraci informací o zabezpečení |
| Samoobslužné resetování HESLA portál je přístupný, když uživatel nemá licenci | Je dostupný |
| Resetovat heslo uživatele ze systému Windows 10 AADJ nebo H + AADJ zamykací obrazovky zařízení, jakmile uživatel zaregistroval | Uživatel můžete resetovat heslo |
| Data o využití a registrace samoobslužného resetování HESLA jsou k dispozici správcům téměř v reálném čase | Je k dispozici prostřednictvím protokolů auditu |

### <a name="support-plan"></a>Plán podpory

Během samoobslužného resetování HESLA nevytváří obvykle problémy uživatelů, je důležité mít podporu, kterou zaměstnanci připravili řešit problémy, které mohou nastat.

Když správce můžete změnit nebo resetování hesla pro koncové uživatele na portálu Azure AD, je lepší řešení těchto potíží prostřednictvím procesu samoobslužné podpory.

V provozní příručce části tohoto dokumentu vytvoření seznamu případy podpory a jejich pravděpodobné příčiny a vytvořit průvodce pro řešení.

### <a name="auditing-and-reporting"></a>Auditování a vytváření sestav

Po nasazení, řada organizací chtít vědět, jak nebo pokud samoobslužné resetování hesla (SSPR) je doopravdy používá. Funkci vytváření sestav, které poskytuje Azure Active Directory (Azure AD) pomáhá lze zodpovědět otázky pomocí předem připravených sestav.

Protokoly auditu pro registraci a obnovení hesla jsou k dispozici po dobu 30 dnů. Proto pokud auditování zabezpečení v rámci podniku vyžaduje delší dobu uchování, protokoly nutné exportovat a shrnout do nástroje SIEM, jako [Azure Sentinelu](../../sentinel/connect-azure-active-directory.md), Splunk nebo ArcSight.

V tabulce, jako je ten níže zdokumentujte plán zálohování, systém a odpovědnost smluvní strany souhlasí. Možná nebudete potřebovat samostatné auditování a vytváření sestav zálohování, ale měli byste mít samostatnou zálohu, ze kterého můžete obnovit z chyby.

|   | Četnost stahování | Cílový systém | Odpovídá stran |
| --- | --- | --- | --- |
| Auditování zálohování |   |   |   |
| Generování sestav zálohování |   |   |   |
| Zálohu pro obnovení po havárii |   |   |   |

## <a name="implementation"></a>Implementace

Implementace se skládá ze tří fází:

- Konfigurovat uživatele a licence
- Konfigurace samoobslužného resetování HESLA Azure AD pro registraci a samoobslužné funkce
- Nakonfigurovat službu Azure AD Connect pro zpětný zápis hesla

### <a name="communicate-the-change"></a>Sdělte změnu

Proces implementace komunikační plán, který jste vytvořili ve fázi plánování.

### <a name="ensure-groups-are-created-and-populated"></a>Zkontrolujte skupiny jsou vytvořeny a naplněny

Odkazovat části Plánování ověřování hesla metody a zkontrolujte tyto skupiny uplatněna pro implementaci zkušebního projektu nebo produkční prostředí jsou k dispozici, a všichni odpovídající uživatelé se přidají do skupin.

### <a name="apply-licenses"></a>Licence

Skupiny, kterou budete implementovat musíte mít Azure AD premium přiřazenou licenci. Licence můžete přiřadit přímo do skupiny nebo můžete použít existující zásady licence, jako pomocí Powershellu nebo na základě skupin licencí.

Informace o přiřazování licencí skupinám uživatelů najdete v článku, [přiřazení licencí pro uživatele na základě členství ve skupinách v Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurace samoobslužného resetování HESLA

#### <a name="enable-groups-for-sspr"></a>Povolit skupiny pro samoobslužné resetování HESLA

1. Přístup k webu Azure portal pomocí účtu správce.
1. Vyberte všechny služby a v dialogovém okně Filtr zadejte Azure Active Directory a potom vyberte Azure Active Directory.
1. V okně služby Active Directory vyberte resetování hesla.
1. V podokně vlastností vyberte vybrané. Pokud chcete, aby všem uživatelům povolit, vyberte všechny.
1. Výchozí heslo resetovat okno zásad, zadejte název skupiny pro první, vyberte ho a pak klikněte na vybrat v dolní části obrazovky a vyberte Uložit v horní části obrazovky.
1. Tento postup opakujte pro každou skupinu.

#### <a name="configure-the-authentication-methods"></a>Konfigurovat metody ověřování

Odkazovat na plánování, z části Plánování metody ověřování hesla tohoto dokumentu.

1. Vyberte registraci, v části vyžadují uživateli při přihlášení registraci, vyberte Ano a pak nastavte počet dní před vypršením platnosti a pak vyberte možnost uložit.
1. Vyberte oznámení a konfigurace na základě vašeho plánu a vyberte Uložit.
1. Vyberte vlastní nastavení a konfigurace na základě vašeho plánu a vyberte Uložit.
1. Vyberte místní integraci a konfigurace na základě vašeho plánu a vyberte Uložit.

### <a name="enable-sspr-in-windows"></a>Povolit samoobslužné resetování HESLA ve Windows

Zařízení s Windows 10 běží verze 1803 nebo novější, které jsou buď připojené k Azure AD nebo hybridní připojená k Azure AD můžou resetovat svá hesla na přihlašovací obrazovce Windows. Informace a postup pro konfiguraci této funkce najdete v článku [z přihlašovací obrazovky resetování hesla Azure AD](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Nakonfigurovat zpětný zápis hesla

Postup konfigurace zpětného zápisu hesla pro vaši organizaci najdete v článku [s návody: Nakonfigurovat zpětný zápis hesla](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Správa samoobslužného resetování HESLA

Požadované role pro správu funkce přidružené k samoobslužné resetování hesla.

| Obchodní roli a modelové osoby | Role Azure AD (v případě potřeby) |
| :---: | :---: |
| Technické podpory úrovně 1 | Správce hesel |
| Technickou podporu úrovně 2 | Správce uživatelů |
| Samoobslužné resetování HESLA správce | Globální správce |

### <a name="support-scenarios"></a>Scénáře podpory

Pokud chcete povolit váš úspěch týmu podpory, můžete vytvořit nejčastější dotazy na základě otázek, který jste dostali od uživatelů. Následující tabulka obsahuje běžné scénáře podpory.

| Scénáře | Popis |
| --- | --- |
| Uživatel nemá žádné dostupné metody registrované ověřování | Uživatel se snaží obnovit své heslo, ale nemá žádné metody ověřování, že zaregistrovaný k dispozici (Příklad: během chvilky vlevo jejich mobilní telefon a nemá přístup k e-mailu) |
| Uživatel není přijetí textového nebo volání na jejich office nebo na mobilní telefon | Uživatel se pokusil ověřit svoji identitu prostřednictvím textová zpráva nebo volání, ale text/volání, nadále nepřijímá. |
| Uživatel nemá přístup k portálu pro resetování hesel | Uživatel chce, aby se resetování hesla, ale není povolen pro resetování hesla a proto nemůže přistupovat k stránky a aktualizovat hesla. |
| Uživatel nemůže nastavit nové heslo | Uživatel se dokončí ověření během procesu resetování hesla, ale nelze nastavit nové heslo. |
| Uživatel neuvidí odkaz resetovat heslo na zařízení s Windows 10 | Uživatel se pokouší o resetování hesla ze zamykací obrazovky Windows 10, ale zařízení buď není připojená k Azure AD nebo není aktivované zásady zařízení Intune |

Můžete také obsahovat informace, jako je následující další řešení potíží.

- Skupiny, které jsou povolené pro samoobslužné resetování HESLA.
- Které metody ověřování jsou nakonfigurované.
- Zásady přístupu týkající se nebo podnikové sítě.
- Řešení potíží pro běžné scénáře.

Můžete také odkazovat na naší online dokumentaci k řešení problémů samoobslužného resetování hesla a pochopit postup řešení potíží pro nejběžnější scénáře samoobslužné resetování HESLA.

## <a name="next-steps"></a>Další postup

- [Zvažte implementaci ochrana hesel Azure AD](concept-password-ban-bad.md)

- [Zvažte implementaci Azure AD inteligentní uzamčení](howto-password-smart-lockout.md)
