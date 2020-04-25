---
title: Řešení potíží s samoobslužným resetováním hesla – Azure Active Directory
description: Řešení potíží samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1224aef044c558147f4c7234f6514d0de2f09e0c
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143978"
---
# <a name="troubleshoot-self-service-password-reset"></a>Řešení potíží se samoobslužným resetováním hesla

Máte potíže se službou Azure Active Directory (Azure AD) Samoobslužné resetování hesla (SSPR)? Následující informace vám pomohou znovu začít pracovat.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Řešení chyb samoobslužného resetování hesla, které může uživatel vidět

| Chyba | Podrobnosti | Technické podrobnosti |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce zakázal resetování hesla ve vaší organizaci. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se prosím na správce a požádejte ho, aby tuto funkci povolil. Další informace najdete v [nápovědě, zapomenutí hesla služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: zjistili jsme, že správce nepovolil resetování hesla. Kontaktujte prosím správce a požádejte ho, aby pro vaši organizaci povolil resetování hesla. |
| WritebackNotEnabled = 10 |Je nám líto, ale heslo teď nemůžete resetovat, protože správce pro vaši organizaci nepovolil potřebnou službu. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby zkontroloval konfiguraci vaší organizace. Další informace o této potřebné službě najdete v tématu [Konfigurace zpětného zápisu hesla](howto-sspr-writeback.md). | SSPR_0010: zjistili jsme, že zpětný zápis hesla nebyl povolen. Kontaktujte prosím správce a požádejte ho, aby povolil zpětný zápis hesla. |
| SsprNotEnabledInUserPolicy = 11 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce nenakonfiguroval resetování hesla pro vaši organizaci. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se na správce a požádejte ho o konfiguraci resetování hesla. Další informace o konfiguraci resetování hesel najdete v tématu [rychlý Start: Samoobslužné resetování hesla služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: vaše organizace nedefinovala zásady pro resetování hesla. Kontaktujte prosím správce a požádejte ho, aby definoval zásady pro resetování hesla. |
| UserNotLicensed = 12 | Je nám líto, ale heslo teď nemůžete resetovat, protože ve vaší organizaci chybí požadované licence. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby zkontroloval přiřazení licence. Další informace o licencování najdete v tématu [požadavky na licencování pro Samoobslužné resetování hesla služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: vaše organizace nemá požadované licence potřebné k provedení resetování hesla. Kontaktujte prosím správce a požádejte ho, aby zkontroloval přiřazení licencí. |
| UserNotMemberOfScopedAccessGroup = 13 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce nenakonfiguroval váš účet na používání resetování hesla. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby si nakonfigurovali svůj účet pro resetování hesla. Další informace o konfiguraci účtu pro resetování hesla najdete v tématu [zavedení resetování hesel pro uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: nejste členem skupiny, která je povolená pro resetování hesla. Obraťte se na správce a žádost o přidání do skupiny. |
| UserNotProperlyConfigured = 14 | Je nám líto, ale heslo teď nemůžete resetovat, protože ve vašem účtu chybí potřebné informace. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby vám heslo resetoval. Po opětovném přístupu k účtu budete muset zaregistrovat potřebné informace. Chcete-li zaregistrovat informace, postupujte podle kroků v článku [Registrace pro Samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) . | SSPR_0014: k resetování hesla je potřeba další bezpečnostní údaje. Pokud chcete pokračovat, obraťte se na správce a požádejte ho, aby heslo resetoval. Po přístupu k účtu můžete zaregistrovat další bezpečnostní údaje na adrese https://aka.ms/ssprsetup. Správce může do svého účtu přidat další informace o zabezpečení, a to pomocí kroků uvedených v části [nastavení a čtení dat ověřování pro resetování hesla](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Je nám líto, ale v tuto chvíli nemůžeme resetovat vaše heslo kvůli problému s konfigurací resetování hesel vaší organizace. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se prosím na správce a požádejte ho, aby prošetřil. Další informace o potenciálním problému najdete v tématu [řešení potíží se zpětným zápisem hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: nemůžeme resetovat heslo z důvodu chyby v místní konfiguraci. Obraťte se prosím na správce a požádejte ho, aby prošetřil. |
| OnPremisesConnectivityError = 30 | Je nám líto, ale v tuto chvíli nemůžeme resetovat vaše heslo kvůli problémům s připojením k vaší organizaci. Neexistuje žádná akce, která by se měla provést, ale pokud to zkusíte znovu, zkuste problém vyřešit. Pokud se problém opakuje, obraťte se prosím na správce a požádejte ho, aby prošetřil. Další informace o problémech s připojením najdete v tématu [řešení potíží s připojením ke zpětnému zápisu hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: nemůžeme resetovat vaše heslo z důvodu špatného připojení k místnímu prostředí. Obraťte se na správce a požádejte ho o prošetření.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Řešení potíží s konfigurací resetování hesla v Azure Portal

| Chyba | Řešení |
| --- | --- |
| V Azure Portal se nezobrazuje oddíl **resetování hesla** v Azure AD. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí. <br> <br> Přiřaďte licenci k příslušnému účtu správce. Můžete postupovat podle kroků v článku [přiřazení, ověření a vyřešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Nezobrazuje se konkrétní možnost konfigurace. | Mnohé prvky uživatelského rozhraní jsou skryté, dokud je nebudete potřebovat. Zkuste povolit všechny možnosti, které chcete zobrazit. |
| Nevidím kartu **integrace na místní úrovni** . | Tato možnost se zobrazí jenom v případě, že jste stáhli Azure AD Connect a nakonfigurovali zpětný zápis hesla. Další informace najdete v tématu [Začínáme s Azure AD Connect pomocí expresního nastavení](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Řešení potíží s vytvářením sestav pro resetování hesel

| Chyba | Řešení |
| --- | --- |
| V kategorii událostí auditu **Samoobslužná správa hesel** se nezobrazují žádné typy aktivit správy hesel. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí. <br> <br> Tento problém můžete vyřešit tak, že přiřadíte licenci k příslušnému účtu správce. Postupujte podle kroků uvedených v článku [přiřazení, ověření a vyřešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Registrace uživatelů se zobrazí víckrát. | V současné době se při registraci uživatele protokolují každou jednotlivou datovou část, která je zaregistrovaná jako samostatná událost. <br> <br> Pokud chcete tato data agregovat a máte větší flexibilitu v tom, jak si ji můžete zobrazit, můžete si ji stáhnout a data v Excelu otevřít jako kontingenční tabulku.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Řešení potíží s registračním portálem pro resetování hesla

| Chyba | Řešení |
| --- | --- |
| V adresáři není povoleno resetování hesla. **Správce vám nepovolil použití této funkce.** | Přepněte příznak **Samoobslužné resetování hesla s povoleným** **výběrem** nebo **vše** a pak vyberte **Uložit**. |
| Uživatel nemá přiřazenou licenci Azure AD. **Správce vám nepovolil použití této funkce.** | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí. <br> <br> Tento problém můžete vyřešit tak, že přiřadíte licenci k příslušnému účtu správce. Postupujte podle kroků uvedených v článku [přiřazení, ověření a vyřešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Při zpracování požadavku došlo k chybě. | Příčinou může být mnoho problémů, ale obecně Tato chyba je způsobená výpadkem služby nebo problémem s konfigurací. Pokud se zobrazí tato chyba a má vliv na vaši firmu, obraťte se na podporu Microsoftu, kde najdete další pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Řešení potíží s portálem pro resetování hesla

| Chyba | Řešení |
| --- | --- |
| V adresáři není povoleno resetování hesla. | Přepněte příznak **Samoobslužné resetování hesla s povoleným** **výběrem** nebo **vše** a pak vyberte **Uložit**. |
| Uživatel nemá přiřazenou licenci Azure AD. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí. <br> <br> Tento problém můžete vyřešit, pokud přiřadíte licenci k příslušnému účtu správce. Postupujte podle kroků uvedených v článku [přiřazení, ověření a vyřešení problémů s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| V adresáři je povoleno resetování hesla, ale uživatel má chybějící nebo poškozené ověřovací informace. | Než budete pokračovat, zajistěte, aby měl uživatel správně vytvořené kontaktní údaje pro soubor v adresáři. Další informace najdete v tématu [data používaná samoobslužným resetováním hesla služby Azure AD](howto-sspr-authenticationdata.md). |
| V adresáři je povoleno resetování hesla, ale pokud je zásada nastavená tak, aby vyžadovala dvě metody ověřování, má uživatel pouze jedno kontaktní údaje v souboru. | Než budete pokračovat, ujistěte se, že uživatel má alespoň dvě správně nakonfigurované metody kontaktu. Příkladem je číslo mobilního telefonu *i* telefon do kanceláře. |
| Pro resetování hesla je povolený adresář a uživatel je správně nakonfigurovaný, ale uživatel není schopen kontaktovat. | Může to být výsledkem dočasné chyby služby nebo pokud jsou k dispozici nesprávná kontaktní data, která nejdou správně zjistit. <br> <br> Pokud uživatel počká 10 sekund, "zkusit znovu" a "obraťte se na správce". Pokud uživatel vybere "zkusit znovu", znovu se pokusí zavolat. Pokud uživatel vybere "kontaktujte správce", pošle mu e-mail s žádostí o resetování hesla, které se pro tento uživatelský účet provede. |
| Uživatel nikdy neobdrží resetování hesla SMS nebo telefonní hovor. | Může to být výsledkem poškozeného telefonního čísla v adresáři. Ujistěte se, že je telefonní číslo ve formátu "+ CCC xxxyyyzzzzXeeee". <br> <br> Resetování hesla nepodporuje rozšíření, ani když v adresáři zadáte nějaký. Rozšíření jsou odstraněna před odesláním volání. Použijte číslo bez přípony nebo integrujte rozšíření do telefonního čísla ve vaší soukromé pobočce Exchange (PBX). |
| Uživatel nikdy neobdrží e-mail pro resetování hesla. | Nejběžnější příčinou tohoto problému je, že zpráva je odmítnutá filtrem spamu. U e-mailu ověřte složku s nevyžádanou poštou, Nevyžádaná pošta nebo odstraněnou položku. <br> <br> Ujistěte se také, že jste kontrolovali správný e-mailový účet pro zprávu. |
| Nastavil (a) jsem zásadu pro resetování hesla, ale pokud účet správce používá resetování hesla, tato zásada se nepoužije. | Společnost Microsoft spravuje a řídí zásady resetování hesel správce, aby bylo zajištěno nejvyšší úroveň zabezpečení. |
| Uživateli se zabrání, aby se resetování hesla příliš mnohokrát za den. | Implementujeme mechanismus automatického omezování, který uživatelům zablokuje, aby se v krátkém časovém úseku příliš mnohokrát pokusil resetovat hesla. K omezování dochází v těchto případech: <br><ul><li>Uživatel se snaží ověřit telefonní číslo pětkrát za hodinu.</li><li>Uživatel se pokusí použít bránu bezpečnostních otázek pětkrát za hodinu.</li><li>Uživatel se několikrát pokusí resetovat heslo pro stejný uživatelský účet pětkrát za hodinu.</li></ul>Chcete-li tento problém vyřešit, dejte uživateli pokyn, aby počkal na 24 hodin od posledního pokusu. Uživatel pak může resetovat heslo. |
| Uživatel uvidí chybu při ověřování jejich telefonního čísla. | K této chybě dochází, pokud se zadané telefonní číslo neshoduje s telefonním číslem v souboru. Ujistěte se, že uživatel zadává úplné telefonní číslo, včetně kódu oblasti a země, při pokusu o použití metody pro resetování hesla na telefonu. |
| Při zpracování požadavku došlo k chybě. | Příčinou může být mnoho problémů, ale obecně Tato chyba je způsobená výpadkem služby nebo problémem s konfigurací. Pokud se zobrazí tato chyba a má vliv na vaši firmu, obraťte se na podporu Microsoftu, kde najdete další pomoc. |
| Porušení místních zásad | Heslo nesplňuje místní zásady hesel služby Active Directory. |
| Heslo nevyhovuje zásadám fuzzy | Heslo, které se použilo, se zobrazí v [seznamu zakázaných hesel](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) a nemusí se používat. |

## <a name="troubleshoot-password-writeback"></a>Řešení potíží se zpětným zápisem hesla

| Chyba | Řešení |
| --- | --- |
| Služba resetování hesla se nespustí místně. V protokolu událostí aplikace Azure AD Connectho počítače se zobrazí chyba 6800. <br> <br> Po zaregistrování, federovaném, předávacím ověřování nebo heslem synchronizovaným uživatelům nejde resetovat hesla. | Když je povolen zpětný zápis hesla, synchronizační modul zavolá knihovnu zpětného zápisu, aby provedl konfiguraci (připojování) tím, že komunikuje se službou clouding pro registraci. Jakékoli chyby, ke kterým došlo během připojování nebo spuštění koncového bodu služby Windows Communication Foundation (WCF) pro zpětný zápis hesla, mají za následek chyby v protokolu událostí v počítači Azure AD Connect. <br> <br> Při restartování služby Azure AD Sync (ADSync) se při konfiguraci zpětného zápisu spustí koncový bod WCF. Pokud se ale spuštění koncového bodu nezdaří, zaprotokoluje se událost 6800 a spustí se služba synchronizace. Přítomnost této události znamená, že se koncový bod zpětného zápisu hesla nespustil. Podrobnosti protokolu událostí pro událost 6800 společně s položkami protokolu událostí generovanými komponentou PasswordResetService určují, proč nelze koncový bod spustit. Zkontrolujte tyto chyby protokolu událostí a pokuste se restartovat Azure AD Connect, pokud zpětný zápis hesla stále nefunguje. Pokud potíže potrvají, zkuste zakázat a znovu povolit zpětný zápis hesla.
| Když se uživatel pokusí resetovat heslo nebo odemknout účet se zapnutým zpětným zápisem hesla, operace se nezdařila. <br> <br> Kromě toho se v protokolu událostí Azure AD Connect, který obsahuje, zobrazí událost s informacemi o tom, že synchronizační modul vrátil chybu HR = 800700CE, zpráva = název souboru nebo přípona je moc dlouhá, až nastane operace odemknutí. | Vyhledejte účet služby Active Directory pro Azure AD Connect a resetujte heslo, aby neobsahovalo více než 256 znaků. Pak otevřete **synchronizační službu** z nabídky **Start** . Přejděte ke **konektorům** a najděte **konektor služby Active Directory**. Vyberte ji a pak vyberte **vlastnosti**. Přejděte na stránku **přihlašovací údaje** a zadejte nové heslo. Kliknutím na **tlačítko OK** stránku zavřete. |
| V posledním kroku procesu instalace Azure AD Connect se zobrazí chyba s oznámením, že se nepodařilo nakonfigurovat zpětný zápis hesla. <br> <br> Protokol událostí aplikace Azure AD Connect obsahuje chybu 32009 s textem "Chyba při získávání ověřovacího tokenu". | K této chybě dochází v následujících dvou případech: <br><ul><li>Zadali jste nesprávné heslo pro účet globálního správce zadaný na začátku procesu instalace Azure AD Connect.</li><li>Pokusili jste se použít federovaného uživatele pro účet globálního správce zadaný na začátku procesu instalace Azure AD Connect.</li></ul> Chcete-li tento problém vyřešit, ujistěte se, že nepoužíváte federovaný účet pro globálního správce, kterého jste zadali na začátku procesu instalace. Ujistěte se také, že je zadané heslo správné. |
| Protokol událostí Azure AD Connect počítače obsahuje chybu 32002, která je vyvolána spuštěním PasswordResetService. <br> <br> Chyba čtení: "Chyba při připojování k ServiceBus. Poskytovatel tokenů nemohl poskytnout token zabezpečení. " | Vaše místní prostředí se nemůže připojit ke koncovému bodu Azure Service Bus v cloudu. Tato chyba je obvykle způsobená pravidlem brány firewall blokujícím odchozí připojení ke konkrétnímu portu nebo webové adrese. Další informace najdete v tématu [požadavky na připojení](../hybrid/how-to-connect-install-prerequisites.md) . Po aktualizaci těchto pravidel restartujte Azure AD Connect počítač a zpětný zápis hesla by měl začít znovu pracovat. |
| Po nějaké době se nemůžou resetovat hesla uživatelů, kteří se synchronizovaly, předávací ověřování nebo hesla-hash synchronizující s heslem. | Ve výjimečných případech se služba zpětného zápisu hesla může Azure AD Connect po restartování nedaří restartovat. V těchto případech nejdřív ověřte, jestli se zpětný zápis hesla zdá být povolený místně. Můžete to ověřit pomocí Průvodce Azure AD Connect nebo PowerShellu (viz předchozí část HowTos). Pokud se zdá, že je funkce povolená, zkuste povolit nebo zakázat funkci znovu prostřednictvím uživatelského rozhraní nebo PowerShellu. Pokud to nepomůže, zkuste dokončit odinstalaci a znovu nainstalovat Azure AD Connect. |
| Federované, předávací ověřování nebo hesla synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokusí resetovat hesla, uvidí chybu po pokusu o odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br ><br> Kromě tohoto problému se při operacích resetování hesla může zobrazit chyba, že agentovi pro správu byl odepřen přístup v místních protokolech událostí. | Pokud se v protokolu událostí zobrazují tyto chyby, zkontrolujte, že účet agenta pro správu služby Active Directory (ADMA), který jste zadali v průvodci v době konfigurace, má potřebná oprávnění ke zpětnému zápisu hesla. <br> <br> Po zadání tohoto oprávnění může trvat až jednu hodinu, než se oprávnění trickle prostřednictvím úlohy na `sdprop` pozadí na řadiči domény (DC). <br> <br> Aby bylo resetování hesla správné, musí být oprávnění označeno popisovačem zabezpečení objektu uživatele, jehož heslo Probíhá resetování. Dokud se toto oprávnění nebude zobrazovat u objektu uživatele, resetování hesla se nadále nebude podařit se zprávou o odepření přístupu. |
| Federované, předávací ověřování nebo hesla, která jsou synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokoušejí resetovat hesla, se zobrazí chyba po odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému se při operacích resetování hesla může v protokolech událostí ve službě Azure AD Connect zobrazit chyba s informacemi o tom, že se nepovedlo najít objekt. | Tato chyba obvykle indikuje, že synchronizační modul nemůže najít objekt uživatele v prostoru konektoru služby Azure AD nebo propojený objekt úložiště úložiště služby Azure AD. <br> <br> Pokud chcete tento problém vyřešit, ujistěte se, že je uživatel skutečně synchronizovaný z místního prostředí do Azure AD prostřednictvím aktuální instance Azure AD Connect a kontroluje stav objektů v prostorech konektoru a v MV. Ověřte, zda je objekt služby Active Directory Certificate Services (AD CS) připojen k objektu MV prostřednictvím pravidla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federované, předávací ověřování nebo hesla synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokusí resetovat hesla, uvidí chybu po odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému se během operací resetování hesla může v protokolech událostí ve službě Azure AD Connect zobrazit chyba s informacemi o tom, že se našla zpráva s více shodami. | To znamená, že synchronizační modul zjistil, že je objekt MV připojen k více než jednomu objektu služby AD CS prostřednictvím "Microsoft.InfromADUserAccountEnabled.xxx". To znamená, že uživatel má povolený účet ve více než jedné doménové struktuře. Tento scénář není podporován pro zpětný zápis hesla. |
| Operace hesla selžou s chybou konfigurace. Protokol událostí aplikace obsahuje Azure AD Connect chyba 6329 s textem "0x8023061f (operace se nezdařila, protože synchronizace hesla není u tohoto agenta pro správu povolena") ". | K této chybě dojde, pokud je konfigurace Azure AD Connect změněna tak, aby přidala novou doménovou strukturu služby Active Directory (nebo pro odebrání a čtení existující doménové struktury) poté, co byla funkce zpětného zápisu hesla již povolena. Operace s heslem pro uživatele v těchto nedávno přidaných doménových strukturách selžou. Chcete-li tento problém vyřešit, zakažte a znovu povolte funkci zpětného zápisu hesla po dokončení změn konfigurace doménové struktury. |

## <a name="password-writeback-event-log-error-codes"></a>Událost zpětného zápisu hesla – kódy chyb protokolu

Osvědčeným postupem při odstraňování potíží se zpětným zápisem hesla je kontrola protokolu událostí aplikace na počítači Azure AD Connect. Tento protokol událostí obsahuje události ze dvou zdrojů zájmu zpětného zápisu hesla. Zdroj PasswordResetService popisuje operace a problémy související s operacemi zpětného zápisu hesla. Zdroj ADSync popisuje operace a problémy související s nastavením hesel v prostředí služby Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Pokud je zdrojem události ADSync

| kód | Název nebo zpráva | Popis |
| --- | --- | --- |
| 6329 | BAIL: MMS (4924) 0x80230619: "omezení brání změně hesla na aktuálně zadaný." | K této události dojde, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje požadavky na stáří, historii, složitost a filtrování v doméně. <br> <br> Pokud máte minimální stáří hesla a v časovém intervalu jste nedávno změnili heslo, nebudete moct znovu změnit heslo, dokud nedosáhne zadaného stáří ve vaší doméně. Pro účely testování musí být minimální stáří nastavené na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které se nepoužilo v posledních *n* časech, kde *N* je nastavení historie hesla. Pokud vyberete heslo, které se v posledních *N* časech používalo, zobrazí se v tomto případě chyba. Pro účely testování by měla být historie hesel nastavená na 0. <br> <br> Pokud máte požadavky na složitost hesla, budou všechny tyto zásady vynutily, když se uživatel pokusí změnit nebo resetovat heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdařila. |
| 6329 | MMS (3040): admaexport. cpp (2837): Server neobsahuje ovládací prvek zásad hesel LDAP. | K tomuto problému dochází, pokud se na řadičích domény nepovoluje LDAP_SERVER_POLICY_HINTS_OID Control (1.2.840.113556.1.4.2066). Chcete-li použít funkci zpětného zápisu hesla, je nutné povolit ovládací prvek. K tomu je potřeba, aby řadiče domény byly na Windows serveru 2008 R2 nebo novějším. |
| HR 8023042 | Synchronizační modul vrátil chybu HR = 80230402, zpráva = pokus o získání objektu se nezdařil, protože existují duplicitní položky se stejnou kotvou. | K této chybě dochází, pokud je ve více doménách povoleno stejné ID uživatele. Příkladem je, že synchronizujete účet a doménové struktury prostředků a máte v každé doménové struktuře k dispozici stejné ID uživatele a povoleno. <br> <br> K této chybě může dojít také v případě, že použijete nejedinečný atribut kotvy, jako je například alias nebo hlavní název uživatele (UPN), a dva uživatelé sdílejí stejný atribut ukotvení. <br> <br> Chcete-li tento problém vyřešit, ujistěte se, že nemáte žádné duplicity uživatelů v rámci vašich domén a že pro každého uživatele použijete jedinečný atribut Anchor. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Pokud je zdrojem události PasswordResetService

| kód | Název nebo zpráva | Popis |
| --- | --- | --- |
| 31001 | PasswordResetStart | Tato událost označuje, že místní služba zjistila požadavek na resetování hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnotu hash hesla, které pocházejí z cloudu. Tato událost představuje první událost při každé operaci zpětného zápisu hesla a obnovení. |
| 31002 | PasswordResetSuccess | Tato událost označuje, že uživatel během operace resetování hesla vybral nové heslo. Zjistili jsme, že toto heslo splňuje požadavky na heslo společnosti. Heslo se úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31003 | PasswordResetFail | Tato událost označuje, že uživatel vybral heslo a heslo bylo úspěšně doručeno do místního prostředí. Ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost a filtrování pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například doména nebo podniková skupina pro správu, která nepovoluje operace s nastavením hesla.</li></ul>|
| 31004 | OnboardingEventStart | Tato událost nastane, pokud povolíte zpětný zápis hesla pomocí Azure AD Connect a zahájili jsme registraci vaší organizace na webovou službu zpětného zápisu hesla. |
| 31005 | OnboardingEventSuccess | Tato událost označuje, že proces připojování byl úspěšný a že funkce zpětného zápisu hesla je připravená k použití. |
| 31006 | ChangePasswordStart | Tato událost označuje, že místní služba zjistila požadavek na změnu hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnotu hash hesla, které pocházejí z cloudu. Tato událost představuje první událost při každé operaci zpětného zápisu změny hesla. |
| 31007 | ChangePasswordSuccess | Tato událost indikuje, že uživatel během operace změny hesla vybral nové heslo. zjistili jsme, že heslo splňuje požadavky na heslo společnosti a že se heslo úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31008 | ChangePasswordFail | Tato událost indikuje, že uživatel vybral heslo a že se heslo úspěšně přijalo do místního prostředí, ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost a filtrování pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například Domain nebo Enterprise Admins, což zakazuje operace sady hesel.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Místní služba zjistila požadavek na resetování hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnoty hash hesla, které pocházejí od správce jménem uživatele. Tato událost představuje první událost při každé operaci zpětného zápisu hesla, kterou iniciuje správce. |
| 31010 | ResetUserPasswordByAdminSuccess | Správce vybral nové heslo během operace resetování hesla iniciované správcem. Zjistili jsme, že toto heslo splňuje požadavky na heslo společnosti. Heslo se úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Správce vybral jménem uživatele heslo. Heslo bylo úspěšně doručeno do místního prostředí. Ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost a filtrování pro doménu. Pokud chcete tento problém vyřešit, zkuste nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například Domain nebo Enterprise Admins, což zakazuje operace sady hesel.</li></ul>  |
| 31012 | OffboardingEventStart | Tato událost nastane, pokud zakážete zpětný zápis hesla pomocí Azure AD Connect a indikujeme, že jsme zahájili zrušení vaší organizace na webovou službu pro zpětný zápis hesla. |
| 31013| OffboardingEventSuccess| Tato událost označuje, že proces zrušení byl úspěšný a že funkce zpětného zápisu hesla byla úspěšně zakázána. |
| 31014| OffboardingEventFail| Tato událost označuje, že proces zrušení nebyl úspěšný. Důvodem může být chyba oprávnění v cloudu nebo místním účtu správce, který jste zadali během konfigurace. K této chybě může dojít také v případě, že se pokoušíte použít globálního správce federovaného cloudu při zákazu zpětného zápisu hesla. Chcete-li tento problém vyřešit, zkontrolujte oprávnění správce a ujistěte se, že při konfiguraci funkce zpětného zápisu hesla nepoužíváte federovaný účet.|
| 31015| WriteBackServiceStarted| Tato událost označuje, že služba zpětného zápisu hesla byla úspěšně spuštěna. Je připraven přijmout požadavky na správu hesel z cloudu.|
| 31016| WriteBackServiceStopped| Tato událost označuje, že služba zpětného zápisu hesla byla zastavena. Všechny požadavky na správu hesel z cloudu nebudou úspěšné.|
| 31017| AuthTokenSuccess| Tato událost označuje, že jsme úspěšně načetli autorizační token pro globálního správce zadaný během Azure AD Connect nastavení pro spuštění procesu odinstalace nebo zprovoznění.|
| 31018| KeyPairCreationSuccess| Tato událost označuje, že jsme úspěšně vytvořili šifrovací klíč hesla. Tento klíč slouží k šifrování hesel z cloudu, které se mají odeslat do místního prostředí.|
| 32000| UnknownError| Tato událost indikuje, že během operace správy hesel došlo k neznámé chybě. Další podrobnosti najdete v textu výjimky v události. Pokud máte problémy, zkuste zakázat a znovu povolit zpětný zápis hesla. Pokud to neuděláte, zahrňte do svého specialisty podpory kopii protokolu událostí společně se zadaným ID sledování programu Insider.|
| 32001| ServiceError| Tato událost indikuje, že došlo k chybě při připojování ke službě cloudového resetování hesla. K této chybě obvykle dochází v případě, že se místní služba nemohla připojit k webové službě resetování hesla.|
| 32002| ServiceBusError| Tato událost indikuje, že došlo k chybě při připojování k instanci Service Bus vašeho tenanta. K tomu může dojít, Pokud blokujete odchozí připojení v místním prostředí. Zkontrolujte bránu firewall, abyste měli jistotu, že povolíte připojení přes https://ssprdedicatedsbprodncu.servicebus.windows.netTCP 443 a na, a pak to zkuste znovu. Pokud stále dochází k problémům, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32003| InPutValidationError| Tato událost označuje, že vstup předaný do našeho rozhraní API webové služby byl neplatný. Zkuste operaci zopakovat.|
| 32004| DecryptionError| Tato událost označuje, že došlo k chybě při dešifrování hesla, které bylo doručeno z cloudu. Příčinou může být neshoda dešifrovacího klíče mezi cloudovou službou a místním prostředím. Pokud chcete tento problém vyřešit, zakažte a znovu povolte zpětný zápis hesla v místním prostředí.|
| 32005| ConfigurationError| Během připojování ukládáme informace specifické pro tenanta do konfiguračního souboru ve vašem místním prostředí. Tato událost znamená, že při ukládání tohoto souboru nebo při spuštění služby došlo k chybě. při čtení souboru došlo k chybě. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla, aby se vynutilo přepsání konfiguračního souboru.|
| 32007| OnBoardingConfigUpdateError| Během připojování pošleme data z cloudu do místní služby pro resetování hesla. Tato data se pak zapíší do souboru v paměti předtím, než se odešlou do služby synchronizace, která se uloží bezpečně na disk. Tato událost označuje, že došlo k potížím při zápisu nebo aktualizaci těchto dat v paměti. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla, aby se vynutil přepis tohoto konfiguračního souboru.|
| 32008| ValidationError| Tato událost znamená, že jsme dostali neplatnou odpověď z webové služby pro resetování hesla. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32009| AuthTokenError| Tato událost označuje, že se nám nepovedlo získat autorizační token pro účet globálního správce zadaný při Azure AD Connect instalaci. Tato chyba může být způsobena nesprávným uživatelským jménem nebo heslem určeným pro účet globálního správce. K této chybě může dojít také v případě, že zadaný účet globálního správce je federovaný. Pokud chcete tento problém vyřešit, spusťte znovu konfiguraci se správným uživatelským jménem a heslem a ujistěte se, že správce je účet spravovaný (jenom cloudový nebo synchronizovaný s heslem).|
| 32010| CryptoError| Tato událost indikuje, že došlo k chybě při generování šifrovacího klíče pro heslo nebo při dešifrování hesla, které přicházejí z cloudové služby. Tato chyba zřejmě indikuje problém s vaším prostředím. Další informace o tom, jak tento problém vyřešit, najdete v podrobnostech v protokolu událostí. Můžete také zkusit zakázat a znovu povolit službu zpětného zápisu hesla.|
| 32011| OnBoardingServiceError| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou resetování hesla, aby zahájila proces připojování. K tomu může dojít v důsledku pravidla brány firewall nebo v případě, že došlo k potížím s získáním ověřovacího tokenu pro vašeho tenanta. Chcete-li tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes protokol TCP 443 a https://ssprdedicatedsbprodncu.servicebus.windows.netTCP 9350-9354 nebo do. Také se ujistěte, že účet správce Azure AD, který používáte k připojování, není federovaný.|
| 32013| OffBoardingError| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou resetování hesla, aby zahájila proces odhlašování. K tomu může dojít v důsledku pravidla brány firewall nebo v případě, že došlo k potížím s získáním autorizačního tokenu pro vašeho tenanta. Pokud chcete tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes 443 https://ssprdedicatedsbprodncu.servicebus.windows.netnebo do a že účet správce Azure Active Directory, který používáte k odpojení, není federovaný.|
| 32014| ServiceBusWarning| Tato událost označuje, že se musel znovu připojit k instanci Service Bus vašeho tenanta. Za běžných podmínek by to nemělo být obavy, ale pokud se tato událost několikrát zobrazí, zvažte možnost zkontrolovat připojení k síti Service Bus, zejména v případě, že se jedná o připojení s vysokou latencí nebo malou šířkou pásma.|
| 32015| ReportServiceHealthError| Aby bylo možné monitorovat stav služby zpětného zápisu hesel, pošleme data prezenčního signálu do webové služby pro resetování hesla každých pět minut. Tato událost označuje, že při posílání těchto informací o stavu zpět do cloudové webové služby došlo k chybě. Tyto informace o stavu neobsahují žádné osobní údaje a jsou čistě prezenční a základní Statistika služby, aby bylo možné v cloudu poskytovat informace o stavu služby.|
| 33001| ADUnKnownError| Tato událost označuje, že služba Active Directory vrátila neznámou chybu. Další informace najdete v protokolu událostí Azure AD Connect serveru pro události ze zdroje ADSync.|
| 33002| ADUserNotFoundError| Tato událost označuje, že uživatel, který se pokouší resetovat nebo změnit heslo, nebyl nalezen v místním adresáři. K této chybě může dojít, když byl uživatel místně odstraněn, ale ne v cloudu. K této chybě může dojít také v případě, že dojde k potížím se synchronizací. Další informace najdete v protokolech synchronizace a v posledních několika podrobnostech o spuštění synchronizace.|
| 33003| ADMutliMatchError| Když požadavek na resetování hesla nebo žádost o změnu pochází z cloudu, použijeme kotvy cloudu, které jste zadali během procesu instalace Azure AD Connect k určení, jak propojit tento požadavek zpět s uživatelem v místním prostředí. Tato událost označuje, že jsme v místním adresáři našli dva uživatele se stejným atributem kotvy cloudu. Další informace najdete v protokolech synchronizace a v posledních několika podrobnostech o spuštění synchronizace.|
| 33004| ADPermissionsError| Tato událost označuje, že účet služby Agent pro správu služby Active Directory (ADMA) nemá v daném účtu příslušná oprávnění k nastavení nového hesla. Ujistěte se, že účet ADMA v doménové struktuře uživatele má oprávnění k resetování hesla u všech objektů v doménové struktuře. Další informace o tom, jak nastavit oprávnění, najdete v části Krok 4: nastavení příslušných oprávnění služby Active Directory. K této chybě může dojít také v případě, že je atribut uživatele AdminCount nastaven na hodnotu 1.|
| 33005| ADUserAccountDisabled| Tato událost označuje, že došlo k pokusu o resetování nebo změnu hesla k účtu, který byl zablokován místně. Povolte účet a zkuste operaci zopakovat.|
| 33006| ADUserAccountLockedOut| Tato událost označuje, že došlo k pokusu o resetování nebo změnu hesla k účtu, který byl uzamčen místně. K uzamčení může dojít, když uživatel v krátké době vyzkouší příliš mnoho operací změna nebo resetování hesla. Odemkněte účet a zkuste operaci zopakovat.|
| 33007| ADUserIncorrectPassword| Tato událost označuje, že při provádění operace změny hesla zadal uživatel nesprávné aktuální heslo. Zadejte správné aktuální heslo a zkuste to znovu.|
| 33008| ADPasswordPolicyError| K této události dojde, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje požadavky na stáří, historii, složitost a filtrování v doméně. <br> <br> Pokud máte minimální stáří hesla a v časovém intervalu jste nedávno změnili heslo, nebudete moct znovu změnit heslo, dokud nedosáhne zadaného stáří ve vaší doméně. Pro účely testování musí být minimální stáří nastavené na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které se nepoužilo v posledních *n* časech, kde *N* je nastavení historie hesla. Pokud vyberete heslo, které se v posledních *N* časech používalo, zobrazí se v tomto případě chyba. Pro účely testování by měla být historie hesel nastavená na 0. <br> <br> Pokud máte požadavky na složitost hesla, budou všechny tyto zásady vynutily, když se uživatel pokusí změnit nebo resetovat heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdařila.|
| 33009| ADConfigurationError| Tato událost indikuje, že došlo k potížím při zápisu hesla zpátky do místního adresáře kvůli potížím s konfigurací se službou Active Directory. Další informace o tom, jaká chyba se stala, najdete v protokolu událostí aplikace v Azure AD Connectovém počítači pro zprávy ze služby ADSync.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Řešení potíží s připojením ke zpětnému zápisu hesla

Pokud se setkáváte s přerušením služeb v Azure AD Connect, tady je několik rychlých kroků, které můžete provést při řešení tohoto problému:

* [Potvrzení připojení k síti](#confirm-network-connectivity)
* [Restartujte službu Azure AD Connect Sync.](#restart-the-azure-ad-connect-sync-service)
* [Zakázat a znovu povolit funkci zpětného zápisu hesla](#disable-and-re-enable-the-password-writeback-feature)
* [Nainstalovat nejnovější verzi Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Řešení potíží se zpětným zápisem hesla](#troubleshoot-password-writeback)

Obecně platí, že pokud chcete službu obnovit Nejrychlejším způsobem, doporučujeme, abyste provedli tyto kroky v pořadí popsaném výše.

### <a name="confirm-network-connectivity"></a>Potvrzení připojení k síti

Nejběžnějším bodem selhání je to, že nejsou správně nakonfigurované porty brány firewall nebo proxy a nečinné limity. 

Pro Azure AD Connect verze verze 1.1.443.0 a novější potřebujete přístup odchozího protokolu HTTPS k následujícím akcím:

* \*. passwordreset.microsoftonline.com
* \*. servicebus.windows.net

V případě větší členitosti se na aktualizovaný seznam [Microsoft Azure rozsahy IP adres datového centra](https://www.microsoft.com/download/details.aspx?id=41653) aktualizovala každou středu a vstoupí v platnost příští pondělí.

Další informace najdete v článku požadavky na připojení v článku [požadavky pro Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) .

> [!NOTE]
> SSPR může selhat i v případě, že se nastavení "heslo už nevyprší" nebo "uživatel nemůže změnit heslo" nakonfigurovaný v účtu v služba AD DS místním prostředí. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Restartujte službu Azure AD Connect Sync.

Pokud chcete vyřešit problémy s připojením nebo jiné přechodné problémy se službou, restartujte službu Azure AD Connect Sync:

1. Jako správce vyberte **Spustit** na serveru se spuštěným Azure AD Connect.
1. Do vyhledávacího pole zadejte **Services. msc** a vyberte **ENTER**.
1. Vyhledejte položku **Microsoft Azure AD synchronizace** .
1. Klikněte pravým tlačítkem na položku služby, vyberte **restartovat**a potom počkejte na dokončení operace.

   ![Restartování služby Azure AD Sync pomocí grafického uživatelského rozhraní][Service restart]

Tyto kroky znovu navažte připojení ke cloudové službě a vyřešte veškerá přerušení, se kterými se můžete setkat. Pokud restartování služby ADSync problém nevyřeší, doporučujeme, abyste se pokusili zakázat a znovu povolit funkci zpětného zápisu hesla.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Zakázat a znovu povolit funkci zpětného zápisu hesla

Pokud chcete vyřešit problémy s připojením, zakažte a znovu povolte funkci zpětného zápisu hesla:

   1. Jako správce otevřete Průvodce konfigurací Azure AD Connect.
   1. V **připojení ke službě Azure AD**zadejte svoje přihlašovací údaje globálního správce služby Azure AD.
   1. V **Služba AD DS připojit k**zadejte přihlašovací údaje správce služby AD DS.
   1. V části **jedinečně identifikující vaše uživatele**vyberte tlačítko **Další** .
   1. V části **volitelné funkce**zrušte zaškrtnutí políčka **zpětný zápis hesla** .
   1. Na zbývajících stránkách dialogového okna vyberte **Další** beze změny, dokud se nedostanete na stránku **připraveno ke konfiguraci** .
   1. Zajistěte, aby se na **stránce připraveno ke konfiguraci** zobrazila možnost **zpětného zápisu hesla** jako **zakázaná** , a pak kliknutím na zelenou **konfiguraci** potvrďte provedené změny.
   1. V části **dokončeno**zrušte zaškrtnutí možnosti **synchronizovat nyní** a potom kliknutím na tlačítko **Dokončit** zavřete průvodce.
   1. Znovu otevřete Průvodce konfigurací Azure AD Connect.
   1. Opakujte kroky 2-8, s tím rozdílem, že na stránce **volitelné funkce** vyberete možnost **zpětného zápisu hesla** a službu znovu povolíte.

Tyto kroky znovu naváže spojení s naší cloudovou službou a vyřeší případné přerušení, se kterými se můžete setkat.

Pokud zakážete a pak znovu povolíte funkci zpětného zápisu hesla, doporučujeme přeinstalovat Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Nainstalovat nejnovější verzi Azure AD Connect

Přeinstalace Azure AD Connect dokáže vyřešit problémy s konfigurací a připojením mezi našimi Cloud Services a místním prostředím Active Directory.

Tento krok doporučujeme provést až po pokusu o provedení prvních dvou kroků uvedených výše.

> [!WARNING]
> Pokud jste přizpůsobili předem připravená pravidla synchronizace, *před pokračováním v upgradu je zálohujte a pak je po dokončení ručně znovu nasaďte.*

1. Stáhněte si nejnovější verzi Azure AD Connect z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Vzhledem k tomu, že jste již nainstalovali Azure AD Connect, je nutné provést místní upgrade a aktualizovat instalaci Azure AD Connect na nejnovější verzi.
1. Spusťte stažený balíček a podle pokynů na obrazovce aktualizujte Azure AD Connect počítač.

Předchozí kroky by měly znovu navázat spojení s naší cloudovou službou a vyřešit všechna přerušení, se kterými se můžete setkat.

Pokud instalace nejnovější verze serveru Azure AD Connect nevyřešila váš problém, doporučujeme, abyste si vyzkoušeli a znovu znovu povolili zpětný zápis hesla jako konečný krok po instalaci nejnovější verze.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Ověřte, že Azure AD Connect má požadované oprávnění ke zpětnému zápisu hesla.

Pro zpětný zápis hesla vyžaduje Azure AD Connect oprávnění k **resetování** hesla služby Active Directory. Chcete-li zjistit, zda Azure AD Connect má požadovaná oprávnění pro daný místní uživatelský účet služby Active Directory, můžete použít funkci efektivního oprávnění systému Windows:

1. Přihlaste se k serveru Azure AD Connect a spusťte **Synchronization Service Manager** výběrem možnosti **Spustit** > **synchronizaci služby**.
1. Na kartě **konektory** vyberte konektor on-premises **Active Directory Domain Services** a pak vyberte **vlastnosti**.  
   ![Synchronization Service Manager zobrazení úprav vlastností](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. V automaticky otevíraném okně vyberte **připojit k doménové struktuře služby Active Directory** a poznamenejte si vlastnost **uživatelské jméno** . Tato vlastnost je účet služba AD DS, který Azure AD Connect používá k provedení synchronizace adresářů. Aby Azure AD Connect mohl provést zpětný zápis hesla, musí mít účet služba AD DS oprávnění resetovat heslo.  

   ![Hledání uživatelského účtu služby Active Directory služby synchronizace](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Přihlaste se k místnímu řadiči domény a spusťte aplikaci **Uživatelé a počítače služby Active Directory** .
1. Vyberte **zobrazení** a ujistěte se, že je povolená možnost **Pokročilé funkce** .  

   ![Uživatelé a počítače služby Active Directory zobrazují pokročilé funkce](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Vyhledejte uživatelský účet služby Active Directory, který chcete ověřit. Klikněte pravým tlačítkem na název účtu a vyberte **vlastnosti**.  
1. V automaticky otevíraném okně přejdete na kartu **zabezpečení** a vyberte **Upřesnit**.  
1. V místním okně **Upřesnit nastavení zabezpečení pro správce** přejděte na kartu **efektivní přístup** .
1. Vyberte **Vybrat uživatele**, vyberte účet služba AD DS, který používá Azure AD Connect (viz krok 3) a pak vyberte **Zobrazit efektivní přístup**.

   ![Karta platného přístupu zobrazující účet synchronizace](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Posuňte se dolů a vyhledejte **heslo pro resetování**. Pokud má položka značku zaškrtnutí, má účet služba AD DS oprávnění resetovat heslo vybraného uživatelského účtu služby Active Directory.  

   ![Ověřuje se, jestli má účet synchronizace oprávnění resetovat heslo.](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóra Azure AD

Pokud máte obecné dotazy týkající se služby Azure AD a samoobslužného resetování hesla, můžete si komunitu požádat o pomoc ve [fórech Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Mezi členy komunity patří inženýri, správci produktů, odborníky MVP a odborníci na IT.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Pokud nemůžete najít odpověď na problém, naši týmy podpory vám budou mít k dispozici vždycky.

Abychom vám mohli správně pomoct, požádáme o to, abyste při otevírání případu zadali co nejvíc podrobností. Mezi tyto podrobnosti patří:

* **Obecný popis chyby**: co je to chyba? Jak se jednalo o chování, které se zaznamenalo? Jak můžeme chybu reprodukována? Poskytněte co nejvíce podrobností.
* **Stránka**: na kterou stránku jste se zaznamenali, když jste si všimli chybu? Pokud jste schopni a snímek obrazovky stránky, zahrňte adresu URL.
* **Kód podpory**: Jaký byl kód podpory, který byl vygenerován při uživateli, který chybu viděl?
   * Chcete-li najít tento kód, reprodukování chyby a potom vyberte odkaz na **Kód podpory** v dolní části obrazovky a odešlete pracovníkovi podpory identifikátor GUID, který je výsledkem.

   ![Vyhledejte kód podpory v dolní části obrazovky.][Support code]

  * Pokud se nacházíte na stránce bez kódu podpory v dolní části, vyberte F12, vyhledejte identifikátor SID a CID a odešlete tyto dva výsledky technickému pracovníkovi podpory.
* **Datum, čas a časové pásmo**: zahrnuje přesné datum a čas v *časovém pásmu* , ke kterému došlo k chybě.
* **ID uživatele**: kdo byl uživateli, který tuto chybu viděl? Příkladem je *contoso.com\@uživatele*.
   * Je tento Federovaný uživatel?
   * Je tento uživatel předávacího ověřování?
   * Je toto heslo – synchronizovaný uživatel – hash?
   * Je tento uživatel výhradně pro Cloud?
* **Licencování**: má uživatel přiřazenou licenci Azure AD?
* **Protokol událostí aplikace**: Pokud používáte zpětný zápis hesla a chyba se nachází ve vaší místní infrastruktuře, zahrňte do Azure AD Connect serveru kopii protokolu událostí aplikace.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Restartujte službu Azure AD Sync."
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kód podpory je umístěný v pravém dolním rohu okna."

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o resetování hesla prostřednictvím služby Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
