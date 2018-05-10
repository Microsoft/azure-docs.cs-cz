---
title: Řešení potíží – Azure Active Directory resetování hesla pomocí samoobslužné služby
description: Řešení problémů s Azure AD samoobslužného resetování hesel
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: d3e43d3484d321c93b4ac3b0e2b947b69af5d2c6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="troubleshoot-self-service-password-reset"></a>Řešení potíží s samoobslužné resetování hesla

Máte problém s Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR)? Informace, které následuje můžete získat věcí znovu funguje.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Řešení chyb resetování hesla pomocí samoobslužné služby, které může uživatel zobrazit

| Chyba | Podrobnosti | Technické podrobnosti |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Omlouváme se, ale že v tuto chvíli nelze obnovit heslo, protože váš správce zakázal resetování hesla pro vaši organizaci. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte jej o povolení této funkce. Další informace najdete v tématu [nápovědy, zapomenuté heslo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Zjistili jsme, že resetování hesla nebylo povoleno správcem. Obraťte se na správce a požádejte jej o povolení resetování hesla pro vaši organizaci. |
| WritebackNotEnabled = 10 |Omlouváme se, ale že v tuto chvíli nelze obnovit heslo, protože správce nepovolil nezbytné služby pro vaši organizaci. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte je o Zkontrolujte konfiguraci vaší organizace. Další informace o této potřebné služby, najdete v části [konfigurace zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: Zjistili jsme, že tento zpětný zápis hesla nebylo povoleno. Obraťte se na správce a požádejte jej o povolení zpětného zápisu hesla. |
| SsprNotEnabledInUserPolicy = 11 | Omlouváme se, ale že v tuto chvíli nelze obnovit heslo, protože váš správce nenakonfiguroval resetování hesla pro vaši organizaci. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte je o konfigurace resetování hesla. Další informace o heslo resetujte konfiguraci najdete v tématu [rychlý start: resetování hesla pomocí samoobslužné služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Vaše organizace nedefinoval zásady resetování hesel. Obraťte se na správce a požádejte je o definovat zásady resetování hesel. |
| UserNotLicensed = 12 | Omlouváme se, ale že v tuto chvíli nelze obnovit heslo, protože požadované licence chybí z vaší organizace. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte je, aby kontrolovat vaše přiřazení licence. Další informace o licencování najdete v tématu [resetovat Licensing požadavky pro hesla pomocí samoobslužné služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Vaše organizace nemá požadované licence nezbytným k provedení resetování hesla. Obraťte se na správce a požádejte je o kontrolu přiřazení licence. |
| UserNotMemberOfScopedAccessGroup = 13 | Omlouváme se, ale že v tuto chvíli nelze obnovit heslo, protože váš správce nenakonfiguroval váš účet se má použít resetování hesla. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte je o konfiguraci vašeho účtu pro resetování hesla. Další informace o konfiguraci účtu pro resetování hesla naleznete v tématu [resetování hesla pro uživatele zavádět](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Nejste členem skupiny povolen pro resetování hesla. Obraťte se na správce a požadavek, který se má přidat do skupiny. |
| UserNotProperlyConfigured = 14 | Omlouváme se, ale že v tuto chvíli nelze resetovat heslo, protože chybí požadované informace z vašeho účtu. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Vás kontaktovat správce a požádejte je, aby vám heslo resetoval. Až budete mít přístup ke svému účtu znovu, budete muset registraci potřebné informace. Pokud chcete zaregistrovat informace, postupujte podle kroků v [zaregistrovat pro resetování hesla pomocí samoobslužné služby](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) článku. | SSPR_0014: K resetování hesla se vyžaduje další bezpečnostní údaje. Chcete-li pokračovat, obraťte se na správce a požádejte je o resetování hesla. Až budete mít přístup ke svému účtu, můžete zaregistrovat další bezpečnostní údaje na adrese https://aka.ms/ssprsetup. Správce můžete přidat další bezpečnostní údaje k účtu pomocí kroků v [sady a data čtení ověřování pro resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Je nám líto, že nemůžeme resetovat heslo v tuto chvíli z důvodu problému s konfigurací resetování hesla vaší organizace. Neexistuje žádná další akce, které můžete provést k vyřešení této situaci. Obraťte se na správce a požádejte je o prozkoumat. Další informace o potenciální problém, najdete v části [řešení zpětného zápisu hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nepovedlo se nám k resetování hesla došlo k chybě v konfiguraci na místě. Obraťte se na správce a požádejte je o prozkoumat. |
| OnPremisesConnectivityError = 30 | Je nám líto, že nemůžeme resetovat heslo v tuto chvíli kvůli potížím s připojením k vaší organizaci. Neexistuje žádné akci nyní provést, ale může být problém vyřešen, pokud zkuste to znovu později. Pokud potíže potrvají, obraťte se na správce a požádejte je o prozkoumat. Další informace o problémy s připojením najdete v tématu [řešení potíží s připojením zpětný zápis hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nemůžeme resetovat heslo kvůli nízký připojení s místním prostředí. Obraťte se na správce a požádejte je o prozkoumat.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Řešení potíží s konfigurací resetování hesla na portálu Azure

| Chyba | Řešení |
| --- | --- |
| Se nezobrazí **resetování hesla** části v rámci Azure AD na portálu Azure. | To může dojít, pokud nemáte Azure AD Premium nebo Basic licenci přiřazené správci provádění této operace. <br> <br> Přiřadíte licenci k účtu správce, který je nejistá. Můžete podle kroků v [přiřadit zkontrolujte a vyřešte problémy s licencí](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) článku.|
| Možnost konkrétní konfigurace se nezobrazí. | Mnoho prvků uživatelského rozhraní jsou skryté, dokud není potřeba. Zkuste povolit všechny možnosti, které chcete zobrazit. |
| Se nezobrazí **integrace v místním** kartě. | Tato možnost jenom se zobrazí, pokud jste si stáhli Azure AD Connect a nakonfigurovali zpětný zápis hesla. Další informace najdete v tématu [Začínáme s Azure AD Connect pomocí expresního nastavení](./../connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Řešení potíží s reporting resetování hesla

| Chyba | Řešení |
| --- | --- |
| Všechny typy heslo správy aktivit v se nezobrazí **Samoobslužná správa hesel** kategorie události auditu. | To může dojít, pokud nemáte Azure AD Premium nebo Basic licenci přiřazené správci provádění této operace. <br> <br> Tento problém můžete vyřešit přiřazení licence k účtu správce, který je nejistá. Postupujte podle kroků v [přiřadit zkontrolujte a vyřešte problémy s licencí](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) článku. |
| Registrace uživatele zobrazit více než jednou. | Když se uživatel zaregistruje, jsme v současné době protokolu každé jednotlivé část dat, která je registrována jako samostatná událost. <br> <br> Pokud chcete mít větší flexibilitu v tom, jak si můžete zobrazit a shromažďovat tato data, můžete stáhnout sestavu a otevřete data jako kontingenční tabulky v aplikaci Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Řešení potíží s portálu registrace resetování hesel

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. **Váš správce vám nepovolil se pomocí této funkce.** | Přepínač **povolit samoobslužné resetování hesla** příznak, který **vybrané** nebo **všechny** a pak vyberte **Uložit**. |
| Uživatel nemá Azure AD Premium nebo Basic přiřazenou licenci. **Váš správce vám nepovolil se pomocí této funkce.** | To může dojít, pokud nemáte Azure AD Premium nebo Basic licenci přiřazené správci provádění této operace. <br> <br> Tento problém můžete vyřešit přiřazení licence k účtu správce, který je nejistá. Postupujte podle kroků v [přiřadit zkontrolujte a vyřešte problémy s licencí](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) článku.|
| Dojde k chybě při zpracování požadavku. | To může být způsobeno řadu problémů, ale obecně tato chyba je způsobená výpadku služeb nebo o problém s konfigurací. Pokud se zobrazí tato chyba a ovlivňuje vaši firmu, kontaktujte podporu Microsoftu o další pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Řešení potíží s portálu pro resetování hesla

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. | Přepínač **povolit samoobslužné resetování hesla** příznak, který **vybrané** nebo **všechny** a pak vyberte **Uložit**. |
| Uživatel nemá Azure AD Premium nebo Basic přiřazenou licenci. | To může dojít, pokud nemáte Azure AD Premium nebo Basic licenci přiřazené správci provádění této operace. <br> <br> Tento problém lze vyřešit, chcete-li přiřadit licenci k účtu správce, který je nejistá. Postupujte podle kroků v [přiřadit zkontrolujte a vyřešte problémy s licencí](../active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) článku. |
| Adresář je povolen pro resetování hesla, ale uživatel má chybějící nebo nesprávně informace o ověřování. | Než budete pokračovat, ujistěte se, že tento uživatel má ve správném formátu kontaktní údaje na soubor v adresáři. Další informace najdete v tématu [obnovit Data využívaná hesla pomocí samoobslužné služby Azure AD](howto-sspr-authenticationdata.md). |
| Adresář je povolen pro resetování hesla, ale uživatel má pouze jedna část kontaktní údaje na soubor, pokud je zásada nastavená tak, aby vyžadovala dvě metody ověření. | Než budete pokračovat, ujistěte se, že uživatel má alespoň dva způsoby kontaktování správně nakonfigurované. Příklad s o číslo mobilního telefonu *a* office telefonní číslo. |
| Adresář je povolen pro resetování hesla a je správně nakonfigurován, ale uživatel se nepodařilo kontaktovat. | To může být výsledek k dočasné chybě nebo pokud je nesprávný kontaktní data, která nejde správně zjistit. <br> <br> Pokud uživatel čeká 10 sekund, "opakujte" a "obraťte se na správce" odkazy zobrazí. Pokud uživatel vybere "opakujte", se pokusí volání. Pokud uživatel vybere "obraťte se na správce", odešle e-mailu s formuláři na své správce požaduje heslo resetovat provést pro tento uživatelský účet. |
| Uživatel obdrží nikdy resetování hesla služby SMS nebo telefonní hovor. | To může být výsledkem poškozený telefonní číslo v adresáři. Zkontrolujte, zda telefonní číslo je ve formátu "+ xxxyyyzzzzXeeee kopie". <br> <br> Resetování hesla nepodporuje rozšíření, i v případě, že zadáváte jednu v adresáři. Rozšíření se odstraní a předtím, než je odeslán volání. Použít několik bez přípony nebo systém integrovat rozšíření do telefonní číslo ve vaší privátní větve exchange (PBX). |
| Uživatel nikdy obdrží e-mailu, resetování hesla. | Nejčastější příčinou tohoto problému je, že je zpráva odmítnut filtr proti spamu. Zkontrolujte nevyžádané pošty, nevyžádanou poštou nebo odstraněných položek složku pro e-mailu. <br> <br> Ujistěte se také, že při kontrole správné e-mailový účet pro zprávu. |
| Nastavení zásad resetování hesel, ale pokud účet správce používá resetování hesla, není použita tuto zásadu. | Spravuje Microsoft a ovládací prvky zásadu a zajistit tak nejvyšší úroveň zabezpečení pro resetování hesla správce. |
| Uživatel je zabránit v pokusu o vytvoření nového příliš mnohokrát za den. | Implementaci mechanismus automatického omezení uživatelům bloku v pokusu o vytvoření nového hesla příliš mnohokrát v krátké době. Omezení dojde při: <br><ul><li>Uživatel se pokusí ověřit telefonní číslo 5krát za jednu hodinu.</li><li>Uživatel se pokusí použít bránu otázky zabezpečení 5krát za jednu hodinu.</li><li>Uživatel se pokusí resetovat heslo pro stejným uživatelským účtem 5krát za jednu hodinu.</li></ul>Chcete-li tento problém vyřešit, požádejte počkejte 24 hodin po poslední pokus o uživateli. Uživatel pak můžete obnovit své heslo. |
| Uživateli se zobrazí k chybě při ověření svého telefonního čísla. | K této chybě dojde, pokud zadané telefonní číslo neodpovídá telefonního čísla na soubor. Ujistěte se, že uživatel zadá úplné telefonní číslo, včetně kód oblasti a země, pokud se pokus o použití metody založené na telefonu pro resetování hesla. |
| Dojde k chybě při zpracování požadavku. | To může být způsobeno řadu problémů, ale obecně tato chyba je způsobená výpadku služeb nebo o problém s konfigurací. Pokud se zobrazí tato chyba a ovlivňuje vaši firmu, kontaktujte podporu Microsoftu o další pomoc. |

## <a name="troubleshoot-password-writeback"></a>Řešení potíží s zpětný zápis hesla

| Chyba | Řešení |
| --- | --- |
| Službu resetování hesla se nespustí na místě. Chyba 6800 se zobrazí v protokolu událostí aplikace na Azure AD Connect počítač. <br> <br> Po registraci, federovaný předávací ověřování nebo synchronizovat hodnoty hash hesla uživatele nelze resetovat vlastní hesla. | Pokud je povolen zpětný zápis hesla, synchronizační modul volá knihovnu zpětný zápis do provedení konfigurace (registrace) podle komunikaci ke cloudové službě registrace. Všechny chyby došlo během registrace nebo při spouštění služby Windows Communication Foundation (WCF) koncový bod pro výsledky zpětného zápisu hesla v chyby v protokolu událostí v počítači Azure AD Connect. <br> <br> Při restartování služby Azure AD Sync (ADSync) Pokud bylo nakonfigurované zpětný zápis, koncový bod WCF spuštění. Ale pokud se nezdaří spuštění koncového bodu, jsme protokolu událostí 6800 a synchronizační službu spustit. Přítomnost Tato událost znamená, že koncový bod zpětný zápis hesla se nepodařilo spustit. Podrobnosti protokolu událostí pro tuto událost 6800, společně s protokolu událostí, které položky vygenerujte komponentu PasswordResetService uveďte, proč není možné spustit až koncový bod. Přečtěte si tyto chyby v protokolu událostí a pokuste se službu Azure AD Connect restartovat, pokud zpětný zápis hesla stále nepracuje. Pokud potíže potrvají, zkuste zakázat a znovu povolit zpětný zápis hesla.
| Když se uživatel pokusí resetovat heslo nebo odemknout účet s povolen zpětný zápis hesla, operace se nezdaří. <br> <br> Kromě toho se zobrazí události v protokolu událostí Azure AD Connect, který obsahuje: "synchronizačního modulu vrátila chyba hr = 800700CE, zpráva = Název souboru nebo rozšíření je příliš dlouhá" po odemčení operace probíhá. | Najděte účet služby Active Directory pro Azure AD Connect a resetování hesla tak, aby obsahoval maximálně 127 znaků. Otevřete **synchronizační služba** z **spustit** nabídky. Přejděte do **konektory** a najděte **konektor služby Active Directory**. Vyberte ho a potom vyberte **vlastnosti**. Vyhledejte **pověření** stránky a zadejte nové heslo. Vyberte **OK** zavřete stránku. |
| V posledním kroku procesu instalace Azure AD Connect zobrazí chybu oznamující, že tento zpětný zápis hesla se nepodařilo nakonfigurovat. <br> <br> V protokolu událostí aplikace Azure AD Connect obsahuje chybu 32009 s textem "Chyba při získávání ověření tokenu." | K této chybě dojde v těchto dvou případů: <br><ul><li>Zadali jste nesprávné heslo pro zadaný na začátku procesu instalace Azure AD Connect účet globálního správce.</li><li>Pokusili jste se použít federovaného uživatele pro účet globálního správce zadaný na začátku procesu instalace Azure AD Connect.</li></ul> Chcete-li tento problém vyřešit, zkontrolujte, že nepoužíváte federovaný účet globálního správce, které zadáte na začátku procesu instalace. Ujistěte se také, že zadané heslo je správné. |
| V protokolu událostí Azure AD Connect počítače obsahuje chyby 32002, která je vyvolána spuštěním PasswordResetService. <br> <br> Chyba čtení: "Chyba připojení k službě sběrnice. Zprostředkovatel tokenu se nepodařilo poskytnout token zabezpečení. " | Vaše místní prostředí není možné se připojit ke koncovému bodu Azure Service Bus v cloudu. Tato chyba je obvykle způsobena pravidlo brány firewall blokuje odchozí připojení na konkrétní port nebo webovou adresu. V tématu [připojení požadavky](./../connect/active-directory-aadconnect-prerequisites.md) Další informace. Po aktualizaci těchto pravidel restartovat počítač Azure AD Connect a zpětný zápis hesla by se měl spustit znovu funguje. |
| Po pracovní nějakou dobu federovaný, předávací ověřování nebo synchronizovat hodnoty hash hesla uživatele nelze resetovat vlastní hesla. | Ve výjimečných případech může selhat službu zpětný zápis hesla k restartování při restartování Azure AD Connect. V těchto případech nejdřív zkontrolujte, zda zpětný zápis hesla se zdá být povolené na místě. Můžete zkontrolovat pomocí Průvodce Azure AD Connect nebo prostředí PowerShell (viz předchozí část HowTos). Pokud se zobrazí tuto funkci povolit, zkuste povolit nebo zakázat funkci znovu buď prostřednictvím uživatelského rozhraní nebo Powershellu. Pokud to nepomůže, zkuste dokončení odinstalovat a znovu nainstalujte Azure AD Connect. |
| Federované, předávací ověřování a synchronizovat hodnoty hash hesla uživatele, kteří pokus o resetování hesla naleznete v chybu po pokusu o odeslání hesla. Chyba označuje, že došlo k chybě služby. <br ><br> Kromě tyto potíže odstranit během operace resetování hesla, může se zobrazit chyba, agent pro správu byl odepřen přístup v protokolech událostí místní. | Pokud se zobrazí tyto chyby v protokolu událostí, potvrďte, že účet agenta správy Active Directory (ADMA), který byl zadán v průvodci v době konfigurace musí mít potřebná oprávnění pro zpětný zápis hesla. <br> <br> Všimněte si, že po lze toto oprávnění, může trvat až jednu hodinu pro oprávnění k skapat dolů prostřednictvím `sdprop` úlohy na pozadí na řadiči domény (DC). <br> <br> Oprávnění pro resetování hesel k práci, musí být opatřena popisovač zabezpečení objektu uživatele, jejichž hesla je obnovena. Dokud nebude tato oprávnění se zobrazí na objekt uživatele, resetování hesla i nadále neúspěšné a zobrazí se zpráva o odepření přístupu. |
| Federované, předávací ověřování nebo synchronizovat hodnoty hash hesla uživatele, kteří pokus o resetování hesla, najdete v chybě po odeslání hesla. Chyba označuje, že došlo k chybě služby. <br> <br> Kromě tyto potíže odstranit během operace resetování hesla, může se zobrazit chybu v protokolech událostí ze služby Azure AD Connect indikující chybu "Objekt nebyl nalezen". | Tato chyba obvykle označuje, že synchronizační modul se nepodařilo najít objekt uživatele v prostoru konektoru služby Azure AD nebo propojené metaverse (MV) nebo objektu prostoru konektoru služby Azure AD. <br> <br> Chcete-li vyřešit tento problém, ujistěte se, že uživatel skutečně synchronizovaných z místní do služby Azure AD prostřednictvím aktuální instance služby Azure AD Connect a zkontrolovat stav objekty v prostor konektoru a více hodnot. Potvrďte, že objekt služby Active Directory Certificate Services (AD CS) je připojen k objektu MV prostřednictvím pravidlo "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federované, předávací ověřování nebo synchronizovat hodnoty hash hesla uživatele, kteří pokus o resetování hesla najdete v chybě po odeslání hesla. Chyba označuje, že došlo k chybě služby. <br> <br> Kromě tyto potíže odstranit během operace resetování hesla, může se zobrazit chybu v protokolech událostí ze služby Azure AD Connect, která označuje, že je chyba "Nalezeno více odpovídajících". | To znamená, že synchronizační modul zjistil, že objektu MV připojen k více než jeden objekt služby AD CS prostřednictvím "Microsoft.InfromADUserAccountEnabled.xxx". To znamená, zda má uživatel aktivovaný účet ve více než jedné doménové struktuře. Všimněte si, že tento scénář není podporován pro zpětný zápis hesla. |
| Operace s heslem se nezdaří s chybou konfigurace. V protokolu událostí aplikace obsahuje chybu Azure AD Connect 6329 s textem "0x8023061f (operace se nezdařila, protože na tento Agent pro správu není povolena synchronizace hesel)". | K této chybě dojde, že pokud konfigurace Azure AD Connect se změní přidat novou doménovou strukturu služby Active Directory (nebo na odebrat a znovu přidejte existující doménové struktury) po již byla povolena funkce zpětného zápisu hesla. Operace s heslem pro uživatele v těchto nedávno přidali selhání doménové struktury. Chcete-li problém vyřešit, zakažte a pak znovu povolit funkci zpětného zápisu hesla až dokončíte změny konfigurace doménové struktury. |

## <a name="password-writeback-event-log-error-codes"></a>Kódy chyb protokolu událostí zpětný zápis hesla

Osvědčeným postupem při řešení problémů s zpětný zápis hesla je kontrola protokolu událostí aplikace na počítači pro Azure AD Connect. Tento protokol událostí neobsahuje události ze dvou zdrojů, které vás zajímají pro zpětný zápis hesla. Zdroj PasswordResetService popisuje operace a problémy související s operaci zpětného zápisu hesla. Zdroj ADSync popisuje operace a problémy související s nastavení hesla v prostředí služby Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Pokud je zdroj události ADSync

| Kód | Název nebo zprávy | Popis |
| --- | --- | --- |
| 6329 | NICHŽ: MMS(4924) 0x80230619: "omezení zabrání heslo se změnil na aktuální zadanému." | K této události dojde, když se pokusí nastavení hesla na vaše místní adresář, který nesplňuje stáří hesla, historie, složitost nebo filtrování požadavky domény službu zpětný zápis hesla. <br> <br> Pokud máte nejmenší stáří hesla a v nedávné době změnili heslo v rámci dané okno času, nejste moct změnit heslo znovu, dokud nedosáhne zadané stáří ve vaší doméně. Pro účely testování by měla minimální věk nastavena na hodnotu 0. <br> <br> Pokud máte požadavky na heslo historie povolena, pak je nutné vybrat heslo, které nebyl použit za posledních *N* krát, kde *N* je nastavení historie hesel. Pokud vyberete hesla, která byla použita v posledních *N* krát, pak můžete zobrazit informace o selhání v tomto případě. Pro účely testování historii hesel je třeba nastavit na hodnotu 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich se vynucují, když se uživatel pokusí o změnu nebo resetování hesla. <br> <br> Pokud máte povolené filtry heslo a uživatel vybere heslo, které splňují kritéria filtrování, pak resetovat nebo změnit operace selže. |
| 6329 | MMS(3040): admaexport.cpp(2837): server neobsahuje ovládacího prvku LDAP heslo zásad. | K tomuto problému dochází, pokud LDAP_SERVER_POLICY_HINTS_OID řízení (1.2.840.113556.1.4.2066) není povolena na řadiče domény. Pokud chcete používat funkce zpětného zápisu hesla, je nutné povolit ovládacího prvku. Uděláte to tak, musí být řadiče domény na Windows Server 2008 (s nejnovější SP) nebo novější. Pokud jsou vaše řadiče domény v 2008 (pre-R2), tak také musíte použít opravu hotfix [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Synchronizační modul vrácena chyba hr = 80230402, zpráva = pokus o získání objektu se nezdařilo, protože existují duplicitní položky s stejné ukotvení. | K této chybě dojde, pokud stejné ID uživatele je povoleno ve více doménách. Příkladem je, pokud se synchronizuje doménových struktur účtů a prostředků a mít stejné ID uživatele existuje a povolené v každé doménové struktuře. <br> <br> Této chybě může dojít také jako alias nebo UPN a dva uživatelé sdílet tento stejný atribut ukotvení používáte atribut jedinečný ukotvení. <br> <br> Chcete-li vyřešit tento problém, ujistěte se, že nemáte žádné duplicitní uživatele v rámci domény a že používáte atribut ukotvení jedinečný pro každého uživatele. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Pokud je zdroj události PasswordResetService

| Kód | Název nebo zprávy | Popis |
| --- | --- | --- |
| 31001 | PasswordResetStart | Tato událost označuje, že místní služby zjistil požadavek federované, předávací ověřování, nebo synchronizovat hodnoty hash hesla uživatele, který pochází z cloudu pro vytvoření nového hesla. Tato událost je první událost v každé operace zpětný zápis resetování hesla. |
| 31002 | PasswordResetSuccess | Tato událost označuje, že vybraný uživatel nové heslo během operace obnovení hesla. Jsme určili, zda toto heslo splňuje požadavky na podnikové heslo. Hesla byla úspěšně zapsána zpět do místního prostředí služby Active Directory. |
| 31003 | PasswordResetFail | Tato událost označuje, že vybraný uživatel heslo a heslo byly úspěšně přijaty do místního prostředí. Ale když jsme se pokusili nastavení hesla v místním prostředí služby Active Directory, došlo k selhání. Této chybě může dojít z několika důvodů: <br><ul><li>Pro uživatele heslo nesplňuje stáří, historie, složitost, nebo filtrovat požadavky pro doménu. Chcete-li vyřešit tento problém, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla pro uživatelský účet v.</li><li>Uživatelský účet je v chráněné skupině, jako je například skupiny pro správce domény nebo enterprise, která zakáže heslo množinové operace.</li></ul>|
| 31004 | OnboardingEventStart | K této události dojde, pokud zapnete zpětný zápis hesla s Azure AD Connect a jsme registrační spustili vaší organizace k webové službě zpětný zápis hesla. |
| 31005 | OnboardingEventSuccess | Tato událost označuje, že do registračního procesu bylo úspěšné a že schopností zpětný zápis hesla je připravený k použití. |
| 31006 | ChangePasswordStart | Tato událost označuje, že místní služby byla nalezena žádost o změnu hesla pro federované, předávací ověřování, nebo synchronizovat hodnoty hash hesla uživatele, který pochází z cloudu. Tato událost je první událost v každé operace zpětného zápisu změny hesla. |
| 31007 | ChangePasswordSuccess | Tato událost označuje, že uživatel vybrané nové heslo během operace změny hesla zjistili jsme, že heslo splňuje požadavky podnikové heslo a že hesla byla úspěšně zapsána zpět do místního prostředí služby Active Directory. |
| 31008 | ChangePasswordFail | Tato událost označuje, že vybraný uživatel heslo a že heslo byly úspěšně přijaty do místního prostředí, ale když jsme se pokusili nastavení hesla v místním prostředí služby Active Directory, došlo k selhání. Této chybě může dojít z několika důvodů: <br><ul><li>Pro uživatele heslo nesplňuje stáří, historie, složitost, nebo filtrovat požadavky pro doménu. Chcete-li vyřešit tento problém, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla pro uživatelský účet v.</li><li>Účet uživatele je v chráněné skupiny, jako je například domény nebo enterprise admins, která zakáže heslo množinové operace.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Místní služba zjištěna, že žádost pro federované, předávací ověřování, nebo synchronizovat hodnoty hash hesla uživatele pocházející z správce jménem uživatele pro resetování hesla. Tato událost je první událost v každé operace zpětný zápis resetování hesla, které je iniciováno správcem. |
| 31010 | ResetUserPasswordByAdminSuccess | Správce vybrat nové heslo během operace správy spouštěná resetování hesla. Jsme určili, zda toto heslo splňuje požadavky na podnikové heslo. Hesla byla úspěšně zapsána zpět do místního prostředí služby Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Správce vybrat heslo jménem uživatele. Heslo byly úspěšně přijaty do místního prostředí. Ale když jsme se pokusili nastavení hesla v místním prostředí služby Active Directory, došlo k selhání. Této chybě může dojít z několika důvodů: <br><ul><li>Pro uživatele heslo nesplňuje stáří, historie, složitost, nebo filtrovat požadavky pro doménu. Zkuste nové heslo k tomuto problému.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla pro uživatelský účet v.</li><li>Účet uživatele je v chráněné skupiny, jako je například domény nebo enterprise admins, která zakáže heslo množinové operace.</li></ul>  |
| 31012 | OffboardingEventStart | Tato událost dochází, pokud zakážete zpětný zápis hesla s Azure AD Connect a určuje, že jsme spuštěné odpojení vaší organizace k webové službě zpětný zápis hesla. |
| 31013| OffboardingEventSuccess| Tato událost označuje, že proces odpojení bylo úspěšné a že schopností zpětný zápis hesla se úspěšně vypnul. |
| 31014| OffboardingEventFail| Tato událost označuje, že proces odpojení nebyla úspěšná. Může to být kvůli chybě oprávnění na cloudu nebo na místní účet správce zadaný během konfigurace. Chyba může vyskytnout, pokud se pokoušíte použít globální správce federované cloudu při zakazování zpětný zápis hesla. Chcete-li tento problém vyřešit, zkontrolujte příslušná oprávnění pro správu a ujistěte se, že nepoužíváte federovaný účet při konfiguraci funkce zpětného zápisu hesla.|
| 31015| WriteBackServiceStarted| Tato událost označuje, že služba zpětný zápis hesla byla úspěšně spuštěna. Je připraven k přijímání požadavků správy hesel z cloudu.|
| 31016| WriteBackServiceStopped| Tato událost označuje, že služba zpětný zápis hesla byla zastavena. Všechny žádosti správu hesel z cloudu nebude úspěšná.|
| 31017| AuthTokenSuccess| Tato událost označuje, že jsme úspěšně načíst autorizační token pro globální správce. zadaný při instalaci Azure AD Connect k zahájení procesu odpojení nebo registrace.|
| 31018| KeyPairCreationSuccess| Tato událost označuje, že jsme vytvořili úspěšně heslo šifrovacího klíče. Tento klíč se používá k šifrování hesla z cloudu k odeslání do místního prostředí.|
| 32000| Neznámé chyby| Tato událost označuje, že došlo k neznámé chybě během operace správy heslo. Podívejte se na text výjimky v události pro další podrobnosti. Pokud máte problémy, zkuste zakázáním a následným opětovným povolením zpětný zápis hesla. Pokud to nepomůže, obsahovat kopii protokolu událostí společně s sledování zevnitř zadané ID na pracovníka podpory.|
| 32001| ServiceError| Tato událost vyplývá, že došlo k chybě připojení k cloudu heslo pro resetování služby. K této chybě obvykle dojde při místní služby se nemohl připojit k resetování hesla webové službě.|
| 32002| ServiceBusError| Tato událost vyplývá, že došlo k chybě připojení k instanci služby Service Bus vašeho klienta. To může nastat, když jste blokování odchozí připojení v místním prostředí. Zkontrolujte bránu firewall, aby Ujistěte se, že povolíte připojení přes TCP 443 a na https://ssprsbprodncu-sb.accesscontrol.windows.net/a potom akci opakujte. Pokud potíže potrvají, zkuste zakázáním a následným opětovným povolením zpětný zápis hesla.|
| 32003| InPutValidationError| Tato událost znamená, že vstup naše webového rozhraní API služby byla neplatná. Opakujte operaci znovu.|
| 32004| DecryptionError| Tato událost označuje, že došlo k chybě při dešifrování hesla, které byly přijaty z cloudu. Může to být kvůli neshodě dešifrovací klíče mezi cloudové služby a v místním prostředí. Chcete-li vyřešit tento problém, zakažte a znovu povolit zpětný zápis hesla v místním prostředí.|
| 32005| ConfigurationError| Během registrace uložíme informace specifické pro klienta v konfiguračním souboru v místním prostředí. Tato událost označuje, že došlo k chybě při ukládání tohoto souboru nebo, při spuštění služby, že došlo chybě při čtení souboru. Chcete-li tento problém vyřešit, zkuste zakázáním a následným opětovným povolením zpětný zápis hesla můžete vynutit přepisování konfiguračního souboru.|
| 32007| OnBoardingConfigUpdateError| Během registrace budeme posílat data z cloudu do místní službu resetování hesla. Tato data se pak zapisovány do souboru v paměti před odesláním do služby sync bezpečně uložena na disku. Tato událost označuje, že došlo k potížím s zápis nebo aktualizaci dat v paměti. Chcete-li tento problém vyřešit, zkuste zakázáním a následným opětovným povolením zpětný zápis hesla k vynucení přepsání tohoto souboru konfigurace.|
| 32008| ValidationError| Tato událost označuje, že jsme obdržel neplatnou odpověď od resetování hesla webovou službu. Chcete-li tento problém vyřešit, zkuste zakázáním a následným opětovným povolením zpětný zápis hesla.|
| 32009| AuthTokenError| Tato událost znamená, že jsme nelze získat o autorizační token pro účet globálního správce zadaný při instalaci Azure AD Connect. Tato chyba může být způsobeno chybné uživatelské jméno nebo heslo zadané pro účet globálního správce. Tato chyba může vyskytnout, pokud je Federovaná zadaný účet globálního správce. Chcete-li tento problém vyřešit, znovu spustit konfiguraci s správné uživatelské jméno a heslo a zajistěte, aby správce spravovaný účet (jenom pro cloud nebo synchronizovat hesla).|
| 32010| CryptoError| Tato událost ukazuje na došlo k chybě generování šifrovacího klíče hesla nebo dešifrovací heslo, které dorazí z cloudové služby. Tuto chybu pravděpodobně znamená problém s vaším prostředím. Podívejte se na podrobnosti o další informace o tom, jak vyřešit tento problém protokolu událostí. Můžete také zkusit zakázáním a následným opětovným povolením služby zpětný zápis hesla.|
| 32011| OnBoardingServiceError| Tato událost označuje, že místní služby nelze správně komunikují resetování hesla webovou službu k zahájení procesu registrace. Tato situace může nastat v důsledku pravidlo brány firewall nebo pokud je k potížím při získávání tokenu ověření pro vašeho klienta. Chcete-li tento problém vyřešit, zkontrolujte, že nejsou blokuje odchozí připojení přes TCP 443 a TCP 9350-9354 nebo k https://ssprsbprodncu-sb.accesscontrol.windows.net/. Také zajistěte, aby účet správce Azure AD, kterou používáte zaváděním není federovaný.|
| 32013| OffBoardingError| Tato událost označuje, že místní služby nelze správně komunikovat s resetování hesla webovou službu na zahájení procesu odpojení. Tato situace může nastat v důsledku pravidlo brány firewall nebo pokud je k potížím při získávání tokenu autorizaci pro vašeho klienta. Chcete-li tento problém vyřešit, zkontrolujte, že nejsou blokuje odchozí připojení přes 443 nebo na https://ssprsbprodncu-sb.accesscontrol.windows.net/, a že účet správce Azure Active Directory, který používáte k offboard není federovaný.|
| 32014| ServiceBusWarning| Tato událost naznačuje, když jsme se můžete znovu pokusit připojit k instanci služby Service Bus vašeho klienta. Za normálních podmínek to by nemělo být žádný problém, ale pokud se zobrazí tato událost mnohokrát, zkuste zkontrolovat síťové připojení k Service Bus, zvlášť pokud je připojení s vysokou latencí nebo malou šířkou pásma.|
| 32015| ReportServiceHealthError| Chcete-li sledovat stav služby zpětný zápis hesla, odešleme dat prezenčního signálu naše resetování hesla webové služby každých pět minut. Tato událost označuje, že došlo k chybě při odesílání této informace o stavu zpět k webové službě cloud. Tato informace o stavu nezahrnuje k objektu osobní údaje (OII) nebo data identifikovatelné osobní údaje (PII) a je čistě prezenčního signálu a služby na úrovni basic statistiky tak, aby můžeme poskytnout informace o stavu služby v cloudu.|
| 33001| ADUnKnownError| Tato událost ukazuje na to, že došlo k neznámé chybě služby Active Directory. Zkontrolujte události ze zdroje ADSync pro další informace v protokolu událostí serveru Azure AD Connect.|
| 33002| ADUserNotFoundError| Tato událost označuje, že uživatel, který se pokouší resetovat nebo změnit heslo nebyl nalezen v místním adresáři. Této chybě může dojít, když uživatel byl odstraněný místní, ale není v cloudu. Této chybě může také dojít, pokud došlo k potížím se synchronizací. Zkontrolujte protokoly synchronizace a posledních několik synchronizace spustit podrobnosti o další informace.|
| 33003| ADMutliMatchError| Když pro vytvoření nového hesla nebo žádost o změnu pochází z cloudu, použijeme ukotvení cloudu zadaný během procesu instalace služby Azure AD Connect určit, jak propojit tento požadavek zpět na uživatele v místním prostředí. Tato událost označuje, že jsme nalezena dva uživatelé ve vaší místní adresář se stejným atributem ukotvení cloudu. Zkontrolujte protokoly synchronizace a posledních několik synchronizace spustit podrobnosti o další informace.|
| 33004| ADPermissionsError| Tato událost značí, že účet služby agenta správy Active Directory (ADMA) nemá příslušná oprávnění na dotyčném nastavit nové heslo účtu. Zajistěte, aby ADMA účet v doménové struktuře uživatele má resetování a změna hesla oprávnění u všech objektů v doménové struktuře. Další informace o tom, jak nastavit oprávnění, najdete v kroku 4: nastavení příslušných oprávnění služby Active Directory.|
| 33005| ADUserAccountDisabled| Tato událost označuje, že jsme se pokusili resetovat nebo změnit heslo pro účet, který byl zakázané místní. Zapnout účet a operaci opakujte.|
| 33006| ADUserAccountLockedOut| Tato událost označuje, že jsme se pokusili resetovat nebo změnit heslo pro účet, který byl uzamčen na místě. Uzamčení může dojít, když má uživatel o změnu nebo resetování hesla operaci příliš mnohokrát v krátké době. Odemknout účet a operaci opakujte.|
| 33007| ADUserIncorrectPassword| Tato událost označuje, zda uživatel zadal nesprávný aktuální heslo při provádění heslo změnit operaci. Zadejte správné aktuální heslo a zkuste to znovu.|
| 33008| ADPasswordPolicyError| K této události dojde, když se pokusí nastavení hesla na vaše místní adresář, který nesplňuje stáří hesla, historie, složitost nebo filtrování požadavky domény službu zpětný zápis hesla. <br> <br> Pokud máte nejmenší stáří hesla a v nedávné době změnili heslo v rámci dané okno času, nejste moct změnit heslo znovu, dokud nedosáhne zadané stáří ve vaší doméně. Pro účely testování by měla minimální věk nastavena na hodnotu 0. <br> <br> Pokud máte požadavky na heslo historie povolena, pak je nutné vybrat heslo, které nebyl použit za posledních *N* krát, kde *N* je nastavení historie hesel. Pokud vyberete hesla, která byla použita v posledních *N* krát, pak můžete zobrazit informace o selhání v tomto případě. Pro účely testování historii hesel je třeba nastavit na hodnotu 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich se vynucují, když se uživatel pokusí o změnu nebo resetování hesla. <br> <br> Pokud máte povolené filtry heslo a uživatel vybere heslo, které splňují kritéria filtrování, pak resetovat nebo změnit operace selže.|
| 33009| ADConfigurationError| Tato událost označuje, že byl problém zápis, heslo zpět do místního adresáře z důvodu konfigurace problém se službou Active Directory. Zkontrolujte protokol událostí aplikace na Azure AD Connect počítač pro zprávy ze služby ADSync Další informace, na kterém došlo k chybě.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Řešení potíží s připojením zpětný zápis hesla

Pokud dojde k přerušení poskytování služeb s komponentou zpětný zápis hesla služby Azure AD Connect, zde jsou některé rychlé kroky, které můžete provést k vyřešení tohoto problému:

* [Zkontrolujte připojení k síti](#confirm-network-connectivity)
* [Restartujte službu Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Zakázat a znovu povolit funkci zpětného zápisu hesla](#disable-and-re-enable-the-password-writeback-feature)
* [Nainstalovat nejnovější verzi Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Řešení potíží s zpětný zápis hesla](#troubleshoot-password-writeback)

Obecně platí Chcete-li obnovit služby nejvíce rychlé způsobem, doporučujeme, můžete provést tyto kroky v pořadí, jak jsme vysvětlili výše.

### <a name="confirm-network-connectivity"></a>Zkontrolujte připojení k síti

Nejběžnější bodem selhání je, aby brána firewall a nebo jsou nesprávně nakonfigurované porty proxy a časové limity nečinnosti. 

Azure AD Connect verze 1.1.443.0 a vyšší, můžete potřebovat HTTPS odchozí přístup k následující:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Pro další členitosti odkazovat aktualizovaný seznam [Microsoft Azure Datacenter rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653) aktualizovat každou středu a umístí platit následující pondělí.

Další informace najdete v tématu požadavky připojení [požadavky pro Azure AD Connect](./../connect/active-directory-aadconnect-prerequisites.md) článku.



### <a name="restart-the-azure-ad-connect-sync-service"></a>Restartujte službu Azure AD Connect Sync

Chcete-li vyřešit potíže s připojením k nebo jiné přechodné problémy se službou, restartujte službu Azure AD Connect Sync:

   1. Jako správce, vyberte **spustit** na serveru se službou Azure AD Connect.
   2. Zadejte **services.msc** v pole hledání a vyberte **Enter**.
   3. Vyhledejte **Microsoft Azure AD Sync** položku.
   4. Klikněte pravým tlačítkem na položku služby, vyberte **restartujte**a potom počkejte na dokončení operace.

   ![Restartujte službu Azure AD Sync][Service restart]

Tyto kroky znovu navázat připojení s cloudovou službou a vyřešte všechny přerušení, které mohou vykazovat. Pokud restartování služby ADSync váš problém nevyřeší, doporučujeme pokusu zakázat a znovu povolit funkci zpětného zápisu hesla.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Zakázat a znovu povolit funkci zpětného zápisu hesla

Chcete-li vyřešit problémy s připojením, zakažte a znovu povolit funkci zpětného zápisu hesla:

   1. Jako správce otevřete Průvodce konfigurace Azure AD Connect.
   2. V **připojit k Azure AD**, zadejte přihlašovací údaje Azure AD globálního správce.
   3. V **připojit ke službě AD DS**, zadejte své přihlašovací údaje Správce služby AD Domain Services.
   4. V **Jednoznačná identifikace uživatelů**, vyberte **Další** tlačítko.
   5. V **volitelné funkce**, zrušte **zpětný zápis hesla** zaškrtávací políčko.
   6. Vyberte **Další** prostřednictvím zbývající stránky dialogové okno beze změny jakéhokoli až na **připraveno ke konfiguraci** stránky.
   7. Ujistěte se, že **připraveno ke konfiguraci stránky** ukazuje **zpětný zápis hesla** možnost jako **zakázána** a pak vyberte zeleným **konfigurace** tlačítko potvrzení změny.
   8. V **dokončeno**, zrušte **synchronizovat** a pak vyberte možnost **Dokončit** zavřete průvodce.
   9. Otevřete Průvodce konfigurace Azure AD Connect.
   10. Opakujte kroky 2-8, s výjimkou toho, ujistěte se, vyberete **zpětný zápis hesla** možnost **volitelné funkce** stránky znovu zapnout službu.

Tyto kroky znovu navázat připojení s naší cloudové služby a vyřešte všechny přerušení, které mohou vykazovat.

Pokud zakázáním a následným opětovným povolením funkce zpětného zápisu hesla váš problém nevyřeší, doporučujeme znovu nainstalujte Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Nainstalovat nejnovější verzi Azure AD Connect

Konfigurace a problémy s připojením mezi naší cloudové služby a prostředí místní služby Active Directory může vyřešit opětovné instalace Azure AD Connect.

Doporučujeme, abyste tento krok proveďte jenom po pokusíte první dva kroky popsaných výše.

> [!WARNING]
> Pokud jste upravili pravidla synchronizace se na pole *před pokračováním upgradu je zálohování a poté ručně znovu je po dokončení.*

   1. Stáhnout nejnovější verzi služby Azure AD Connect z [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Protože jste již nainstalovali Azure AD Connect, musíte provést upgrade na místě k aktualizaci instalace Azure AD Connect na nejnovější verzi.
   3. Spuštění staženého balíčku a postupujte podle na obrazovce pokyny aktualizujte počítač Azure AD Connect.

V předchozím postupu by měl znovu navázat připojení s naší cloudové služby a vyřešte všechny přerušení, které mohou vykazovat.

Pokud instalaci nejnovější verze server Azure AD Connect váš problém nevyřeší, doporučujeme vyzkoušet zakázáním a následným opětovným povolením zpětného zápisu hesla v posledním kroku po instalaci nejnovější verze.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Ověřte, že Azure AD Connect má požadovaná oprávnění pro zpětný zápis hesla

Azure AD Connect vyžaduje služby Active Directory **resetovat heslo** oprávnění k provedení zpětný zápis hesla. Pokud chcete zjistit, jestli má Azure AD Connect požadované oprávnění pro danou místní uživatelský účet služby Active Directory, můžete použít funkci Windows efektivní oprávnění:

   1. Přihlaste se k serveru Azure AD Connect a spustit **Synchronization Service Manager** výběrem **spustit** > **synchronizační služba**.
   2. V části **konektory** vyberte místní **Active Directory Domain Services** konektoru a pak vyberte **vlastnosti**.  

   ![Efektivní oprávnění – krok 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. V místním okně, vyberte **připojit k doménové struktuře služby Active Directory** a poznamenejte si **uživatelské jméno** vlastnost. Tato vlastnost je účet služby AD DS použít k provedení synchronizace adresářů přes Azure AD Connect. Pro Azure AD Connect provést zpětný zápis hesla musí mít účet služby AD DS resetovat heslo oprávnění.  
   
   ![Efektivní oprávnění – krok 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Přihlaste se k řadiči domény místní a spustit **Active Directory Users and Computers** aplikace.
   5. Vyberte **zobrazení** a zajistěte, aby **upřesňující funkce** je povolena možnost.  
   
   ![Efektivní oprávnění – krok 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Vyhledejte uživatelský účet služby Active Directory, který chcete ověřit. Klikněte pravým tlačítkem na název účtu a vyberte **vlastnosti**.  
   
   ![Efektivní oprávnění – krok 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. V místním okně, přejděte na **zabezpečení** a vyberte **Upřesnit**.  
   
   ![Efektivní oprávnění - krok 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. V **Upřesnit nastavení zabezpečení pro správce** místní okno, přejděte na **platný přístup** kartě.
   9. Vyberte **vyberte uživatele**, vyberte účet služby AD DS bude použit službou Azure AD Connect (viz krok 3) a potom vyberte **zobrazit platný přístup**.  
   
   ![Efektivní oprávnění - kroku 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Posuňte se dolů a vyhledejte **resetovat heslo**. Pokud má položka zaškrtnutí, účet služby AD DS, nemá oprávnění resetovat heslo vybraného uživatelského účtu služby Active Directory.  
   
   ![Efektivní oprávnění - krok 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóra pro Azure AD

Pokud máte obecný dotaz týkající se Azure AD a resetování hesla pomocí samoobslužné služby, můžete požádat komunitou o pomoc na [fóra Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členové komunity služby zahrnují engineers, správce produktu, MVP a ostatními Odborníci v oblasti IT.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu společnosti Microsoft

Pokud nemůžete najít odpověď na problém, jsou vždy k dispozici další vám pomůže našimi týmy podpory.

K usnadnění správně, požádáme zadejte množství podrobností nejdříve při otevírání případu. Tyto podrobnosti patří:

* **Obecný popis chyby**: co je chyba? Jak se chování, které bylo zaznamenali? Jak jsme chybu reprodukovat? Nejdříve zadejte množství podrobností.
* **Stránka**: jaké stránky jste byli na když jste si všimli chybu? Zahrnují adresu URL, pokud jste moct a snímek obrazovky stránky.
* **Podpora kód**: co se podpora kód, který byl vygenerován při uživatele viděli chyba?
    * Tento kód najdete tomu chybu reprodukovat a pak vyberte **podporují kód** odkaz v dolní části obrazovky a odesílat pracovník podpory identifikátor GUID, který výsledky.

    ![Podpora kód v dolní části obrazovky][Support code]

    * Pokud jste na stránce bez podpory kódu v dolní části, vyberte F12 a vyhledejte SID a CID a odesílat tyto dva výsledky pracovníkovi podpory.
* **Datum, čas a časové pásmo**: zahrnují přesné datum a čas *s časovým pásmem* , k níž došlo k chybě.
* **ID uživatele**: kdo byl uživatel, který viděli chybu? Příkladem je *user@contoso.com*.
    * Je to federovaného uživatele?
    * Je to předávací ověřování uživatele?
    * Je to synchronizovat hodnoty hash hesla uživatele?
    * Je to čistě cloudové uživatele?
* **Licencování**: má uživatel přiřazenou licenci Azure AD Premium nebo Azure AD Basic?
* **Protokol událostí aplikace**: Pokud používáte zpětný zápis hesla a chyba je ve vaší místní infrastruktuře, zahrnují komprimované kopie protokolu událostí aplikací ze serveru Azure AD Connect.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Restartujte službu Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kód podpory se nachází v pravém dolním rohu okna"

## <a name="next-steps"></a>Další postup

Následující články poskytují další informace o prostřednictvím služby Azure AD pro vytvoření nového hesla:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
