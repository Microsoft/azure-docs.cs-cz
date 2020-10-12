---
title: Místní zpětný zápis hesla pomocí samoobslužného resetování hesla – Azure Active Directory
description: Přečtěte si, jak můžou být události změny nebo resetování hesla v Azure Active Directory zapsané zpátky do místního adresářového prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 690dead3cb0059dd1b20ff042a93c36d674e62d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90052677"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Jak funguje zpětný zápis hesla samoobslužné služby pro resetování hesla v Azure Active Directory?

Samoobslužné resetování hesla (SSPR) pro Azure Active Directory (Azure AD) umožňuje uživatelům resetovat hesla v cloudu, ale většina společností má i místní prostředí Active Directory Domain Services (služba AD DS), ve kterém jejich uživatelé existují. Zpětný zápis hesla je funkce povolená u [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , která umožňuje v reálném čase zapisovat změny hesel v cloudu zpátky do stávajícího místního adresáře. Když uživatelé v této konfiguraci mění nebo resetují hesla pomocí SSPR v cloudu, aktualizovaná hesla se taky zapisují zpátky do místního služba AD DS prostředí.

> [!IMPORTANT]
> Tento koncepční článek vysvětluje správce, jak funguje zpětný zápis hesla samoobslužné služby pro resetování hesla. Pokud už jste koncoví uživatelé zaregistrovali pro Samoobslužné resetování hesla a potřebujete se zpátky do svého účtu, pokračujte na https://aka.ms/sspr .
>
> Pokud váš IT tým nepovolil možnost resetovat si vlastní heslo, obraťte se na helpdesk a získáte další pomoc.

Zpětný zápis hesla se podporuje v prostředích, která používají následující modely hybridních identit:

* [Synchronizace hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Předávací ověřování](../hybrid/how-to-connect-pta.md)
* [Služba Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)

Zpětný zápis hesla poskytuje následující funkce:

* **Vynucování zásad pro hesla místních Active Directory Domain Services (služba AD DS)**: když uživatel resetuje heslo, před jeho potvrzením do tohoto adresáře se ověří, jestli splňuje vaše místní služba AD DS zásady. Tato revize zahrnuje kontrolu historie, složitosti, stáří, filtrů hesel a dalších omezení hesla, která definujete v služba AD DS.
* **Nulování zpětná vazba**: zpětný zápis hesla je synchronní operace. Uživatelé se okamžitě oznámí, pokud heslo nesplňuje zásady nebo se z nějakého důvodu nedá resetovat ani změnit.
* **Podporuje změny hesla z přístupového panelu a Microsoft 365**: Pokud se pro uživatele synchronizovaných hodnot hash nebo hesla, jejichž platnost vypršela, změnila hesla, která vypršela nebo jsou neprošlá, tato hesla se napíší zpátky do služba AD DS
* **Podporuje zpětný zápis hesla, když ho správce resetuje z Azure Portal**: Když správce resetuje heslo uživatele v [Azure Portal](https://portal.azure.com), pokud je tento uživatel federovaný nebo je hodnota hash hesla synchronizovaná, heslo se zapíše zpátky do místního prostředí. Tato funkce se v současnosti nepodporuje na portálu pro správu Office.
* **Nevyžaduje žádná pravidla brány firewall pro příchozí připojení**: zpětný zápis hesla používá jako základní komunikační kanál Azure Service Bus Relay. Veškerá komunikace je odchozí přes port 443.

> [!NOTE]
> Účty správců, které existují v chráněných skupinách v místní službě AD, se dají používat se zpětným zápisem hesla. Správci můžou změnit heslo v cloudu, ale nemůžou pomocí resetování hesla resetovat zapomenuté heslo. Další informace o chráněných skupinách najdete [v tématu chráněné účty a skupiny v služba AD DS](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Pokud chcete začít se zpětným zápisem SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení zpětného zápisu pro Samoobslužné resetování hesla (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Jak funguje zpětný zápis hesla

Při pokusu o resetování nebo změně hesla v cloudu se může zobrazit následující akce:

1. Provede se ověření, jaký typ hesla uživatel má. Pokud je heslo spravované místně:
   * Provede se ověření, zda je služba zpětného zápisu v provozu. V takovém případě může uživatel pokračovat.
   * Pokud je služba zpětného zápisu vypnutá, uživateli se dozvíte, že heslo nejde resetovat hned teď.
1. V dalším kroku uživatel předává příslušné ověřovací brány a dosáhne stránky pro **resetování hesla** .
1. Uživatel vybere nové heslo a potvrdí ho.
1. Když uživatel vybere **Odeslat**, heslo ve formátu prostého textu se zašifruje pomocí symetrického klíče vytvořeného během procesu nastavení zpětného zápisu.
1. Šifrované heslo je zahrnuté v datové části, která se posílá přes kanál HTTPS k přenosu Service Bus konkrétního tenanta (to je nastavené během procesu nastavení zpětného zápisu). Tento přenos je chráněný náhodně generovaným heslem, které ví jenom vaše místní instalace.
1. Jakmile zpráva dosáhne služby Service Bus, koncový bod pro resetování hesla se automaticky probudí a zjistí, že se čeká na vyřízení žádosti o resetování.
1. Služba pak vyhledá uživatele pomocí atributu kotva cloudu. Aby bylo toto vyhledávání úspěšné, musí být splněny následující podmínky:

   * Objekt uživatele musí existovat v prostoru konektoru služba AD DS.
   * Objekt uživatele musí být propojený s odpovídajícím objektem úložiště metaverse (MV).
   * Objekt uživatele musí být propojený s odpovídajícím objektem konektoru služby Azure AD.
   * Odkaz z objektu služba AD DSového konektoru na MV musí mít pravidlo synchronizace `Microsoft.InfromADUserAccountEnabled.xxx` na tomto odkazu.

   Když se volání doplní z cloudu, modul synchronizace používá atribut **cloudAnchor** k vyhledání objektu prostoru konektoru služby Azure AD. Pak následuje odkaz zpátky na objekt MV a potom následuje odkaz zpátky na objekt služba AD DS. Vzhledem k tomu, že může existovat více služba AD DS objektů (více doménových struktur) pro stejného uživatele, synchronizační modul spoléhá na `Microsoft.InfromADUserAccountEnabled.xxx` odkaz pro výběr správné.

1. Po nalezení uživatelského účtu se provede pokus o resetování hesla přímo v příslušné služba AD DS doménové struktuře.
1. Je-li operace set hesla úspěšná, uživatel má informaci, že heslo bylo změněno.

   > [!NOTE]
   > Pokud je hodnota hash hesla uživatele synchronizovaná s Azure AD pomocí synchronizace hodnot hash hesel, existuje možnost, že místní zásady hesel jsou slabé, než zásady pro heslo pro Cloud. V takovém případě se zásady místních zásad vynutily. Tato zásada zajišťuje, že vaše místní zásady se vynutily v cloudu, bez ohledu na to, jestli k zajištění jednotného přihlašování použijete synchronizaci hodnot hash hesel nebo federaci.

1. Pokud operace nastavení hesla neproběhne úspěšně, vyzve uživatele k zadání chyby. Operace může selhat z následujících důvodů:
    * Služba byla mimo provoz.
    * Heslo, které vybralo, nesplňuje zásady organizace.
    * Nepovedlo se najít uživatele v místním služba AD DSovém prostředí.

   Chybové zprávy poskytují uživatelům pokyny, aby se mohli pokusit o vyřešení bez zásahu správce.

## <a name="password-writeback-security"></a>Zabezpečení zpětného zápisu hesla

Zpětný zápis hesla je vysoce Zabezpečená služba. Aby bylo zajištěno, že jsou vaše informace chráněny, je povolený model zabezpečení se čtyřmi vrstvami:

* **Služba pro předávání přes konkrétního tenanta**
   * Při nastavování služby se nastaví předávání Service Bus pro konkrétního tenanta, které je chráněné náhodně generovaným silným heslem, ke kterému má přístup Microsoft nikdy.
* **Uzamčená, kryptograficky silný šifrovací klíč hesla**
   * Po vytvoření služby Service Bus Relay se vytvoří silný symetrický klíč that'is, který slouží k šifrování hesla, když se nachází na lince. Tento klíč se používá jenom v tajném úložišti vaší společnosti v cloudu, které je silně uzamčené a auditované, stejně jako jakékoli jiné heslo v adresáři.
* **Standardní obor TLS (Transport Layer Security)**
   1. V případě, že dojde k resetování hesla nebo k operaci změny v cloudu, heslo ve formátu prostého textu se zašifruje pomocí veřejného klíče.
   1. Šifrované heslo se umístí do zprávy HTTPS, která se pošle přes zašifrovaný kanál pomocí certifikátů Microsoft TLS/SSL k předávání Service Bus.
   1. Po doručení zprávy ve službě Service Bus se Váš místní agent probudí a ověří ve službě Service Bus pomocí silného hesla, které bylo dříve vygenerováno.
   1. Místní agent převezme šifrovanou zprávu a dešifruje ji pomocí privátního klíče.
   1. Místní Agent se pokusí nastavit heslo prostřednictvím rozhraní služba AD DS SetPassword API. Tento krok umožňuje vynucení služba AD DS místních zásad hesel (například složitost, stáří, historie a filtry) v cloudu.
* **Zásady pro vypršení platnosti zprávy**
   * Pokud se zpráva nachází v Service Bus, protože vaše místní služba nefunguje, vyprší časový limit a po několika minutách se odeberou. Časový limit a odebrání zprávy zvyšují zabezpečení ještě více.

### <a name="password-writeback-encryption-details"></a>Podrobnosti šifrování zpětného zápisu hesla

Jakmile uživatel odešle resetování hesla, požadavek na obnovení projde několika kroky šifrování, než dorazí do místního prostředí. Tyto kroky šifrování zajišťují maximální spolehlivost a zabezpečení služby. Jsou popsány takto:

1. **Šifrování hesla pomocí 2048 klíče RSA**: když uživatel odešle heslo pro zápis zpátky do místního počítače, vlastní odeslané heslo se zašifruje pomocí 2048 klíče RSA.
1. **Šifrování na úrovni balíčku pomocí AES-GCM**: celý balíček, heslo plus požadovaná metadata se šifrují pomocí AES-GCM. Toto šifrování zabrání komukoli s přímým přístupem k základnímu Service Busmu kanálu v zobrazení nebo manipulaci s obsahem.
1. **Veškerá komunikace probíhá přes protokol TLS/SSL**: veškerá komunikace s Service Bus se odehrává v kanálu SSL/TLS. Toto šifrování zabezpečuje obsah od neautorizovaných třetích stran.
1. **Automatická výměna klíčů každých šest měsíců**: všechny klíče převezmou každých šest měsíců nebo se pokaždé, když se zpětný zápis hesla zakáže, a pak znovu povolí Azure AD Connect, aby se zajistilo maximum zabezpečení a zabezpečení služby.

### <a name="password-writeback-bandwidth-usage"></a>Využití šířky pásma zpětného zápisu hesla

Zpětný zápis hesla je služba s nízkou šířkou pásma, která odesílá požadavky pouze zpět do místního agenta za následujících okolností:

* Pokud je funkce povolená nebo zakázaná prostřednictvím Azure AD Connect, odesílají se dvě zprávy.
* Jedna zpráva se pošle každých pět minut jako prezenční signál služby, pokud je služba spuštěná.
* Při každém odeslání nového hesla se odesílají dvě zprávy:
   * První zpráva je žádost o provedení operace.
   * Druhá zpráva obsahuje výsledek operace a je odeslána v následujících případech:
      * Pokaždé, když se během samoobslužného resetování hesla uživatele odešle nové heslo.
      * Pokaždé, když se během operace změny hesla uživatele odešle nové heslo.
      * Pokaždé, když se nové heslo odešle během resetování hesla uživatele iniciované správcem (jenom z portálů pro správu Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Požadavky na velikost zprávy a šířku pásma

Velikost každé zprávy popsané výše je obvykle v rozsahu 1 KB. I v případě extrémního zatížení služba zpětného zápisu hesla sama o sobě spotřebovává několik kilobitů za sekundu šířky pásma. Vzhledem k tomu, že se každá zpráva odesílá v reálném čase, a to jenom v případě, že to vyžaduje operace aktualizace hesla, a protože je velikost zprávy malá, je využití šířky pásma pro funkci zpětného zápisu moc malé, aby nemohlo měřitelné dopady.

## <a name="supported-writeback-operations"></a>Podporované operace zpětného zápisu

Hesla se zapisují zpátky do všech těchto situací:

* **Podporované operace koncového uživatele**
   * Všechny operace samoobslužné změny hesla koncového uživatele.
   * Všechny operace samoobslužné změny hesla koncových uživatelů, například vypršení platnosti hesla.
   * Jakékoli Samoobslužné resetování hesla koncového uživatele, které pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com).

* **Podporované operace Správce**
   * Jakékoli samoobslužné operace hesla pro samoobslužné změny pro správce
   * Jakákoli operace změny hesla samoobslužného hesla pro správce, například vypršení platnosti hesla.
   * Jakékoli Samoobslužné resetování hesla, které pochází z portálu pro [resetování hesla](https://passwordreset.microsoftonline.com).
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z [Azure Portal](https://portal.azure.com).
   * Všichni správci iniciovali resetování hesla koncových uživatelů z [rozhraní Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Nepodporované operace zpětného zápisu

Hesla se nezapisují zpátky v následujících situacích:

* **Nepodporované operace koncového uživatele**
   * Libovolný koncový uživatel resetuje vlastní heslo pomocí prostředí PowerShell verze 1, verze 2 nebo rozhraní Microsoft Graph API.
* **Nepodporované operace Správce**
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z PowerShellu verze 1, verze 2 nebo rozhraní Microsoft Graph API (podporuje se [Microsoft Graph API beta](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) ).
   * Jakékoli resetování hesla koncového uživatele iniciované správcem v [centru pro správu Microsoft 365](https://admin.microsoft.com).
   * Žádný správce nemůže použít nástroj pro resetování hesla k resetování vlastního hesla pro zpětný zápis hesla.

> [!WARNING]
> Pomocí zaškrtávacího políčka "uživatel musí změnit heslo při příštím přihlášení" v místních služba AD DS nástroje pro správu, jako jsou uživatelé a počítače služby Active Directory, nebo Centrum správy služby Active Directory se podporuje jako funkce verze Preview Azure AD Connect. Další informace najdete v tématu [implementace synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít se zpětným zápisem SSPR, dokončete následující kurz:

> [!div class="nextstepaction"]
> [Kurz: povolení zpětného zápisu pro Samoobslužné resetování hesla (SSPR)](./tutorial-enable-sspr-writeback.md)