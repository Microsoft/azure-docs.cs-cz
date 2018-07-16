---
title: Azure AD SSPR se zpětným zápisem hesla | Dokumentace Microsoftu
description: Pomocí služby Azure AD a zpětný zápis hesla do místního adresáře služby Azure AD Connect
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fce92845591f20f7f2e9cff1a856e0904629255b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054790"
---
# <a name="password-writeback-overview"></a>Přehled zpětný zápis hesla

Se zpětným zápisem hesla můžete konfigurovat Azure Active Directory (Azure AD) k jejich zpětnému zápisu do místní Active Directory. Zpětný zápis hesla eliminuje potřebu vytvářet a spravovat složitá místní řešení resetování hesla hesla pomocí samoobslužné služby, a poskytuje pohodlný způsob založené na cloudu pro vaše uživatele na resetování hesel v místním, ať jsou kdekoli. Zpětný zápis hesla je součástí [Azure Active Directory Connect](./../connect/active-directory-aadconnect.md) , který lze povolit a používat stávající Odběratelé Premium [edice Azure Active Directory](../fundamentals/active-directory-whatis.md).

Zpětný zápis hesla poskytuje následující funkce:

* **Poskytuje zpětnou vazbu delay nule**: zpětný zápis hesla je asynchronní operace. Uživatelům se zobrazí oznámení okamžitě pokud své heslo nesplňuje zásady nebo nemohl být vynulována ani změněna z jakéhokoli důvodu.
* **Resetování hesel podporuje pro uživatele, kteří používají služby Active Directory Federation Services (AD FS) nebo jiné technologie federace**: se zpětným zápisem hesla, tak dlouho, dokud federovaných uživatelských účtů, které jsou synchronizovány do vašeho tenanta Azure AD budou mít možnost si jejich hesel místní služby Active Directory můžete spravujte z cloudu.
* **Resetování hesel podporuje pro uživatele, kteří používají** [synchronizaci hodnot hash hesel](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): když služba pro resetování hesel zjistí, zda je synchronizovaný uživatelský účet povolen pro synchronizaci hodnot hash hesel, tato obnoveno účtu místní a cloudové heslo současně. Zpětný zápis hesla není závislý na synchronizaci hodnot hash hesel.
* **Resetování hesel podporuje pro uživatele, kteří používají předávací ověřování**: se zpětným zápisem hesla, tak dlouho, dokud předávací ověřování účty jsou synchronizované do vašeho tenanta Azure AD, nebudou mít možnost spravovat jejich místní aktivní Adresář hesla z cloudu.
* **Změní heslo podporuje z přístupového panelu a Office 365**: když federovaný nebo uživatelé se synchronizovaným heslem pocházet ke změně hesla vypršela platnost, nebo bez vypršení platnosti jsme tyto zpětnému zápisu do vašeho místního prostředí Active Directory.
* **Když správce obnoví je na webu Azure Portal podporuje zpětný zápis hesla**: vždy, když správce obnoví heslo uživatele v [webu Azure portal](https://portal.azure.com), pokud daného uživatele je Federovaná nebo synchronizovaným heslem, nastavíme heslo správce vybere v místní službě Active Directory také. Tato funkce není aktuálně podporována v portálu pro správu Office.
* **Vynucuje vaše zásady hesel místní služby Active Directory**: když uživatel může resetovat hesla, jsme Ujistěte se, že splňují zásady vaší místní služby Active Directory předtím, než jsme potvrzení do tohoto adresáře. Tato kontrola zahrnuje kontrolu historie, složitost, stáří, filtrů hesla a další omezení hesel, které jste definovali v místní službě Active Directory.
* **Nevyžaduje se žádná pravidla brány firewall pro příchozí**: zpětný zápis hesla předávání přes Azure Service Bus používá jako základní komunikační kanál. Nemusíte otevírat žádné příchozí porty na bráně firewall pro tuto funkci používat.
* **Není podporován pro uživatelské účty, které existují v rámci chráněné skupiny v místní službě Active Directory**: Další informace o chráněné skupiny, najdete v části [chráněné účty a skupiny ve službě Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Jak funguje zpětný zápis hesla

Hash federované nebo heslo synchronizované uživatele jde o resetování nebo změna hesla v cloudu, když se stane toto:

1. Zkontrolujeme zjistíte, jaký druh hesel má uživatel. Pokud vidíme, že heslo je spravovaná místně:
   * Zkontrolujeme, pokud službu zpětného zápisu je zprovozněný. Pokud je zprovozněný, informujeme uživatele pokračovat.
   * Pokud službu zpětného zápisu není zapnutý, můžeme říct uživatelům, že nyní nelze resetovat své heslo.
2. V dalším kroku uživatel prochází příslušný ověřovací brány a dosáhne **resetovat heslo** stránky.
3. Uživatel vybere nové heslo a potvrdí ji.
4. Když uživatel vybere **odeslat**, jsme symetrický klíč, který jste vytvořili během procesu instalace zpětný zápis šifrovat heslo jako prostý text.
5. Po šifrování hesla, jsme jej zahrnout do datové části, který bude posílat přes kanál protokolu HTTPS na vaší specifickým pro tenanta služby Service bus relay, (, které jsme také nastavili pro vás během procesu instalace zpětný zápis). Toto propojení je chráněn náhodně generované heslo, které zná pouze místní instalace.
6. Po dosáhne zprávy služby Service bus, koncový bod obnovení hesla automaticky probudí a vidí, že má žádost o resetování čekající.
7. Služba uživatel pak hledá atributem ukotvení cloudu. Pro toto vyhledávání k úspěšnému:

    * Objekt uživatele musí existovat v prostoru konektoru Active Directory.
    * Objekt uživatele musí být spojen do odpovídajícího objektu úložiště metaverse (MV).
    * Objekt uživatele je potřeba propojit odpovídající objekt konektoru služby Azure Active Directory.
    * Propojení MV objekt konektoru služby Active Directory musí mít synchronizačního pravidla `Microsoft.InfromADUserAccountEnabled.xxx` na odkaz. <br> <br>
    Při volání pocházejí z cloudu, synchronizační modul používá **cloudAnchor** atribut k vyhledání objekt prostoru konektoru služby Azure Active Directory. Potom následuje odkaz zpět do objektu MV a pak následuje odkaz zpět do objektu služby Active Directory. Vzhledem k tomu může existovat více objektů služby Active Directory (s více doménovými strukturami) pro stejného uživatele, synchronizační modul se může spolehnout `Microsoft.InfromADUserAccountEnabled.xxx` odkaz vybrat tu správnou.

    > [!Note]
    > V důsledku této logiky pro heslo zpětného zápisu pro práci s Azure AD Connect musí být schopný komunikovat s emulátoru primárního řadiče domény (PDC). Pokud je potřeba povolit ručně, můžete připojit služby Azure AD Connect k emulátoru primárního řadiče domény. Klikněte pravým tlačítkem myši **vlastnosti** konektoru synchronizace služby Active Directory a potom vyberte **Konfigurovat oddíly adresáře**. Z něj, vyhledejte **nastavení připojení řadiče domény** a vyberte políčko s názvem **použít pouze upřednostňované řadiče domény**. I v případě, že upřednostňovaného řadiče domény není emulátor primárního řadiče domény, Azure AD Connect se pokusí připojit k primární řadič domény pro zpětný zápis hesla.

8. Poté, co uživatel účet nenajde, pokusu o resetování hesla přímo v příslušné doménové struktuře služby Active Directory.
9. Pokud je operace nastavení hesla úspěšné, můžeme říct uživatelům, že se že změnilo heslo.
    > [!NOTE]
    > Pokud se heslo uživatele je do služby Azure AD synchronizovat pomocí synchronizace hesel, může se stát, že v místních zásadách hesel je nižší než zásady hesel cloudu. V takovém případě stále vynucujeme cokoli, co v místních zásadách je a místo toho používat synchronizaci hodnot hash hesel k synchronizaci hodnoty hash hesla. Tato zásada zajistí, že vaše místní je tato zásada vynucená v cloudu, bez ohledu na to používáte synchronizaci hesel nebo federace k poskytování jednotného přihlašování.

10. Jestli operace se nezdaří nastavit heslo, vrátí chybu pro uživatele a mohly zkuste to znovu. Operace může selhat, protože:
    * Služba se dolů.
    * Heslo, které vybrali nesplňuje zásad vaší organizace.
    * Jsme nemusí najít uživatele v místní službě Active Directory.

    Pro mnoho z těchto případů jsme konkrétní zprávu a říct uživatelům, co mohou udělat pro vyřešení problému.

## <a name="configure-password-writeback"></a>Nakonfigurovat zpětný zápis hesla

Doporučujeme používat funkci Automatické aktualizace [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) Pokud budete chtít použít zpětný zápis hesla.

DirSync a Azure AD Sync se již nejsou podporovány jako způsob, jak povolit zpětný zápis hesla. Další informace, které pomohou vaší přechodu, naleznete v tématu [Upgrade z nástroje DirSync a Azure AD Sync](../connect/active-directory-aadconnect-dirsync-deprecated.md).

Následující postup předpokládá, že jste už nakonfigurovali Azure AD Connect ve vašem prostředí s použitím [Express](./../connect/active-directory-aadconnect-get-started-express.md) nebo [vlastní](./../connect/active-directory-aadconnect-get-started-custom.md) nastavení.

1. Ke konfiguraci a povolení zpětného zápisu hesla, přihlaste se k serveru Azure AD Connect a spustit **Azure AD Connect** Průvodce konfigurací.
2. Na **úvodní** stránce **konfigurovat**.
3. Na **další úkoly** stránce **přizpůsobit možnosti synchronizace**a pak vyberte **Další**.
4. Na **připojit ke službě Azure AD** stránky, zadejte přihlašovací údaje globálního správce a pak vyberte **Další**.
5. Na **připojení adresáře** a **domén/organizačních jednotek** filtrování stránky, vyberte **Další**.
6. Na **volitelné funkce** stránce, zaškrtněte políčko vedle položky **zpětný zápis hesla** a vyberte **Další**.
   ![Povolení zpětného zápisu hesla ve službě Azure AD Connect][Writeback]
7. Na **připraveno ke konfiguraci** stránce **konfigurovat** a počkejte na dokončení procesu.
8. Až se dokončit konfiguraci, vyberte **ukončovací**.

Související se zpětným zápisem hesla, běžné úloh odstraňování potíží naleznete v části [řešení potíží se zpětným zápisem hesla](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) v našem článku Poradce při potížích.

## <a name="active-directory-permissions"></a>Oprávnění služby Active Directory

Účet zadané v nástroji Azure AD Connect musí mít následující položky. Pokud chcete být v rozsahu pro samoobslužné resetování HESLA:

* **Resetování hesla** 
* **Změnit heslo** 
* **Oprávnění k zápisu** na `lockoutTime`  
* **Oprávnění k zápisu** na `pwdLastSet`
* **Rozšířená oprávnění** buď:
   * Kořenový objekt *každou doménu* v této doménové struktuře
   * Uživatelské organizační jednotky (OU), kterou chcete být v rozsahu pro samoobslužné resetování HESLA

Pokud si nejste jisti které účtu popsané účtu odkazuje na otevřete konfigurace Azure Active Directory Connect uživatelského rozhraní a klikněte **zobrazit aktuální konfiguraci** možnost. Účet, který je potřeba přidat oprávnění je uvedený v části **synchronizaci adresářů**.

Pokud tato oprávnění můžete nastavit, lze účtu služby MA pro každou doménovou strukturu spravovat hesla v zastoupení uživatelských účtů v dané doménové struktuře. 

> [!IMPORTANT]
> Pokud opomenete toto přiřazení oprávnění, pak, i když je potřeba nakonfigurovat správně, zobrazí se zpětný zápis uživatelů dojde k chybě při pokusu o ke správě svých místních hesel z cloudu.
>

> [!NOTE]
> To může trvat až hodinu nebo déle těchto oprávnění k replikaci adresáře ke všem objektům ve vašem adresáři.
>

Pokud chcete nastavit příslušná oprávnění pro zpětný zápis hesla na výskyt, proveďte následující kroky:

1. Otevřete pomocí účtu, který má oprávnění pro správu příslušné domény Active Directory Users and Computers.
2. Z **zobrazení** nabídky, ujistěte se, že **pokročilé funkce** zapnutý.
3. V levém panelu klikněte pravým tlačítkem na objekt, který reprezentuje kořen domény, a vyberte **vlastnosti** > **zabezpečení** > **Upřesnit**.
4. Z **oprávnění** kartu, vyberte možnost **přidat**.
5. Vyberte účet, který oprávnění se používají pro (z instalace služby Azure AD Connect).
6. V **se vztahuje na** rozevíracího seznamu vyberte **podřízené uživatelské** objekty.
7. V části **oprávnění**, zaškrtněte políčka pro následující:
    * **Resetování hesla**
    * **Změnit heslo**
    * **Zápis lockoutTime**
    * **Zápis pwdLastSet**
8. Vyberte **použít/OK** použít změny a zavřete všechna otevřená dialogová.

## <a name="licensing-requirements-for-password-writeback"></a>Licenční požadavky pro zpětný zápis hesla

Informace o licencování najdete v tématu [licence potřebné pro zpětný zápis hesla](concept-sspr-licensing.md) nebo na následujících stránkách:

* [Azure Active Directory, ceny za Web](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Místní režimy ověřování, které jsou podporovány pro zpětný zápis hesla

Podpora zpětného zápisu hesla pro následující typy heslo uživatele:

* **Uživatelů pouze cloudu**: zpětný zápis hesla se nedá použít v této situaci, protože neexistuje žádný místní heslo.
* **Synchronizaci hesel uživatelů**: zpětný zápis hesla se nepodporuje.
* **Federovaní uživatelé**: zpětný zápis hesla se nepodporuje.
* **Předávací ověřování uživatelů**: zpětný zápis hesla se nepodporuje.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Operace uživatelů a správců, které jsou podporovány pro zpětný zápis hesla

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

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Uživatele a správce operací, které nejsou podporovány pro zpětný zápis hesla

Hesla jsou *není* zpětný zápis v některém z následujících situací:

* **Operace nepodporované koncového uživatele**
  * Všichni koncoví uživatelé resetovat vlastní hesla s použitím prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API
* **Nepodporovaný správce operací**
  * Jakékoli s koncovým uživatelem iniciované správcem heslo resetovat z [portálu pro správu Office](https://portal.office.com)
  * Jakékoli s koncovým uživatelem iniciované správcem heslo resetovat z prostředí PowerShell verze 1, verze 2 nebo Azure AD Graph API

Pracujeme na odstranění těchto omezení, ale nemáme konkrétní časové osy, které jsme zatím sdílet.

## <a name="password-writeback-security-model"></a>Modelu zabezpečení zpětného zápisu hesla

Zpětný zápis hesla je vysoce zabezpečená služba. Zajistit ochranu vašich informací jsme povolili model čtyři vrstveného zabezpečení, který je popsaný v následujícím způsobem:

* **Specifickým pro tenanta služby Service bus relay**
  * Při nastavování služby nastavíme specifickým pro tenanta služby Service bus relay, který je chráněn náhodně generované silné heslo, které Microsoft nikdy nemá přístup k.
* **Uzamčená a přidělení kryptograficky silného heslo šifrovacího klíče**
  * Po vytvoření služby Service bus relay vytvoříme silné symetrický klíč používaný k šifrování hesla, protože jde o přenosu. Tento klíč se nachází pouze ve vaší společnosti úložiště tajných kódů v cloudu, což je silně uzamčen a auditovat, stejně jako jakékoli jiné heslo v adresáři.
* **Oborový standard zabezpečení TLS (Transport Layer)**
  1. Pokud heslo resetovat nebo změnit operace probíhá, v cloudu, jsme trvat heslo jako prostý text a šifrování s veřejným klíčem.
  2. Doporučujeme umístit, který do zprávy protokolu HTTPS, který je odeslán přes zašifrovaný kanál pomocí certifikátů SSL společnosti Microsoft do vaší služby Service bus relay.
  3. Po ve službě Service bus přijde zpráva, místního agenta probudí a provede ověření ke službě service bus pomocí silné heslo, které se dříve vygeneroval.
  4. Místní agent vybere zašifrovanou zprávu a dešifruje ji pomocí soukromého klíče, který jsme vygenerována.
  5. Místního agenta, pokusí se nastavit heslo prostřednictvím rozhraní API SetPassword služby AD DS. Tento krok je co umožňuje vynucovat vaší služby Active Directory v místních zásadách hesel (třeba složitost, stáří, historie a filtry) v cloudu.
* **Zásady vypršení platnosti zpráv** 
  * Pokud zpráva umístěná ve službě Service bus, protože místní služba je mimo provoz, vyprší časový limit a po několika minutách se odebere. Časový limit a odebrání zprávy zvyšuje úroveň zabezpečení ještě více.

### <a name="password-writeback-encryption-details"></a>Podrobnosti o šifrování pro zpětný zápis hesla

Poté, co uživatel odešle resetování hesla, žádost o resetování prochází několika šifrování kroky předtím, než dorazí ve vašem místním prostředí. Tyto kroky pro šifrování zajistit maximální služby spolehlivost a zabezpečení. Tyto toky jsou popsané následujícím způsobem:

* **Krok 1: Heslo šifrování pomocí klíče RSA 2048 bitů**: po odeslání hesla pro zapsání zpět do místního, zadané heslo, samotného je zašifrovaný pomocí 2048bitový klíč RSA.
* **Krok 2: Šifrování na úrovni balíčku s AES-GCM**: celý balíček, heslo a požadovaná metadata, se šifrují pomocí AES-GCM. Šifrování zabraňuje každý, kdo má přímý přístup k základním kanálu služby Service Bus zobrazení a manipulaci s obsahem.
* **Krok 3: Veškerá komunikace probíhá přes protokol TLS/SSL**: veškerá komunikace s služby Service Bus probíhá kanál SSL/TLS. Toto šifrování zabezpečuje obsah od neoprávněné třetích stran.
* **Automatickou výměnu klíče po každých šesti měsících**: po každých šesti měsících nebo zpětný zápis hesla každý čas je zakázané a pak znova aktivovat na Azure AD Connect. Abychom mohli automaticky přes všechny klíče k zajištění maximální služby zabezpečení a bezpečnosti.

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

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Povolení zpětného zápisu hesla ve službě Azure AD Connect"
