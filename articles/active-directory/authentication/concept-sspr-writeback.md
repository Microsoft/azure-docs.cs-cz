---
title: Místní integrace zpětného zápisu hesla s azure ad sspr – Azure Active Directory
description: Získejte hesla cloudu zapsaná zpět do místní infrastruktury služby AD
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
ms.openlocfilehash: 7fe58ae95c8d9c6b93c7e92e093541af009781ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454427"
---
# <a name="what-is-password-writeback"></a>Co je zpětný zápis hesla?

S cloudový nástroj pro resetování hesla je skvělé, ale většina společností stále má místní adresář, kde existují jejich uživatelé. Jak Microsoft podporuje synchronizaci tradiční místní služby Active Directory se změnami hesel v cloudu? Zpětný zápis hesla je funkce povolená pomocí [služby Azure AD Connect,](../hybrid/whatis-hybrid-identity.md) která umožňuje, aby se změny hesel v cloudu zapisovaly zpět do existujícího místního adresáře v reálném čase.

Zpětný zápis hesla je podporován v prostředích, která používají:

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronizace hodnot hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Předávací ověřování](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zpětný zápis hesla přestane fungovat pro zákazníky, kteří používají Azure AD Connect verze 1.0.8641.0 a starší, když [služba Azure Access Control (ACS) je vyřazena na 7 listopadu 2018](../azuread-dev/active-directory-acs-migration.md). Azure AD Connect verze 1.0.8641.0 a starší již nepovolí zpětný zápis hesla v té době, protože závisí na ACS pro tuto funkci.
>
> Chcete-li se vyhnout přerušení služby, upgradujte z předchozí verze Služby Azure AD Connect na novější verzi, přečtěte si článek [Azure AD Connect: Upgrade z předchozí verze na nejnovější](../hybrid/how-to-upgrade-previous-version.md)
>

Zpětný zápis hesla poskytuje:

* **Vynucení místních zásad hesel služby Active Directory**: Když uživatel resetuje své heslo, je zaškrtnuto, zda splňuje místní zásady služby Active Directory před jeho potvrzením do tohoto adresáře. Tato recenze zahrnuje kontrolu historie, složitosti, věku, filtrů hesel a dalších omezení hesel, která jste definovali v místní službě Active Directory.
* **Zpětná vazba s nulovým zpožděním**: Zpětný zápis hesla je synchronní operace. Vaši uživatelé budou okamžitě upozorněni, pokud jejich heslo nesplňuje zásady nebo je nelze z nějakého důvodu obnovit nebo změnit.
* **Podporuje změny hesel z přístupového panelu a Office 365**: Pokud federované nebo heslo hash synchronizované uživatelé přijdou změnit jejich vypršela nebo non-vypršela hesla, tato hesla jsou zapsány zpět do místního prostředí služby Active Directory.
* **Podporuje zpětný zápis hesla, když je správce resetuje z portálu Azure:** Kdykoli správce resetuje heslo uživatele na [webu Azure Portal](https://portal.azure.com), pokud je tento uživatel federovaný nebo je synchronizován hash hesla, heslo se zapíše zpět do místního prostředí. Tato funkce momentálně není na portálu pro správu Office podporovaná.
* **Nevyžaduje žádná příchozí pravidla brány firewall**: Zpětný zápis hesla používá relé Azure Service Bus jako základní komunikační kanál. Veškerá komunikace je odchozí přes port 443.

> [!NOTE]
> Účty správce, které existují v rámci chráněných skupin v místním účtu Služby AD, lze použít s zpětným zápisem hesla. Správci mohou změnit své heslo v cloudu, ale nemohou použít resetování hesla k resetování zapomenutého hesla. Další informace o chráněných skupinách naleznete [v tématu Chráněné účty a skupiny ve službě Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky na zpětný zápis hesla

**Samoobslužné resetování/změna/odemknutí hesla s místním zpětným zápisem je prémiová funkce Azure AD**. Další informace o licencování najdete v [cenovém webu Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Chcete-li použít zpětný zápis hesla, musíte mít v tenantovi přiřazenou jednu z následujících licencí:

* Azure AD Premium P1
* Azure AD Premium P2
* Podniková mobilita + zabezpečení E3 nebo A3
* Podniková mobilita + zabezpečení E5 nebo A5
* Microsoft 365 E3 nebo A3
* Microsoft 365 E5 nebo A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Samostatné licenční plány Office 365 *nepodporují "Samoobslužné resetování/změnu/odemknutí pomocí místního zpětného zápisu"* a vyžadují, abyste měli jeden z předchozích plánů, aby tato funkce fungovala.

## <a name="how-password-writeback-works"></a>Jak funguje zpětný zápis hesla

Když federovaný nebo heslo hash synchronizované uživatel pokusí obnovit nebo změnit své heslo v cloudu, dojde k následujícím akcím:

1. Kontrola se provádí zjistit, jaký typ hesla má uživatel. Pokud je heslo spravováno místně:
   * Kontrola se provádí, chcete-li zjistit, zda je služba zpětného zápisu v provozu. Pokud ano, uživatel může pokračovat.
   * Pokud je služba zpětného zápisu vypnutá, je uživatel informován, že jeho heslo nelze resetovat právě teď.
1. Dále uživatel předá příslušné ověřovací brány a dosáhne stránky **Obnovit heslo.**
1. Uživatel vybere nové heslo a potvrdí ho.
1. Když uživatel vybere **Odeslat**, heslo ve formátu prostého textu je zašifrováno symetrickým klíčem vytvořeným během procesu nastavení zpětného zápisu.
1. Šifrované heslo je součástí datové části, která se odesílá přes kanál HTTPS do přenosu service bus specifického pro klienta (který je nastaven pro vás během procesu nastavení zpětného zápisu). Toto relé je chráněno náhodně generovaným heslem, které zná pouze místní instalace.
1. Jakmile se zpráva dostane do sběrnice, koncový bod pro obnovení hesla se automaticky probudí a zjistí, že má požadavek na obnovení čekající na vyřízení.
1. Služba pak hledá uživatele pomocí atributu kotvy cloudu. Pro toto vyhledávání uspět:

   * Objekt uživatele musí existovat v prostoru konektoru služby Active Directory.
   * Objekt uživatele musí být propojen s odpovídajícím metaverse (MV) objektem.
   * Objekt uživatele musí být propojen s odpovídajícím objektem konektoru služby Azure Active Directory.
   * Propojení z objektu konektoru služby Active Directory na `Microsoft.InfromADUserAccountEnabled.xxx` mv musí mít pravidlo synchronizace na propojení.
   
   Když volání přichází z cloudu, synchronizační modul používá atribut **cloudAnchor** k vyhledat objekt prostoru konektoru Azure Active Directory. Potom následuje odkaz zpět na objekt MV a potom následuje odkaz zpět na objekt služby Active Directory. Vzhledem k tomu, že pro stejného uživatele může existovat více objektů `Microsoft.InfromADUserAccountEnabled.xxx` služby Active Directory (více doménových doménových struktury), synchronizační modul spoléhá na odkaz a vybere správný objekt.

1. Po nalezení uživatelského účtu se pokusíte obnovit heslo přímo v příslušné doménové struktuře služby Active Directory.
1. Pokud je operace sady hesel úspěšná, uživateli bude sděleno, že jeho heslo bylo změněno.
   > [!NOTE]
   > Pokud je hash hesla uživatele synchronizována s Azure AD pomocí synchronizace hash hesla, je pravděpodobné, že místní zásady hesel je slabší než zásady hesla cloudu. V tomto případě je vynuceno místní zásady. Tato zásada zajišťuje, že vaše místní zásady jsou vynucené v cloudu, bez ohledu na to, zda používáte synchronizaci hash hesel nebo federaci k poskytování jednotného přihlášení.

1. Pokud se operace nastavení hesla nezdaří, zobrazí se chyba, která uživatele vyzve k opakování. Operace může selhat, protože:
    * Služba byla vypnutá.
    * Vybrané heslo nesplňovalo zásady organizace.
    * Nelze najít uživatele v místní službě Active Directory.

      Chybové zprávy poskytují pokyny uživatelům, aby se mohli pokusit vyřešit bez zásahu správce.

## <a name="password-writeback-security"></a>Zabezpečení zpětného zápisu hesla

Zpětný zápis hesla je vysoce zabezpečená služba. Chcete-li zajistit ochranu vašich informací, je povolen čtyřvrstvý model zabezpečení, který je popsán takto:

* **Relé servisní sběrnice specifické pro nájemce**
   * Při nastavování služby je nastaveno přenos sběrnice specifické pro klienta, které je chráněno náhodně generovaným silným heslem, ke kterému microsoft nikdy nemá přístup.
* **Uzamčeno, kryptograficky silný, šifrovací klíč hesla**
   * Po vytvoření relé sběrnice je vytvořen silný symetrický klíč, který se používá k šifrování hesla při jeho doručení přes drát. Tento klíč žije pouze v tajném úložišti vaší společnosti v cloudu, který je silně uzamčen a auditován, stejně jako jakékoli jiné heslo v adresáři.
* **Standardní zabezpečení transportní vrstvy (TLS)**
   1. Dojde-li v cloudu k obnovení nebo změně hesla, heslo ve formátu prostého textu se zašifruje pomocí veřejného klíče.
   1. Šifrované heslo je umístěno do zprávy HTTPS, která je odeslána prostřednictvím šifrovaného kanálu pomocí certifikátů Microsoft TLS/SSL do přenosu sběrnice.
   1. Po doručení zprávy do sběrnice se místní agent probudí a ověří se do sběrnice pomocí silného hesla, které bylo dříve vygenerováno.
   1. Místní agent vyzvedne šifrovanou zprávu a dešifruje ji pomocí soukromého klíče.
   1. Místní agent se pokusí nastavit heslo prostřednictvím rozhraní AD DS SetPassword API. Tento krok umožňuje vynucení místních zásad hesel služby Active Directory (například složitost, stáří, historie a filtry) v cloudu.
* **Zásady vypršení platnosti zprávy**
   * Pokud je zpráva v servisní sběrnici, protože místní služba neobsahuje, vynese časový čas a po několika minutách se odebere. Časový čas a odebrání zprávy zvyšuje zabezpečení ještě více.

### <a name="password-writeback-encryption-details"></a>Podrobnosti šifrování zpětného zápisu hesla

Poté, co uživatel odešle resetování hesla, žádost o obnovení projde několika kroky šifrování před tím, než dorazí do místního prostředí. Tyto kroky šifrování zajišťují maximální spolehlivost a zabezpečení služby. Jsou popsány takto:

* **Krok 1: Šifrování hesla s 2048bitovým klíčem RSA**: Poté, co uživatel odešle heslo, které má být zapsáno zpět do místního prostředí, je samotné odeslané heslo zašifrováno 2048bitovým klíčem RSA.
* **Krok 2: Šifrování na úrovni balíčku s AES-GCM**: Celý balíček, heslo plus požadovaná metadata, je šifrována pomocí AES-GCM. Toto šifrování zabrání komukoli, kdo má přímý přístup k podkladovému kanálu ServiceBus, zobrazit obsah nebo s ním manipulovat.
* **Krok 3: Veškerá komunikace probíhá přes TLS/SSL**: Veškerá komunikace s ServiceBus probíhá v kanálu SSL/TLS. Toto šifrování zabezpečuje obsah před neoprávněnými třetími stranami.
* **Automatické převrácení klíčů každých šest měsíců**: Všechny klíče vrátit každých šest měsíců, nebo pokaždé, když heslem zpětný zápis je zakázána a pak znovu povolena na Azure AD Connect, aby bylo zajištěno maximální zabezpečení služeb a zabezpečení.

### <a name="password-writeback-bandwidth-usage"></a>Využití šířky pásma zpětného zápisu hesla

Zpětný zápis hesla je služba s malou šířkou pásma, která odesílá pouze požadavky zpět místnímu agentovi za následujících okolností:

* Dvě zprávy se posílají, když je funkce povolená nebo zakázaná prostřednictvím služby Azure AD Connect.
* Jedna zpráva je odeslána jednou za pět minut jako prezenční signál služby tak dlouho, dokud je služba spuštěna.
* Při každém odeslání nového hesla jsou odesílány dvě zprávy:
   * První zpráva je požadavek na provedení operace.
   * Druhá zpráva obsahuje výsledek operace a je odeslána za následujících okolností:
      * Pokaždé, když je během samoobslužného resetování hesla uživatele odesláno nové heslo.
      * Pokaždé, když je během operace změny hesla uživatele odesláno nové heslo.
      * Pokaždé, když se během resetování uživatelského hesla iniciovaného správcem (jenom z portálů pro správu Azure) odehrajete nové heslo.

#### <a name="message-size-and-bandwidth-considerations"></a>Důležité informace o velikosti zprávy a šířce pásma

Velikost každé zprávy popsané výše je obvykle pod 1 KB. I při extrémním zatížení spotřebovává samotná služba zpětného zápisu hesla několik kilobitů za sekundu šířky pásma. Vzhledem k tomu, že každá zpráva je odeslána v reálném čase, pouze v případě, že to vyžaduje operace aktualizace hesla a protože velikost zprávy je tak malá, využití šířky pásma funkce zpětného zápisu je příliš malý na to, aby měl měřitelný dopad.

## <a name="supported-writeback-operations"></a>Podporované operace zpětného zápisu

Hesla jsou zapsána zpět ve všech následujících situacích:

* **Podporované operace s koncovým uživatelem**
   * Jakákoli samoobslužná služba koncového uživatele dobrovolně mění funkci hesla
   * Jakákoli samoobslužná operace síly koncových uživatelů, například vypršení platnosti hesla
   * Jakékoli samoobslužné resetování hesla koncového uživatele, které pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
* **Podporované operace správce**
   * Každý správce samoobslužné dobrovolné změny hesla operace
   * Všechny samoobslužné operace vynucení správce změnit heslo operace, například vypršení platnosti hesla
   * Jakékoli samoobslužné resetování hesla správce, které pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z [webu Azure Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nepodporované operace zpětného zápisu

Hesla *nejsou* zapsána zpět v žádné z následujících situací:

* **Nepodporované operace koncových uživatelů**
   * Libovolný koncový uživatel resetuje své vlastní heslo pomocí prostředí PowerShell verze 1, verze 2 nebo rozhraní Microsoft Graph API
* **Nepodporované operace správce**
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z prostředí PowerShell verze 1, verze 2 nebo rozhraní Microsoft Graph API
   * Jakékoli resetování hesla koncového uživatele iniciované správcem z [Centra pro správu Microsoftu 365](https://admin.microsoft.com)

> [!WARNING]
> Použití zaškrtávacího políčka "Uživatel musí změnit heslo při příštím přihlášení" v místních nástrojích pro správu služby Active Directory, jako jsou uživatelé a počítače služby Active Directory nebo Centrum správy služby Active Directory, je podporováno jako funkce náhledu služby Azure AD Connect. Další informace naleznete v článku [Implementace synchronizace hodnot hash hesel se synchronizací Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Další kroky

Povolení zpětného zápisu hesla pomocí kurzu: [Povolení zpětného zápisu hesla](tutorial-enable-writeback.md)
