---
title: Místní integrace zpětný zápis hesla se samoobslužné resetování HESLA Azure AD
description: Získat místní zpětný zápis hesel cloudové AD infratstructure
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3d9d6aef4fafd6013c86fd5d5883222c0f32b34d
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49319366"
---
# <a name="what-is-password-writeback"></a>Co je zpětný zápis hesla?

Nástroj pro resetování hesla cloudových je skvělé, ale většina společností má stále v místním adresáři, kde existují svým uživatelům. Jak funguje zachování podpory Microsoft tradiční místní Active Directory (AD) synchronizované s změny hesla v cloudu? Zpětný zápis hesla je povolená funkce [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , která umožňuje změny hesla v cloudu a možné zpětně zapsat do existujícího místního adresáře v reálném čase.

Zpětný zápis hesla je podporována v prostředí, které používá:

* [Active Directory Federation Services (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Synchronizace hodnoty hash hesel](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Předávací ověřování](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Zpětný zápis hesla, přestanou fungovat pro zákazníky, kteří používají Azure AD Connect verze 1.0.8641.0 a starší při [Azure Access Control service (ACS) je dostupné jenom do 7. listopadu 2018](../develop/active-directory-acs-migration.md). Azure AD Connect verze 1.0.8641.0 a starší se už nebude povolovat zpětného zápisu hesla v daném čase protože závisejí na služby ACS, které tuto funkci.
>
> Pokud chcete zabránit přerušení služby, upgrade z předchozí verze služby Azure AD Connect na novější verzi, najdete v článku [Azure AD Connect: Upgrade z předchozí verze na nejnovější verzi](../hybrid/how-to-upgrade-previous-version.md)
>

Zpětný zápis hesla nabízí:

* **Vynucení zásad hesel místní služby Active Directory**: když uživatel může resetovat hesla, je zaškrtnuté políčko k zajištění splňuje zásady vaší místní služby Active Directory před potvrzením do tohoto adresáře. Tato kontrola zahrnuje kontrolu historie, složitost, stáří, filtrů hesla a další omezení hesel, které jste definovali v místní službě Active Directory.
* **Zpětná vazba delay nule**: zpětný zápis hesla je asynchronní operace. Uživatelům se zobrazí oznámení okamžitě pokud své heslo nesplňuje zásady nebo nemohl být vynulována ani změněna z jakéhokoli důvodu.
* **Změní heslo podporuje z přístupového panelu a Office 365**: když federovaný nebo synchronizaci hodnoty hash hesla uživatelé navštíví ke změně hesla vypršela platnost, nebo vypršela platnost, tato hesla jsou zapsány zpět do vašeho místního prostředí Active Directory.
* **Podporuje zpětný zápis hesla, když správce obnoví je na webu Azure Portal**: vždy, když správce obnoví heslo uživatele [webu Azure portal](https://portal.azure.com), pokud daného uživatele je Federovaná nebo hodnoty hash hesla synchronizovat, je heslo místní zpětný zápis. Tato funkce není aktuálně podporována v portálu pro správu Office.
* **Nevyžaduje se žádná pravidla brány firewall pro příchozí**: zpětný zápis hesla předávání přes Azure Service Bus používá jako základní komunikační kanál. Veškerá komunikace je odchozí port 443.

> [!Note]
> Uživatelské účty, které existují v rámci chráněné skupiny v místní službě Active Directory nelze použít se zpětným zápisem hesla. Další informace o chráněné skupiny, najdete v části [chráněné účty a skupiny ve službě Active Directory](https://technet.microsoft.com/library/dn535499.aspx).
>

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky pro zpětný zápis hesla

**Samoobslužné služby heslo resetovat/změny/odemknutí přes místní zpětný zápis je Prémiová funkce služby Azure AD**. Další informace o licencích najdete v článku [cenami služby Azure Active Directory web](https://azure.microsoft.com/pricing/details/active-directory/).

Pokud chcete použít zpětný zápis hesla, musí mít jeden z přiřazené ve svém tenantovi následující licence:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 nebo A3
* Enterprise Mobility + Security E5 nebo A5
* Microsoft 365 E3 nebo A3
* Microsoft 365 E5 nebo A5
* Microsoft 365 F1

> [!WARNING]
> Office 365 samostatné licenční plány *nepodporují zpětný zápis hesla* a vyžadují, abyste měli jeden z předchozích plánů pro tuto funkci pracovat.
>

## <a name="how-password-writeback-works"></a>Jak funguje zpětný zápis hesla

Při synchronizaci hodnoty hash hesla nebo federované pokusy uživatelů o resetování nebo změna hesla v cloudu, provedou se tyto akce:

1. Kontrola se provádí zjistíte, jaký druh hesel má uživatel. Pokud je heslo spravovaná místně:
   * Kontrola se provádí jestli službu zpětného zápisu je zprovozněný. Pokud se jedná, můžete přejít uživatele.
   * Pokud službu zpětného zápisu je vypnutý, uživatel informován, že nyní nelze resetovat své heslo.
1. V dalším kroku uživatel prochází příslušný ověřovací brány a dosáhne **resetovat heslo** stránky.
1. Uživatel vybere nové heslo a potvrdí ji.
1. Když uživatel vybere **odeslat**, heslo ve formátu prostého textu je zašifrovaný pomocí symetrický klíč vytvořený během procesu instalace zpětného zápisu.
1. Zašifrované heslo je součástí datové části, který bude posílat přes kanál protokolu HTTPS na vaší specifickým pro tenanta služby Service bus relay (který je nastaven pro vás během procesu instalace zpětný zápis). Toto propojení je chráněn náhodně generované heslo, které zná pouze místní instalace.
1. Po dosáhne zprávy služby Service bus, koncový bod obnovení hesla automaticky probudí a vidí, že má žádost o resetování čekající.
1. Služba uživatel pak hledá atributem ukotvení cloudu. Pro toto vyhledávání k úspěšnému:

   * Objekt uživatele musí existovat v prostoru konektoru Active Directory.
   * Objekt uživatele musí být spojen do odpovídajícího objektu úložiště metaverse (MV).
   * Objekt uživatele je potřeba propojit odpovídající objekt konektoru služby Azure Active Directory.
   * Propojení MV objekt konektoru služby Active Directory musí mít synchronizačního pravidla `Microsoft.InfromADUserAccountEnabled.xxx` na odkaz.
   
   Při volání pocházejí z cloudu, synchronizační modul používá **cloudAnchor** atribut k vyhledání objekt prostoru konektoru služby Azure Active Directory. Potom následuje odkaz zpět do objektu MV a pak následuje odkaz zpět do objektu služby Active Directory. Vzhledem k tomu může existovat více objektů služby Active Directory (s více doménovými strukturami) pro stejného uživatele, synchronizační modul se může spolehnout `Microsoft.InfromADUserAccountEnabled.xxx` odkaz vybrat tu správnou.

   > [!Note]
   > V důsledku této logiky pro heslo zpětného zápisu pro práci s Azure AD Connect musí být schopný komunikovat s emulátoru primárního řadiče domény (PDC). Pokud je potřeba povolit ručně, můžete připojit služby Azure AD Connect k emulátoru primárního řadiče domény. Klikněte pravým tlačítkem myši **vlastnosti** konektoru synchronizace služby Active Directory a potom vyberte **Konfigurovat oddíly adresáře**. Z něj, vyhledejte **nastavení připojení řadiče domény** a vyberte políčko s názvem **použít pouze upřednostňované řadiče domény**. I v případě, že upřednostňovaného řadiče domény není emulátor primárního řadiče domény, Azure AD Connect se pokusí připojit k primární řadič domény pro zpětný zápis hesla.

1. Poté, co uživatel účet nachází, je proveden pokus o resetování hesla přímo v příslušné doménové struktuře služby Active Directory.
1. Pokud je operace nastavení hesla úspěšné, uživatel je řekli, že se že změnilo heslo.
   > [!NOTE]
   > Pokud hodnoty hash hesla uživatele synchronizována do služby Azure AD prostřednictvím synchronizace hodnot hash hesel, může se stát, že v místních zásadách hesel je nižší než zásady hesel cloudu. V takovém případě se vynucuje místní zásady. Tato zásada zajistí, že vaše místní je tato zásada vynucená v cloudu, bez ohledu na to používáte synchronizaci hodnot hash hesel nebo federace k poskytování jednotného přihlašování.
   >

1. Pokud se operace nezdaří nastavit heslo, chybu zobrazí výzvu k akci opakujte. Operace může selhat, protože:
   * Služba se dolů.
   * Heslo, které vybrali nesplňuje zásad vaší organizace.
   * Nepovedlo se najít uživatele v místní službě Active Directory.

    Chybové zprávy poskytují pokyny pro uživatele, tak může pokusit vyřešit bez zásahu správce.

## <a name="password-writeback-security"></a>Zabezpečení zpětného zápisu hesla

Zpětný zápis hesla je vysoce zabezpečená služba. K zajištění, že vaše informace chránit, je povolen model čtyři vrstveného zabezpečení podle popisu v následující:

* **Specifickým pro tenanta služby Service bus relay**
   * Při nastavování služby Service bus relay specifickým pro tenanta služby nastaven, který je chráněn náhodně generované silné heslo, které Microsoft nikdy nemá přístup k.
* **Uzamčená a přidělení kryptograficky silného heslo šifrovacího klíče**
   * Po vytvoření propojovací service bus se vytvoří silné symetrický klíč, který se používá k šifrování hesla, protože jde o přenosu. Tento klíč se nachází pouze ve vaší společnosti úložiště tajných kódů v cloudu, což je silně uzamčen a auditovat, stejně jako jakékoli jiné heslo v adresáři.
* **Oborový standard zabezpečení TLS (Transport Layer)**
   1. Když heslo resetovat nebo změnit operace probíhá, v cloudu, heslo jako prostý text je zašifrovaná pomocí veřejného klíče.
   1. Zašifrované heslo je umístěn do zprávy protokolu HTTPS, který je odeslán přes zašifrovaný kanál pomocí certifikátů SSL společnosti Microsoft do vaší služby Service bus relay.
   1. Po ve službě Service bus přijde zpráva, místního agenta probudí a provede ověření ke službě service bus pomocí silné heslo, které se dříve vygeneroval.
   1. Místní agent vybere zašifrovanou zprávu a dešifruje ji pomocí soukromého klíče.
   1. Místního agenta, pokusí se nastavit heslo prostřednictvím rozhraní API SetPassword služby AD DS. Tento krok je co umožňuje vynucení zásady hesel místní služby Active Directory (například složitost, stáří, historie a filtry) v cloudu.
* **Zásady vypršení platnosti zpráv**
   * Pokud zpráva umístěná ve službě Service bus, protože místní služba je mimo provoz, vyprší časový limit a po několika minutách se odebere. Časový limit a odebrání zprávy zvyšuje úroveň zabezpečení ještě více.

### <a name="password-writeback-encryption-details"></a>Podrobnosti o šifrování pro zpětný zápis hesla

Poté, co uživatel odešle resetování hesla, žádost o resetování prochází několika šifrování kroky předtím, než dorazí ve vašem místním prostředí. Tyto kroky pro šifrování zajistit maximální služby spolehlivost a zabezpečení. Tyto toky jsou popsané následujícím způsobem:

* **Krok 1: Heslo šifrování pomocí klíče RSA 2048 bitů**: po odeslání hesla pro zapsání zpět do místního, zadané heslo, samotného je zašifrovaný pomocí 2048bitový klíč RSA.
* **Krok 2: Šifrování na úrovni balíčku s AES-GCM**: celý balíček, heslo a požadovaná metadata, se šifrují pomocí AES-GCM. Šifrování zabraňuje každý, kdo má přímý přístup k základním kanálu služby Service Bus zobrazení a manipulaci s obsahem.
* **Krok 3: Veškerá komunikace probíhá přes protokol TLS/SSL**: veškerá komunikace s služby Service Bus probíhá kanál SSL/TLS. Toto šifrování zabezpečuje obsah od neoprávněné třetích stran.
* **Automatické vrácení klíče přes každých šest měsíců**: vrátit všechny klíče přes každých šest měsíců, nebo zpětný zápis hesla každý čas je zakázané a pak znova aktivovat na Azure AD Connect, k zajištění maximální služby zabezpečení a bezpečnosti.

### <a name="password-writeback-bandwidth-usage"></a>Využití šířky pásma zpětný zápis hesla

Zpětný zápis hesla je služba s malou šířkou pásma, která odesílá požadavky zpět do místního agenta za následujících okolností:

* Dvě zprávy se posílají, když tuto funkci povolit nebo zakázat prostřednictvím služby Azure AD Connect.
* Jedna zpráva přijde každých pět minut jako prezenční signál služby pro za předpokladu, že služba běží.
* Dvě zprávy, které jsou odesílány každý čas odeslání nové heslo:
   * První zprávy je požadavkem k provedení této operace.
   * Druhá zpráva obsahuje výsledek operace a odesílají je v následujících případech:
      * Pokaždé, když nové heslo je odesíláno během resetování hesla pomocí samoobslužné služby uživatele.
      * Pokaždé, když nové heslo je odesíláno během operace změny hesla uživatele.
      * Pokaždé, když nové heslo je odesíláno během iniciované správcem uživatelské heslo resetovat (pouze z portálů pro správu Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Aspekty velikosti a šířka pásma zprávy

Velikost jednotlivých zpráv je popsáno výše, je obvykle v části 1 KB. I v rámci extrémní zatížení samotnou službu zpětného zápisu hesla využívá několik kilobity za sekundu šířky pásma. Protože každá zpráva se odesílá v reálném čase, pouze v případě potřeby operace aktualizace hesla, a protože velikost zprávy je tak malý, využití šířky pásma schopností zpětný zápis je příliš malá, aby měřitelný dopad.

## <a name="supported-writeback-operations"></a>Operace zpětného zápisu podporované

Hesel, zapíšou se zpět v následujících situacích:

* **Operace podporovaných koncových uživatelů**
   * Jakékoli koncového uživatele samoobslužných služeb dobrovolném změnit heslo operace
   * Jakékoli koncového uživatele samoobslužných služeb platnost změnit heslo operace, třeba vypršení platnosti hesla
   * Jakékoli koncového uživatele samoobslužné resetování hesla, které mohou být [portál pro resetování hesel](https://passwordreset.microsoftonline.com)
* **Podporované správce operací**
   * Jakékoli správce samoobslužné dobrovolném změnit heslo operace
   * Jakékoli správce samoobslužné platnost změnit heslo operace, třeba vypršení platnosti hesla
   * Jakékoli správce samoobslužného resetování hesla, které mohou být [portál pro resetování hesel](https://passwordreset.microsoftonline.com)
   * Jakékoli s koncovým uživatelem iniciované správcem heslo resetovat z [webu Azure portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Operace zpětného zápisu nepodporované

Hesla jsou *není* zpětný zápis v některém z následujících situací:

* **Operace nepodporované koncového uživatele**
   * Všichni koncoví uživatelé resetovat vlastní hesla s použitím prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API
* **Nepodporovaný správce operací**
   * Jakékoli s koncovým uživatelem iniciované správcem heslo resetovat z [portálu pro správu Office](https://portal.office.com)
   * Jakékoli s koncovým uživatelem iniciované správcem heslo resetovat z prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API

## <a name="next-steps"></a>Další postup

Povolení zpětného zápisu hesla pomocí tohoto kurzu: [povolení zpětného zápisu hesla](tutorial-enable-writeback.md)
