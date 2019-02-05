---
title: Samoobslužné resetování hesla řešení problémů s Azure Active Directory
description: Řešení problémů s Azure AD samoobslužné resetování hesla
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.openlocfilehash: 8176575236677ed8a1468bffa71d722c6e0e7092
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55694393"
---
# <a name="troubleshoot-self-service-password-reset"></a>Řešení potíží s resetováním hesla pomocí samoobslužné služby

Dochází k potížím s Azure Active Directory (Azure AD) samoobslužné resetování hesla (SSPR) Informace, které následuje můžete využít zase fungovaly.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Řešení potíží s chybami resetování hesla pomocí samoobslužné služby, které může uživatel zobrazit

| Chyba | Podrobnosti | Technické podrobnosti |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože správce pro vaši organizaci resetování hesla zakázal. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby tuto funkci povolil. Další informace najdete v tématu [Nápověda k zapomenutému heslu Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Zjistili jsme, že resetování hesla není povolená vaším správcem. Obraťte se na správce a požádejte ho o povolení pro vaši organizaci resetování hesla. |
| WritebackNotEnabled = 10 |Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože váš správce nepovolil vyžadovanou službu pro vaši organizaci. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o kontrolu konfigurace vaší organizace. Další informace o této potřebné službě, naleznete v tématu [konfigurací zpětného zápisu hesla](howto-sspr-writeback.md). | SSPR_0010: Zjistili jsme, že tento zpětný zápis hesla nebyl povolen. Obraťte se na správce a požádejte ho o povolení zpětného zápisu hesla. |
| SsprNotEnabledInUserPolicy = 11 | Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože správce nenakonfiguroval resetování hesla pro vaši organizaci. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby nakonfiguroval resetování hesla. Další informace o heslo resetovat konfiguraci najdete v tématu [rychlý start: Azure AD samoobslužné resetování hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Vaše organizace nedefinovala zásady resetování hesla. Obraťte se na správce a požádejte ho, aby definoval zásady resetování hesla. |
| UserNotLicensed = 12 | Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože chybí licence z vaší organizace požadované. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o kontrolu přiřazení licencí. Další informace o licencování najdete v tématu [licenčním požadavkům pro hesla pomocí samoobslužné služby Azure AD resetování](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Vaše organizace nemá požadované licence, které jsou nezbytné k provedení resetování hesla. Obraťte se na správce a požádejte ho o kontrolu přiřazení licencí. |
| UserNotMemberOfScopedAccessGroup = 13 | Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože správce nenakonfiguroval váš účet se má používat resetování hesla. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o konfiguraci účtu pro resetování hesla. Další informace o konfiguraci účtu pro resetování hesla najdete v tématu [zavedení resetování hesla pro uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Nejste členem skupiny povolen pro resetování hesla. Obraťte se na správce a požádejte přidán do skupiny. |
| UserNotProperlyConfigured = 14 | Je nám líto, že v tuto chvíli nemůžete resetovat heslo, protože chybí požadované informace z vašeho účtu. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Kontaktujte správce a požádejte ho, aby vám heslo resetoval. Až budete mít přístup ke svému účtu znovu, budete muset zaregistrovat potřebné informace. K registraci informací, postupujte podle kroků v [zaregistrovat pro resetování hesla pomocí samoobslužné služby](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) článku. | SSPR_0014: K resetování hesla je potřeba další bezpečnostní údaje. Chcete-li pokračovat, obraťte se na správce a požádejte ho o resetování hesla. Jakmile budete mít přístup ke svému účtu, můžete zaregistrovat další bezpečnostní údaje na adrese https://aka.ms/ssprsetup. Váš správce můžete přidat další bezpečnostní údaje k vašemu účtu pomocí kroků v [sadu a čtení ověřovacích dat pro resetování hesla](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Je nám líto, že nemůžeme resetovat heslo v tuto chvíli kvůli problému s konfigurací resetování hesla vaší organizace. Neexistuje žádná další akce, které můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o prověření. Další informace o potenciální problém, naleznete v tématu [řešení potíží se zpětným zápisem hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nepovedlo se nám k resetování hesla z důvodu chyby v konfiguraci vaší místní. Obraťte se na správce a požádejte ho o prověření. |
| OnPremisesConnectivityError = 30 | Je nám líto, že nemůžeme resetovat heslo v tuto chvíli kvůli problémům s připojením k vaší organizaci. Neexistuje žádná akce se má provést hned teď ale problémem může být vyřešen, pokud se pokusíte znovu později. Pokud se problém nevyřeší, obraťte se na správce a požádejte ho o prověření. Další informace o problémech s připojením najdete v tématu [řešení potíží s připojením zpětného zápisu hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nemůžeme resetovat heslo kvůli špatnému připojení k místním prostředí. Obraťte se na správce a požádejte ho o prověření.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Řešení potíží s konfigurací resetování hesla na webu Azure Portal

| Chyba | Řešení |
| --- | --- |
| Nevidím **resetování hesla** části Azure AD na webu Azure Portal. | To může nastat, pokud nemáte Azure AD Premium nebo Basic licence přiřazené k provedení operace správce. <br> <br> Přiřadíte licenci na dotyčný účet správce. Můžete postupovat podle kroků v [přiřadit zkontrolujte a vyřešte problémy s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) článku.|
| Nevidím možnost konkrétní konfiguraci. | Mnoho prvků uživatelského rozhraní jsou skryté, dokud se v případě potřeby zapíná. Zkuste povolit všechny možnosti, které chcete zobrazit. |
| Nevidím **integrace v místním** kartu. | Tato možnost pouze se zobrazí, pokud jste stáhli Azure AD Connect a nakonfigurovali zpětný zápis hesla. Další informace najdete v tématu [Začínáme s Azure AD Connect s použitím expresního nastavení](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Řešení potíží s vytváření sestav resetování hesel

| Chyba | Řešení |
| --- | --- |
| Nevidím žádné heslo správu typy aktivit **samoobslužné správy hesel** kategorii událostí auditu. | To může nastat, pokud nemáte Azure AD Premium nebo Basic licence přiřazené k provedení operace správce. <br> <br> Tento problém lze vyřešit tak, že přiřadíte licence dotyčný účet správce. Postupujte podle pokynů [přiřadit zkontrolujte a vyřešte problémy s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) článku. |
| Registrace uživatele zobrazit více než jednou. | Nyní když se uživatel zaregistruje, jsme protokolu každé jednotlivé část dat, který je registrovaný jako samostatná událost. <br> <br> Pokud chcete tato data agregovat a větší flexibilitu v tom, jak můžete zobrazit, můžete stáhnout sestavu a otevřete data jako kontingenční tabulky v Excelu.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Řešení potíží s portálem registrace pro resetování hesla

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. **Váš správce nepovolil tuto funkci používat.** | Přepínač **samoobslužné resetování hesla povoleno** příznak **vybrané** nebo **všechny** a pak vyberte **Uložit**. |
| Uživatel nemá k Azure AD Premium nebo Basic přiřazenou licenci. **Váš správce nepovolil tuto funkci používat.** | To může nastat, pokud nemáte Azure AD Premium nebo Basic licence přiřazené k provedení operace správce. <br> <br> Tento problém lze vyřešit tak, že přiřadíte licence dotyčný účet správce. Postupujte podle pokynů [přiřadit zkontrolujte a vyřešte problémy s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) článku.|
| Dojde k chybě zpracování žádosti. | Příčinou může být mnoho problémů, ale obecně tato chyba je způsobená výpadku služeb nebo o problém s konfigurací. Pokud se zobrazí tato chyba, a to má vliv na vaši firmu, kontaktujte podporu Microsoftu o další pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Řešení potíží s portálem pro resetování hesla

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. | Přepínač **samoobslužné resetování hesla povoleno** příznak **vybrané** nebo **všechny** a pak vyberte **Uložit**. |
| Uživatel nemá k Azure AD Premium nebo Basic přiřazenou licenci. | To může nastat, pokud nemáte Azure AD Premium nebo Basic licence přiřazené k provedení operace správce. <br> <br> Tento problém lze vyřešit, je-li přiřadit licenci na dotyčný účet správce. Postupujte podle pokynů [přiřadit zkontrolujte a vyřešte problémy s licencemi](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) článku. |
| Adresář je povolen pro resetování hesla, ale uživatel má chybějící nebo nesprávně ověřovacích informací. | Než budete pokračovat, ujistěte se, že tento uživatel má správně formátovaných kontaktní údaje na soubor v adresáři. Další informace najdete v tématu [obnovit Data používaná hesla pomocí samoobslužné služby Azure AD](howto-sspr-authenticationdata.md). |
| Adresář je povolen pro resetování hesla, ale uživatel má pouze jednu část kontaktní údaje na soubor, když je zásada nastavená tak, aby vyžadovala dvě metody ověřování. | Než budete pokračovat, ujistěte se, že uživatel má alespoň dva způsoby kontaktování správně nakonfigurovaný. Příklad dochází k danému o číslo mobilního telefonu *a* Telefon do kanceláře. |
| Adresář je povolen pro resetování hesla a uživatel správně nakonfigurovaný, ale uživatel není schopen kontaktovat. | K tomu může být výsledek k dočasné chybě nebo pokud je nesprávná kontaktní údaje, které nelze správně zjistit. <br> <br> Pokud uživatel čeká 10 sekund, "opakujte" a "kontaktujte správce" odkazy jsou zobrazeny. Pokud si uživatel vybere "opakujte", zopakuje pokus o volání. Pokud si uživatel vybere "kontaktujte správce", odešle e-mail formuláře na jejich správcům požadujícím mají být provedeny pro tento uživatelský účet resetování hesla. |
| Uživatel dostane nikdy resetování hesla služby SMS nebo telefonní hovor. | To může být výsledkem poškozený telefonní číslo v adresáři. Ujistěte se, že je telefonní číslo ve formátu "RCS xxxyyyzzzzXeeee plus (+). <br> <br> Resetování hesla nepodporuje rozšíření, i v případě, že zadáváte jednu v adresáři. Rozšíření jsou odebrána, před odesláním volání. Použití čísla bez přípony nebo systém integrovat rozšíření do telefonní číslo ve vaší pobočková ústředna (Ústředny). |
| Uživatel nikdy obdrží e-mailu resetování hesla. | Nejčastější příčinou tohoto problému je, že zpráva zamítnul filtr proti spamu. Zkontrolujte nevyžádané pošty, složce nevyžádané nebo odstraněných položek e-mailu. <br> <br> Také se ujistěte, že při kontrole správné e-mailový účet pro zprávu. |
| Nastavím zásady resetování hesla, ale pokud účet správce používá resetování hesla, tyto zásady se nepoužije. | Microsoft spravuje a ovládací prvky zásad pro zajištění nejvyšší úrovně zabezpečení pro resetování hesla správce. |
| Uživatel je zabráněno v pokusu o příliš mnohokrát v jednom dni resetování hesla. | Můžeme implementovat mechanismus automatického omezení k blokování uživatelů z resetovat svá hesla příliš mnohokrát v krátké době. Omezení šířky pásma nastane, když: <br><ul><li>Uživatel se pokusí ověřit telefonní číslo pětkrát za jednu hodinu.</li><li>Uživatel se pokusí použít bránu otázky zabezpečení pětkrát za jednu hodinu.</li><li>Uživatel se pokusí resetovat heslo pro stejný uživatelský účet pětkrát za jednu hodinu.</li></ul>Chcete-li vyřešit tento problém, dáte pokyn, aby uživatel čekat po 24 hodinách od poslední pokus o. Uživatel pak můžete resetovat své heslo. |
| Uživateli se zobrazí chyby při ověření svého telefonního čísla. | Tato chyba nastane, pokud zadané telefonní číslo se neshoduje s telefonního čísla na soubor. Ujistěte se, že uživatel zadá celé telefonní číslo, včetně kód oblasti a země, při pokusu o použití metody založené na telefonu pro resetování hesla. |
| Dojde k chybě zpracování žádosti. | Příčinou může být mnoho problémů, ale obecně tato chyba je způsobená výpadku služeb nebo o problém s konfigurací. Pokud se zobrazí tato chyba, a to má vliv na vaši firmu, kontaktujte podporu Microsoftu o další pomoc. |
| Porušení zásad místní | Heslo nesplňuje zásady hesel místní služby Active Directory. |
| Heslo nesplňuje zásady pro přibližné | Heslo, které bylo použito se zobrazí v seznamu zakázaných hesel a nelze použít. |

## <a name="troubleshoot-password-writeback"></a>Řešení potíží s zpětný zápis hesla

| Chyba | Řešení |
| --- | --- |
| Služba pro resetování hesel místním nespustí. Zobrazí se chyba 6800 v protokolu událostí aplikace Azure AD Connect počítače. <br> <br> Po připojení federovaný předávací ověřování nebo synchronizaci hodnoty hash hesla uživatele resetovat jejich hesla. | Když je povolený zpětný zápis hesla, synchronizační modul voláním zpětný zápis knihovny a proveďte konfiguraci (připojování) komunikaci se službou registrace cloudu. Případné chyby zjištěné při připojování nebo při spouštění Windows Communication Foundation (WCF) koncový bod pro výsledky zpětného zápisu hesla v chyby v protokolu událostí na svém počítači služby Azure AD Connect. <br> <br> Při restartování služby Azure AD Sync (ADSync) Pokud byly nakonfigurované zpětný zápis, koncového bodu WCF spuštění. Ale pokud se nezdaří spuštění koncového bodu, budeme protokolovat události 6800 a nechat spustit synchronizační službu. Přítomnost Tato událost znamená, že koncový bod zpětný zápis hesla se nespustila. Podrobnosti protokolu událostí pro tuto událost 6800, spolu s protokolu událostí, které položky vygenerovat komponentou PasswordResetService určit, proč nelze spustit koncový bod tohoto. Zkontrolujte tyto chyby v protokolu událostí a pokuste se restartovat Azure AD Connect, pokud zpětný zápis hesla není funkční. Pokud se problém nevyřeší, zkuste zakázat a znovu povolit zpětný zápis hesla.
| Když se uživatel pokusí resetovat heslo nebo odemknout účet se zpětným zápisem hesla povolené, operace se nezdaří. <br> <br> Kromě toho se zobrazí události v protokolu událostí služby Azure AD Connect, který obsahuje: "Modul Synchronizace vrácena chyba hr = 800700CE, zpráva = Název souboru nebo rozšíření je příliš dlouhá" po odemčení operace probíhá. | Najděte účet služby Active Directory pro Azure AD Connect a resetování hesla tak, aby obsahoval maximálně 127 znaků. Otevřete **synchronizační služba** z **Start** nabídky. Přejděte do **konektory** a najít **konektor služby Active Directory**. Vyberte ho a pak vyberte **vlastnosti**. Přejděte **pověření** stránku a zadejte nové heslo. Vyberte **OK** zavřete stránku. |
| V posledním kroku procesu instalace služby Azure AD Connect se zobrazí chyba oznamující, že nebylo možné nakonfigurovat tuto zpětný zápis hesla. <br> <br> V protokolu událostí aplikace Azure AD Connect obsahuje chybu 32009 s textem "Chyba při získávání ověření tokenu." | K této chybě dochází v těchto dvou případů: <br><ul><li>Zadali jste nesprávné heslo pro zadaný na začátku procesu instalace služby Azure AD Connect účet globálního správce.</li><li>Pokusili jste se s použitím federovaného uživatele pro zadaný na začátku procesu instalace služby Azure AD Connect účet globálního správce.</li></ul> Chcete-li tento problém vyřešit, ujistěte se, že nepoužíváte federovaný účet globálního správce, který jste zadali na začátku procesu instalace. Také se ujistěte, že zadané heslo je správné. |
| Protokol událostí služby Azure AD Connect počítače obsahuje chyby 32002, která je vyvolána spuštěním PasswordResetService. <br> <br> Chyba čtení: "Došlo k chybě připojení k Service Bus. Poskytovatel tokenu nemohl poskytnout token zabezpečení." | V místním prostředí není možné se připojit ke koncovému bodu služby Azure Service Bus v cloudu. Tato chyba je obvykle způsobeno pravidlo brány firewall blokující odchozí připojení ke konkrétní port nebo webovou adresu. Zobrazit [požadavky na připojení](../hybrid/how-to-connect-install-prerequisites.md) pro další informace. Po aktualizaci těchto pravidel, restartujte počítač služby Azure AD Connect a zpětný zápis hesla by měla začít znovu pracovat. |
| Po pracovní nechystáte nějakou dobu federovaný, předávací ověřování nebo synchronizaci hodnoty hash hesla uživatele nelze resetování hesel. | V některých výjimečných případech může selhat službu zpětného zápisu hesla k restartování při restartování služby Azure AD Connect. V těchto případech se nejdřív zkontrolujte, zda zpětný zápis hesla se zdá být povoleno v místním. Můžete zkontrolovat, že pomocí Průvodce Azure AD Connect nebo prostředí PowerShell (viz předchozí oddíl HowTos). Pokud se zobrazí tuto funkci povolit, zkuste povolení nebo zakázání funkce znovu buď přes uživatelské rozhraní nebo Powershellu. Pokud to nepomůže, zkuste kompletní odinstalovat a znovu nainstalujte služby Azure AD Connect. |
| Federované předávací ověřování nebo synchronizaci hodnoty hash hesla uživatele, kteří se pokusí resetovat heslo zobrazit chyba po pokusu o odeslání hesla. Chyba udává, že došlo k chybě služby. <br ><br> Kromě tento problém během operace resetování hesel, může se zobrazit chyba, že agent pro správu byl odepřen přístup do protokolů událostí místních. | Pokud se zobrazí tyto chyby v protokolu událostí, přesvědčte se, že účet agenta správy Active Directory (ADMA), který byl zadán v průvodci v době konfigurace potřebná oprávnění pro zpětný zápis hesla. <br> <br> Poté, co je dána toto oprávnění, může trvat až jednu hodinu pro oprávnění k skapat dolů prostřednictvím `sdprop` úlohy na pozadí na řadiči domény (DC). <br> <br> Oprávnění pro resetování hesel v práci, musí být opatřena popisovač zabezpečení objektu uživatele, jejichž hesla je obnovena. Dokud tato oprávnění se zobrazí v objektu user, resetování hesla i nadále neúspěšné a zobrazí se zpráva o odepření přístupu. |
| Federované předávací ověřování nebo synchronizaci hodnoty hash hesla uživatele, kteří se pokusí resetovat svá hesla, najdete v chybě po odeslání hesla. Chyba udává, že došlo k chybě služby. <br> <br> Kromě tohoto problému během operace resetování hesel, může se zobrazit chybu v protokolech událostí ze služby Azure AD Connect udávající chybu "Objekt nebyl nalezen". | Tato chyba obvykle znamená, že synchronizační modul nelze najít objekt uživatele v prostoru konektoru Azure AD nebo propojené úložiště metaverse (MV) nebo objekt prostoru konektoru Azure AD. <br> <br> Chcete-li řešit tento problém, ujistěte se, že uživatel je skutečně synchronizované z místní do služby Azure AD prostřednictvím aktuální instance služby Azure AD Connect a kontrolovat stav objektů v prostor konektoru a MV. Potvrďte, že objekt služby Active Directory Certificate Services (AD CS) je připojen k objektu MV prostřednictvím pravidla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federované předávací ověřování nebo synchronizaci hodnoty hash hesla uživatele, kteří se pokusí resetovat heslo zobrazit chyba po odeslání hesla. Chyba udává, že došlo k chybě služby. <br> <br> Kromě tohoto problému během operace resetování hesel, může se zobrazit chybu v protokolech událostí ze služby Azure AD Connect, která označuje, že dochází k chybě "Nalezeno více shod". | To znamená, že synchronizační modul zjistil, že objektu MV je připojen k více než jeden objekt služby AD CS prostřednictvím "Microsoft.InfromADUserAccountEnabled.xxx". To znamená, že uživatel má povolený účet ve více než jednu doménovou strukturu. Tento scénář není podporován pro zpětný zápis hesla. |
| Operace s heslem se nezdaří s chybou konfigurace. V protokolu událostí aplikace obsahuje chybu služby Azure AD Connect 6329 s textem "0x8023061f (operace se nezdařila, protože tento Agent pro správu není povolená synchronizace hesel)". | Tato chyba nastane, že pokud se po přidání nové doménové struktury služby Active Directory (nebo na odebrat a znovu přidat existující doménové struktury) změnit konfiguraci služby Azure AD Connect již byla povolena funkce zpětný zápis hesla. Operace s heslem pro uživatele v těchto nedávno přidaly selhání doménových struktur. Chcete-li vyřešit tento problém, zakažte a pak znovu povolit funkci zpětného zápisu hesla po dokončení změny konfigurace doménové struktury. |

## <a name="password-writeback-event-log-error-codes"></a>Kódy chyb protokolu událostí pro zpětný zápis hesla

Osvědčeným postupem při řešení potíží se zpětným zápisem hesla je ke kontrole protokolu událostí aplikace na svém počítači služby Azure AD Connect. Tento protokol událostí neobsahuje události ze dvou zdrojů, které vás zajímají pro zpětný zápis hesla. Zdroj PasswordResetService popisuje operace a problémy související s operaci zpětného zápisu hesla. Zdroj ADSync popisuje operace a problémy související s nastavení hesla v prostředí služby Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Pokud je zdroj události ADSync

| Kód | Název nebo zprávy | Popis |
| --- | --- | --- |
| 6329 | NICHŽ: MMS(4924) 0x80230619: "Omezení brání změny do aktuálního zadat heslo." | Když službu zpětného zápisu hesla, pokusí se nastavit heslo na váš místní adresář, který nesplňuje stáří hesla, historie, složitost nebo filtrování požadavky domény dojde k této události. <br> <br> Pokud máte minimální stáří hesla a jste nedávno změnili heslo v rámci této časové okno, nejste schopni změnit heslo znovu, dokud nedosáhne zadané stáří ve vaší doméně. Pro účely testování, by měla minimální stáří nastavena na hodnotu 0. <br> <br> Pokud máte požadavky na heslo historie povolené, pak je nutné vybrat heslo, které nebyl použit za posledních *N* krát, kde *N* je nastavení historie hesel. Pokud vyberete heslo, které se používá v posledním *N* vícekrát, pak můžete zobrazit informace o selhání v tomto případě. Pro účely testování, je třeba nastavit historii hesel na 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich se vynucují, když se uživatel pokusí o změnu nebo resetování hesla. <br> <br> Pokud máte povolené filtry hesla a uživatel vybere heslo, které splňují kritéria filtru, klikněte resetovat nebo změnit operace se nezdaří. |
| 6329 | MMS(3040): admaexport.cpp(2837): Server neobsahuje prvek zásad heslo protokolu LDAP. | K tomuto problému dochází, pokud ovládací prvek LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066) není povolena na řadiče domény. Pokud chcete použít funkci zpětného zápisu hesla, třeba povolte stažení ovládacího prvku. Uděláte to tak, musí být řadiče domény v systému Windows Server 2008 (s nejnovější aktualizací SP) nebo novější. Pokud vaše řadiče domény jsou 2008 (starší verzi než R2), pak také musíte použít opravu hotfix [KB2386717](https://support.microsoft.com/kb/2386717). |
| HR 8023042 | Modul synchronizace vrácena chyba hr = 80230402, zpráva = pokus o získání objektu se nezdařilo, protože existují duplicitní položky s stejné kotvě. | Tato chyba nastane, pokud stejného ID uživatele je povoleno ve více doménách. Příkladem je Pokud jste synchronizaci doménových struktur účtů a prostředků a mají stejné ID uživatele k dispozici a povoleno v každé doménové struktuře. <br> <br> K této chybě může dojít také jako alias nebo hlavní název uživatele a dva uživatelé sdílet tento stejný atribut ukotvení používáte atribut ukotvení není jedinečný. <br> <br> Chcete-li vyřešit tento problém, ujistěte se, že nemáte žádné duplicitní uživatele v rámci vaší domény a používat atribut jedinečné ukotvení pro každého uživatele. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Pokud je zdroj události PasswordResetService

| Kód | Název nebo zprávy | Popis |
| --- | --- | --- |
| 31001 | PasswordResetStart | Tato událost označuje, že místní služba zjistila požadavek federované předávací ověřování, nebo synchronizaci hodnoty hash hesla uživatele, který pochází z cloudu pro vytvoření nového hesla. Tato událost je první událost v každé operace zpětný zápis resetování hesla. |
| 31002 | PasswordResetSuccess | Tato událost označuje, že uživatel vybrané nové heslo při operaci resetování hesla. Můžeme určit, že toto heslo splňuje požadavky na heslo podnikové. Heslo se úspěšně zapsala zpátky do místního prostředí Active Directory. |
| 31003 | PasswordResetFail | Tato událost označuje, že vybraná heslo uživatele a heslo úspěšně doručeno do místního prostředí. Ale když jsme se pokusili o nastavení hesla v místním prostředí služby Active Directory, došlo k chybě. Toto selhání může dojít z několika důvodů: <br><ul><li>Které uživatelské heslo nesplňuje věk, historie, složitost, nebo filtrovat požadavky pro doménu. Chcete-li vyřešit tento problém, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění pro nastavení nového hesla na dotyčném uživatelský účet.</li><li>Účet uživatele je ve skupině ochrany, jako je například skupiny pro správu domén nebo podnikové, která nepovoluje operace nastavení hesla.</li></ul>|
| 31004 | OnboardingEventStart | Této události dojde, když zapnete zpětný zápis hesla s Azure AD Connect a připojování jsme začali vaše organizace k webové službě zpětný zápis hesla. |
| 31005 | OnboardingEventSuccess | Tato událost označuje, že proces zařazení do systému bylo úspěšné a že funkci zpětného zápisu hesla je připravený k použití. |
| 31006 | ChangePasswordStart | Tato událost označuje, že místní služba byla nalezena žádost o změnu hesla pro federované předávací ověřování, nebo synchronizaci hodnoty hash hesla uživatele, který pochází z cloudu. Tato událost je první událost v každé operace zpětného zápisu změny hesla. |
| 31007 | ChangePasswordSuccess | Tato událost ukazuje na to, že uživatel zvolili nové heslo během operace změny hesla, jsme určili, že heslo splňuje požadavky firemní heslo a, že heslo se úspěšně zapsala zpět do místního prostředí Active Directory. |
| 31008 | ChangePasswordFail | Tato událost ukazuje na to, že vybraný uživatel heslo a že heslo úspěšně doručeno do místního prostředí, ale když jsme se pokusili o nastavení hesla v místním prostředí služby Active Directory, došlo k chybě. Toto selhání může dojít z několika důvodů: <br><ul><li>Které uživatelské heslo nesplňuje věk, historie, složitost, nebo filtrovat požadavky pro doménu. Chcete-li vyřešit tento problém, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění pro nastavení nového hesla na dotyčném uživatelský účet.</li><li>Účet uživatele je ve skupině ochrany, jako je například domény nebo enterprise admins, což nepovoluje operace nastavení hesla.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Místní služba zjistila požadavek pro federované předávací ověřování, nebo synchronizaci hodnoty hash hesla uživatele pocházející od správce jménem uživatele pro vytvoření nového hesla. Tato událost je první událost v každé operace zpětný zápis resetování hesla, který iniciuje správce. |
| 31010 | ResetUserPasswordByAdminSuccess | Nové heslo správce vybrat během operace resetování hesel iniciované správcem. Můžeme určit, že toto heslo splňuje požadavky na heslo podnikové. Heslo se úspěšně zapsala zpátky do místního prostředí Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Správce vybrat heslo jménem uživatele. Heslo úspěšně přišla v místním prostředí. Ale když jsme se pokusili o nastavení hesla v místním prostředí služby Active Directory, došlo k chybě. Toto selhání může dojít z několika důvodů: <br><ul><li>Které uživatelské heslo nesplňuje věk, historie, složitost, nebo filtrovat požadavky pro doménu. Vyzkoušejte nové heslo pro tento problém vyřešit.</li><li>Účet služby ADMA nemá příslušná oprávnění pro nastavení nového hesla na dotyčném uživatelský účet.</li><li>Účet uživatele je ve skupině ochrany, jako je například domény nebo enterprise admins, což nepovoluje operace nastavení hesla.</li></ul>  |
| 31012 | OffboardingEventStart | Tato událost nastane, pokud je zakázat zpětný zápis hesla s Azure AD Connect a označuje, že jsme začali odpojování vaší organizaci k webové službě zpětný zápis hesla. |
| 31013| OffboardingEventSuccess| Tato událost ukazuje na to, že proces odpojování bylo úspěšné a že funkce zpětný zápis hesla se úspěšně vypnul. |
| 31014| OffboardingEventFail| Tato událost ukazuje na to, že proces odpojování nebyla úspěšná. To může být z důvodu chyby oprávnění v cloudu nebo místní účet správce zadaný během konfigurace. Této chybě může také dojít, pokud se pokoušíte použít globální správce federované cloudu při zakazování zpětný zápis hesla. Chcete-li tento problém vyřešit, zkontrolujte vaše oprávnění správce a ujistěte se, že nepoužíváte federovaný účet při konfiguraci funkce zpětný zápis hesla.|
| 31015| WriteBackServiceStarted| Tato událost ukazuje na to, že služba zpětný zápis hesla byla úspěšně spuštěna. Je připraven k přijímání požadavků správy hesel z cloudu.|
| 31016| WriteBackServiceStopped| Tato událost ukazuje na to, že se zastavila službu zpětného zápisu hesla. Požadavky sady management jakékoli heslo z cloudu nebude úspěšné.|
| 31017| AuthTokenSuccess| Tato událost označuje, můžeme úspěšně načetl autorizační token pro globální správce zadaný během instalace služby Azure AD Connect k zahájení procesu odpojování nebo registrace.|
| 31018| KeyPairCreationSuccess| Tato událost ukazuje na to, že jsme vytvořili úspěšně heslo šifrovacího klíče. Tento klíč se používá k šifrování hesla z cloudu k odeslání do místního prostředí.|
| 32000| Neznámé chyby| Tato událost ukazuje na to, že došlo k neznámé chybě během operace správy hesel. Podívejte se na text výjimky v události pro další podrobnosti. Pokud máte problémy, zkuste zakázat a znovu ji povolit zpětný zápis hesla. Pokud to nepomůže, obsahovat kopii protokolů událostí společně s sledování insider zadané ID pro pracovníka podpory.|
| 32001| ServiceError| Tato událost ukazuje na došlo k chybě připojení k tomuto heslu cloudu resetování služby. Této chybě obvykle dochází, když místní služby se nemohl připojit k resetování hesla webové službě.|
| 32002| ServiceBusError| Tato událost ukazuje na došlo k chybě připojení k instanci služby Service Bus vašeho tenanta. To může nastat, pokud se blokování odchozích připojení v místním prostředí. Zkontrolujte bránu firewall, aby Ujistěte se, že povolíte připojení přes TCP 443 a získat https://ssprsbprodncu-sb.accesscontrol.windows.net/a pak to zkuste znovu. Pokud potíže potrvají, zkuste zakázat a znovu ji povolit zpětný zápis hesla.|
| 32003| InPutValidationError| Tato událost ukazuje na to, že vstup předaný do webového rozhraní API služby není platný. Zkuste operaci zopakovat.|
| 32004| DecryptionError| Tato událost ukazuje na to, že došlo k chybě při dešifrování hesla, které byly přijaty z cloudu. To může být z důvodu neshody dešifrovací klíče mezi cloudovou službu a v místním prostředí. Chcete-li vyřešit tento problém, zakažte a pak znovu povolení zpětného zápisu hesla ve vašem místním prostředí.|
| 32005| ConfigurationError| Během registrace uložíme informace specifické pro klienta v konfiguračním souboru v místním prostředí. Tato událost ukazuje na to, že došlo k chybě při ukládání tohoto souboru nebo že při spuštění služby, došlo k chybě při čtení souboru. Chcete-li tento problém vyřešit, zkuste zakázat a znovu ji povolit zpětný zápis hesla k vynucení přepsání konfiguračního souboru.|
| 32007| OnBoardingConfigUpdateError| Během registrace pošleme data z cloudu na místní službu resetování hesla. Tato data je pak zapsány do souboru v paměti před odesláním do služby synchronizace bezpečně uloží na disku. Tato událost znamená, že došlo k potížím s vytváření nebo aktualizaci dat v paměti. Chcete-li tento problém vyřešit, zkuste zakázat a znovu ji povolit zpětný zápis hesla k vynucení přepsání tohoto konfiguračního souboru.|
| 32008| ValidationError| Tato událost ukazuje na to, že jsme obdržel neplatnou odezvu z resetování hesla webové služby. Chcete-li tento problém vyřešit, zkuste zakázat a znovu ji povolit zpětný zápis hesla.|
| 32009| AuthTokenError| Tato událost ukazuje na to, že jsme nelze získat o autorizační token pro globální správce účet zadaný během instalace služby Azure AD Connect. Tato chyba může být způsobeno chybné uživatelské jméno nebo heslo zadané pro účet globálního správce. K této chybě může dojít, pokud federované zadaný účet globálního správce. Chcete-li tento problém vyřešit, spusťte znovu konfigurační správné uživatelské jméno a heslo a ujistěte se, že správce spravovaného účtu (jenom pro cloud nebo synchronizovat hesla).|
| 32010| CryptoError| Tato událost ukazuje na došlo k chybě při generování klíče heslo šifrování nebo dešifrování hesla přicházející z cloudové služby. Tato chyba pravděpodobně znamená problém s vaším prostředím. Podívejte se na podrobnosti protokolu událostí na další informace o tom, jak tento problém vyřešit. Můžete také zkusit zakázáním a následným opětovným povolením službu zpětného zápisu hesla.|
| 32011| OnBoardingServiceError| Tato událost ukazuje na to, že místní službu nebylo správně komunikovat se službou web resetování hesla k zahájení procesu registrace. K tomu může dojít v důsledku pravidlo brány firewall nebo pokud dojde k problému, získání tokenu ověřování pro vašeho tenanta. Chcete-li vyřešit tento problém, ujistěte se, že nejsou blokuje odchozí připojení přes TCP 443 a TCP 9350-9354 nebo k https://ssprsbprodncu-sb.accesscontrol.windows.net/. Také zajistěte, aby účet správce Azure AD, který používáte pro připojení není Federovaná.|
| 32013| OffBoardingError| Tato událost ukazuje na to, že místní službu nebylo správně komunikovat se službou web resetování hesla k zahájení odpojování. K tomu může dojít v důsledku pravidlo brány firewall nebo pokud došlo k potížím při získávání o autorizační token pro vašeho tenanta. Chcete-li vyřešit tento problém, ujistěte se, že nejsou blokuje odchozí připojení přes 443 nebo na https://ssprsbprodncu-sb.accesscontrol.windows.net/, a že účet správce Azure Active Directory, který používáte k odpojení není federovaný.|
| 32014| ServiceBusWarning| Tato událost ukazuje na to, že jsme museli znovu připojit k instanci služby Service Bus vašeho tenanta. Za normálních podmínek to by neměl být žádný problém, ale pokud se zobrazí tato událost v mnoha případech, je vhodné zkontrolovat vaše síťové připojení k Service Bus, zejména v případě, že jde o vysokou latencí a malou šířkou pásma připojení.|
| 32015| ReportServiceHealthError| Pokud chcete monitorovat stav služby zpětný zápis hesla, pošleme dat prezenčního signálu naši službu resetování hesla webové každých pět minut. Tato událost ukazuje na to, že došlo k chybě při odesílání informací o tomto stavu zpět do cloud web service. Informace o tomto stavu zahrnout údaje objektu (OII) nebo identifikovatelné osobní údaje (PII) dat a je čistě zjišťování prezenčního signálu a statistiky služby na úrovni basic, takže můžeme poskytnout informace o stavu služby v cloudu.|
| 33001| ADUnKnownError| Tato událost ukazuje na to, že došlo k neznámé chybě službou Active Directory. Zkontrolujte protokol událostí serveru služby Azure AD Connect pro události ze zdroje ADSync pro další informace.|
| 33002| ADUserNotFoundError| Tato událost ukazuje na to, že uživatel, který se pokouší provést resetování nebo změna hesla nebyl nalezen v místním adresáři. Této chybě může dojít po odstraněné místní uživatele, ale ne v cloudu. K této chybě může dojít, pokud dojde k problému se synchronizací. Zkontrolujte protokoly synchronizace a posledních několik podrobností synchronizaci spustit další informace.|
| 33003| ADMutliMatchError| Při resetování hesla nebo změnit požadavek pochází z cloudu, používáme Cloudová ukotvení zadaný během procesu instalace služby Azure AD Connect a zjistěte, jak propojit tento požadavek zpět na uživatele v místním prostředí. Tato událost označuje, že jsme našli dva uživatele v adresáři v místním na stejný atribut Cloudová ukotvení. Zkontrolujte protokoly synchronizace a posledních několik podrobností synchronizaci spustit další informace.|
| 33004| ADPermissionsError| Tato událost ukazuje na to, že účet služby agenta správy Active Directory (ADMA) nemá příslušná oprávnění nastavit nové heslo daného účtu. Zajistěte, aby ADMA účet doménové struktury uživatele byl resetován a změnit heslo oprávnění u všech objektů v doménové struktuře. Další informace o tom, jak nastavit oprávnění, najdete v kroku 4: Nastavte příslušná oprávnění služby Active Directory.|
| 33005| ADUserAccountDisabled| Tato událost ukazuje na to, že jsme se pokusili o resetování nebo změna hesla pro účet, který byl zablokuje v místním. Zapnout účet a zkuste operaci zopakovat.|
| 33006| ADUserAccountLockedOut| Tato událost ukazuje na to, že jsme se pokusili o resetování nebo změna hesla pro účet, který byl uzamčen v místním. Uzamčení může dojít, když uživatel má pokusili změnu nebo resetování hesla operace příliš mnohokrát v krátké době. Odemknutí účtu a zkuste operaci zopakovat.|
| 33007| ADUserIncorrectPassword| Tato událost ukazuje na to, že uživatel zadal nesprávný aktuální heslo při provádění heslo změnit operace. Zadejte aktuální heslo a zkuste to znovu.|
| 33008| ADPasswordPolicyError| Když službu zpětného zápisu hesla, pokusí se nastavit heslo na váš místní adresář, který nesplňuje stáří hesla, historie, složitost nebo filtrování požadavky domény dojde k této události. <br> <br> Pokud máte minimální stáří hesla a jste nedávno změnili heslo v rámci této časové okno, nejste schopni změnit heslo znovu, dokud nedosáhne zadané stáří ve vaší doméně. Pro účely testování, by měla minimální stáří nastavena na hodnotu 0. <br> <br> Pokud máte požadavky na heslo historie povolené, pak je nutné vybrat heslo, které nebyl použit za posledních *N* krát, kde *N* je nastavení historie hesel. Pokud vyberete heslo, které se používá v posledním *N* vícekrát, pak můžete zobrazit informace o selhání v tomto případě. Pro účely testování, je třeba nastavit historii hesel na 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich se vynucují, když se uživatel pokusí o změnu nebo resetování hesla. <br> <br> Pokud máte povolené filtry hesla a uživatel vybere heslo, které splňují kritéria filtru, klikněte resetovat nebo změnit operace se nezdaří.|
| 33009| ADConfigurationError| Tato událost ukazuje na to, že byl problém zápisu hesla zpět do místního adresáře kvůli potížím s konfigurací se službou Active Directory. Zkontrolujte protokol událostí aplikace Azure AD Connect počítače pro zprávy ze služby ADSync Další informace, na kterém došlo k chybě.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Řešení potíží s připojením zpětného zápisu hesla

Pokud dojde k přerušení služeb s komponentou zpětný zápis hesla služby Azure AD Connect, tady jsou některé rychlé kroky, které můžete provést k vyřešení tohoto problému:

* [Zkontrolujte připojení k síti](#confirm-network-connectivity)
* [Restartujte službu Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Zakázat a znovu povolit funkci zpětného zápisu hesla](#disable-and-re-enable-the-password-writeback-feature)
* [Nainstalujte nejnovější verzi Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Řešení potíží s zpětný zápis hesla](#troubleshoot-password-writeback)

Obecně platí Pokud chcete obnovit vaši službu co nejrychlejším způsobem, doporučujeme, proveďte tyto kroky v pořadí, jak jsme vysvětlili výše.

### <a name="confirm-network-connectivity"></a>Zkontrolujte připojení k síti

Aby brána firewall je největší společný bod selhání a nebo jsou nesprávně nakonfigurované porty serveru proxy a časový limit nečinnosti. 

Pro Azure AD Connect verze 1.1.443.0 a vyšší, budete potřebovat přístup odchozí připojení HTTPS takto:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

Pro větší členitostí odkazovat na aktualizovaný seznam [Microsoft Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/details.aspx?id=41653) aktualizuje každou středu a začíná platit následující pondělí.

Další informace najdete v tématu požadavky připojení [požadavky pro Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) článku.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Restartujte službu Azure AD Connect Sync

Chcete-li vyřešit potíže s připojením nebo jiné přechodné problémy se službou, restartujte službu Azure AD Connect Sync:

   1. Jako správce, vyberte **Start** na serveru se službou Azure AD Connect.
   1. Zadejte **services.msc** vyhledávacího pole a vyberte **Enter**.
   1. Hledat **Microsoft Azure AD Sync** položka.
   1. Klikněte pravým tlačítkem na položku služby, vyberte **restartovat**a potom počkejte na dokončení operace.

   ![Restartujte službu Azure AD Sync][Service restart]

Tyto kroky znovu navázat připojení s cloudovou službou a vyřešit přerušení práce, které se mohou setkat. Restartovat službu ADSync váš problém nevyřeší, doporučujeme pokusu zakázat a znovu povolit funkci zpětného zápisu hesla.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Zakázat a znovu povolit funkci zpětného zápisu hesla

Chcete-li vyřešit potíže s připojením, zakázat a znovu povolit funkci zpětného zápisu hesla:

   1. Jako správce otevřete Průvodce konfigurace Azure AD Connect.
   1. V **připojit ke službě Azure AD**, zadejte přihlašovací údaje globálního správce Azure AD.
   1. V **připojit ke službě AD DS**, zadejte svoje přihlašovací údaje správce AD Domain Services.
   1. V **Jednoznačná identifikace uživatelů**, vyberte **Další** tlačítko.
   1. V **volitelné funkce**, zrušte zaškrtnutí políčka **zpětný zápis hesla** zaškrtávací políčko.
   1. Vyberte **Další** prostřednictvím na zbývajících stránkách beze změny jakéhokoli, dokud se nedostanete do dialogového okna **připraveno ke konfiguraci** stránky.
   1. Ujistěte se, že **připraveno ke konfiguraci stránky** ukazuje **zpětný zápis hesla** možnost jako **zakázané** a zvolte zelené **konfigurovat** tlačítko potvrďte provedené změny.
   1. V **dokončeno**, zrušte zaškrtnutí políčka **synchronizovat** a potom vyberte možnost **Dokončit** zavřete průvodce.
   1. Znovu otevřete Průvodce konfigurace Azure AD Connect.
   1. Zopakujte kroky 2 až 8, s tím rozdílem, vyberte **zpětný zápis hesla** možnost **volitelné funkce** stránku a znovu povolit službu.

Tyto kroky znovu navázat připojení k naší cloudové služby a řešení, které vám může dojít k přerušení práce.

Pokud váš problém nelze vyřešit zakázáním a následným opětovným povolením funkci zpětného zápisu hesla, doporučujeme, abyste znovu nainstalujte Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Nainstalujte nejnovější verzi Azure AD Connect

Konfigurace a problémy s připojením mezi naše cloudové služby a prostředí místní služby Active Directory lze vyřešit opětovná instalace služby Azure AD Connect.

Doporučujeme, abyste tento krok proveďte až po pokusu o první dva kroky popsané dříve.

> [!WARNING]
> Pokud jste upravili out-of-the-box synchronizační pravidla, *zálohování je před pokračováním v upgradu a poté ručně znovu nasadit až budete hotovi.*
>

1. Stáhněte si nejnovější verzi služby Azure AD Connect z [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Vzhledem k tomu, že jste již nainstalovali Azure AD Connect, budete muset provést místní upgrade na nejnovější verzi aktualizace instalace služby Azure AD Connect.
1. Spuštění staženého balíčku a postupujte podle na obrazovce pokyny k aktualizaci vašeho počítače v Azure AD Connect.

V předchozích krocích by měl znovu navázat připojení s naší cloudovou službou a vyřešte všechny přerušení, které se mohou setkat.

Pokud instalaci nejnovější verze serveru služby Azure AD Connect váš problém nevyřeší, doporučujeme, zkuste zakázat a následným opětovným povolením zpětného zápisu hesla v posledním kroku po instalaci nejnovější verze.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Ověřte, že Azure AD Connect má požadované oprávnění pro zpětný zápis hesla

Azure AD Connect vyžaduje služby Active Directory **resetovat heslo** oprávnění k provedení zpětný zápis hesla. Chcete-li zjistit, jestli služby Azure AD Connect má požadované oprávnění pro danou místní uživatelský účet Active Directory, můžete použít funkci Windows efektivního oprávnění:

1. Přihlaste se k serveru služby Azure AD Connect a spustit **Synchronization Service Manager** tak, že vyberete **Start** > **synchronizační služba**.
1. V části **konektory** kartu, vyberte místní **Active Directory Domain Services** konektoru a pak vyberte **vlastnosti**.  
   ![Efektivní oprávnění – krok 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. V místním okně vyberte **připojit k doménové struktuře služby Active Directory** a poznamenejte si **uživatelské jméno** vlastnost. Tato vlastnost je účet služby AD DS používá k provedení synchronizace adresářů Azure AD Connect. Pro Azure AD Connect k provádění zpětného zápisu hesla musí mít účet služby AD DS resetovat heslo oprávnění.  
   
   ![Efektivní oprávnění – krok 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Přihlaste se k řadiči domény s místními a spustit **Active Directory Users and Computers** aplikace.
1. Vyberte **zobrazení** a ujistěte se, že **rozšířené funkce** je povolená možnost.  
   
   ![Efektivní oprávnění – krok 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Vyhledejte uživatelský účet Active Directory, kterou chcete ověřit. Klikněte pravým tlačítkem na název účtu a vyberte **vlastnosti**.  
   
   ![Efektivní oprávnění – krok 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

1. V místním okně, přejděte **zabezpečení** kartě a vyberte **Upřesnit**.  
   
   ![Efektivní oprávnění – krok 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
1. V **Upřesnit nastavení zabezpečení pro správce** automaticky otevírané okno, přejděte **platného přístupu** kartu.
1. Vyberte **vybrat uživatele**, vyberte účet služby AD DS používá Azure AD Connect (viz krok 3) a pak vyberte **zobrazit platný přístup**.

   ![Efektivní oprávnění - kroku 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Posuňte se dolů a najděte **resetovat heslo**. Pokud má položka zaškrtávací políčko, má účet služby AD DS oprávnění resetovat heslo vybraného uživatelského účtu služby Active Directory.  
   
   ![Efektivní oprávnění - krok 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóra služby Azure AD

Pokud máte obecný dotaz ohledně služby Azure AD a resetování hesla pomocí samoobslužné služby, můžete požádat o komunity pomoc na [fóra služby Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členy komunity patří technici, produktové manažery, MVP a ostatními Odborníci v oblasti IT.

## <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Pokud nemůžete najít odpověď na problém, jsou vždy k dispozici při další naše týmy podpory.

K usnadnění správně, můžeme požádat, zadejte co nejvíce podrobností nejvíce při otevření případu. Mezi tyto podrobnosti patří:

* **Obecný popis chyby**: Co je chyba? Jak se chování, které bylo si všimli? Jak jsme chybu reprodukovat? Zadejte co nejvíce podrobností nejvíce.
* **Stránka**: Které stránce jste byli na kdy jste si všimli chybu? Zahrnují adresu URL, pokud jste byli schopni a snímek obrazovky stránky.
* **Podpora kódu**: Jak se kód podpory, který se vygeneroval při uživateli zobrazila chyba?
    * Tento kód najdete chybu reprodukovat a pak vyberte **podpory kódu** odkaz v dolní části obrazovky a identifikátor GUID, který výsledky odeslat pracovníkem technické podpory.

    ![Kód podpory, v dolní části obrazovky][Support code]

    * Pokud jste na stránce bez podpory kód v dolní části, vyberte F12 a vyhledejte identifikátor SID a CID a odesílat tyto dva výsledky pro pracovníka podpory.
* **Datum, čas a časové pásmo**: Uveďte přesné datum a čas *s časovým pásmem* , ke které došlo k chybě.
* **ID uživatele**: Kdo byl uživatel, který viděli chybu? Příkladem je *user@contoso.com*.
    * Je to federovaného uživatele?
    * Je to předávací ověřování uživatele?
    * Je to synchronizaci hodnoty hash hesla uživatele?
    * Je to výhradně cloudový uživatel?
* **Licencování**: Má uživatel přiřazenou licenci Azure AD Premium nebo Azure AD Basic?
* **Protokol událostí aplikace**: Pokud používáte zpětný zápis hesla a chyba je ve vaší místní infrastruktuře, zahrnují ZIP kopii protokolu událostí aplikací ze serveru služby Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Restartujte službu Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kód podpory, který se nachází v pravém dolním rohu okna"

## <a name="next-steps"></a>Další postup

Následující články poskytují další informace o resetování hesla přes Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
