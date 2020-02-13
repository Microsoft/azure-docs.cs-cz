---
title: Integrace místního zpětného zápisu hesel pomocí Azure AD SSPR-Azure Active Directory
description: Získat cloudová hesla, která se napíší zpátky do místní infrastruktury AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c92048d2fce4a098da1e707ec8f7d75479d563f1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161575"
---
# <a name="what-is-password-writeback"></a>Co je zpětný zápis hesla?

Použití nástroje pro resetování hesla na Cloud je skvělé, ale většina společností má stále místní adresář, ve kterém jejich uživatelé existují. Jak Microsoft podporuje udržování tradiční místní služby Active Directory (AD) v synchronizaci se změnami hesel v cloudu? Zpětný zápis hesla je funkce povolená u [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , která umožňuje v reálném čase zapisovat změny hesel v cloudu zpátky do stávajícího místního adresáře.

Zpětný zápis hesla se podporuje v prostředích, která používají:

* [Active Directory Federation Services (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Synchronizace hodnoty hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Předávací ověřování](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zpětný zápis hesla přestane fungovat pro zákazníky, kteří používají Azure AD Connect verze 1.0.8641.0 a starší, pokud [je služba Azure Access Control Service (ACS) vyřazena 7. listopadu 2018](../azuread-dev/active-directory-acs-migration.md). Azure AD Connect verze 1.0.8641.0 a starší již v tuto chvíli neumožňují zpětný zápis hesla, protože pro tuto funkci závisí na službě ACS.
>
> Chcete-li se vyhnout výpadkům služby, upgradujte z předchozí verze Azure AD Connect na novější verzi, přečtěte si článek [Azure AD Connect: upgrade z předchozí verze na nejnovější](../hybrid/how-to-upgrade-previous-version.md)
>

Zpětný zápis hesla poskytuje:

* **Vynucování místních zásad hesel pro službu Active Directory**: když uživatel resetuje heslo, před jeho potvrzením do tohoto adresáře se ověří, jestli splňuje vaše místní zásady Active Directory. Tato revize zahrnuje kontrolu historie, složitosti, stáří, filtrů hesel a dalších omezení hesla, která jste definovali v místní službě Active Directory.
* **Nulování zpětná vazba**: zpětný zápis hesla je synchronní operace. Uživatelé se okamžitě oznámí, pokud heslo nesplňuje zásady nebo ho z nějakého důvodu nemůžete resetovat ani změnit.
* **Podporuje změny hesla z přístupového panelu a sady Office 365**: když se uživatelům synchronizovaných nebo hesel synchronizovaná hodnota hash změnila hesla, jejichž platnost vypršela nebo která vypršela, tato hesla se zapisují zpátky do místního prostředí Active Directory.
* **Podporuje zpětný zápis hesla, když ho správce resetuje z Azure Portal**: kdykoli správce resetuje heslo uživatele v [Azure Portal](https://portal.azure.com), pokud je tento uživatel federovaný nebo je hodnota hash hesla synchronizovaná, heslo se zapíše zpátky do místního prostředí. Tato funkce se v současnosti nepodporuje na portálu pro správu Office.
* **Nevyžaduje žádná pravidla brány firewall pro příchozí připojení**: zpětný zápis hesla používá jako základní komunikační kanál Azure Service Bus Relay. Veškerá komunikace je odchozí přes port 443.

> [!NOTE]
> Účty správců, které existují v chráněných skupinách v místní službě AD, se dají používat se zpětným zápisem hesla. Správci můžou změnit heslo v cloudu, ale nemůžou pomocí resetování hesla resetovat zapomenuté heslo. Další informace o chráněných skupinách najdete v tématu [chráněné účty a skupiny ve službě Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

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
1. Služba pak vyhledá uživatele pomocí atributu kotva cloudu. Aby bylo toto vyhledávání úspěšné:

   * Objekt uživatele musí existovat v prostoru konektoru služby Active Directory.
   * Objekt uživatele musí být propojený s odpovídajícím objektem úložiště metaverse (MV).
   * Objekt uživatele musí být propojený s odpovídajícím objektem Azure Active Directory konektoru.
   * Odkaz z objektu konektoru služby Active Directory na MV musí mít pravidlo synchronizace `Microsoft.InfromADUserAccountEnabled.xxx` na odkazu.
   
   Když se volání dostane z cloudu, modul synchronizace používá atribut **cloudAnchor** k vyhledání objektu prostoru konektoru Azure Active Directory. Pak následuje odkaz zpátky na objekt MV a pak následuje odkaz zpátky na objekt služby Active Directory. Vzhledem k tomu, že může existovat více objektů služby Active Directory (více doménových struktur) pro stejného uživatele, synchronizační modul spoléhá na odkaz `Microsoft.InfromADUserAccountEnabled.xxx`, aby vybral správné řešení.

1. Po nalezení uživatelského účtu se provede pokus o resetování hesla přímo v příslušné doménové struktuře služby Active Directory.
1. Je-li operace set hesla úspěšná, uživatel má informaci, že heslo bylo změněno.
   > [!NOTE]
   > Pokud je hodnota hash hesla uživatele synchronizovaná s Azure AD pomocí synchronizace hodnot hash hesel, existuje možnost, že místní zásady hesel jsou slabé, než zásady pro heslo pro Cloud. V takovém případě se zásady místních zásad vynutily. Tato zásada zajišťuje, že vaše místní zásady se vynutily v cloudu, bez ohledu na to, jestli k zajištění jednotného přihlašování použijete synchronizaci hodnot hash hesel nebo federaci.

1. Pokud operace nastavení hesla neproběhne úspěšně, vyzve uživatele k zadání chyby. Operace může selhat z těchto důvodů:
    * Služba byla mimo provoz.
    * Heslo, které si zvolili, nevyhovělo zásadám organizace.
    * Nepovedlo se najít uživatele v místní službě Active Directory.

      Chybové zprávy poskytují uživatelům pokyny, aby se mohli pokusit o vyřešení bez zásahu správce.

## <a name="password-writeback-security"></a>Zabezpečení zpětného zápisu hesla

Zpětný zápis hesla je vysoce Zabezpečená služba. Aby bylo zajištěno, že jsou vaše informace chráněny, je povolený čtyřstránkový model zabezpečení, jak je popsáno níže:

* **Služba pro předávání přes konkrétního tenanta**
   * Při nastavování služby se nastaví předávání Service Bus pro konkrétního tenanta, které je chráněné náhodně generovaným silným heslem, ke kterému má přístup Microsoft nikdy.
* **Uzamčená, kryptograficky silný šifrovací klíč hesla**
   * Po vytvoření služby Service Bus Relay se vytvoří silný symetrický klíč, který slouží k šifrování hesla při jeho přenosech. Tento klíč se používá jenom v tajném úložišti vaší společnosti v cloudu, které je silně uzamčené a auditované, stejně jako jakékoli jiné heslo v adresáři.
* **Standardní obor TLS (Transport Layer Security)**
   1. V případě, že dojde k resetování hesla nebo k operaci změny v cloudu, heslo ve formátu prostého textu se zašifruje pomocí veřejného klíče.
   1. Šifrované heslo se umístí do zprávy HTTPS, která se pošle přes zašifrovaný kanál pomocí certifikátů Microsoft SSL pro předávání přes Service Bus.
   1. Po doručení zprávy ve službě Service Bus se Váš místní agent probudí a ověří ve službě Service Bus pomocí silného hesla, které bylo dříve vygenerováno.
   1. Místní agent převezme šifrovanou zprávu a dešifruje ji pomocí privátního klíče.
   1. Místní Agent se pokusí nastavit heslo prostřednictvím rozhraní služba AD DS SetPassword API. Tento krok umožňuje vynucení místních zásad hesel ve službě Active Directory (například složitost, stáří, historie a filtry) v cloudu.
* **Zásady pro vypršení platnosti zprávy**
   * Pokud se zpráva nachází v Service Bus, protože vaše místní služba nefunguje, vyprší časový limit a po několika minutách se odeberou. Časový limit a odebrání zprávy zvyšují zabezpečení ještě více.

### <a name="password-writeback-encryption-details"></a>Podrobnosti šifrování zpětného zápisu hesla

Jakmile uživatel odešle resetování hesla, požadavek na obnovení projde několika kroky šifrování, než dorazí do místního prostředí. Tyto kroky šifrování zajišťují maximální spolehlivost a zabezpečení služby. Jsou popsány takto:

* **Krok 1: šifrování hesla pomocí 2048 klíče RSA**: když uživatel odešle heslo pro zápis zpátky do místního počítače, vlastní odeslané heslo se zašifruje pomocí 2048 klíče RSA.
* **Krok 2: šifrování na úrovni balíčku pomocí AES-GCM**: celý balíček, heslo plus požadovaná metadata se šifrují pomocí AES-GCM. Toto šifrování brání komukoli s přímým přístupem k základnímu ServiceBus kanálu v zobrazení nebo manipulaci s obsahem.
* **Krok 3: veškerá komunikace přes protokol TLS/SSL**: veškerá komunikace s ServiceBus dochází v kanálu SSL/TLS. Toto šifrování zabezpečuje obsah od neautorizovaných třetích stran.
* **Automatická výměna klíčů každých šest měsíců**: všechny klíče převezmou každých šest měsíců nebo se pokaždé, když se zpětný zápis hesla zakáže, a pak znovu povolí Azure AD Connect, aby se zajistilo maximální zabezpečení a bezpečnost služby.

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
   * Všechny operace samoobslužné změny hesla koncových uživatelů
   * Všechny operace samoobslužné změny hesla koncového uživatele, například vypršení platnosti hesla
   * Jakékoli Samoobslužné resetování hesla koncového uživatele, které pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
* **Podporované operace Správce**
   * Operace hesla pro samoobslužnou změnu hesla všech správců
   * Jakákoli operace změny hesla samoobslužného hesla pro správce, například vypršení platnosti hesla
   * Jakékoli Samoobslužné resetování hesla pro správce, které pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z [Azure Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nepodporované operace zpětného zápisu

Hesla se *nezapisují zpátky* v následujících situacích:

* **Nepodporované operace koncového uživatele**
   * Libovolný koncový uživatel resetuje vlastní heslo pomocí prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API
* **Nepodporované operace Správce**
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z PowerShellu verze 1, verze 2 nebo Azure AD Graph API
   * Jakékoli resetování hesla koncových uživatelů iniciované správcem z [centra pro správu Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> Pomocí zaškrtávacího políčka Uživatel musí změnit heslo při příštím přihlášení v místních nástrojích pro správu služby Active Directory, jako jsou uživatelé a počítače služby Active Directory, nebo Centrum správy služby Active Directory se podporuje jako funkce verze Preview Azure AD Connect. Další informace najdete v článku o [implementaci synchronizace hodnot hash hesel pomocí Azure AD Connect synchronizace](../hybrid/how-to-connect-password-hash-synchronization.md#public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon).

## <a name="next-steps"></a>Další kroky

Povolení zpětného zápisu hesla pomocí kurzu: [Povolení zpětného zápisu hesla](tutorial-enable-writeback.md)
