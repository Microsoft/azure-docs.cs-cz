---
title: Poradce při potížích s samoobslužným resetováním hesla – Azure Active Directory
description: Řešení potíží s samoobslužným resetováním hesla služby Azure AD
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
ms.openlocfilehash: a22c0cc922e021edc37dfbb2d89fdd20c77b2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848761"
---
# <a name="troubleshoot-self-service-password-reset"></a>Řešení potíží se samoobslužným resetováním hesla

Máte potíže s samoobslužným resetováním hesla služby Azure Active Directory (Azure AD) (SSPR)? Následující informace vám mohou pomoci znovu zpracovat věci.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Poradce při potížích s chybami resetování samoobslužných hesel, které se uživateli mohou zobrazit

| Chyba | Podrobnosti | Technické podrobnosti |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Je nám líto, ale v tuto chvíli nemůžete obnovit heslo, protože správce zakázal resetování hesla pro vaši organizaci. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o povolení této funkce. Další informace najdete v [tématu Nápověda, zapomněl jsem heslo Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Zjistili jsme, že správce nepovolil resetování hesla. Obraťte se na správce a požádejte ho, aby povolil resetování hesla pro vaši organizaci. |
| WritebackNotEnabled = 10 |Je nám líto, ale v tuto chvíli nelze obnovit heslo, protože správce nepovolil potřebnou službu pro vaši organizaci. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby zkontroloval konfiguraci vaší organizace. Další informace o této nezbytné službě naleznete v [tématu Konfigurace zpětného zápisu hesla](howto-sspr-writeback.md). | SSPR_0010: Zjistili jsme, že zpětný zápis hesla nebyl povolen. Obraťte se na správce a požádejte ho, aby povolil zpětný zápis hesla. |
| SsprNotEnabledInUserPolicy = 11 | Je nám líto, ale v tuto chvíli nelze obnovit heslo, protože správce nenakonfiguroval resetování hesla pro vaši organizaci. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho o konfiguraci resetování hesla. Další informace o konfiguraci pro resetování hesla najdete [v tématu Rychlý start: Samoobslužné resetování hesla služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Vaše organizace nedefinovala zásady pro resetování hesla. Obraťte se na správce a požádejte ho, aby definoval zásady pro obnovení hesla. |
| Uživatelnotlicenci = 12 | Je nám líto, ale v tuto chvíli nelze obnovit heslo, protože ve vaší organizaci chybí požadované licence. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby zkontroloval přiřazení licence. Další informace o licencování najdete v [tématu Licenční požadavky na samoobslužné resetování hesla Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Vaše organizace nemá požadované licence potřebné k provedení resetování hesla. Obraťte se na správce a požádejte ho, aby zkontroloval přiřazení licencí. |
| UživatelNotMemberOfScopedAccessGroup = 13 | Je nám líto, ale v tuto chvíli nemůžete obnovit heslo, protože správce nenakonfiguroval váš účet tak, aby používal resetování hesla. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby nakonfiguroval váš účet pro resetování hesla. Další informace o konfiguraci účtu pro resetování hesla najdete v [tématu Zavedení resetování hesla pro uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Nejste členem skupiny s povoleným pro resetování hesla. Obraťte se na správce a požádejte o přidání do skupiny. |
| Uživatelsprávně nakonfigurován = 14 | Je nám líto, ale v tuto chvíli nemůžete obnovit heslo, protože ve vašem účtu chybí potřebné informace. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na správce a požádejte ho, aby vám resetoval heslo. Poté, co budete mít přístup ke svému účtu znovu, musíte zaregistrovat potřebné informace. Chcete-li zaregistrovat informace, postupujte podle pokynů v článku [Registrace pro samoobslužné resetování hesla.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) | SSPR_0014: K obnovení hesla jsou potřeba další bezpečnostní údaje. Chcete-li pokračovat, obraťte se na správce a požádejte ho o obnovení hesla. Po přístupu ke svému účtu můžete zaregistrovat https://aka.ms/ssprsetupdalší bezpečnostní údaje na adrese . Správce může k vašemu účtu přidat další bezpečnostní údaje podle pokynů v části [Nastavení a čtení ověřovacích dat pro resetování hesla](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Je nám líto, ale v tuto chvíli nemůžeme obnovit heslo z důvodu problému s konfigurací resetování hesla vaší organizace. Neexistuje žádná další akce, kterou můžete provést k vyřešení této situace. Obraťte se na svého správce a požádejte ho, aby to prošetřil. Další informace o možném problému naleznete v [tématu Poradce při potížích s zpětnou platností hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Nemůžeme obnovit heslo z důvodu chyby v místní konfiguraci. Obraťte se na svého správce a požádejte ho, aby to prošetřil. |
| OnPremisesConnectivityError = 30 | Je nám líto, ale v tuto chvíli nemůžeme obnovit vaše heslo z důvodu problémů s připojením k vaší organizaci. Nyní není třeba provést žádnou akci, ale problém může být vyřešen, pokud to zkusíte později. Pokud problém přetrvává, obraťte se na správce a požádejte ho, aby to prošetřil. Další informace o problémech s připojením najdete [v tématu Poradce při potížích s zpětnou platností hesla](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Nemůžeme obnovit heslo kvůli špatnému připojení k místnímu prostředí. Obraťte se na správce a požádejte ho, aby to prošetřil.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Poradce při potížích s konfigurací pro resetování hesla na webu Azure Portal

| Chyba | Řešení |
| --- | --- |
| Nevidím oddíl Reset **ování hesla** v části Azure AD na webu Azure Portal. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci provádějícímu operaci. <br> <br> Přiřaďte licenci k dotyčnému účtu správce. Můžete postupovat podle kroků v článku [Přiřazení, ověření a řešení problémů s licencemi.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Nevidím konkrétní možnost konfigurace. | Mnoho prvků uživatelského rozhraní jsou skryté, dokud jsou potřeba. Zkuste povolit všechny možnosti, které chcete zobrazit. |
| Karta Místní integrace se **nezobrazuje.** | Tato možnost se zobrazí jenom v případě, že jste stáhli Azure AD Connect a nakonfigurovali zpětný zápis hesla. Další informace najdete [v tématu Začínáme s Azure AD Connect pomocí expresní nastavení](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Poradce při potížích s vytvářením sestav pro obnovení hesla

| Chyba | Řešení |
| --- | --- |
| V kategorii událostí auditu **samoobslužné správy hesel** se nezobrazují žádné typy aktivit správy hesel. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci provádějícímu operaci. <br> <br> Tento problém můžete vyřešit přiřazením licence k dotyčnému účtu správce. Postupujte podle pokynů v článku [Přiřazení, ověření a řešení problémů s licencemi.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Registrace uživatelů se zobrazují vícekrát. | V současné době, když se uživatel zaregistruje, zaznamenáváme každou jednotlivou část dat, která je registrována jako samostatná událost. <br> <br> Pokud chcete tato data agregovat a mít větší flexibilitu v tom, jak je můžete zobrazit, můžete si sestavu stáhnout a otevřít data jako kontingenční tabulku v aplikaci Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Poradce při potížích s registračním portálem pro obnovení hesla

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. **Správce nepovolil použití této funkce.** | Přepněte příznak **samoobslužné resetování hesla** na **Selected** or **All** a pak vyberte **Uložit**. |
| Uživatel nemá přiřazenou licenci Azure AD. **Správce nepovolil použití této funkce.** | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci provádějícímu operaci. <br> <br> Tento problém můžete vyřešit přiřazením licence k dotyčnému účtu správce. Postupujte podle pokynů v článku [Přiřazení, ověření a řešení problémů s licencemi.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Při zpracování požadavku došlo k chybě. | To může být způsobeno mnoha problémy, ale obecně je tato chyba způsobena výpadkem služby nebo problémem s konfigurací. Pokud se zobrazí tato chyba a má vliv na vaši firmu, obraťte se na podporu společnosti Microsoft pro další pomoc. |

## <a name="troubleshoot-the-password-reset-portal"></a>Poradce při potížích s portálem pro obnovení hesla

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. | Přepněte příznak **samoobslužné resetování hesla** na **Selected** or **All** a pak vyberte **Uložit**. |
| Uživatel nemá přiřazenou licenci Azure AD. | K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci provádějícímu operaci. <br> <br> Tento problém můžete vyřešit, pokud přiřadíte licenci k dotyčnému účtu správce. Postupujte podle pokynů v článku [Přiřazení, ověření a řešení problémů s licencemi.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Adresář je povolen pro resetování hesla, ale uživatel má chybějící nebo poškozené ověřovací informace. | Než budete pokračovat, ujistěte se, že uživatel správně vytvořil kontaktní data v souboru v adresáři. Další informace najdete [v tématu Data používaná samoobslužným heslem Azure AD](howto-sspr-authenticationdata.md). |
| Adresář je povolen pro resetování hesla, ale uživatel má pouze jednu část kontaktních dat v souboru, pokud je zásada nastavena tak, aby vyžadovala dvě metody ověření. | Než budete pokračovat, ujistěte se, že uživatel má alespoň dvě správně nakonfigurované metody kontaktu. Příkladem je mít jak číslo mobilního *telefonu,* tak telefonní číslo kanceláře. |
| Adresář je povolen pro resetování hesla a uživatel je správně nakonfigurován, ale uživatel nemůže být kontaktován. | Může to být důsledek dočasné chyby služby nebo pokud jsou k dispozici nesprávné kontaktní údaje, které nemůžeme správně zjistit. <br> <br> Pokud uživatel čeká 10 sekund, zobrazí se odkazy "zkuste to znovu" a zobrazí se odkazy "kontaktujte správce". Pokud uživatel vybere "zkuste to znovu", opakuje volání. Pokud uživatel vybere možnost "Kontaktujte správce", odešle e-mail s formulářem správcům, kteří požadují obnovení hesla pro tento uživatelský účet. |
| Uživatel nikdy neobdrží sms nebo telefonní hovor pro resetování hesla. | To může být výsledkem poškozeného telefonního čísla v adresáři. Ujistěte se, že telefonní číslo je ve formátu "+ccc xxxyyyzzzzXeeee". <br> <br> Resetování hesla nepodporuje rozšíření, i když zadáte jeden v adresáři. Rozšíření jsou odstraněny před odesláním volání. Použijte číslo bez rozšíření nebo rozšíření integrujte do telefonního čísla ve vaší pobočkové ústředně (PBX). |
| Uživatel nikdy neobdrží e-mail pro resetování hesla. | Nejčastější příčinou tohoto problému je, že zpráva je odmítnuta filtrem nevyžádané pošty. Zkontrolujte složku spamu, nevyžádané pošty nebo odstraněných položek pro e-mail. <br> <br> Také se ujistěte, že kontrolujete správný e-mailový účet pro zprávu. |
| Nastavil(a) jsem zásadu pro resetování hesla, ale pokud účet správce používá resetování hesla, tato zásada se nepoužije. | Společnost Microsoft spravuje a řídí zásady pro resetování hesla správce, aby zajistila nejvyšší úroveň zabezpečení. |
| Uživateli je zabráněno v pokusu o obnovení hesla příliš mnohokrát za den. | Implementujeme mechanismus automatického omezení, který uživatelům blokuje pokus o resetování hesel příliš mnohokrát v krátkém časovém období. K omezení dochází, když: <br><ul><li>Uživatel se pokusí ověřit telefonní číslo pětkrát za hodinu.</li><li>Uživatel se pokusí použít bránu bezpečnostních otázek pětkrát za hodinu.</li><li>Uživatel se pokusí obnovit heslo pro stejný uživatelský účet pětkrát za hodinu.</li></ul>Chcete-li tento problém vyřešit, pokyn uživateli čekat 24 hodin po posledním pokusu. Uživatel pak může obnovit své heslo. |
| Uživatel i ono vidět jeden chybový vidět do ověřovat jejich telefonní číslo. | K této chybě dochází, pokud zadané telefonní číslo neodpovídá telefonnímu číslu v souboru. Ujistěte se, že uživatel zadává úplné telefonní číslo, včetně kódu oblasti a země, když se pokusí použít metodu telefonu pro resetování hesla. |
| Při zpracování požadavku došlo k chybě. | To může být způsobeno mnoha problémy, ale obecně je tato chyba způsobena výpadkem služby nebo problémem s konfigurací. Pokud se zobrazí tato chyba a má vliv na vaši firmu, obraťte se na podporu společnosti Microsoft pro další pomoc. |
| Místní porušení zásad | Heslo nesplňuje místní zásady hesel služby Active Directory. |
| Heslo není v souladu s přibližnou zásadou | Použité heslo se zobrazí v [seznamu zakázaných hesel](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) a nelze je použít. |

## <a name="troubleshoot-password-writeback"></a>Poradce při potížích s zpětnou platností hesla

| Chyba | Řešení |
| --- | --- |
| Služba resetování hesla se nespustí místně. V protokolu událostí aplikace počítače Azure AD Connect se zobrazí chyba 6800. <br> <br> Po zapnutí, federované, předávací ověřování nebo heslo-hash synchronizované uživatelé nemohou obnovit svá hesla. | Pokud je povoleno zpětné hohen, modul synchronizace volá knihovnu zpětného zápisu k provedení konfigurace (onboarding) komunikací do cloudové onboarding ové služby. Jakékoli chyby zjištěné při zařazování nebo při spuštění koncového bodu WCF (Windows Communication Foundation) pro zpětný zápis hesla má za následek chyby v protokolu událostí v počítači Azure AD Connect. <br> <br> Během restartování služby Azure AD Sync (ADSync), pokud byl nakonfigurován zpětný zápis, spustí se koncový bod WCF. Ale pokud se nezdaří spuštění koncového bodu, budeme protokolovat událost 6800 a nechat spuštění služby synchronizace. Přítomnost této události znamená, že koncový bod zpětného zápisu hesla nebyl zahájen. Podrobnosti protokolu událostí pro tuto událost 6800 spolu s položkami protokolu událostí generovanými komponentou PasswordResetService označují, proč nelze spustit koncový bod. Zkontrolujte tyto chyby protokolu událostí a zkuste restartovat Azure AD Connect, pokud zpětný zápis hesla stále nefunguje. Pokud problém přetrvává, zkuste zakázat a znovu povolit zpětný zápis hesla.
| Pokud se uživatel pokusí obnovit heslo nebo odemknout účet s povoleným zpětným zápisem hesla, operace se nezdaří. <br> <br> Kromě toho se zobrazí událost v protokolu událostí Azure AD Connect, který obsahuje: "Synchronizační modul vrátil chybu hr = 800700CE, message=Název souboru nebo rozšíření je příliš dlouhá" po operaci odemknutí. | Najděte účet Služby Active Directory pro Azure AD Connect a resetujte heslo tak, aby obsahovalo maximálně 256 znaků. Potom otevřete **službu synchronizace** z nabídky **Start.** Vyhledejte **položku Konektory** a vyhledejte **konektor služby Active Directory**. Vyberte ji a pak vyberte **Vlastnosti**. Přejděte na stránku **Přihlašovací údaje** a zadejte nové heslo. Chcete-li stránku zavřít, zavřete možnost **OK.** |
| V posledním kroku procesu instalace Azure AD Connect se zobrazí chyba označující, že zpětný zápis hesla nelze nakonfigurovat. <br> <br> Protokol událostí aplikace Azure AD Connect obsahuje chybu 32009 s textem "Chyba získávání auth tokenu." | K této chybě dochází v následujících dvou případech: <br><ul><li>Zadali jste nesprávné heslo pro účet globálního správce zadaný na začátku procesu instalace služby Azure AD Connect.</li><li>Pokusili jste se použít federovaného uživatele pro účet globálního správce zadaný na začátku procesu instalace služby Azure AD Connect.</li></ul> Chcete-li tento problém vyřešit, ujistěte se, že nepoužíváte federovaný účet pro globálního správce, který jste zadali na začátku procesu instalace. Také se ujistěte, že zadané heslo je správné. |
| Protokol událostí počítače Azure AD Connect obsahuje chybu 32002, která je vyvolána spuštěním služby PasswordResetService. <br> <br> Chyba přečte: "Chyba připojení k ServiceBus. Zprostředkovatel tokenu nemohl poskytnout token zabezpečení." | Vaše místní prostředí se nemůže připojit ke koncovému bodu Azure Service Bus v cloudu. Tato chyba je obvykle způsobena pravidlem brány firewall, které blokuje odchozí připojení k určitému portu nebo webové adrese. Další informace najdete v [tématu Požadavky na připojení.](../hybrid/how-to-connect-install-prerequisites.md) Po aktualizaci těchto pravidel restartujte počítač Azure AD Connect a zpětný zápis hesla by měl začít znovu pracovat. |
| Po určité době práce nemohou federované, předávací ověřování nebo synchronizovaní uživatelé s heslem a hash obnovit svá hesla. | V některých výjimečných případech může selhání služby zpětného zápisu hesla při restartování služby Azure AD Connect. V těchto případech nejprve zkontrolujte, zda se zdá, že zpětný zápis hesla je povolen místně. Kontrolu můžete zkontrolovat pomocí průvodce Azure AD Connect nebo PowerShellu (viz předchozí část HowTos). Pokud se zdá, že je tato funkce povolena, zkuste ji znovu povolit nebo zakázat prostřednictvím rozhraní UI nebo Prostředí PowerShell. Pokud to nepomůže, zkuste kompletní odinstalaci a přeinstalaci Služby Azure AD Connect. |
| Federované, předávací ověřování nebo synchronizované heslo hash uživatelé, kteří se pokusí obnovit svá hesla vidět chybu po pokusu o odeslání hesla. Chyba označuje, že došlo k potížím se službou. <br ><br> Kromě tohoto problému se může během operací resetování hesla zobrazit chyba, že agentovi správy byl odepřen přístup v místních protokolech událostí. | Pokud se tyto chyby zobrazí v protokolu událostí, zkontrolujte, zda účet Agent adma (ADMA) služby Active Directory, který byl zadán v průvodci v době konfigurace, má potřebná oprávnění pro zpětný zápis hesla. <br> <br> Po udělení tohoto oprávnění může trvat až jednu hodinu, `sdprop` než se oprávnění prosáknou dolů prostřednictvím úlohy na pozadí v řadiči domény (DC). <br> <br> Aby resetování hesla fungovalo, musí být oprávnění vyznačeno na popisovači zabezpečení objektu uživatele, jehož heslo se resetuje. Dokud se toto oprávnění nezobrazí u objektu uživatele, obnovení hesla se nezdaří se zprávou o odepření přístupu. |
| Federované, předávací ověřování nebo synchronizovaní uživatelé s hodnotou hash hesel, kteří se pokoušejí obnovit svá hesla, vidí chybu po odeslání hesla. Chyba označuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému během operací resetování hesla se může zobrazit chyba v protokolech událostí ze služby Azure AD Connect označující chybu "Objekt nebyl nalezen". | Tato chyba obvykle označuje, že synchronizační modul nemůže najít objekt uživatele v prostoru konektoru Azure AD nebo propojený metaverse (MV) nebo objektu konektoru Azure AD. <br> <br> Chcete-li tento problém vyřešit, ujistěte se, že uživatel je skutečně synchronizovány z místního do Služby Azure AD prostřednictvím aktuální instance Azure AD Connect a zkontrolujte stav objektů v prostoru konektoru a MV. Zkontrolujte, zda je objekt služby AD CS (AD CS) připojen k objektu MV pomocí pravidla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federované, předávací ověřování nebo synchronizované heslo hash uživatelé, kteří se pokusí obnovit svá hesla vidět chybu po odeslání hesla. Chyba označuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému během operací resetování hesla se může zobrazit chyba v protokolech událostí ze služby Azure AD Connect, která označuje, že došlo k chybě "Bylo nalezeno více shod". | To znamená, že synchronizační modul zjistil, že objekt MV je připojen k více než jednomu objektu služby AD CS prostřednictvím "Microsoft.InfromADUserAccountEnabled.xxx". To znamená, že uživatel má povolený účet ve více než jedné doménové struktuře. Tento scénář není podporován pro zpětný zápis hesla. |
| Operace s heslem se nezdaří s chybou konfigurace. Protokol událostí aplikace obsahuje chybu 6329 připojení Azure AD Connect s textem "0x8023061f (Operace se nezdařila, protože synchronizace hesel není v tomto agentovi správy povolena)". | K této chybě dochází, pokud se změní konfigurace služby Azure AD Connect tak, aby byla po povolení funkce zpětného zápisu hesla změněna na přidání nové doménové struktury služby Active Directory (nebo odebrání a přečtení existující doménové struktury). Operace s heslem pro uživatele v těchto nedávno přidaných doménových strukturách se nezdaří. Chcete-li problém vyřešit, zakažte a znovu povolte funkci zpětného zápisu hesla po dokončení změn konfigurace doménové struktury. |

## <a name="password-writeback-event-log-error-codes"></a>Chybové kódy protokolu zápisu hesla

Při řešení potíží s zpětným zápisem hesla je vhodné zkontrolovat protokol událostí aplikace v počítači Azure AD Connect. Tento protokol událostí obsahuje události ze dvou zdrojů zájmu pro zpětný zápis hesla. Zdroj PasswordResetService popisuje operace a problémy související s operací zpětného zápisu hesla. Zdroj ADSync popisuje operace a problémy související s nastavením hesel v prostředí služby Active Directory.

### <a name="if-the-source-of-the-event-is-adsync"></a>Pokud je zdrojem události ADSync

| kód | Název nebo zpráva | Popis |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "Omezení zabraňuje změně hesla na aktuální zadané." | K této události dochází, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje požadavky na stáří hesla, historii, složitost nebo filtrování domény. <br> <br> Pokud máte minimální stáří hesla a nedávno jste v tomto časovém okně změnili heslo, nebudete moci heslo znovu změnit, dokud nedosáhne zadaného věku ve vaší doméně. Pro účely testování by měl být minimální věk nastaven na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které nebylo použito v posledních *N* časech, kde *N* je nastavení historie hesel. Pokud vyberete heslo, které bylo použito v posledních *N* časech, pak se v tomto případě zobrazí selhání. Pro účely testování by měla být historie hesel nastavena na 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich jsou vynuceny, když se uživatel pokusí změnit nebo obnovit heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdaří. |
| 6329 | MMS(3040): admaexport.cpp(2837): Server neobsahuje ovládací prvek zásad y hesel LDAP. | K tomuto problému dochází, pokud LDAP_SERVER_POLICY_HINTS_OID ovládací prvek (1.2.840.113556.1.4.2066) není povolena na řadiče domény. Chcete-li použít funkci zpětného zápisu hesla, musíte ovládací prvek povolit. K tomu musí být řadiče domény v systému Windows Server 2008R2 nebo novějším. |
| HR 8023042 | Synchronizační modul vrátil chybu hr=80230402, message=Pokus o získání objektu se nezdařil, protože existují duplicitní položky se stejnou kotvou. | K této chybě dochází, pokud je stejné ID uživatele povoleno ve více doménách. Příkladem je, pokud synchronizujete doménové struktury účtů a prostředků a máte stejné ID uživatele v každé doménové struktuře. <br> <br> K této chybě může dojít také v případě, že použijete nejedinečný atribut ukotvení, například alias nebo hlavní název uživatele, a dva uživatelé sdílejí stejný atribut ukotvení. <br> <br> Chcete-li tento problém vyřešit, ujistěte se, že nemáte žádné duplicitní uživatele v rámci domén a že používáte jedinečný atribut kotvy pro každého uživatele. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Pokud je zdrojem události PasswordResetService

| kód | Název nebo zpráva | Popis |
| --- | --- | --- |
| 31001 | PasswordResetStart | Tato událost označuje, že místní služba zjistila požadavek na obnovení hesla pro federovaného uživatele se synchronizovaným předávacím nastavením nebo synchronizovaným heslem hash, který pochází z cloudu. Tato událost je první událostí v každé operaci zpětného zápisu resetování hesla. |
| 31002 | PasswordResetÚspěch | Tato událost označuje, že uživatel během operace obnovení hesla vybral nové heslo. Zjistili jsme, že toto heslo splňuje požadavky na firemní heslo. Heslo bylo úspěšně zapsáno zpět do místního prostředí služby Active Directory. |
| 31003 | PasswordResetFail | Tato událost označuje, že uživatel vybral heslo a heslo bylo úspěšně doručeno do místního prostředí. Když jsme se však pokusili nastavit heslo v místním prostředí služby Active Directory, došlo k chybě. K tomuto selhání může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtr pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla k příslušnému uživatelskému účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například doména nebo skupina správců rozlehlé sítě, která zakazuje operace nastavení hesla.</li></ul>|
| 31004 | OnboardingEventStart | K této události dojde, pokud povolíte zpětný zápis hesla pomocí služby Azure AD Connect a zahájili jsme připojení vaší organizace k webové službě zpětného zápisu hesla. |
| 31005 | OnboardingEventÚspěch | Tato událost označuje, že proces registrace byl úspěšný a že funkce zpětného zápisu hesla je připravena k použití. |
| 31006 | ChangePasswordStart | Tato událost označuje, že místní služba zjistila žádost o změnu hesla pro federovaného uživatele se synchronizovaným předávacím nastavením nebo synchronizovaným heslem hash, který pochází z cloudu. Tato událost je první událostí v každé operaci zpětného zápisu změny hesla. |
| 31007 | ChangePasswordÚspěch | Tato událost označuje, že uživatel vybral nové heslo během operace změny hesla, zjistili jsme, že heslo splňuje požadavky na firemní heslo a že heslo bylo úspěšně zapsáno zpět do místního prostředí služby Active Directory. |
| 31008 | ChangePasswordFail | Tato událost označuje, že uživatel vybral heslo a že heslo bylo úspěšně doručeno do místního prostředí, ale když jsme se pokusili nastavit heslo v místním prostředí služby Active Directory, došlo k chybě. K tomuto selhání může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtr pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla k příslušnému uživatelskému účtu.</li><li>Uživatelský účet je v chráněné skupině, jako jsou doménové nebo podnikové správce, což zakazuje operace nastavení hesla.</li></ul> |
| 31009 | Obnovení UserPasswordByAdminStart | Místní služba zjistila požadavek na obnovení hesla pro federovaného uživatele se synchronizovaným nastavením hodnot hash nebo s heslem a hash, který pochází od správce jménem uživatele. Tato událost je první událostí v každé operaci zpětného zápisu resetování hesla, která je iniciována správcem. |
| 31010 | ObnoveníUserPasswordByAdminÚspěch | Správce vybral nové heslo během operace resetování hesla iniciované správcem. Zjistili jsme, že toto heslo splňuje požadavky na firemní heslo. Heslo bylo úspěšně zapsáno zpět do místního prostředí služby Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Správce vybral heslo jménem uživatele. Heslo bylo úspěšně doručeno do místního prostředí. Když jsme se však pokusili nastavit heslo v místním prostředí služby Active Directory, došlo k chybě. K tomuto selhání může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtr pro doménu. Zkuste nový hesel k vyřešení tohoto problému.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla k příslušnému uživatelskému účtu.</li><li>Uživatelský účet je v chráněné skupině, jako jsou doménové nebo podnikové správce, což zakazuje operace nastavení hesla.</li></ul>  |
| 31012 | OffboardingEventStart | K této události dojde, pokud zakážete zpětný zápis hesla pomocí služby Azure AD Connect a označuje, že jsme začali offboarding vaší organizace na hesle writeback webové služby. |
| 31013| OffboardingEventÚspěch| Tato událost označuje, že proces offboardingu byl úspěšný a že funkce zpětného zápisu hesla byla úspěšně zakázána. |
| 31014| OffboardingEventFail| Tato událost označuje, že proces offboardingu nebyl úspěšný. To může být způsobeno chybou oprávnění v cloudu nebo místním účtu správce určeném během konfigurace. K chybě může dojít také v případě, že se pokoušíte použít federovaného globálního správce cloudu při zakázání zpětného zápisu hesla. Chcete-li tento problém vyřešit, zkontrolujte oprávnění správce a ujistěte se, že nepoužíváte federovaný účet při konfiguraci funkce zpětného zápisu hesla.|
| 31015| Služba WriteBackServicebyla| Tato událost označuje, že služba zpětného zápisu hesla byla úspěšně spuštěna. Je připraven přijímat požadavky na správu hesel z cloudu.|
| 31016| Služba WriteBackServicebyla zastavena.| Tato událost označuje, že služba zpětného zápisu hesla byla zastavena. Žádné požadavky na správu hesel z cloudu nebudou úspěšné.|
| 31017| AuthTokenÚspěch| Tato událost označuje, že jsme úspěšně načetli autorizační token pro globálního správce určeného během instalace služby Azure AD Connect, abychom zahájili proces offboardingu nebo registrace.|
| 31018| KeyPairCreationÚspěch| Tato událost označuje, že jsme úspěšně vytvořili šifrovací klíč hesla. Tento klíč se používá k šifrování hesel z cloudu, které mají být odeslány do místního prostředí.|
| 32000| Neznámýchyba| Tato událost označuje, že během operace správy hesel došlo k neznámé chybě. Další podrobnosti naleznete v textu výjimky v události. Pokud máte potíže, zkuste zakázat a znovu povolit zpětný zápis hesla. Pokud to nepomůže, přidejte kopii protokolu událostí spolu s id sledování zadané zasvěcených do vašeho pracovníka podpory.|
| 32001| Chyba služby| Tato událost označuje, že došlo k chybě při připojování ke službě resetování hesla v cloudu. K této chybě obvykle dochází, když se místní služba nemohla připojit k webové službě pro obnovení hesla.|
| 32002| Chyba servicebusu| Tato událost označuje, že došlo k chybě při připojování k instanci service bus vašeho klienta. K tomu může dojít, pokud blokujete odchozí připojení v místním prostředí. Zkontrolujte bránu firewall, abyste se ujistili, https://ssprdedicatedsbprodncu.servicebus.windows.netže povolíte připojení přes protokol TCP 443 a program , a akci opakujte. Pokud potíže přetrvávají, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32003| Chyba ověření inputu| Tato událost označuje, že vstup předaná rozhraní API naší webové služby byl neplatný. Opakujte operaci.|
| 32004| Chyba dešifrování| Tato událost označuje, že došlo k chybě dešifrování hesla, které přišlo z cloudu. To může být způsobeno nesouladem dešifrovacího klíče mezi cloudovou službou a místním prostředím. Chcete-li tento problém vyřešit, zakažte a znovu povolte zpětný zápis hesla v místním prostředí.|
| 32005| Chyba konfigurace| Během registrace ukládáme informace specifické pro klienta do konfiguračního souboru ve vašem místním prostředí. Tato událost označuje, že došlo k chybě při ukládání tohoto souboru nebo že při spuštění služby došlo k chybě při čtení souboru. Chcete-li tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla vynutit přepsání konfiguračního souboru.|
| 32007| Chyba onboardingconfigUpdateError| Během registrace odesíláme data z cloudu do místní služby pro resetování hesla. Tato data jsou pak zapsána do souboru v paměti před odesláním do synchronizační služby, která má být bezpečně uložena na disku. Tato událost označuje, že došlo k potížím s zápisem nebo aktualizací těchto dat v paměti. Chcete-li tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla vynutit přepsání tohoto konfiguračního souboru.|
| 32008| Validationerror| Tato událost označuje, že jsme obdrželi neplatnou odpověď od webové služby pro resetování hesla. Chcete-li tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32009| AuthTokenError| Tato událost označuje, že se nám nepodařilo získat token autorizace pro účet globálního správce zadaný během instalace služby Azure AD Connect. Tato chyba může být způsobena špatným uživatelským jménem nebo heslem určeným pro účet globálního správce. K této chybě může dojít také v případě, že je federován zadaný účet globálního správce. Chcete-li tento problém vyřešit, znovu spusťte konfiguraci se správným uživatelským jménem a heslem a ujistěte se, že správce je spravovaný účet (pouze cloud nebo heslo).|
| 32010| Kryptochyba| Tato událost označuje, že došlo k chybě při generování šifrovacího klíče hesla nebo dešifrování hesla, které přichází z cloudové služby. Tato chyba pravděpodobně označuje problém s prostředím. Další informace o řešení tohoto problému naleznete v podrobnostech protokolu událostí. Můžete také zkusit zakázat a znovu povolit službu zpětného zápisu hesla.|
| 32011| Chyba služby onboardingservice| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou pro resetování hesla a zahájit proces registrace. K tomu může dojít v důsledku pravidla brány firewall nebo pokud došlo k potížím se získáním ověřovacího tokenu pro vašeho klienta. Chcete-li tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes protokoly TCP https://ssprdedicatedsbprodncu.servicebus.windows.net443 a TCP 9350-9354 nebo na . Taky se ujistěte, že účet správce Azure AD, který používáte k na palubě není federovaný.|
| 32013| Chyba offboardingu| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou pro resetování hesla a zahájit proces offboardingu. K tomu může dojít v důsledku pravidla brány firewall nebo pokud došlo k potížím se získáním autorizačního tokenu pro vašeho tenanta. Chcete-li tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes 443 nebo na https://ssprdedicatedsbprodncu.servicebus.windows.net, a že účet správce Služby Azure Active Directory, který používáte k offboard není federovaný.|
| 32014| Upozornění na servicebus| Tato událost označuje, že jsme se museli znovu pokusit připojit k instanci service bus vašeho klienta. Za normálních podmínek by to nemělo být problém, ale pokud se tato událost zobrazí mnohokrát, zvažte kontrolu síťového připojení k service bus, zejména pokud se jedná o připojení s vysokou latencí nebo nízkou šířkou pásma.|
| 32015| Chyba ReportServiceHealthError| Abybylo možné sledovat stav služby zpětného zápisu hesla, každých pět minut odesíláme údaje o prezenčním signálu do naší webové služby pro resetování hesla. Tato událost označuje, že došlo k chybě při odesílání těchto informací o stavu zpět do cloudové webové služby. Tyto informace o stavu neobsahují údaje o identifikovatelných objektech (OII) ani osobní údaje o datech a jsou čistě prezenční a základní statistiky služeb, abychom mohli poskytovat informace o stavu služby v cloudu.|
| 33001| ADUnKnownError| Tato událost označuje, že služba Active Directory vrátila neznámou chybu. Další informace najdete v protokolu událostí serveru Azure AD Connect, kde najdete události ze zdroje ADSync.|
| 33002| ADUserNotFoundError| Tato událost označuje, že uživatel, který se pokouší obnovit nebo změnit heslo, nebyl v místním adresáři nalezen. K této chybě může dojít, když byl uživatel odstraněn místně, ale ne v cloudu. K této chybě může dojít také v případě, že došlo k potížím se synchronizací. Další informace naleznete v protokolech synchronizace a v několika posledních podrobnostech spuštění synchronizace.|
| 33003| ADMutliMatchError| Když požadavek na obnovení hesla nebo změnu pochází z cloudu, použijeme cloudovou kotvu určenou během procesu instalace služby Azure AD Connect k určení, jak propojit tento požadavek zpět s uživatelem ve vašem místním prostředí. Tato událost označuje, že jsme našli dva uživatele ve vašem místním adresáři se stejným atributem kotvy cloudu. Další informace naleznete v protokolech synchronizace a v několika posledních podrobnostech spuštění synchronizace.|
| 33004| Chyba oprávnění adpermissions| Tato událost označuje, že účet služby ADMA (ADMA) agenta služby Active Directory nemá příslušná oprávnění k nastavení nového hesla. Ujistěte se, že účet ADMA v doménové struktuře uživatele má obnovit a změnit oprávnění hesla pro všechny objekty v doménové struktuře. Další informace o nastavení oprávnění naleznete v tématu Krok 4: Nastavení příslušných oprávnění služby Active Directory. K této chybě může dojít také v případě, že atribut uživatele AdminCount je nastavena na 1.|
| 33005| ADUserAccountDisabled| Tato událost označuje, že jsme se pokusili obnovit nebo změnit heslo pro účet, který byl zakázán místně. Povolte účet a opakujte operaci.|
| 33006| ADUserAccountLockedOut| Tato událost označuje, že jsme se pokusili obnovit nebo změnit heslo pro účet, který byl uzamčen v místním prostředí. Uzamčení může dojít, když uživatel se pokusil změnit nebo obnovit operaci hesla příliš mnohokrát v krátkém období. Odemkněte účet a zkuste operaci znovu.|
| 33007| ADUserIncorrectPassword| Tato událost označuje, že uživatel zadal nesprávné aktuální heslo při provádění operace změny hesla. Zadejte správné aktuální heslo a akci opakujte.|
| 33008| Chyba zásad ADPasswordPolicy| K této události dochází, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje požadavky na stáří hesla, historii, složitost nebo filtrování domény. <br> <br> Pokud máte minimální stáří hesla a nedávno jste v tomto časovém okně změnili heslo, nebudete moci heslo znovu změnit, dokud nedosáhne zadaného věku ve vaší doméně. Pro účely testování by měl být minimální věk nastaven na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které nebylo použito v posledních *N* časech, kde *N* je nastavení historie hesel. Pokud vyberete heslo, které bylo použito v posledních *N* časech, pak se v tomto případě zobrazí selhání. Pro účely testování by měla být historie hesel nastavena na 0. <br> <br> Pokud máte požadavky na složitost hesla, všechny z nich jsou vynuceny, když se uživatel pokusí změnit nebo obnovit heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdaří.|
| 33009| Chyba konfigurace služby AD| Tato událost znamená, že došlo k potížím se zápisem hesla zpět do místního adresáře z důvodu problému s konfigurací služby Active Directory. Další informace o tom, u kterých došlo k chybě v počítači Azure AD Connect, naleznete v protokolu událostí aplikace počítače Azure AD Connect.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Poradce při potížích s připojením k zpětnému zápisu hesla

Pokud dochází k přerušení služby s součástí zpětný zápis hesla Azure AD Connect, tady jsou některé rychlé kroky, které můžete provést k vyřešení tohoto problému:

* [Potvrzení připojení k síti](#confirm-network-connectivity)
* [Restartování služby Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Zakázání a opětovné povolení funkce zpětného zápisu hesla](#disable-and-re-enable-the-password-writeback-feature)
* [Instalace nejnovější verze Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Poradce při potížích s zpětnou platností hesla](#troubleshoot-password-writeback)

Obecně doporučujeme provést tyto kroky v pořadí popsaném dříve, abyste službu obnovili co nejrychlejším způsobem.

### <a name="confirm-network-connectivity"></a>Potvrzení připojení k síti

Nejběžnějším bodem selhání je, že porty brány firewall nebo proxy a časové limity nečinnosti jsou nesprávně nakonfigurovány. 

Pro Azure AD Connect verze 1.1.443.0 a vyšší, budete potřebovat odchozí přístup HTTPS na následující:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Další rozlišovací schopnost narazíte na aktualizovaný seznam [rozsahů IP adres datového centra Microsoft Azure, který](https://www.microsoft.com/download/details.aspx?id=41653) je aktualizován každou středu a uveden v platnost následující pondělí.

Další informace najděte požadavky na připojení v článku [Požadavky pro Azure AD Connect.](../hybrid/how-to-connect-install-prerequisites.md)

> [!NOTE]
> Program Samoreset může také selhat, pokud "Heslo nikdy nevyprší" nebo "Uživatel nemůže změnit heslo" nastavení jsou konfigurovány na účtu ve službě AD DS v místním prostředí. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Restartování služby Azure AD Connect Sync

Chcete-li vyřešit problémy s připojením nebo jiné přechodné problémy se službou, restartujte službu Azure AD Connect Sync:

1. Jako správce vyberte **Spustit** na serveru se spuštěným službou Azure AD Connect.
1. Do vyhledávacího pole zadejte **soubor services.msc** a vyberte **zadat**.
1. Vyhledejte položku **Microsoft Azure AD Sync.**
1. Klepněte pravým tlačítkem myši na položku služby, vyberte **restartovat**a počkejte na dokončení operace.

   ![Restartujte službu Azure AD Sync pomocí grafického uživatelského rozhraní][Service restart]

Tyto kroky obnovit připojení ke cloudové službě a vyřešit všechna přerušení, které může docházet. Pokud restartování služby ADSync problém nevyřeší, doporučujeme, abyste se pokusili zakázat a znovu povolit funkci zpětného zápisu hesla.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Zakázání a opětovné povolení funkce zpětného zápisu hesla

Chcete-li vyřešit problémy s připojením, zakažte a znovu povolte funkci zpětného zápisu hesla:

   1. Jako správce otevřete Průvodce konfigurací služby Azure AD Connect.
   1. V **seznamu Připojit se k Azure AD**zadejte svoje přihlašovací údaje globálního správce Azure AD.
   1. V **popřípadě ke službě AD DS**zadejte přihlašovací údaje správce služby AD Domain Services.
   1. V **části Jedinečně identifikace uživatelů**vyberte tlačítko **Další.**
   1. V **poli Volitelné funkce**zrušte zaškrtnutí **políčka Zpětný zápis hesla.**
   1. Vyberte **Další** prostřednictvím zbývajících dialogových stránek bez změny, dokud se nedostanete na stránku **Připraveno ke konfiguraci.**
   1. Ujistěte se, že **stránka Připraveno ke konfiguraci** zobrazuje možnost **zpětného zápisu hesla** jako **zakázanou,** a pak vyberte zelené tlačítko **Konfigurovat** pro potvrzení změn.
   1. V **části Dokončeno**zrušte zaškrtnutí políčka **Synchronizovat a** potom vyberte **Dokončit,** chcete-li průvodce zavřít.
   1. Znovu otevřete Průvodce konfigurací služby Azure AD Connect.
   1. Opakujte kroky 2-8, s výjimkou ujistěte se, že vyberete možnost **vrácení zpětného zápisu hesla** na stránce **Volitelné funkce** znovu povolit službu.

Tyto kroky znovu navazují vaše spojení s naší cloudovou službou a řeší případné přerušení, ke kterým můžete docházet.

Pokud zakázání a opětovné povolení funkce zpětného zápisu hesla problém nevyřeší, doporučujeme přeinstalovat Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Instalace nejnovější verze Azure AD Connect

Přeinstalace služby Azure AD Connect může vyřešit problémy s konfigurací a připojením mezi našimi cloudovými službami a místním prostředím služby Active Directory.

Doporučujeme provést tento krok až po pokusu o první dva kroky, které byly popsány dříve.

> [!WARNING]
> Pokud jste přizpůsobili pravidla synchronizace předem, *zálohujte je před pokračováním v upgradu a po dokončení je ručně znovu nasaďte.*

1. Stáhněte si nejnovější verzi služby Azure AD Connect ze služby [Stažení softwaru společnosti Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Vzhledem k tomu, že jste už nainstalovali Azure AD Connect, musíte provést místní upgrade, abyste aktualizovali instalaci Služby Azure AD Connect na nejnovější verzi.
1. Spusťte stažený balíček a podle pokynů na obrazovce aktualizujte počítač Azure AD Connect.

Předchozí kroky by měly obnovit vaše spojení s naší cloudovou službou a vyřešit všechna přerušení, ke kterým můžete docházet.

Pokud instalace nejnovější verze serveru Azure AD Connect problém nevyřeší, doporučujeme zkusit zakázat a znovu povolit zpětný zápis hesla jako poslední krok po instalaci nejnovější verze.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Ověřte, že Azure AD Connect má požadovaná oprávnění pro zpětný zápis hesla

Azure AD Connect vyžaduje oprávnění **hesla pro obnovení služby** Active Directory k provedení zpětného zápisu hesla. Chcete-li zjistit, zda má služba Azure AD Connect požadovaná oprávnění pro daný místní uživatelský účet služby Active Directory, můžete použít funkci Oprávnění k účinnému systému Windows:

1. Přihlaste se k serveru Azure AD Connect a spusťte **Správce synchronizačních služeb** výběrem **možnosti Spustit** > **synchronizační službu**.
1. Na kartě **Konektory** vyberte místní konektor **služby Active Directory Domain Services** a pak vyberte **Vlastnosti**.  
   ![Správce služeb synchronizace, který ukazuje, jak upravit vlastnosti](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. V rozbalovacím okně vyberte **Připojit k doménové struktuře služby Active Directory** a poznamenejte si vlastnost Uživatelské **jméno.** Tato vlastnost je účet služby AD DS používaný službou Azure AD Connect k provedení synchronizace adresářů. Aby služba Azure AD Connect mohla provádět zpětný zápis hesla, musí mít účet služby AD DS oprávnění k obnovení hesla.  

   ![Vyhledání uživatelského účtu služby Synchronizace služby Active Directory](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Přihlaste se k místnímu řadiči domény a spusťte aplikaci **Uživatelé a počítače služby Active Directory.**
1. Vyberte **Zobrazit** a ujistěte se, že je povolena možnost **Upřesnit funkce.**  

   ![Uživatelé a počítače služby Active Directory zobrazují rozšířené funkce](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Vyhledejte uživatelský účet služby Active Directory, který chcete ověřit. Klepněte pravým tlačítkem myši na název účtu a vyberte **příkaz Vlastnosti**.  
1. V automaticky otevíraném okně přejděte na kartu **Zabezpečení** a vyberte **Upřesnit**.  
1. V **rozbalovacím** okně Upřesnit nastavení zabezpečení pro správce přejděte na kartu Efektivní **přístup.**
1. Vyberte **Vybrat uživatele**, vyberte účet služby AD DS používaný službou Azure AD Connect (viz krok 3) a pak vyberte **Zobrazit efektivní přístup**.

   ![Karta Efektivní přístup zobrazující účet synchronizace](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Posuňte se dolů a vyhledejte **možnost Obnovit heslo**. Pokud je položka zaškrtnuta, má účet služby AD DS oprávnění k resetování hesla vybraného uživatelského účtu služby Active Directory.  

   ![Ověření, že účet synchronizace má oprávnění Obnovit heslo](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fóra Azure AD

Pokud máte obecnou otázku týkající se Azure AD a samoobslužné resetování hesla, můžete požádat komunitu o pomoc na [fórech Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členy komunity jsou inženýři, produktoví manažeři, MVP a další IT profesionálové.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Pokud nemůžete najít odpověď na problém, naše týmy podpory jsou vždy k dispozici, aby vám pomohly dále.

Abychom vám řádně pomohli, žádáme vás, abyste při otevírání případu poskytli co nejvíce podrobností. Mezi tyto podrobnosti patří:

* **Obecný popis chyby**: Jaká je chyba? Jaké bylo chování, které bylo zaznamenáno? Jak můžeme chybu reprodukovat? Poskytněte co nejvíce podrobností.
* **Stránka**: Na jaké stránce jste byli, když jste si všimli chyby? Pokud je to možné, uveďte adresu URL a snímek obrazovky stránky.
* **Kód podpory**: Jaký byl kód podpory, který byl vygenerován, když uživatel viděl chybu?
   * Chcete-li najít tento kód, reprodukovat chybu, pak vyberte odkaz **kód podpory** v dolní části obrazovky a odešlete pracovníka podpory GUID, který výsledky.

   ![Vyhledání kódu podpory v dolní části obrazovky][Support code]

  * Pokud jste na stránce bez kódu podpory v dolní části, vyberte F12 a vyhledejte SID a CID a odeslat tyto dva výsledky pracovníka podpory.
* **Datum, čas a časové pásmo**: Zahrňte přesné datum a čas *s časovým pásmem,* ve které došlo k chybě.
* **ID uživatele**: Kdo byl uživatel, který viděl chybu? Příkladem je *\@uživatel contoso.com*.
   * Je to federovaný uživatel?
   * Jedná se o předávacího uživatele ověřování?
   * Jedná se o uživatele synchronizované s heslem a hash?
   * Je to jen pro cloud uživatele?
* **Licencování**: Má uživatel přiřazenou licenci Azure AD?
* **Protokol událostí aplikace**: Pokud používáte zpětný zápis hesla a chyba je v místní infrastruktuře, zahrňte kopii protokolu událostí aplikace z serveru Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Restartování služby Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kód podpory se nachází v pravém dolním rohu okna"

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
