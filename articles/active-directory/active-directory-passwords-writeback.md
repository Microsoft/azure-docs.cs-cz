---
title: Azure AD SSPR se zpětným zápisem hesel | Microsoft Docs
description: Používání Azure AD a Azure AD Connect pro zpětný zápis hesla do místního adresáře
services: active-directory
keywords: Správa hesel služby Active directory, správou hesel Azure AD samoobslužném resetování hesla služby
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: a7a81f51e0873da38f25deb687e4d03ccf67e11a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="password-writeback-overview"></a>Přehled zpětný zápis hesla

Pomocí zpětný zápis hesla můžete konfigurovat Azure Active Directory (Azure AD) pro zpětný zápis hesel do služby Active Directory v místě. Zpětný zápis hesla eliminuje nutnost nastavit a spravovat složitá místní řešení hesla pomocí samoobslužné služby resetování (SSPR) a nabízí pohodlný způsob cloudu pro uživatelům resetovat vlastní hesla na místě, kde se nacházejí. Zpětný zápis hesla je součástí [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) , lze povolit a používat aktuální odběratele Premium [edice služby Azure Active Directory](active-directory-whatis.md).

Zpětný zápis hesla poskytuje následující funkce:

* **Poskytuje zpětnou vazbu nula delay**: zpětný zápis hesla je asynchronní operace. Uživatelé jsou okamžitě upozorněni, pokud své heslo nesplňuje zásady, nebo nelze resetovat nebo změnit z jakéhokoli důvodu.
* **Resetování hesel podporuje pro uživatele, kteří používají služby Active Directory Federation Services (AD FS) ani jiné technologie federation**: S zpětný zápis hesla, tak dlouho, dokud federované uživatelské účty jsou synchronizovány do vašeho klienta Azure AD, je to možné Správa hesel místní služby Active Directory z cloudu.
* **Resetování hesel podporuje pro uživatele, kteří používají** [synchronizace hodnot hash hesel](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): Pokud službu resetování hesla zjistí, zda účet synchronizované uživatele je povoleno synchronizace hodnot hash hesel, jsme resetovat obou tohoto účtu na místě a cloud heslo současně.
* **Resetování hesel podporuje pro uživatele, které používají předávací ověřování**: S zpětný zápis hesla, tak dlouho, dokud účty předávací ověřování jsou synchronizovány do vašeho klienta Azure AD je bylo možné spravovat své místní Active Directory hesla z cloudu.
* **Změní heslo podporuje z přístupového panelu a Office 365**: když federovaný nebo heslo, které jsou synchronizované uživatele pocházet ke změně hesla vypršela platnost, nebo jiný vypršela platnost, můžeme tyto zpětnému zápisu do místního prostředí služby Active Directory.
* **Podporuje zpětný zápis hesla, když správce obnoví je na portálu Azure**: vždy, když správce obnoví heslo uživatele v [portál Azure](https://portal.azure.com), pokud je Federovaná tohoto uživatele nebo heslo synchronizován, vytvoříme heslo správce vybere v místní službě Active Directory také. Tato funkce není aktuálně podporována na portálu správy Office.
* **Vynucuje vaše zásady pro hesla služby Active Directory v místě**: Pokud uživatel resetuje heslo, jsme Ujistěte se, že splňuje zásady vaší místní služby Active Directory před jsme ji zapište do tohoto adresáře. Tato kontrola zahrnuje kontrolu historii, složitost, stáří, filtry hesel a další omezení heslo, které jste definovali v místní službě Active Directory.
* **Nevyžaduje žádná pravidla brány firewall pro příchozí**: zpětný zápis hesla předávání přes Azure Service Bus používá jako základní komunikační kanál. Nemusíte otevírat žádné příchozí porty na bráně firewall pro tuto funkci používat.
* **Není podporována pro uživatelské účty, které existují v rámci chráněné skupiny ve službě Active Directory v místě**: Další informace o chráněné skupiny najdete v tématu [chráněné účty a skupiny ve službě Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Jak funguje zpětný zápis hesla

Když se federovaný nebo heslo hash synchronizovaný uživatel dodává můžete resetovat nebo změnit své heslo v cloudu, dojde k následující položky:

1. Jsme zkontrolujte jaký typ hesla, uživatel má. Pokud vidíte, že heslo je spravovaná místně:
   * Jsme zkontrolujte, jestli služba zpětný zápis je spuštěná. Pokud je spuštěná, nám umožňují pokračovat.
   * Pokud není služba zpětný zápis nahoru, jsme informace pro uživatele, že nyní nelze resetovat své heslo.
2. V dalším kroku uživatele předá odpovídající ověřovací brány a dosáhne **resetovat heslo** stránky.
3. Uživatel vybere nové heslo a potvrzení ho.
4. Když uživatel vybere **odeslání**, s symetrický klíč, který jste vytvořili během procesu instalace zpětného zápisu jsou šifrovány heslo jako prostý text.
5. Po šifrování hesla, jsme její zahrnutí do datové části, která se odešlou přes kanál HTTPS do vaší předávání přes konkrétního klienta service bus, (který také nastavíme pro vás během procesu instalace zpětný zápis). Tato předávání je chráněn náhodně generované heslo, které zná pouze místní instalace.
6. Po zpráva dosáhne sběrnice služby, koncový bod obnovení hesla automaticky probudí a uvidí, že má čekající žádost o resetování.
7. Služba poté hledat uživatele pomocí atribut kotvy cloudu. Pro toto vyhledávání úspěšné:

    * Objekt uživatele musí existovat v prostoru konektoru služby Active Directory.
    * Objekt uživatele musí být propojena na odpovídající objekt úložiště metaverse (MV).
    * Objekt uživatele musí být propojena na odpovídající objekt konektoru služby Azure Active Directory.
    * Odkaz více hodnot z objektu konektor služby Active Directory musí mít synchronizační pravidlo `Microsoft.InfromADUserAccountEnabled.xxx` na odkaz. <br> <br>
    Při volání dodává z cloudu, synchronizační modul používá **cloudAnchor** atribut k vyhledání objektu prostoru konektoru Azure Active Directory. Potom následuje odkaz zpět k objektu MV a pak následuje odkaz zpět do objektu služby Active Directory. Vzhledem k tomu může existovat více objektů služby Active Directory (více doménovými strukturami) pro stejného uživatele, synchronizační modul spoléhá na `Microsoft.InfromADUserAccountEnabled.xxx` odkaz vybrat tu správnou.

    > [!Note]
    > V důsledku této logiky pro heslo zpětný zápis pracovat Azure AD Connect musí být schopni komunikovat se emulátoru primárního řadiče domény (PDC). Pokud potřebujete povolit ručně, můžete připojit Azure AD Connect na emulátoru primárního řadiče domény. Klikněte pravým tlačítkem myši **vlastnosti** synchronizace konektoru služby Active Directory, pak vyberte **konfigurace oddílů adresáře**. Odtud, vyhledejte **nastavení připojení řadiče domény** a vyberte pole s názvem **používat jenom řadiče domény upřednostňované**. I když řadič domény upřednostňované není emulátor primárního řadiče domény, Azure AD Connect se pokusí připojit k primární řadič domény pro zpětný zápis hesla.

8. Poté, co uživatel není nalezen účet jsme pokus o resetování hesla přímo v příslušné doménové struktuře služby Active Directory.
9. Pokud operace nastavení hesla úspěšné, jsme informace pro uživatele, že jejich heslo bylo změněno.
    > [!NOTE]
    > Pokud pro uživatele heslo synchronizována do Azure AD pomocí synchronizace hesel, existuje pravděpodobnost, že místní zásady hesel je nižší než zásady hesel cloudu. V takovém případě jsme stále vynucovat ať zásady místního je a místo toho používat synchronizaci hodnoty hash hesla k synchronizaci hodnoty hash hesla. Tato zásada zajistí, že vaše místní zásady se vynucují v cloudu, bez ohledu používáte synchronizace hesel nebo federování zajistit jednotné přihlašování.

10. Pokud heslo nastavené operace selže, jsme vrátí chybovou zprávu pro uživatele a dát jim zkuste to znovu. Operaci se nemusí podařit, protože:
    * Služba byla dolů.
    * Heslo, které vybral nesplňuje zásady organizace.
    * V místní službě Active Directory se nám nemusí najít uživatele.

    Jsme konkrétní zprávu pro mnohá z těchto případů a informace pro uživatele co dělají řešení problému.

## <a name="configure-password-writeback"></a>Nakonfigurovat zpětný zápis hesla

Doporučujeme použít funkci Automatické aktualizace [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) Pokud chcete použít zpětný zápis hesla.

DirSync a Azure AD Sync již nejsou podporovány jako způsob, jak povolit zpětný zápis hesla. Další informace, které pomůžou s váš přechod, najdete v části [upgradu z nástroje DirSync a Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

Následující postup předpokládá, že jste již nakonfigurovali Azure AD Connect ve vašem prostředí pomocí [Express](./connect/active-directory-aadconnect-get-started-express.md) nebo [vlastní](./connect/active-directory-aadconnect-get-started-custom.md) nastavení.

1. Chcete-li konfigurovat a povolit zpětný zápis hesla, přihlaste se k serveru Azure AD Connect a spusťte **Azure AD Connect** Průvodce konfigurací.
2. Na **úvodní** vyberte **konfigurace**.
3. Na **další úkoly** vyberte **přizpůsobit možnosti synchronizace**a potom vyberte **Další**.
4. Na **připojit k Azure AD** , zadejte přihlašovací údaje globálního správce a pak vyberte **Další**.
5. Na **připojení adresáře** a **domény nebo organizační jednotku** filtrování stránky, vyberte **Další**.
6. Na **volitelné funkce** stránky, zaškrtněte políčko vedle položky **zpětný zápis hesla** a vyberte **Další**.
   ![Povolení zpětného zápisu hesla ve službě Azure AD Connect][Writeback]
7. Na **připraveno ke konfiguraci** vyberte **konfigurace** a počkejte na dokončení procesu.
8. Až uvidíte dokončit konfiguraci, vyberte **ukončení**.

Související s zpětný zápis hesla, běžných úloh odstraňování potíží naleznete v části [řešení zpětného zápisu hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) v našem článek o odstraňování potíží.

## <a name="active-directory-permissions"></a>Oprávnění služby Active Directory

Účet zadán v nástroji Azure AD Connect musí mít následující položky nastavit, pokud chcete být v oboru pro SSPR:

* **Resetování hesla** 
* **Změnit heslo** 
* **Oprávnění k zápisu** na `lockoutTime`  
* **Oprávnění k zápisu** na `pwdLastSet`
* **Rozšířená práva** na buď:
   * Kořenový objekt *každou doménu* v této doménové struktuře
   * Uživatele organizační jednotky (OU), kterou chcete být v oboru pro SSPR

Pokud si nejste jisti, co účet popsané účet odkazuje na otevřete konfigurace Azure Active Directory Connect uživatelského rozhraní a vyberte **zobrazit aktuální konfiguraci** možnost. Účet, který je nutné přidat oprávnění je uveden v části **synchronizovat adresáře**.

Pokud jste nastavili tato oprávnění, můžete účtu služby MA pro každou doménovou strukturu spravovat hesla v zastoupení uživatelských účtů v dané doménové struktuře. 

> [!IMPORTANT]
> Pokud toto přiřazení oprávnění neprovedete, pak i když má být konfigurována správně, zobrazí se zpětný zápis uživatelů dojde k chybě při pokusu o svých místních hesel můžete spravovat z cloudu.
>

> [!NOTE]
> To může trvat až jednu hodinu nebo více těchto oprávnění k replikaci pro všechny objekty ve vašem adresáři.
>

Pokud chcete nastavit příslušná oprávnění pro zpětný zápis hesla hesla, proveďte následující kroky:

1. Otevřete pomocí účtu, který má oprávnění pro správu příslušné domény Active Directory Users and Computers.
2. Z **zobrazení** nabídky, zajistěte, aby **pokročilé funkce** je zapnutý.
3. V levém panelu klikněte pravým tlačítkem na objekt, který reprezentuje kořen domény, a vyberte **vlastnosti** > **zabezpečení** > **Upřesnit**.
4. Z **oprávnění** vyberte **přidat**.
5. Vyberte účet, který oprávnění se používají pro (z instalace Azure AD Connect).
6. V **se vztahuje na** rozevíracího seznamu vyberte **podřízené uživatelské** objekty.
7. V části **oprávnění**, zaškrtněte políčka pro následující:
    * **Resetování hesla**
    * **Změnit heslo**
    * **Zápis lockoutTime**
    * **Zápis pwdLastSet**
8. Vyberte **použít/OK** použít změny a zavřete všechna otevřená dialogová.

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky pro zpětný zápis hesla

Informace o licencování najdete v tématu [licence potřebné pro zpětný zápis hesla](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) nebo na následujících serverech:

* [Azure Active Directory ceny lokality](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Místní režimy ověřování, které jsou podporovány pro zpětný zápis hesla

Podpora zpětný zápis hesla pro následující typy heslo uživatele:

* **Uživatelé cloudové**: zpětný zápis hesla nelze použít v této situaci, protože neexistuje žádné místní heslo.
* **Synchronizovat hesla uživatelů**: zpětný zápis hesla je podporován.
* **Federovaní uživatelé**: zpětný zápis hesla je podporován.
* **Předávací ověřování uživatelů**: zpětný zápis hesla je podporován.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Uživatele a správce operací, které jsou podporovány pro zpětný zápis hesla

Hesla jsou zapsány zpět v následujících situacích:

* **Operace podporovaných koncových uživatelů**
  * Všechny koncového uživatele samoobslužné služby dobrovolném změnit operace hesla
  * Všechny koncového uživatele samoobslužné služby force změnit heslo operace, například vypršení platnosti hesla
  * Všechny koncových uživatelů samoobslužného resetování hesel, pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
* **Podporované správce operací**
  * Všechny dobrovolném samoobslužné služby správce změnit operace hesla
  * Všechny force samoobslužné služby správce změnit heslo operace, například vypršení platnosti hesla
  * Jakékoli správce samoobslužného resetování hesel, pochází z [portálu pro resetování hesla](https://passwordreset.microsoftonline.com)
  * Resetování z jakékoli hesla iniciované správcem koncového uživatele [portálu Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Uživatele a správce operací, které nejsou podporovány pro zpětný zápis hesla

Hesla jsou *není* zapisovat zpátky v následujících situacích:

* **Operace nepodporované koncového uživatele**
  * Všichni koncoví uživatelé resetování vlastního hesla pomocí prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API
* **Nepodporované správce operací**
  * Resetování z jakékoli hesla iniciované správcem koncového uživatele [portálu pro správu Office](https://portal.office.com)
  * Žádné z verze 1, verze 2 nebo Azure AD Graph API prostředí PowerShell pro vytvoření nového hesla iniciované správcem koncového uživatele

Pracujeme na odebrat tato omezení, ale nemáme konkrétní časové osy, kterou jsme ještě sdílet.

## <a name="password-writeback-security-model"></a>Model zabezpečení zpětný zápis hesla

Zpětný zápis hesla je vysoce zabezpečených služba. K zajištění, že je chránit vaše informace, jsme povolit model vrstvené čtyři zabezpečení, který je popsaný v následujícím způsobem:

* **Předávání přes service-bus konkrétního klienta**
  * Při nastavování služby nastavíme předávání sběrnice služby konkrétního klienta, který je chráněný pomocí náhodně generované silné heslo, které společnost Microsoft nikdy nemá přístup k.
* **Uzamčení dolů, kryptograficky silné heslo šifrovacího klíče**
  * Po vytvoření předávání přes service bus vytvoříme silné symetrického klíče, který používáme k šifrování hesla při přechodu do prostřednictvím sítě. Tento klíč funguje jen v úložišti tajný vaší společnosti v cloudu, což je výraznou uzamčené a auditovat, stejně jako jiné heslo v adresáři.
* **Oborový standard zabezpečení TLS (Transport Layer)**
  1. Pokud heslo resetovat nebo změnit operace probíhá v cloudu, jsme trvat heslo jako prostý text a šifrování pomocí veřejného klíče.
  2. Jsme, umístěte do HTTPS zprávy odesílané přes šifrovaný kanál pomocí certifikátů SSL společnosti Microsoft k vaší předávání přes service bus.
  3. Po příchodu zprávy ve sběrnici service bus, vaše místní agent probudí a k service bus se ověří pomocí silné heslo, který byl vytvořen dříve.
  4. Agent místní převezme šifrované zprávy a dešifruje ji pomocí soukromého klíče, který jsme vygenerovat.
  5. Místní agent, pokusí se nastavit heslo prostřednictvím rozhraní API SetPassword AD DS. Tento krok je co umožňuje vynucovat vašimi zásadami pro hesla služby Active Directory v místě (například složitost, stáří, historie a filtry) v cloudu.
* **Zásady vypršení platnosti zpráv** 
  * Pokud zpráva nachází ve sběrnici service bus, protože vaše místní služba je mimo provoz, časový limit a se odebere po několika minutách. Časový limit a odebrání zprávy zvyšuje úroveň zabezpečení ještě víc.

### <a name="password-writeback-encryption-details"></a>Podrobnosti o šifrování zpětný zápis hesla

Poté, co uživatel odešle resetování hesla, žádost o resetování několik kroků šifrování projde, než ho dorazí v místním prostředí. Tyto kroky pro šifrování zajistit spolehlivost maximální služby a zabezpečení. Jsou popsány následovně:

* **Krok 1: Heslo šifrování pomocí klíče RSA 2048 bitů**: po odeslání hesla k zápisu zpět na místní, odeslané heslo samotné je šifrován klíč RSA 2048 bitů.
* **Krok 2: Šifrování na úrovni balíčku s AES-GCM**: celý balíček, heslo a požadovaná metadata, je šifrován pomocí standardu AES-GCM. Toto šifrování zabraňuje každý, kdo má přímý přístup do základní sběrnice kanálu v zobrazení nebo manipulaci s obsahem.
* **Krok 3: Veškerá komunikace probíhá přes protokol TLS/SSL**: veškerá komunikace se sběrnice se stane v kanálu SSL/TLS. Toto šifrování zabezpečuje obsah z neoprávněným třetím stranám.
* **Automatickou výměnu klíče každých šest měsíců**: každých šest měsíců nebo zpětný zápis hesla každých čas je zakázána a znovu povolena na Azure AD Connect. Nemůžeme automaticky mění všechny klíče zajistit zabezpečení Maximální služby a bezpečnost.

### <a name="password-writeback-bandwidth-usage"></a>Využití šířky pásma zpětný zápis hesla

Zpětný zápis hesla je služba malou šířkou pásma, která pouze odešle požadavky zpět do místního agenta podle následujících okolností:

* Dvě zprávy jsou odesílány, pokud je tato funkce zapnutá nebo vypnutá přes Azure AD Connect.
* Tak dlouho, dokud je služba spuštěná, je odeslána zpráva jeden každých pět minut jako služba prezenčního signálu.
* Dvě zprávy, které se odesílají každý čas odeslání nové heslo:
    * První zprávu je požadavkem k provedení operace.
    * Druhá zpráva obsahuje výsledek operace a je odeslána v následujících případech:
        * Pokaždé, když nové heslo je odesíláno během resetování hesla pomocí samoobslužné služby uživatele.
        * Pokaždé, když nové heslo je odesíláno během operace změny hesla uživatele.
        * Pokaždé, když nové heslo je odesíláno během spouštěná správce uživatelské heslo resetovat (pouze od správce Azure portálů).

#### <a name="message-size-and-bandwidth-considerations"></a>Aspekty zpráva velikost a šířky pásma

Velikost jednotlivých zpráv popsané je obvykle v části 1 KB. I v rámci extrémně zatížení samotnou službu zpětný zápis hesla spotřebovává několik kilobity za sekundu šířky pásma. Protože každá zpráva odeslána v reálném čase, pouze v případě, že operace aktualizace a heslo a protože velikost zprávy je tak malé využití šířky pásma u schopností zpětný zápis je příliš malá tak, aby měl měřitelný dopad.

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](active-directory-passwords-best-practices.md)
* [Resetování nebo změna hesla](active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](active-directory-passwords-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](active-directory-passwords-data.md)
* [Které metody ověřování jsou dostupné pro uživatele?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](active-directory-passwords-policy.md)
* [Jak hlásit aktivitu v SSPR?](active-directory-passwords-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](active-directory-passwords-how-it-works.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Povolení zpětného zápisu hesla ve službě Azure AD Connect"
