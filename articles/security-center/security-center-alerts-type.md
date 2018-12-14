---
title: Výstrahy zabezpečení podle typu ve službě Azure Security Center | Dokumentace Microsoftu
description: Tento článek popisuje různé druhy výstrah zabezpečení dostupných ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 360614a2c4f7c8c397ef9d5039f6ef31f7e90da6
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344070"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Principy výstrah zabezpečení ve službě Azure Security Center
Tento článek vám pomůže porozumět různým typům výstrah zabezpečení a souvisejícím přehledům, které jsou dostupné ve službě Azure Security Center. Další informace o správě těchto výstrah a incidentů najdete v tématu [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md).

Pokud chcete nastavit rozšířené detekce, upgradujte na Azure Security Center Standard. Bezplatná zkušební verze je k dispozici. Pokud chcete provést upgrade, v [zásadách zabezpečení](tutorial-security-policy.md) vyberte **cenovou úroveň**. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> Ve službě Security Center byla vydána ve verzi Limited Preview nová sada detekcí, které využívají záznamy auditu, což je běžný auditní rámec, k detekci škodlivého chování na počítačích s Linuxem. Pokud se chcete připojit k verzi Preview, zašlete [nám](mailto:ASC_linuxdetections@microsoft.com) e-mail s ID vašich předplatných.

## <a name="what-type-of-alerts-are-available"></a>Jaké typy výstrah jsou k dispozici?
Azure Security Center používá celou řadu různých [možností detekce](security-center-detection-capabilities.md) k upozornění zákazníků na potenciální útoky namířené proti jejich prostředím. Tyto výstrahy obsahují cenné informace o tom, co výstrahu aktivovalo, o prostředcích na které cílí, a o zdroji útoku. Informace obsažené ve výstrahách se liší podle typu analýzy použité k rozpoznání příslušné hrozby. Incidenty mohou obsahovat také další kontextové informace, které mohou být užitečné při dalším zkoumání hrozeb.  Tento článek obsahuje informace o následujících typech výstrah:

* Analýza chování virtuálního počítače (VMBA)
* Analýza sítě
* Analýza SQL Database a SQL Data Warehouse
* Kontextové informace

## <a name="virtual-machine-behavioral-analysis"></a>Analýza chování virtuálního počítače
Azure Security Center může pomocí analýzy chování identifikovat ohrožené prostředky na základě analýzy protokolů událostí virtuálního počítače. Například události vytváření procesů a události přihlášení. Kromě toho se pomocí vzájemné souvislosti s dalšími signály hledají podpůrné důkazy rozšířené kampaně.

> [!NOTE]
> Další informace o tom, jak detekce služby Security Center pracuje, najdete v článku [Funkce detekce ve službě Azure Security Center](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Analýza událostí
Security Center používá pokročilou analýzu k identifikaci ohrožených prostředků na základě analýzy protokolů událostí virtuálního počítače. Například události vytváření procesů a události přihlášení. Kromě toho se pomocí vzájemné souvislosti s dalšími signály hledají podpůrné důkazy rozšířené kampaně.

* **Zjištění podezřelého spouštění procesů**: Útočníci často pokoušejí nepozorovaně spustit škodlivý kód tak, že ho maskují za neškodné procesy. Tyto výstrahy indikují, že spuštění procesu odpovídá některému z následujících vzorů:
    * Spustil se proces, o kterém je známo, že se používá ke škodlivým účelům. Zatímco jednotlivé příkazy můžou zdát neškodné, ale, výstraha se vyhodnocuje na základě agregace těchto příkazů.
    * Proces se spustil z neobvyklého umístění.
    * Proces se spustil z umístění, ve kterém se vyskytují známé podezřelé soubory.
    * Proces se spustil z podezřelé cesty.
    * Proces se spustil v neobvyklém kontextu.
    * Proces byl spuštěn neobvyklým účtem.
    * Spustil se proces s podezřelou příponou.
    * Proces se spustil s podezřelou dvojitou příponou.
    * Spustil se proces, v jehož názvu souboru se vyskytuje podezřelý znak pro zápis zprava doleva.
    * Spustil se proces, jehož název je podobný ale liší od běžně spuštění procesu
    * Spustil se proces, jehož název odpovídá známému nástroji používanému útočníky.
    * Spustil se proces s náhodným názvem.
    * Spustil se proces s podezřelou příponou.
    * Spustil se skrytý soubor.
    * Proces se spustil jako podřízený proces dalšího nesouvisejícího procesu.
    * Systémový proces vytvořil neobvyklý proces.
    * Služba Windows Update spustila neobvyklý proces.
    * Proces se spustil s neobvyklým příkazovým řádkem. K tomuto chování dochází při napadení legitimních procesů za účelem spuštění škodlivého obsahu.
    * Z příkazového řádku se provedl pokus o spuštění všech spustitelných souborů (*.exe) v adresáři.
    * Proces se spustil pomocí nástroje PsExec, který se dá použít ke vzdálenému spouštění procesů.
    * Ke spuštění podezřelých podřízených procesů, které můžou být hostiteli škodlivých příkazů nebo je můžou spouštět, se použil nadřazený spustitelný soubor Apache Tomcat® (Tomcat#.exe).
    * Ke spuštění spustitelného kódu, který může být škodlivý, se použil Pomocník s kompatibilitou programů (pcalua.exe) Microsoft Windows.
    * Při ukončení procesu došlo k detekci podezřelého shlukového přenosu.
    * Systémový proces SVCHOST se spustil v neobvyklém kontextu.
    * Systémový proces SVCHOST se spustil v neobvyklé skupině služeb.
    * Spustil se podezřelý příkazový řádek.
    * Skript PowerShellu má společné vlastnosti se známými podezřelými skripty.
    * Spustila se známá škodlivá rutina PowerShellu Powersploit.
    * Předdefinovaný uživatel SQL spustil proces, který za normálních okolností nespouští.
    * Došlo k detekci spustitelného souboru s kódováním base-64, což může znamenat, že se útočník pokouší vyhnout detekci tak, že vytváří spustitelný soubor průběžně pomocí sekvence příkazů.

* **Podezřelá aktivita prostředku RDP**: Útočníci často cílí na otevřené porty pro správu jako je port RDP útoky hrubou silou. Tyto výstrahy indikují podezřelou aktivitu přihlášení Vzdálené plochy, což může znamenat toto:
    * Došlo k pokusům o přihlášení Vzdálené plochy.
    * Došlo k pokusům o přihlášení Vzdálené plochy pomocí neplatných účtů.
    * Došlo k pokusu o přihlášení vzdálené plochy, některé z nich bylo možné úspěšně se přihlásit k počítači.
* **Podezřelá aktivita prostředku SSH**: Útočníci často cílí na otevřené porty pro správu jako jsou SSH útoky hrubou silou. Tyto výstrahy indikují podezřelou aktivitu přihlášení SSH, což může znamenat toto:
    * Došlo k neúspěšným pokusům o přihlášení SSH.
    * Došlo k pokusům o přihlášení SSH, přičemž některé z nich byly úspěšné.
* **Podezřelá hodnota registru WindowPosition**: Tato výstraha indikuje, že se pokusil změnu konfigurace registru WindowPosition, což může naznačovat skrytí oken aplikací v neviditelných částech plochy.
* **Možný pokus o obejití Applockeru**: Applockeru lze omezovat procesy, které můžou běžet na Windows, omezuje jeho vystavení škodlivým softwarem. Tato výstraha indikuje možný pokus o obejití omezení AppLockeru tím, že ke spuštění nedůvěryhodného kódu se používají důvěryhodné spustitelné soubory (které jsou v zásadách AppLockeru povolené).
* **Podezřelá komunikace pojmenovaného kanálu**: Tato výstraha indikuje, že data byla zapsána do místního pojmenovaného kanálu z příkazu konzoly Windows. O pojmenovaných kanálech je známo, že je útočníci používají ke komunikaci se škodlivým implantátem a k jeho úkolování.
* **Dekódování spustitelného souboru pomocí integrovaného nástroje certutil.exe**: Tato výstraha indikuje, že integrovaný nástroj pro správce, certutil.exe, byla použita k dekódování spustitelného souboru. O útočnících je známo, že zneužívají funkce legitimních nástrojů pro správce k provádění škodlivých akcí, například pomocí nástroje, jako je certutil.exe, dekódují škodlivý spustitelný soubor, který se následně spustí.
* **Protokol událostí byl vymazán**: Tato výstraha indikuje podezřelou operaci, který často používají útočníci se pokouší zakrýt svoje stopy vymazání protokolu událostí.
* **Soubory protokolu pro zakázání a odstranění služby IIS**: Tato výstraha indikuje, že soubor protokolu IIS byl zakázán nebo odstraněn, což se často používá útočníci se pokouší zakrýt svoje stopy.
* **Podezřelé odstranění souborů**: Tato výstraha indikuje podezřelé odstranění souborů, které je možné použít ze strany útočníka odebrat důkazy o škodlivé binární soubory.
* **Byly odstraněny všechny stínové kopie souboru**: Tato výstraha indikuje, že se odstranily stínové kopie.
* **Příkazy pro čištění podezřelý soubor**: Tato výstraha indikuje kombinaci příkazů systeminfo, používá k provedení samočinného čištění aktivity po ohrožení zabezpečení.  *Systeminfo.exe* je sice legitimní nástroj Windows, ale jeho spuštění dvakrát za sebou s následným použitím příkazu pro odstranění, jak k tomu došlo tady, není obvyklé.
* **Podezřelé vytvoření účtu**: Tato výstraha indikuje, že byl vytvořen účet s nápadně podobá existujícímu integrovanému účtu s oprávněním správce. Tento postup můžou použít útočníci k vytvoření podvodného účtu, aniž by došlo k jeho detekci.
* **Aktivita stínové kopie svazku podezřelé**: Tato výstraha indikuje aktivitu odstranění stínové kopie u daného prostředku. Stínová kopie svazku je důležitý artefakt, který uchovává snímky dat. Tato aktivita je spojena s Ransomwarem, ale může být také legitimní.
* **Metoda přetrvávání v registru Windows**: Tato výstraha indikuje pokus o zachování spustitelného souboru v registru Windows. Tuto techniku používá malware často k tomu, aby nedošlo k jeho odstranění při spuštění počítače.
* **Podezřelé nové pravidlo brány firewall**: Tato výstraha indikuje, že nové pravidlo brány Firewall byly přidané prostřednictvím *netsh.exe* chcete povolit přenosy ze spustitelného souboru v podezřelém umístění.
* **Podezřelé spouštění příkazu xcopy**: Tato výstraha indikuje posloupnost spuštění příkazu XCOPY, která může signalizovat, že jedna z vašich počítačů došlo k napadení a jeho použití k šíření malwaru.
* **Potlačení právních upozornění uživatelům při přihlášení zobrazí**: Tato výstraha indikuje ke změně klíče registru, který určuje, zda se zobrazí právních upozornění uživatelům při přihlášení. Jde o běžnou aktivitu prováděnou útočníky po napadení hostitele.
* **Zjištění neobvyklé kombinace velkých a malých písmen na příkazovém řádku**: Tato výstraha indikuje použití kombinace velkých a malých písmen na příkazovém řádku, kde je technika používaná útočníky ke skrytí před pravidly počítače pro velká a malá písmena nebo hodnoty hash.
* **Obfuskovaný příkazový řádek**: Tato výstraha indikuje, že byly zjištěny podezřelých indikátorů obfuskace na příkazovém řádku.
* **Dotazování více doménových účtů**: Útočníci často dotazování účty domény AD při rekognoskaci uživatelů, účtů správců domén, řadičů domény a vztahy důvěryhodnosti mezi doménami. Tato výstraha indikuje, že během krátkého časového období se uskutečnilo dotazování na neobvyklý počet různých účtů domény.
* **Možná aktivita místní rekognoskace**: Tato výstraha indikuje, že byl proveden kombinaci příkazů systeminfo s aktivitou rekognoskace.  *Systeminfo.exe* je sice legitimní nástroj Windows, ale jeho spuštění dvakrát za sebou není obvyklé.
* **Možné spuštění spustitelného souboru KeyGen**: Tato výstraha indikuje, že byl proveden procesu, jehož název svědčí o nástroj keygen. Tyto nástroje se obvykle používají k překonání mechanismů pro licencování softwaru, ale jejich soubor ke stažení často obsahuje i jiný škodlivý software.
* **Podezřelé spuštění přes rundll32.exe**: Tato výstraha indikuje, že tento rundll32.exe byla použita pro proces s neobvyklým názvem, odpovídá schématu názvů procesů, kterou útočníci používají k instalaci implantátu první fáze na napadený hostitelský.
* **Podezřelá kombinace HTA a Powershellu**: Tato výstraha indikuje, že Microsoft HTML Application Host (HTA) spouští příkazy Powershellu. Jde o techniku používanou útočníky ke spuštění škodlivých skriptů PowerShellu.
* **Změna klíče registru, kterou lze zneužít k obejití nástroje Řízení uživatelských účtů**: Tato výstraha indikuje, že byl změněn klíč registru, který může zneužít k obejití nástroje Řízení uživatelských účtů (Řízení uživatelských účtů), který je kterou útočníci často používají pro přesun z bez oprávnění umožňovala zvlášť (standardní uživatel) pro přístup k oprávněním (například správce) na napadený hostitelský.
* **Použití podezřelého názvu domény na příkazovém řádku**: Tato výstraha indikuje, že byl použit podezřelého názvu domény, což může být známka toho, že útočník škodlivý nástroj, který hostuje a jako koncové body pro řízení a příkaz a průsak dat ven.
* **Byl vytvořen účet na více hostitelích během 24 hodin časového období**: Tato výstraha indikuje, že byl proveden pokus o vytvoření stejného uživatelského účtu na více hostitelích, které může být známka toho útočník neoprávněně sítě po jedné nebo více entit sítě být ohrožený.
* **Podezřelé použití příkazu CACLS snížit stavu zabezpečení systému**: Tato výstraha indikuje, že se změnil změna seznamu řízení přístupu (CACLS). Útočníci tento postup často používají k získání úplného přístupu k systémovým binárním souborům, jako jsou ftp.exe, net.exe, wscript.exe atd.
* **Podezření na parametry útok Kerberos Golden Ticket**: Tato výstraha indikuje, že byly spuštěny parametry příkazového řádku, které odpovídají útoku Kerberos Golden Ticket. Napadený klíč krbtgt může útočník zneužít k zosobnění libovolného uživatele.
* **Povolení klíče registru WDigest UseLogonCredential**: Tato výstraha indikuje, že klíč registru byl změněn na povolit přihlašování v přihlašovacích údajích k uložení ve formátu prostého textu v paměti LSA, které lze potom získat z paměti.
* **Možné podezřelé použití nástroje Telegram**: Tato výstraha indikuje instalaci telegramu, bezplatné cloudové rychlé zasílání zpráv služby používají útočníci přenášejí škodlivé binární soubory pro všechny počítače, telefon nebo tablet.
* **Vytvoření nového bodu ASEP**: Tato výstraha indikuje vytvoření nového ASEP (Auto Start Extensibility Point), což způsobí, že název procesu, který je identifikován na příkazovém řádku mají být automaticky spuštěny a je možné k přetrvávání ze strany útočníka.
* **Podezřelé Set-ExecutionPolicy a WinRM změny**: Tato výstraha indikuje změny konfigurace, které jsou spojené s použitím škodlivého Webshellu chinachopper.
* **Zakázání důležitých služeb**: Tato výstraha indikuje, že příkaz "net.exe stop" se použila k zastavení důležitých služeb, jako jsou SharedAccess nebo Windows Security Center.
* **Podezřelé použití přepínače FTP -s**: Tato výstraha indikuje použití FTP "-s" přepínače, který je možné malwarem, připojit ke vzdálenému serveru FTP a stahovat další škodlivé binární soubory.
* **Podezřelé spuštění příkazu VBScript.Encode**: Tato výstraha indikuje, *VBScript.Encode* byl proveden příkaz, který kóduje skripty do nečitelným textem a znesnadňuje uživatelům kontrolu kódu.
* **Přidělení objektu VBScript HTTP**: Tato výstraha indikuje vytvoření souboru VBScript pomocí příkazového řádku; které slouží ke stahování škodlivých souborů.
* **Útok**: Tato výstraha indikuje, že útočník může funkci binární dostupnost (třeba prstem, klávesnice na obrazovce, program Předčítání) Pokud chcete přístup zadními vrátky.
* **Indikátory ransomwaru Petya**: Tato výstraha indikuje, že byly dodrženy techniky spojené s ransomwarem Petya.
* **Pokus o zakázání rozhraní AMSI**: Tato výstraha indikuje pokus o zakázání kontroly rozhraní (AMSI), antimalwaru, které by se vypnula detekce antimalwaru.
* **Indikátory Ransomwaru**: Tato výstraha indikuje podezřelou aktivitu tradičně spojujeme s ransomwarem zamykací obrazovka a šifrování.
* **Výstupní soubor kolekce trasování podezřelé**: Tato výstraha indikuje, že trasování (například síťové aktivity) se shromažďují a výstup na neobvyklý typ souboru.
* **Vysoce rizikový software**: Tato výstraha indikuje používání softwaru, který je spojený s instalací malwaru. Útočníci často přibalují malware k jinak neškodným nástrojům, jako je třeba nástroj uvedený v této výstraze, a nainstalují tento malware nepozorovaně na pozadí.
* **Podezřelé vytvoření souboru**: Tato výstraha indikuje vytvoření nebo spuštění procesu, pomocí kterého útočníci po otevření přílohy v dokumentu útoku phishing stáhnout další malware do napadeného hostitelského počítače.
* **Podezřelé přihlašovací údaje na příkazovém řádku**: Tato výstraha indikuje použití podezřelého hesla ke spuštění souboru. Útočníci používají tuto techniku ke spuštění malwaru Pirpi.
* **Možné spuštění rozesílajícího malware**: Tato výstraha indikuje název souboru, který byl použit útočníky k instalaci malwaru.
* **Podezřelé spuštění přes rundll32.exe**: Tato výstraha indikuje rundll32.exe používaný k provedení notepad.exe nebo reg.exe, technice injektáže procesu, kterou používají útočníci.
* **Podezřelé argumenty příkazového řádku**: Tato výstraha indikuje podezřelé argumenty příkazového řádku, které se používají ve spojení s reverzní prostředí používá skupina aktivity HYDROGEN.
* **Přihlašovací údaje k podezřelé dokumentu**: Tato výstraha indikuje podezřelou předvypočítanou hodnotu hash hesla používá malware používaný ke spuštění souboru.
* **Dynamické vytváření skriptu PS**: Tato výstraha indikuje dynamické skript prostředí PowerShell. Útočníci používají tuto techniku k postupnému vytváření skriptu s cílem obejít systémy IDS (zjišťování neoprávněných vniknutí).
* **Indikátory Metasploit**: Tato výstraha indikuje aktivitu spojenou s architekturou metasploitu, která poskytuje celou řadu útočník funkcí a nástrojů.
* **Podezřelá aktivita účtu**: Tato výstraha indikuje pokus o připojení k počítači pomocí účtu, který byl nedávno napaden.
* **Vytvoření účtu**: Tato výstraha indikuje vytvoření nového účtu v počítači.


### <a name="crash-analysis"></a>Analýza stavu systému


Metoda analýzy paměti ve výpisu stavu systému slouží ke zjištění sofistikovaného malwaru, který je schopný se vyhnout běžným řešením zabezpečení. Různé formy malwaru se snaží snížit pravděpodobnost svého zjištění antivirovými produkty tím, že nikdy nezapisují na disk nebo softwarové komponenty ukládané na disk šifruje. Kvůli této technice je malware jen obtížně zjistitelný pomocí obvyklých antimalwarových postupů. Tento typ malwaru lze ale zjistit pomocí analýzy paměti, protože aby malware mohl fungovat, musí v paměti zanechat stopy.

Pokud dojde k chybě softwaru, ve výpisu stavu systému se zaznamená část paměti v době selhání. Taková chyba může být způsobena malwarem, běžnou aplikací nebo systémovými problémem. Díky analýze paměti ve výpisu stavu systému dokáže služba Security Center zjišťovat techniky, které využívají zranitelností softwaru, získávají přístup k důvěrným datům a nenápadně přetrvávají v napadeném počítači. Díky tomu, že se tato analýza provádí v back-endu služby Security Center, má minimální dopad na výkon hostitele.

* **Zjištění injektáže kódu**: Injektáž kódu představuje vložení spustitelných modulů do spuštěných procesů nebo vláken. Tuto techniku používá malware k přístup k datům, skrýt nebo zabránění jeho odstranění (například trvalost). Tato výstraha znamená, že ve výpisu je injektovaný modul. Vývojáři legitimního softwaru někdy provádějí injektáž kódu bez zlých úmyslů, například kvůli úpravě nebo rozšiřování stávající aplikace nebo součásti operačního systému. Security Center rozlišuje škodlivé injektované moduly a moduly bez zlých úmyslů na základě profilů podezřelého chování. Výsledek kontroly je u výstrahy uveden v poli SIGNATURE a určuje závažnost výstrahy, popis výstrahy a nápravné kroky.
* **Podezřelý segment kódu**: Výstraha podezřelého segmentu kódu indikuje, že segment kódu byla přidělena nestandardní metody, například používané reflektivní injektáží a hloubením procesů. Zpracovávají se další charakteristiky segmentu kódu, které poskytují kontext v souvislosti s jeho možnostmi a chováním.
* **Zjištěn skrytý spustitelný kód**: Skrytý spustitelný kód je datová část, která se spouští potom, co malware zneužije chybu zabezpečení softwaru. Tato výstraha znamená, že při analýze výpisu stavu systému byl nalezen spustitelný kód, který vykazuje chování typické pro škodlivý software. Někdy se může takovým způsobem chovat i software bez zlých úmyslů, pro běžný vývoj softwaru ale toto chování není typické.
* **Zjištěno napadení modulu**: Systém Windows umožňuje softwaru využívat základní systémové funkce prostřednictvím knihoven DLL (Dynamic Link Libraries). Při napadení knihoven DLL malware pozmění pořadí načítání knihoven DLL tak, aby umožnil načtení škodlivých datových částí do paměti, ze které je pak bude možné spustit. Tato výstraha znamená, že při analýze výpisu stavu systému byly nalezeny dva moduly s podobným názvem, ale načítané z různých cest. Jedna z načtených cest odpovídá běžnému umístění binárních souborů systému Windows. Vývojáři legitimního softwaru někdy mění pořadí načítání knihoven DLL bez zlých úmyslů, například při instrumentaci, rozšiřování operačního systému Windows nebo rozšiřování aplikací. Security Center u načteného modulu rozlišuje škodlivé změny pořadí načítání knihoven DLL od pravděpodobně bezpečných změn na základě profilů podezřelého chování.
* **Windows zjištěno maskování za modul**: Malware může používat běžné názvy binárních souborů systému Windows (například SVCHOST. Soubor EXE) nebo modulů (například NTDLL. Knihovny DLL) pro vmísil a skryl škodlivou povahu před správci systému. Tato výstraha znamená, že soubor výpisu stavu systému obsahuje moduly, které svým názvem odpovídají systémovým modulům Windows, ale nesplňují další kritéria typická pro systémové moduly. Další informace o povaze maskovaného modulu může poskytnout analýza kopie modulu na disku.
* **Zjištěna úprava binárního systémového**: Malware může upravit binární systémové soubory, aby mohl nepozorovaně přistupovat k datům nebo skrytě přetrvávat v napadeném systému. Tato výstraha znamená, že při analýze stavu systému byly v paměti nebo na disku nalezeny upravené binární soubory jádra systému Windows. Vývojáři legitimního softwaru někdy upravují systémové soubory v paměti bez zlých úmyslů, například soubory balíčku Detours pro zajištění kompatibility aplikací. Security Center rozlišuje škodlivé moduly od pravděpodobně bezpečných modulů na základě profilů podezřelého chování.

## <a name="network-analysis"></a>Analýza sítě
Detekce síťových hrozeb ve službě Security Center funguje tak, že se automaticky shromažďují informace o zabezpečení z přenosu Azure IPFIX (Internet Protocol Flow Information Export). Za účelem identifikace hrozeb služba tyto informace analyzuje a často přitom koreluje data z různých zdrojů.

* **Možné příchozí SQL, útoky hrubou vynutit pokusy**: Analýza provozu sítě zjistila podezřelou příchozí komunikaci SQL. Tato aktivita odpovídá pokusům o útoky hrubou silou na servery SQL.
* **Podezřelá příchozí síťová aktivita protokolu RDP z více zdrojů**: Analýza provozu sítě zjistila neobvyklou příchozí komunikaci protokolu RDP (Remote Desktop) z více zdrojů. Konkrétně vzorky síťových dat udávají, že se k vašemu počítači připojují jedinečné IP adresy, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat pokus o útok hrubou silou na koncový bod protokolu RDP z více hostitelů (botnet).
* **Podezřelá příchozí síťová aktivita protokolu RDP**: Analýza provozu sítě zjistila neobvyklou příchozí komunikaci protokolu RDP (Remote Desktop). Konkrétně vzorky síťových dat udávají velký počet příchozích připojení k vašemu počítači, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat pokus o útok hrubou silou na koncový bod protokolu RDP.
* **Podezřelá odchozí síťová aktivita protokolu RDP do více cílů**: Analýza provozu sítě zjistila neobvyklou odchozí komunikaci protokolu RDP (Remote Desktop) do více cílů. Tato aktivita může naznačovat, že došlo k napadení vašeho počítače, který se teď používá k útoku hrubou silou na externí koncové body protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.
* **Podezřelá odchozí síťová aktivita protokolu RDP**: Analýza provozu sítě zjistila neobvyklou odchozí komunikaci protokolu RDP (Remote Desktop). Konkrétně vzorky síťových dat udávají velký počet odchozích připojení z vašeho počítače, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat, že došlo k napadení vašeho počítače, který se teď používá k útoku hrubou silou na externí koncové body protokolu RDP. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.
* **Podezřelá příchozí síťová aktivita protokolu SSH**: Analýza provozu sítě zjistila neobvyklou příchozí komunikaci protokolu SSH. Konkrétně vzorky síťových dat udávají velký počet příchozích připojení k vašemu počítači, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat pokus o útok hrubou silou na koncový bod protokolu SSH.
* **Podezřelá příchozí síťová aktivita protokolu SSH z více zdrojů**: Analýza provozu sítě zjistila neobvyklou příchozí komunikaci protokolu SSH. Konkrétně vzorky síťových dat udávají, že se k vašemu počítači připojují jedinečné IP adresy, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat pokus o útok hrubou silou na koncový bod protokolu SSH z více hostitelů (botnet).
* **Podezřelá odchozí síťová aktivita protokolu SSH**: Analýza provozu sítě zjistila neobvyklou odchozí komunikaci protokolu SSH. Konkrétně vzorky síťových dat udávají velký počet odchozích připojení z vašeho počítače, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat, že došlo k napadení vašeho počítače, který se teď používá k útoku hrubou silou na externí koncové body protokolu SSH. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.
* **Podezřelá odchozí síťová aktivita protokolu SSH do více cílů**: Analýza provozu sítě zjistila neobvyklou odchozí komunikaci protokolu SSH do více cílů. Konkrétně vzorky síťových dat udávají, že se váš počítač připojuje na jedinečné IP adresy, což se pro toto prostředí považuje za neobvyklé. Tato aktivita může naznačovat, že došlo k napadení vašeho počítače, který se teď používá k útoku hrubou silou na externí koncové body protokolu SSH. Upozorňujeme, že tento typ aktivity může způsobit, že externí entity označí vaši IP adresu jako škodlivou.
* **Síťová komunikace se škodlivým počítačem zjistil**: Analýza provozu sítě označuje, že váš počítač komunikoval s tím, co je pravděpodobně příkazy a ovládání center.
* **Možného napadení počítače zjištěny**: Analýza provozu sítě zjistila odchozí aktivitu, která může znamenat, že funguje jako součást botnetu. Analýza je založená na IP adresách, ke kterým váš prostředek přistupuje, a na veřejných záznamech DNS.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Analýza SQL Database a SQL Data Warehouse

Analýza prostředků ve službě Security Center se soustředí na služby modelu Platforma jako služba (PaaS), jako je například integrace s funkcí [Detekce hrozeb Azure SQL Database](../sql-database/sql-database-threat-detection.md) a Azure SQL Data Warehouse. Funkce detekce hrozeb detekuje neobvyklé aktivity a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití a aktivuje následující výstrahy:

* **Zranitelnost vůči útoku prostřednictvím injektáže SQL**: Tato výstraha se aktivuje, pokud aplikace vygeneruje Chybný příkaz SQL v databázi. Může to znamenat možnou zranitelnost vůči útokům prostřednictvím injektáže SQL. Existují dva možné důvody vygenerování chybného příkazu:
    * Chyba v kódu aplikace, která způsobí sestavení chybného příkazu jazyka SQL
    * Kód aplikace ani uložené procedury neupravují uživatelský vstup při sestavování chybného příkazu SQL, který může být zneužit pro injektáž SQL.
* **Potenciální útok prostřednictvím injektáže SQL**: Tato výstraha se aktivuje při výskytu aktivního zneužití zranitelnosti identifikované aplikace zranitelnost vůči útoku prostřednictvím injektáže SQL. Znamená to, že se útočník pokouší vložit škodlivé příkazy SQL s použitím zranitelného kódu aplikace nebo uložených procedur.
* **Přístup z neobvyklého umístění**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklé geografické lokality. V některých případech výstraha detekuje legitimní akci (nová aplikace nebo údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
* **Přístup z neobvyklého datového centra Azure**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru z neobvyklého datového centra Azure, která už na tomto serveru se v poslední době. V některých případech výstraha rozpozná legitimní akci (nová aplikace v Azure, Power BI, editor dotazů SQL v Azure). V jiných případech výstraha detekuje škodlivou akci prováděnou z prostředku/služby Azure (bývalý zaměstnanec, externí útočník).
* **Přístup z neznámého objektu zabezpečení**: Tato výstraha se aktivuje, když dojde ke změně vzoru přístupu k systému SQL server, když někdo přihlásil k SQL serveru pomocí neobvyklého objektu zabezpečení (uživatel SQL). V některých případech výstraha detekuje legitimní akci (nová aplikace, údržba prováděná vývojářem). V jiných případech výstraha detekuje škodlivou akci (bývalý zaměstnanec, externí útočník).
* **Přístup z potenciálně škodlivé aplikace**: Tato výstraha se aktivuje, když se potenciálně škodlivé aplikace používá pro přístup k databázi. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok pomocí běžných nástrojů útoku.
* **Přihlašovací údaje SQL útok hrubou silou**: Tato výstraha se aktivuje po neobvykle vysoký počet neúspěšných přihlášení s jinými přihlašovacími údaji. V některých případech výstraha detekuje probíhající test průniku. V jiných případech výstraha detekuje útok hrubou silou.

## <a name="contextual-information"></a>Kontextové informace
Během šetření analytici potřebují další kontext, aby došli k závěru ohledně povahy hrozby a způsobu, jak ji zmírnit.  Představte si například, že byla zjištěna síťová anomálie. Bez informací o tom, co dalšího se v síti děje, a s ohledem na cílový prostředek je velmi obtížné pochopit, jaká opatření je třeba provést. Pro usnadnění, které, bezpečnostní Incident může zahrnovat artefakty, související události a informace, které mohou vyšetřovateli pomoci. Dostupnost dalších informací se bude lišit v závislosti na typu zjištěné hrozby a konfiguraci vašeho prostředí. Nebudou dostupné pro všechny incidenty zabezpečení.

Pokud jsou dostupné další informace, zobrazí se v incidentu zabezpečení pod seznamem výstrah. Může se jednat o informace tohoto typu:

- Události vymazání protokolu
- Zařízení PNP zapojená z neznámého zařízení
- Výstrahy, které nejsou užitečné
- Vytvoření nového účtu
- Dekódovaný soubor pomocí nástroje certutil

![Výstraha na neobvyklý přístup](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Další postup
V tomto článku jste se dozvěděli o různých typech výstrah zabezpečení ve službě Security Center. Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Řešení bezpečnostních incidentů v Azure Security Center](security-center-incident.md)
* [Možnosti detekce v Azure Security Center](security-center-detection-capabilities.md)
* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md)
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Přečtěte si nejčastější dotazy o použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.
