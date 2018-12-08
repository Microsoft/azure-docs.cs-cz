---
title: Osvědčené postupy pro zabezpečení a Správa úloh migrovat do Azure | Dokumentace Microsoftu
description: Po migraci do Azure, získejte doporučené postupy pro provoz, Správa a zabezpečení přenášená zatížení.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: raynew
ms.openlocfilehash: 201f6f463736674cee3f94cc0d0177a8ddb181a1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109725"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Osvědčené postupy pro zabezpečení a Správa úloh migrovat do Azure

Jako je plánování a návrh pro migraci, kromě přemýšlení o migraci, musíte naplánovat pro váš model zabezpečení a správy v Azure po migraci. Tento článek popisuje plánování a osvědčené postupy pro zabezpečení vašeho nasazení Azure po migraci a pro probíhající úlohy, aby vaše nasazení, které běží na libovolné úrovni optimální. 

> [!IMPORTANT]
> Osvědčené postupy a názory, které jsou popsané v tomto článku jsou založené na platformě Azure a služby funkce dostupné v době zápisu. Funkce a možnosti v průběhu času měnit.

## <a name="secure-migrated-workloads"></a>Zabezpečit přenášená zatížení

Po dokončení migrace nejdůležitějších úloh je zabezpečit přenášená zatížení z interních a externích hrozeb. Osvědčené postupy patří:

- Práce s Azure Security Center: Zjistěte, jak pracovat s nemonitoruje, neposuzuje a doporučení poskytovaných službou Azure Security Center
- **Šifrování dat**: získání osvědčené postupy pro šifrování dat v Azure.
- **Nastavení antimalwaru**: ochrana virtuálních počítačů před malwarem a útoky se zlými úmysly.
- **Zabezpečení webové aplikace**: Keep citlivých informací v zabezpečené migrovat webové aplikace.
- **Zkontrolujte předplatná**: ověření, kdo má přístup k prostředkům a předplatná Azure po migraci.
- **Práce s protokoly**: Zkontrolujte Azure auditování a zabezpečení protokoly v pravidelných intervalech.
- **Rozšířené funkce zabezpečení**: pochopit a vyhodnotit jiných funkcích zabezpečení, které Azure nabízí.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Osvědčený postup: doporučení služby Security Center postupujte podle Azure

Microsoft spolupracuje náročné zajistit, že správci tenanta Azure mají informace potřebné k povolení funkcí zabezpečení, které ochranu úloh před útoky.  Azure Security Center zajišťuje jednotnou správu zabezpečení. Ze služby Security Center můžete používat zásady zabezpečení napříč úlohami, omezení rizika ohrožení a detekovat a reagovat vůči útokům. Security Center analyzuje prostředky a konfigurací tenantů Azure a poskytuje doporučení zabezpečení, včetně:

- **Centralizovaná správa zásad** – Zajistěte dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení díky centrální správě zásad zabezpečení napříč všemi hybridními cloudovými úlohami.
- **Nepřetržité posuzování zabezpečení** – Monitorujte stav zabezpečení počítačů, sítí, služeb úložiště, datových služeb a aplikací za účelem zjišťování potenciálních problémů se zabezpečením.
- **Užitečná doporučení** – Napravujte ohrožení zabezpečení dříve, než je budou moci zneužít útočníci, díky užitečným doporučením zabezpečení s určenou prioritou.
- **Výstrahy a incidenty s určenou prioritou** – Zaměřte se nejprve na nejdůležitější hrozby díky výstrahám a incidentům zabezpečení.

Kromě posouzení a doporučení Security Center poskytuje řadu jiných funkcích zabezpečení, které je možné povolit pro konkrétní prostředky.

- **Přístup právě In Time (JIT)**: snížit vaše plochu vystavenou síťovým útokům s pouze v době řízený přístup k portům pro správu na virtuálních počítačích Azure.
    - S RDP k virtuálnímu počítači otevřený port 3389 na Internetu zpřístupňuje virtuálních počítačů pro kontinuální objektu actor chybný aktivity. Azure IP adresy jsou dobře známé a hackerům průběžně sběru dat je pro útoky na otevřené porty 3389. 
    - Pouze v zabezpečení sítě používá čas skupiny (Nsg) a příchozí pravidla tohoto limitu množství času, které konkrétní port je otevřený.
    - S právě včas povolena. Security Center kontroluje, zda uživatel má přístup na základě role (RBAC) zápisu oprávnění k přístupu pro virtuální počítač. Kromě toho určete pravidla, jak můžou uživatelé připojit k virtuálním počítačům. Pokud jsou oprávnění OK, žádosti o přístup se schválí a Security Center je nakonfiguruje skupiny Nsg umožňuje příchozí provoz do vybrané porty množství času, který je zadat. Skupiny Nsg se vraťte se do jejich předchozího stavu, při vypršení časového limitu.
- **Adaptivní řízení aplikací**: kvůli aktualizaci verzí softwaru a malware vypnout virtuální počítače řídit, které aplikace spouštět na nich.
    - pomocí dynamických aplikací na seznam povolených.
    - Adaptivní řízení aplikací vám umožní do seznamu povolených aplikací a zabránit instalaci softwaru neschválené nebo prověřování aplikací na virtuálních počítačích podvodný uživatelé nebo správci.
    - Můžete blokovat nebo oznámení pokusí o spuštění škodlivých aplikací, vyhněte se nechtěné nebo škodlivé aplikace a zajištění dodržování předpisů se zásadami zabezpečení vaší organizace aplikace.
- **Monitorování Integrity souborů**: zajištění integrity souborů běžící na virtuálních počítačích.
    - Nemusíte instalovat software způsobuje problémy virtuálního počítače.  Změna souboru systému může také způsobit snížení selhání nebo výkonu virtuálních počítačů.  Soubor integrity monitorování zkontroluje systémové soubory a nastavení registru pro změny a upozorní, pokud něco se aktualizuje.
    - Security Center doporučuje, které soubory, které jste měli byste sledovat.
Další informace o osvědčený postup: • spravovat virtuální počítač přístup pomocí metody just-in čas • adaptivního řízení aplikací ovládací prvky v Azure Security Center • monitorování Integrity souborů ve službě Azure Security Center

**Víc se uč:**

- [Další informace](https://docs.microsoft.com/azure/security-center/security-center-intro) o službě Azure Security Center.
- [Další informace](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) o právě přístup podle potřeby.
- [Další informace o](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) použít adaptivní řízení aplikací.
- [Začínáme](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) se monitorování Integrity souborů.


## <a name="best-practice-encrypt-data"></a>Osvědčený postup: šifrování dat 

Šifrování je důležitou součástí postupy zabezpečení Azure. Zajištění, že šifrování je povolená na všech úrovní pomáhá zabránit neoprávněným strany získat přístup k citlivým datům, včetně data přenášená i neaktivní uložená. 

### <a name="encryption-for-iaas"></a>Šifrování pro IaaS

- **Virtuální počítače**: k šifrování disků Windows a virtuálních počítačů IaaS s Linuxem můžete použít Azure Disk Encryption pro virtuální počítače.
    - Šifrování disku využívá nástroj Bitlocker pro Windows a DM-Crypt pro Linux zajišťuje šifrování disků s operačním systémem a data.
    - Můžete použít šifrování klíče vytvořeného pomocí Azure, nebo můžete zadat vlastní šifrovací klíče ochrany ve službě Azure Key Vault. 
    - Šifrování disků, se šifrují data virtuálních počítačů IaaS v klidovém stavu (na disk) a při spuštění virtuálního počítače. 
    - Azure Security Center vás upozorní, pokud máte virtuální počítače, které nejsou šifrovány.
- **Úložiště**: ochrana neaktivní data uložená ve službě Azure storage.
    - Data ukládají v účtech úložiště Azure se dají zašifrovat pomocí klíčů AES vygeneroval Microsoft, které jsou FIPS 140-2 standardu nebo můžete použít vlastní klíče.
    - Šifrování služby Storage je povolená pro všechny účty nová a existující úložiště a nejde zakázat.


### <a name="encryption-for-paas"></a>Šifrování pro PaaS

Na rozdíl od IaaS, kde budete spravovat vlastní virtuální počítače a infrastrukturu v PaaS modelu platforma a infrastruktura spravuje poskytovatele, poskytne vám soustředit se na základní aplikaci logiky a možnosti. Mnoho různých typů služeb PaaS každá služba se vyhodnotí jednotlivě z bezpečnostních důvodů. Jako příklad podíváme, jak můžeme povolit šifrování pro službu Azure SQL Database.

- **Funkce Always Encrypted**: ochrana dat v klidovém stavu pomocí průvodce vždy šifrované v SQL Server Management Studio.
    - Můžete vytvořit s funkcí Always Encrypted klíč k šifrování dat jednotlivých sloupců.
    - Always Encrypted klíčů lze uložit jako šifrovaný v databázi metadat nebo uložené v důvěryhodného klíče úložišť, jako je Azure Key Vault.
    - Změny aplikace bude pravděpodobně potřeba pro použití této funkce.
- **Transparentní šifrování dat (TDE)**: chránit databáze SQL Azure v reálném čase šifrování a dešifrování databáze, přidružené zálohy a soubory protokolů transakcí v klidovém stavu.
    - Transparentní šifrování dat umožňuje šifrování aktivity uskutečnit bez nutnosti změn ve vrstvě aplikací.
    - Transparentní šifrování dat můžete použít šifrovací klíče poskytovaných microsoftem, nebo můžete zadat vlastní klíče s využitím podpory vlastního klíče.


**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) Azure Disk Encryption pro virtuální počítače IaaS.
- [Povolit](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) šifrování pro virtuální počítače IaaS s Windows.
- [Další informace o](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) šifrování služby Azure Storage pro neaktivní uložená data.
- [Čtení](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) Přehled funkce Always Encrypted.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) transparentní šifrování dat pro službu Azure SQL Database.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) TDE a přineste si vlastní klíč.

## <a name="best-practice-protect-vms-with-antimalware"></a>Osvědčený postup: ochrana virtuálních počítačů s využitím antimalwaru

Zejména starší Azure migrované virtuální počítače nemusí mít odpovídající úroveň antimalware nainstalované.  Azure poskytuje řešení zdarma koncový bod, který pomáhá chránit virtuální počítače před viry, spyware nebo jiný malware.
- Microsoft Antimalware pro Azure generuje výstrahy, když je známo, že škodlivý nebo nežádoucí software pokusí nainstalovat.
- Je řešení jednoho agenta, které běží na pozadí bez zásahu člověka.
- Ve službě Azure Security Center můžete snadno identifikovat virtuální počítače, které nemáte spuštění služby endpoint protection a podle potřeby instalaci Microsoft Antimalware.

![Antimalware pro virtuální počítače](./media/migrate-best-practices-security-management/antimalware.png)
*Antimalware pro virtuální počítače*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/security/azure-security-antimalware) Microsoft Antimalware.

## <a name="best-practice-secure-web-apps"></a>Osvědčený postup: zabezpečené webové aplikace

Migrované webové aplikace pro rozpoznávání tváře několik problémů:

- Většina starší verze webové aplikace obvykle obsahují citlivé informace v konfiguračních souborech.  Soubory, které obsahují tyto informace mohou způsobovat potíže se zabezpečením, když jsou zálohované aplikací, nebo když kód aplikace je zaregistrované do nebo ze správy zdrojového kódu.
- Kromě toho při migraci webové aplikace, které se nacházejí ve virtuálním počítači je pravděpodobně přesouváte tento počítač z místní sítě a brány firewall chráněného prostředí služby do prostředí směřující internet. Musíte zajistit, abyste nastavili řešení, které provádí stejné jako na místní prostředky ochrany.


Azure poskytuje několik řešení:

- **Služba Azure Key Vault**: dnes vývojáře webových aplikací trvá postup, jak zajistit, že se z těchto souborů úniku citlivých informací. Jedním ze způsobů zabezpečit informace je extrahovat ze souborů a vložit ho do služby Azure Key Vault.
    - Můžete centralizovat úložiště tajných částí aplikace pomocí služby Key Vault a řídit jejich distribuci. Se eliminuje nutnost ukládat informace o zabezpečení v souborech aplikace.
    - Aplikace můžete informace o zabezpečení přístupu v trezoru pomocí identifikátorů URI, aniž by bylo vlastní kód.
    - Služba Azure Key Vault můžete zamezit přístup přes kontrolních mechanismů pro zabezpečení Azure a bez problémů provádět "klíče". Microsoft neznala ani neextrahovala vaše data.
- **App Service Environment**: Pokud migrujete aplikace potřebuje ochrany, můžete zvážit přidání služby App Service Environment a Web Application Firewall k ochraně prostředků aplikace.
    - Azure App Service Environment poskytuje plně izolované a vyhrazené prostředí ve kterém ke spuštění služby App Service, aplikace, například Windows a Linuxové webové aplikace, kontejnery Docker, mobilní aplikace a funkce.
    - Je vhodné pro aplikace, které jsou velmi velkém měřítku, vyžadují izolaci a zabezpečený přístup k nebo mají vysoké využití paměti
- **Firewall webových aplikací**: funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací.
    - Chrání webové aplikace bez nutnosti změny kódu back-endu.
    - Chrání více webových aplikací současně za službou application gateway.
    - Firewall webových aplikací je možné monitorovat pomocí Azure monitoru a je integrovaná do služby Azure Security Center.



![Zabezpečení webové aplikace](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**Víc se uč:**

- [Získejte přehled](https://docs.microsoft.com/azure/key-vault/key-vault-overview) z Azure Key Vault.
- [Další informace o](https://docs.microsoft.com/azure/application-gateway/waf-overview) firewallu webových aplikací.
- [Iseznamte](https://docs.microsoft.com/azure/app-service/environment/intro) do služby App Service Environment.
- [Zjistěte, jak](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) konfigurace webové aplikace pro čtení tajných klíčů ze služby Key Vault.
- [Další informace o](https://docs.microsoft.com/azure/application-gateway/waf-overview) Firewall webových aplikací

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Osvědčený postup: Zkontrolujte oprávnění prostředků a předplatná

Migrujte své úlohy a běží v Azure, pracovníci s přístupem k úloze pohybovat. Bezpečnostní tým by měl zkontrolovat přístup do Azure skupin klienta a prostředků v pravidelných intervalech. Azure nabízí celou řadu nabídky pro správu identit a řízení přístupu, včetně řízení přístupu na základě rolí (RBAC) k autorizaci oprávnění pro přístup k prostředkům Azure.

- RBAC přiřadí přístupová oprávnění pro objekty zabezpečení. Objekty zabezpečení představuje uživatele, skupiny (skupinu uživatelů), instanční (identitu používanou aplikací a služeb) a spravovat identity (automaticky spravuje Azure identity Azure Active Directory).
- RBAC můžete přiřadit role principů zabezpečení, jako je například vlastník, Přispěvatel a Čtenář a role definice (kolekce oprávnění), které definují operace, které může role provádět.
- RBAC můžete také nastavit obory, které nastavení hranic pro roli. Počet úrovní, včetně skupiny pro správu, předplatné, skupinu prostředků nebo prostředek, které je možné nastavit obor
- Ujistěte se, že správci s přístupem k Azure se pouze pro přístup k prostředkům, které chcete povolit.  Pokud nejsou dostatečně podrobné předdefinované role v Azure, můžete vytvořit vlastní role pro oddělení a omezení oprávnění k přístupu.

![Řízení přístupu](./media/migrate-best-practices-security-management/subscription.png)
*řízení přístupu – IAM*

**Víc se uč:**

- [O](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Přečtěte si](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) ke správě přístupu pomocí RBAC a webu Azure portal.
- [Další informace o](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) vlastní role.

## <a name="best-practice-review-audit-and-security-logs"></a>Osvědčený postup: kontrolovat protokoly auditu a zabezpečení

Azure Active Directory (AD) poskytuje protokoly aktivit, které se zobrazí ve službě Azure Monitor. Protokoly zachycení operací provedených v Azure tenantů, když k nim došlo a který je provedl. 

- Protokoly auditu zobrazit historii úloh v tenantovi. Protokoly aktivit přihlášení zobrazit, který provedl úlohy. 
- Přístup k sestavám zabezpečení závisí na vaší licenci Azure AD. V Free a Basic získáte seznam rizikových uživatelů a přihlášení. V edicích Premium 1 a Premium 2 získáte základní informace o události.
- Počet koncových bodů pro dlouhodobé uchovávání dat a přehledy dat můžete směrovat protokoly aktivit.
- Nastavte ji běžnou praxí prohlížet protokoly nebo integrovat svoje zabezpečení informací a událostí (SIEM) nástroje pro správu automaticky zkontrolovat anomálie.  Pokud nepoužíváte Premium 1 nebo 2, musíte udělat spoustu sami, nebo pomocí vašeho systému SIEM.  Analýza zahrnuje hledá rizikových přihlášení a události a vzory útoků jiné uživatele.


![Uživatelé a skupiny](./media/migrate-best-practices-security-management/azure-ad.png)
*Azure AD Uživatelé a skupiny*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) protokoly aktivit Azure AD ve službě Azure Monitor.
- [Zjistěte, jak](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) auditní sestavy aktivit na portálu Azure AD.

## <a name="best-practice-evaluate-other-security-features"></a>Osvědčený postup: Vyzkoušejte jiné funkce zabezpečení

Azure nabízí celou řadu jiných funkcích zabezpečení, které zajišťují rozšířené možnosti zabezpečení. Některé z těchto osvědčených postupů vyžadují licencí doplňku a prémiových možností.

- **Implementace jednotky pro správu Azure AD (AU)**: delegování správce povinnosti, aby pracovníci podpory, může být velmi obtížné s řízením přístupu pouze základní Azure.  Udělení přístupu pracovníci podpory ke správě všech skupin ve službě Azure AD, nemusí být ideální přístup pro organizace na zabezpečení.  Použití AU umožňuje oddělit prostředků Azure do kontejnerů v podobným způsobem jako v místním organizační jednotky (OU).  Použití AU AU správce musí mít licenci Azure AD premium. [Další informace](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Použít vícefaktorové ověřování (MFA)**: Pokud máte licence Azure AD premium můžete povolit a vynucení vícefaktorového ověřování u účtů správce. Phishing je nejběžnějším způsobem, že přihlašovací údaje účtů dojde k ohrožení.  Jakmile objektu actor chybný přihlašovací údaje účtu správce, neexistuje žádné zastavení je z dalekosáhlý akcí, jako je například odstranění všech vašich skupinách prostředků. Vícefaktorové ověřování můžete nastavit různými způsoby, třeba s e-mailu, aplikace authenticator a Telefon textové zprávy. Jako správce můžete vybrat možnost nejméně nežádoucí. Vícefaktorové ověřování se integruje s analýzou hrozeb a reakce zásady podmíněného přístupu a náhodně vyžadují ověřovacím testem MFA. Další informace o [doprovodné materiály k zabezpečení](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices), a [nastavení](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) vícefaktorové ověřování.
- **Implementace podmíněného přístupu**: ve většině organizací malé a střední správci Azure a na tým podpory pravděpodobně nacházejí v jedné zeměpisné oblasti. Většina přihlášení v tomto případě budou přicházet z oblasti stejné. Pokud jsou poměrně statické IP adresy z těchto umístění, má smysl, že by se neměly zobrazovat přihlášení správce z mimo tyto oblasti. I v událost, ve kterém vzdáleného objektu actor chybný ohrožuje přihlašovací údaje správce můžete implementovat funkce zabezpečení, jako je podmíněný přístup v kombinaci s MFA, aby se zabránilo přihlášení ze vzdáleného umístění, nebo z podvodného umístění z náhodných IP adres. [Další informace](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) o podmíněném přístupu a [osvědčené postupy](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) pro podmíněný přístup ve službě Azure AD.
- **Kontrola oprávnění podniková aplikace**: v průběhu času správce klikněte na Microsoft a třetích stran odkazy bez znalosti jejich dopadu na organizaci. Odkazy můžete prezentovat souhlasu obrazovky, které přiřazení oprávnění pro aplikace Azure a může povolit přístup ke čtení dat služby Azure AD, nebo dokonce úplný přístup ke správě celého předplatného Azure. Měli byste pravidelně kontrolovat, aplikace, ke kterým správci a uživatelé mají povolený přístup k prostředkům Azure. Měli byste zajistit, že tyto aplikace mají jenom oprávnění, které jsou nezbytné. Kromě toho čtvrtletně nebo ročně středníkem můžete poslat e-mail uživatele s odkazem na stránky aplikace tak, že jsou to vědět, aplikace, ke kterým jsou jste povolili přístup k datům jejich organizace. [Další informace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) o typech aplikací, a [jak ovládací prvek](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) přiřazení aplikací ve službě Azure AD.



## <a name="managed-migrated-workloads"></a>Spravované migrované úlohy

V této části vám doporučujeme některé osvědčené postupy pro správu Azure, včetně:

- **Správa prostředků**: osvědčené postupy pro skupiny prostředků Azure a prostředky, včetně inteligentní pojmenování, zabraňuje nechtěnému odstranění, Správa oprávnění k prostředkům a označování efektivní prostředků.
- **Použití podrobné plány**: Získejte rychlý přehled o používání plány pro vytváření a správu prostředí nasazení.
- **Kontrola architektury**: revize ukázkové architektury Azure k poslechněte si, jak sestavit vaše nasazení po migraci.
- **Nastavení skupin pro správu**: Pokud máte více předplatných, můžete shromáždit do skupin pro správu a použít nastavení zásad správného řízení do těchto skupin.
- **Nastavení zásad přístupu**: použití zásad dodržování předpisů vašich prostředků Azure.
- **Implementovat strategii BCDR**: dohromady kontinuity podnikových procesů a po havárii (BCDR) strategii zotavení aby byla data v bezpečí, vaše prostředí, které jsou odolné a prostředky a spuštění když dojde k výpadku.
- **Správa virtuálních počítačů**: seskupení virtuálních počítačů do skupiny dostupnosti pro odolnost a vysoká dostupnost. Použití spravovaných disků pro snadnou správu diskem a úložištěm virtuálních počítačů.
- **Monitorovat využití prostředků**: povolení protokolování diagnostiky pro prostředky Azure, vytvářet výstrahy a playbooky proaktivní při řešení potíží a použít řídicí panel Azure jednotný přehled o stavu nasazení a stav.
- **Správa podporu a aktualizace**: pochopení vašeho plánu podpory Azure a jak ho implementovat, získat osvědčené postupy pro udržování aktuálnosti virtuální počítače a vložení procesy pro správu změn.


## <a name="best-practice-name-resource-groups"></a>Osvědčený postup: název skupiny prostředků

Zajištění, že vaší skupiny prostředků tohoto Správci mají smysluplné názvy a členy týmu podpory snadné rozpoznat a přejděte se výrazně vyšší produktivitu a efektivitu.
- Doporučujeme následující zásady pro vytváření názvů.
- Pokud se synchronizace místní služby AD DS do služby Azure AD pomocí služby AD Connect, vezměte v úvahu odpovídající názvy zabezpečení skupiny místní názvy skupin prostředků v Azure.

![Pojmenování](./media/migrate-best-practices-security-management/naming.png)
*pojmenování skupiny prostředků*


**Víc se uč:**

[Další informace o](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) zásady vytváření názvů

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Osvědčený postup: implementace odstranit zámky pro skupiny prostředků

Poslední věcí, kterou je třeba je pro skupinu prostředků, abyste zmizí, protože byl odstraněn omylem. Doporučujeme implementovat odstranit zámky, aby se to nestane.


![Odstranit zámky](./media/migrate-best-practices-security-management/locks.png)
*odstranit zámky*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) zamknutí prostředků, aby se zabránilo neočekávaným změnám.


## <a name="best-practice-understand-resource-access-permissions"></a>Osvědčený postup: pochopení oprávnění přístupu k prostředkům 

Vlastník předplatného má přístup do všech skupin prostředků a prostředků ve vašem předplatném.
- Přidáte lidi střídmě tento cenný přiřazení. Principy následky tyto typy oprávnění je důležité pro zachování prostředí zabezpečené a stabilní.
- Ujistěte se, že umístíte do příslušné prostředky skupiny prostředků:
    - Prostředky se podobá životním cyklem odpovídat společně. V ideálním případě by neměli byste potřebovat přesunout prostředek, pokud musíte odstranit celou skupinu prostředků.
    - Prostředky, které podporují funkci nebo úlohy umístit společně pro zjednodušení správy.

**Víc se uč:**

- [Další informace o](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) uspořádání předplatných a skupin prostředků.

## <a name="best-practice-tag-resources-effectively"></a>Osvědčený postup: efektivně označení prostředků

Často používat pouze název skupiny prostředků související s prostředky neposkytne dostatek metadat pro efektivní provádění mechanismy, jako je interní fakturace nebo správy v rámci předplatného.
- Jako osvědčený postup používejte přidat užitečná metadata, která se jde dotazovat a ohlášeny chyby v Azure značky. 
- Značky poskytují způsob, jak logicky uspořádat prostředky s vlastnostmi, které definujete.  Značky lze použít ke skupinám prostředků nebo prostředky přímo.
- Značky lze použít pro skupinu prostředků nebo u jednotlivých prostředků. Prostředky ve skupině nedědí značky skupiny prostředků.
- Můžete automatizovat označování pomocí Powershellu nebo Azure Automation, nebo značky jednotlivých skupin a prostředky. -označování přístup nebo na samoobslužné služby.  Pokud máte požadavek a změnit systému pro správu na místě, můžete snadno využít informace v požadavku k naplnění značek prostředků specifických pro společnost.


![Označování](./media/migrate-best-practices-security-management/tagging.png)
*označování*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) označování a označit omezení.
- [Kontrola](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) příklady prostředí PowerShell a rozhraní příkazového řádku k nastavení označování a chcete použít značky ze skupiny prostředků na prostředky.
- [Čtení](http://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) označování osvědčené postupy Azure.


## <a name="best-practice-implement-blueprints"></a>Osvědčený postup: provedení podrobné plány.

Stejně jako podrobný plán umožňuje technikům a architektům náčrt parametry návrh projektu, Azure podrobné plány umožňují cloudovým architektům a centrální skupiny IT k definování opakovatelné sadu prostředků Azure, která implementuje a dodržuje standardy organizace vzory a požadavky. Pomocí Azure podrobné plány. vývojové týmy můžete rychle sestavit a vytvořit nové prostředí, které splňují požadavky na dodržování předpisů v organizaci, a které mají sadu předdefinovaných komponenty, například sítě, ke zrychlení vývoje a distribuce.

- Použití podrobné plány můžete orchestrovat nasazení skupiny prostředků, šablon Azure Resource Manageru a přiřazení zásad a role.
- Azure plány jsou uloženy v globálně distribuované služby Azure Cosmos DB. Objekty podrobného plánu se replikují do několika oblastí Azure. Replikace poskytuje nízkou latenci, vysokou dostupnost a jednotný přístup k podrobný plán, bez ohledu na oblast, do které podrobný plán nasadí prostředků.

**Víc se uč:**

- [Čtení](https://docs.microsoft.com/azure/governance/blueprints/overview) o podrobné plány.
- [Kontrola](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) podrobného plánu příklad použitý ke zrychlení AI ve zdravotnictví.

## <a name="best-practice---review-azure-reference-architectures"></a>Osvědčeným postupem – referenční architektury Azure revize

Vytváření zabezpečené, škálovatelné a spravovatelné úloh v Azure může být složitý.  Průběžné změny může být obtížné udržovat tempo s různými funkcemi pro optimální prostředí. S odkazem na Učte se od může být užitečné při navrhování a migraci vašich úloh.  Azure a Azure partneři vytvořili několik ukázkové referenční architektury pro různé typy prostředí. Tyto ukázky jsou navržené tak poskytovat návrhy, které mohou být zdrojem cenných a sestavení. 

Referenční architektury jsou uspořádané podle scénáře. Obsahují doporučujeme postupy a Rady, správy, dostupnost, škálovatelnost a zabezpečení.

**Víc se uč:**

[Další informace o](https://docs.microsoft.com/azure/architecture/reference-architectures/) referenční architektury Azure.
[Kontrola](https://docs.microsoft.com/azure/architecture/example-scenario/) Azure ukázkové scénáře.

## <a name="best-practice-manage-resources-with-management-groups"></a>Osvědčený postup: Správa prostředků pomocí skupin pro správu

Pokud má vaše organizace více předplatných, musíte spravovat přístup, zásady a dodržování předpisů pro ně. Skupiny pro správu Azure představují úroveň rozsahu nad předplatnými.

- Uspořádání předplatných do kontejnerů nazývaných skupiny pro správu a na ně vztahují podmínky zásad správného řízení.
- Všechna předplatná ve skupině pro správu automaticky dědí podmínky skupiny správy.
- Skupiny pro správu poskytují podnikové správy ve velkém měřítku, bez ohledu na to, jaký typ předplatného máte.
- Například můžete použít zásady skupiny správy, který omezuje oblasti, ve kterých je možné vytvořit virtuální počítače. Pak je tato zásada použitá pro všechny skupiny pro správu, předplatná a prostředky v rámci této skupiny pro správu.
- Můžete vytvářet flexibilní strukturu skupin pro správu a předplatné, k uspořádání prostředků do hierarchie pro jednotné zásady a správu přístupu.

Následující diagram ukazuje příklad vytvoření hierarchie pro zásady správného řízení s využitím skupin pro správu.

![Skupiny pro správu](./media/migrate-best-practices-security-management/management-groups.png)
*skupin pro správu*

**Víc se uč:**
- [Další informace](https://docs.microsoft.com/azure/governance/management-groups/index) o uspořádání prostředků do skupin pro správu.

## <a name="best-practice-deploy-azure-policy"></a>Osvědčený postup: nasazení zásad Azure

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad.

- Zásady vynucují různá pravidla a efekty u vašich prostředků, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni.
- Služba Azure Policy vyhodnotí prostředky, vyhledává ty, které nejsou kompatibilní s vašimi zásadami.
- Můžete například vytvořit zásadu, která umožňuje pouze určité velikosti skladové položky virtuálních počítačů ve vašem prostředí. Služba Azure Policy vyhodnotí toto nastavení při vytváření a aktualizaci prostředků a při kontrole stávajících prostředků. 
- Azure poskytuje několik integrovaných zásad, které můžete přiřadit nebo můžete vytvořit svoje vlastní.


![Služba Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*Azure Policy*

**Víc se uč:**
- [Získejte přehled](https://docs.microsoft.com/azure/governance/policy/overview) o službě Azure Policy.
- [Další informace o](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) vytváření a Správa zásad pro vynucování dodržování předpisů.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Osvědčený postup: implementovat strategii BCDR

Plánování pro provozní kontinuitu a zotavení po havárii (BCDR), je důležité cvičení, která se musí dokončit během plánování migrace do Azure. V právní podmínky vaše smlouva obsahuje klauzuli zásahem vyšší moci, která excuse povinnosti větší platnost, jako je například hurikány nebo zemětřesení. Ale máte také povinnosti kolem možnosti k zajištění, že služby budou dál spouštět a obnovení v případě potřeby po strike po havárii. Možnost k tomu může vylepšit nebo zničit vaše společnost budoucnost.


Platforma Azure poskytuje celou řadu funkcí odolnosti:

- **Párování oblastí**: páry oblastí poskytuje místní ochranu v rámci mezí rezidence dat Azure. Azure zajišťuje fyzickou izolaci mezi párování oblastí, upřednostňuje obnovení jedné oblasti v páru v případě rozsáhlého výpadku, nasadí aktualizace systému samostatně v jednotlivých oblastech a umožňuje funkce, jako je geograficky redundantní úložiště Azure k replikaci mezi páry oblastí.
- **Zóny dostupnosti**: zóny dostupnosti ochranu proti selhání celého datového centra Azure tím, že fyzické samostatných zón s oblasti Azure. Každou zónu má zdroj rozlišovací napájení, síťovou infrastrukturu a chladicí mechanismus.
- **Skupiny dostupnosti**: dostupnosti ochranu proti selhání v rámci datacentra. Můžete seskupit virtuální počítače ve skupinách dostupnosti Novoroční s vysokou dostupností. V každé skupině dostupnosti Azure implementuje víc domén selhání najednou, hardware s společný zdroj napájení a síťový přepínač a aktualizačních doménách, které seskupují hardwarových komponent, které můžete provést údržbu nebo restart, základní skupiny ve stejnou dobu. Jako příklad Pokud úloha se pak rozdělí mezi virtuální počítače Azure, můžete umístit dva nebo více virtuálních počítačů pro každou vrstvu aplikace do sady. Můžete například umístit front-endový virtuální počítače v jedné sadě a datovou vrstvu virtuálních počítačů v jiném. Protože je jenom jedna aktualizační doména každý restartuje v sadě a Azure tak zajišťuje jistotu, že virtuální počítače v sadě jsou rozděleny mezi doménami selhání, zajistíte tím, že ne všechny virtuální počítače v sadě se nepodaří ve stejnou dobu.


Při migraci do Azure, je důležité pochopit, i když Platforma Azure poskytuje tyto integrovaných možností a dalším službám BCDR, je potřeba navrhnout Azure nasazení tak, aby mohli využívat Azure odolnost, dostupnost, po havárii obnovení a zálohování služby a funkce.
- Řešení BCDR závisí vaše cíle společnosti a bude mít vliv nasazení vašeho řešení Azure. Infrastruktura jako služba (IaaS) a platforma jako služba (PaaS) nasazení k dispozici různé problémy pro zajištění BCDR.
- Jednou v místě, řešení BCDR by měl být testován pravidelně ke kontrole, zůstane strategie přijatelné.


## <a name="best-practice-back-up-your-deployment"></a>Osvědčený postup: zálohování vašeho nasazení

Ve většině případů místní úlohy byl vyřazen z provozu po migraci, a vaše místní strategie pro zálohování dat musí být rozšířit nebo nahradit. Pokud migrujete celé datové centrum do Azure, je potřeba navrhovat a implementovat řešení úplného zálohování, které využívá technologie Azure nebo integrovaná řešení třetích stran. Vaše vybrané řešení zálohování se bude lišit v závislosti na nasazení vašeho řešení Azure. 


### <a name="back-up-an-iaas-deployment"></a>Zálohování IaaS nasazení


Pro úlohy běžící na virtuálních počítačích Azure IaaS byste zvážit použití následující řešení zálohování:

- **Azure Backup**: poskytuje zálohy konzistentní s aplikací pro Windows Azure a virtuální počítače s Linuxem.
- **Snímky úložiště**: pořizovat snímky objektů blob úložiště.

#### <a name="azure-backup"></a>Azure Backup


Zálohuje Azure Backup vytváří body obnovení dat, které jsou uložené ve službě Azure storage. Azure Backup můžete zálohovat na disky virtuálních počítačů Azure a službou soubory Azure (preview). Služba soubory Azure poskytují sdílené složky v cloudu, přístupné přes protokol SMB.
   
Azure Backup můžete použít k zálohování virtuálních počítačů v několika způsoby.

- **Přímé zálohování z nastavení virtuálního počítače**:
    - Azure Backup je integrovaná do možností virtuálního počítače na webu Azure Portal.
    - Můžete zálohovat tento virtuální počítač jednou denně a obnovení disku virtuálního počítače. 
    - Azure Backup pořídí snímky dat s ohledem na aplikace (VSS).
    - Na virtuálním počítači se neinstaluje žádný agent.
- **Přímé zálohování v trezoru služby Recovery Services**:
    - Nasazení služby Azure Backup Recovery Services vault a vyberte virtuální počítače pro zálohování.
    - Tato metoda poskytuje jedno centrální umístění pro sledování a správa záloh a další zálohy a možnosti obnovení. 
    - Azure Backup nainstaluje agenta Microsoft Azure Recovery Services (MARS) ve virtuálním počítači.
    - Zálohování se až třikrát za den.
    - Zálohování je na úrovni souboru nebo složky které není s ohledem na aplikace. Linux není podporována.
- **Azure Backup serveru: Ochrana virtuálních počítačů do Azure Backup Server**:
   - Azure Backup serveru se službou Azure Backup poskytuje zdarma.
   - Virtuální počítač zálohovaný do místního úložiště záloh Azure.
   - Můžete pak zálohování Azure Backup serveru do Azure na trezor služby Recovery Services.
   - Zálohování je aplikace používající, s plnou členitosti přes často zálohování a uchovávání
   - Můžete zálohovat na úrovni aplikace. Například tím, že zálohování serveru SQL Server nebo SharePoint.

Azure Backup automaticky přiděluje a spravuje úložiště pomocí modelu s průběžnými platbami za úložiště, které skutečně využijete.
- Úložiště může být přiřazen jako sazbou za LRS nebo GRS.
- Azure Backup šifruje data vydávaných za pochodu pomocí AES 256 a odesílá je do Azure přes protokol HTTPS. Zálohovaná data v klidovém stavu v Azure je zašifrovaná pomocí [šifrování služby Storage (SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)a data pro přenos a ukládání.


![Azure Backup](./media/migrate-best-practices-security-management/iaas-backup.png)
*Azure Backup*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) různých typech zálohování.
- [Plánování infrastruktury zálohování](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) pro virtuální počítače Azure.

#### <a name="storage-snapshots"></a>Snímky úložiště

Virtuální počítače Azure jsou uložené jako objekty BLOB stránky ve službě Azure Storage. 

- Snímky zachycují stav objektů blob v konkrétním bodě v čase.
- Jako alternativní záložní metoda pro disky virtuálních počítačů Azure můžete pořídit snímek úložiště objektů BLOB a zkopírujte je do jiného účtu úložiště. 
- Můžete zkopírovat celý objekt blob, nebo použijte přírůstkový snímek kopírování pro kopírování jenom rozdílové změny a zmenšení prostoru úložiště.
- Jako další opatření můžete povolit obnovitelného odstranění pro účty blob storage. Tato funkce povolena je objekt blob, který je odstraněn, ale nikoliv okamžitě odstraněna označená k odstranění. V přechodném období je možné obnovit objekt blob.

**Víc se uč:**

- [Další informace o] (https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction Úložiště objektů blob v azure.
- [Zjistěte, jak](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) vytvořit snímek objektu blob.
- Projděte si ukázkový scénář] (https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup/) pro zálohování úložiště objektů blob.
- [Přečtěte si informace o](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) obnovitelné odstranění.
- [Kontrola](https://docs.microsoft.com/azure/storage/common/storage-disaster-recovery-guidance?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) co dělat, když dojde k výpadku služby Azure Storage?


#### <a name="third-party-backup"></a>Zálohování třetích stran

Kromě toho můžete použít řešení třetích stran pro zálohování virtuálních počítačů Azure a kontejnery úložiště do místního úložiště nebo jiných poskytovatelů cloudových služeb. [Další informace](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) o řešení pro zálohování na Azure Marketplace. 


### <a name="back-up-a-paas-deployment"></a>Zálohování nasazení PaaS


Na rozdíl od IaaS, kde budete spravovat vlastní virtuální počítače a infrastrukturu v PaaS modelu platforma a infrastruktura spravuje poskytovatele, poskytne vám soustředit se na základní aplikaci logiky a možnosti. Mnoho různých typů služeb PaaS každá služba se vyhodnotí jednotlivě pro účely zálohování. Podíváme se na dvě běžné služby Azure PaaS – Azure SQL Database a Azure Functions.

#### <a name="back-up-azure-sql-database"></a>Proveďte zálohu databáze SQL Azure

Azure SQL Database je plně spravovaný modul databáze PaaS. Celá řada z funkce provozní kontinuity, včetně automatizace zálohování.

- SQL Database automaticky provádí týdenní zálohování úplné a rozdílové zálohování každých 12 hodin. Zálohy transakčního protokolu pocházejí pro ochranu databáze před ztrátou dat každých pět až deset minut.
- Zálohy jsou transparentní a není účtovat další poplatky.
- Zálohy jsou uložené v úložišti RA-GRS pro geografickou redundancí a replikované do spárovaných geografické oblasti.
- Uchování zálohy závisí na nákupní model. Úrovně služeb na základě DTU přejít ze sedmi dnů pro úroveň Basic na 35 dní pro další úrovně.
- Obnovení databáze k v daném okamžiku v rámci doby uchování. Můžete také obnovit odstraněnou databázi obnovit do jiné geografické oblasti nebo obnovení z dlouhodobého zálohování, pokud databáze obsahuje zásady dlouhodobého uchovávání informací (LTR).


![Služba Azure backup SQL](./media/migrate-best-practices-security-management/sql-backup.png)
*zálohy Azure SQL*

**Víc se uč:**
- [Automatické zálohování](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) pro službu SQL Database.
- [Obnovení databáze](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) pomocí automatizovaných záloh.

 
#### <a name="back-up-azure-functions"></a>Zálohování Azure Functions

Protože Azure Functions funguje jako kód více nebo méně, měli byste zálohovat je pomocí stejných metod, které můžete použít k ochraně kódu, jako jsou správy zdrojového kódu na Githubu nebo ve službě Azure DevOps

**Víc se uč:**

- [Ochrana dat](https://docs.microsoft.com/azure/devops/articles/team-services-security-whitepaper?view=vsts) pro Azure DevOps.

## <a name="best-practice-set-up-a-disaster-recovery-strategy"></a>Osvědčený postup: nastavení strategii zotavení po havárii

Kromě ochrany dat, plánování BCDR musíte zvážit, jakým způsobem aplikace a úlohy zůstaly dostupné v případě havárie. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Nastavení zotavení po havárii pro aplikace IaaS

Pro úlohy běžící na virtuálních počítačích Azure IaaS a služby Azure storage vezměte v úvahu tato řešení:

- **Azure Site Recovery**: Orchestruje replikaci virtuálních počítačů Azure ve primárního na sekundární oblasti. Když dojde k výpadku, převzetí služeb při selhání z primární oblasti do sekundární, a uživatelé můžou dál přístup k aplikacím. Když si zpět na normální, převezmete služby zpět do primární oblasti.
- **Azure storage**: Azure poskytuje v sestavení odolnost a vysoká dostupnost pro různé typy úložišť:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

Azure Site Recovery je primární služby Azure pro zajištění, které virtuální počítače Azure mohou být přepne do online režimu a aplikací virtuálního počítače k dispozici, když dojde k výpadku. Site Recovery replikuje virtuální počítače z primární do sekundární oblasti Azure. V případě náhlé havárie převzetí služeb při selhání virtuálních počítačů z primární oblasti a i nadále přístup je k běžným způsobem v sekundární oblasti. Pokud operace vrátí do normálu, můžete převzít virtuální počítače zpět do primární oblasti.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**Víc se uč:**
- [Kontrola](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) scénáře zotavení po havárii pro virtuální počítače Azure.
- [Zjistěte, jak](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) nastavit zotavení po havárii pro virtuální počítač Azure po migraci.

#### <a name="azure-storage"></a>Úložiště Azure

Azure storage se replikuje Integrovaná odolnost a vysoká dostupnost.

-   **Geograficky redundantní úložiště (GRS)**: chrání proti výpadku celé oblasti s minimálně 99,99999999999999 % (16. 9) odolnosti objektů v průběhu daného roku.
    - Úložiště data se replikují do sekundární oblasti, které je spárováno primární oblasti.
    - Pokud primární oblast přestane fungovat a Microsoft iniciuje převzetí služeb při selhání do sekundární oblasti, budete mít přístup pro čtení k datům.
- **Čtení geograficky redundantní úložiště s přístupem (pro čtení RA-GRS)**: chrání před výpadky celé oblasti.
    - Úložiště data se replikují do sekundární oblasti.
    - Mají garantované přístup pro čtení pro replikovaná data v sekundární oblasti, bez ohledu na to, jestli Microsoft zahájí převzetí služeb při selhání. Pokud dva nebo více datová centra ve stejné oblasti může být problém, ale vaše data jsou stále k dispozici v geograficky odděleném oblasti.
-   **Zónově redundantního úložiště (ZRS)**: ochrana před selháním datového centra.
    - ZRS synchronně replikuje data mezi tři úložnými clustery v jedné oblasti. Clustery a mezi zónami a každý umístěny ve vlastní zónu dostupnosti.
    - Pokud dojde k havárii, úložiště stále k dispozici. ZRS by měl být minimální cílová pro nejdůležitější úlohy.



**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/storage/common/storage-redundancy) replikace Azure storage.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Nastavení zotavení po havárii pro úlohy PaaS

Možnosti obnovení pro našich příkladů úloh PaaS zvažte po havárii.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Zotavení po havárii Azure SQL serveru

Existuje několik různých možností, každý vliv na ztrátu dat, dobu obnovení a náklady.

Zajištění odolnosti proti místních výpadků a katastrofických selhání můžete použít skupiny převzetí služeb při selhání a aktivní geografické replikace

- **Aktivní geografická replikace**: nasadit aktivní geografickou replikaci pro rychlé zotavení po havárii, když dojde k výpadku datového centra nebo nelze navázat připojení k primární databáze.
    - Geografická replikace vytváří průběžně čitelné repliky databáze v až čtyři sekundární databáze v jedné nebo několika oblastech.
    - V výpadku převzetí služeb při selhání na sekundární oblastech a převést databázi zpět do online režimu.
- **Automatické převzetí služeb při selhání skupiny**: rozšíření – automatické převzetí služeb při selhání skupiny aktivní geografickou replikaci s transparentní převzetí služeb při selhání několika databází.
    - Skupiny služby – automatické převzetí služeb při selhání poskytuje výkonné abstrakce aktivní geografickou replikaci s replikací databáze na úrovni skupiny a automatické převzetí služeb při selhání.
    - Vytvoříte skupinu převzetí služeb při selhání, který obsahuje primární server, který je hostitelem jedné nebo víc primárních databází, sekundární server, který je hostitelem repliky jen pro čtení primární databází naslouchacích procesů, které odkazují na každém serveru a zásadu automatického převzetí služeb při selhání.
    - Koncové body zadaný naslouchací proces zbavují uživatele nutnosti provádět po převzetí služeb při selhání změnit připojovací řetězec SQL.
- **Geografické obnovení**: 
    -   Geografické obnovení umožňuje obnovit databázi do jiné oblasti. Automatizované zálohování všech databází Azure budou replikovat do sekundární oblasti na pozadí. Vždy obnoví databázi z kopie záložních souborů uložených v sekundární oblasti.
-   **Zónově redundantní databáze** poskytují integrovanou podporu pro zóny dostupnosti Azure.
    - Zónově redundantní databáze vylepšit vysokou dostupnost služby Azure SQL Server v případě selhání datového centra.
    - S redundanci zón můžete umístit repliky redundantní databáze v rámci různých zón dostupnosti v oblasti. 



![Geografická replikace](./media/migrate-best-practices-security-management/geo-replication.png)
*geografické replikace*

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) vysoká dostupnost pro Azure SQL Server.
- [Čtení](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) 101 databází SQL Azure pro zotavení po havárii.
- [Získejte přehled](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) aktivní geografickou replikaci a převzetí služeb při selhání skupin.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) návrhu zotavení po havárii.
- [Získejte doporučené postupy](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview#best-practices-of-using-failover-groups-for-business-continuity) pro skupiny převzetí služeb při selhání.
- [Získejte doporučené postupy](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) zabezpečení po geografické obnovení nebo převzetí služeb při selhání.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) zóna redundance
- [Zjistěte, jak](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) provedení postupu zotavení po havárii pro SQL database.

### <a name="disaster-recovery-for-azure-functions"></a>Zotavení po havárii pro Azure Functions

Pokud výpočetní infrastruktura v Azure selže, může být k dispozici aplikaci Azure function app.

- Aby se minimalizovala možnost takové prostojů, použijte dvě funkce aplikace nasazené do různých oblastí.
- Azure Traffic Manager lze nastavit k zjišťování problémů s primární funkce aplikace a automaticky přesměrování provozu do aplikace function app v sekundární oblasti
- Traffic Manager se geograficky redundantní úložiště umožňuje mít stejnou funkci v několika oblastech, v případě selhání v oblasti


![Traffic Manager](./media/migrate-best-practices-security-management/traffic-manager.png)
*Traffic Manageru*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) zotavení po havárii pro aplikace Azure.
- [Další informace o](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) zotavení po havárii a geografická rozptýlenost odolná služba Azure functions.


### <a name="best-practice-use-managed-disks-and-availability-sets"></a>Osvědčený postup: použít spravované disky a skupiny dostupnosti

Azure používá skupiny dostupnosti chcete logicky seskupit virtuální počítače a k izolaci virtuálních počítačů z jiných prostředků v sadě. Virtuální počítače ve skupině dostupnosti jsou rozděleny mezi více domén selhání pomocí oddělené subsystémy, k ochraně před místním selháním a jsou také rozložit do více doména aktualizace tak, aby všechny virtuální počítače v sadě restartovat ve stejnou dobu.

Azure Managed Disks zjednodušit správu disků virtuálních počítačů Azure IaaS tím, že spravují účty úložiště přidružené k diskům virtuálních počítačů.

- Doporučujeme použít spravované disky, kde je to možné. Stačí jenom zadat typ úložiště, které chcete použít a jaké velikosti disku potřebujete, a Azure vytvoří a spravuje disk za vás na pozadí.
- Můžete převést existující disky jej spravovat.
- Měli byste vytvořit virtuální počítače do skupiny dostupnosti z důvodu vysoké odolnosti a dostupnosti. Pokud dojde k plánované nebo neplánované výpadky, skupiny dostupnosti zaručují, že alespoň jedna z vašich virtuálních počítačů v sadě nadále být k dispozici.


![Spravované disky](./media/migrate-best-practices-security-management/managed-disks.png)
*spravované disky*

**Víc se uč:**
- [Získejte přehled](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) spravovaných disků.
- [Další informace o](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) převod na spravované disky.
- [Zjistěte, jak](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Správa dostupnosti virtuálních počítačů s Windows v Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Osvědčený postup: Monitorování využití prostředků a výkonu 

Může mít pro jeho obrovské možnosti škálování přesunout své úlohy do Azure. Přesun úloh však ale neznamená, že Azure bude automaticky provádět škálování bez nutnosti váš vstup. Jako příklad:

- Pokud organizaci marketingové nabízených oznámení nové oznámení TV, která řídí provoz 300 % další, to může způsobit problémy s dostupností lokality. Nově vydaný, migrovaný úloha pravděpodobně dojde k přiřazené limity a havárií.
- Dalším příkladem můžou být distribuované útoky (DDoS) denial-of-service na migrovanou pracovní zátěž. V tomto případě není vhodné pro škálování, ale zdroj útoků zabránit v dosažení vašich prostředků.

Tyto dva případy mají různá řešení, ale pro obě budete potřebovat přehled o tom, co se děje s monitorování výkonu a využití.

- Azure Monitor může pomoct tyto metriky a poskytne odpověď výstrahy, automatické škálování, služby event hubs, logic apps a další.
- Kromě monitorování Azure, můžete integrovat aplikace třetích stran SIEM pro monitorování Azure protokoly pro události auditu a výkonu.


![Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Azure Monitor*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/azure-monitor/overview) Azure Monitor.
- [Získejte doporučené postupy](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) pro monitorování a diagnostiku.
- [Další informace o](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) automatického škálování.
- [Zjistěte, jak](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) směrování Azure dat do nástroje SIEM.

## <a name="best-practice-enable-diagnostic-logging"></a>Osvědčený postup: povolení protokolování diagnostiky

Prostředky Azure generují přiměřený počet protokolování metrik a telemetrie.

- Ve výchozím nastavení většina typů prostředků nemají povolené protokolování diagnostiky.
- Povolením protokolování diagnostiky napříč vašimi prostředky můžete dotazovat data protokolování a vytvářet výstrahy a na jejím základě playbooky.
- Když povolíte protokolování diagnostiky, každý zdroj bude mít konkrétní sadu kategorií. Vyberte jeden nebo více kategorií, protokolování a umístění dat protokolu. Protokoly je možné odeslat do účtu úložiště, se v Centru událostí nebo ke službě Log Analytics. 


![Protokolování diagnostiky](./media/migrate-best-practices-security-management/diagnostics.png)
*protokolování diagnostiky*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) shromažďování a používání dat protokolu.
- [Další informace o podpoře](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) protokolování diagnostiky.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Osvědčený postup: nastavení výstrah a playbooky

Diagnostické protokolování povoleno pro prostředky Azure, můžete začít protokolování data použít k vytvoření vlastní upozornění.

- Proaktivně upozornění se zobrazí při podmínky se nacházejí ve vašich dat z monitorování. Potom mohli vyřešit problémy dříve, než uživatelé systému Všimněte si, že je. Může upozornit na věci, jako je hodnoty metriky, vyhledávací dotazy protokolů, události protokolu aktivit, platforma stav a dostupnost webu.
- Když se aktivují upozornění, můžete spustit Playbook aplikace logiky. Playbooku vám umožní automatizovat a orchestrovat reakci na určité upozornění. Playbooky jsou založené na Azure Logic Apps. Šablony aplikace logiky můžete vytvořit playbooky, nebo vytvořte svoje vlastní.
- Jako jednoduchý příklad můžete vytvořit výstrahu, která se aktivuje, když kontroly portu zranitelnosti skupinu zabezpečení sítě.  Můžete nastavit playbooku, který spouští a zamezí IP adresa původu kontroly.
- Dalším příkladem mohou být aplikace pomocí nevracení paměti.  Když do určité míry získá využití paměti, můžete provést recyklaci playbooku procesu.


![Výstrahy](./media/migrate-best-practices-security-management/alerts.png)
*výstrahy*

**Víc se uč:**
- [Další informace o](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) výstrahy.
- [Další informace o](https://docs.microsoft.com/azure/security-center/security-center-playbooks) playbooků zabezpečení, které reakce na výstrahy Security Center.

## <a name="best-practice-use-the-azure-dashboard"></a>Osvědčený postup: pomocí řídicího panelu Azure

Na webu Azure portal je webové jednotné konzolu, která umožňuje vytvářet, spravovat a monitorovat vše od jednoduchých webových aplikací až po složité cloudové aplikace. Zahrnuje přizpůsobitelné řídicí panely a možnosti usnadnění.
- Můžete vytvořit více řídicích panelů a sdílet je s ostatními uživateli, kteří mají přístup k předplatným Azure.
- V tomto modelu sdílené má váš tým Přehled prostředí Azure, což jim umožní provádět proaktivně při správě systémy v cloudu.


![Řídicí panel Azure](./media/migrate-best-practices-security-management/dashboard.png)
*řídicí panel Azure*

**Víc se uč:**

- [Zjistěte, jak](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) vytvoření řídicího panelu.
- [Další informace o](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) struktura řídicího panelu.


## <a name="best-practice-understand-support-plans"></a>Osvědčený postup: pochopení podporu plánů

V určitém okamžiku bude nutné spolupracovat s pracovníky podpory nebo pracovníky podpory Microsoftu. Je důležité mít sadu zásady a postupy pro podporu během scénářů, jako je zotavení po havárii. Kromě toho by měl pracovníky správci a podporu Trénink při implementaci těchto zásad.

- V nepravděpodobném případě, že problém se službou Azure ovlivňuje vaše úlohy by měl správci vědět, jak odeslat lístek podpory společnosti Microsoft tak, jak nejčastěji odpovídající a efektivní.
- Seznamte se s plánů podpory nabízejí pro Azure. V rozsahu od doby odezvy vyhrazený pro vývojáře instancí na plán Premier support s dobou odezvy kratší než 15 minut.


![Plány podpory](./media/migrate-best-practices-security-management/support.png)
*plány podpory*

**Víc se uč:**
- [Získejte přehled](https://azure.microsoft.com/support/options/) plány podpory Azure.
- [Další informace o](https://azure.microsoft.com/support/legal/sla/) smlouvami o úrovni (SLA).

## <a name="best-practice---manage-updates"></a>Osvědčené postupy – Správa aktualizací

Zachovat virtuální počítače Azure aktualizován s nejnovějším operačním systémem a aktualizací softwaru je obrovská případě. Možnost prezentovat všechny virtuální počítače k tomu, které aktualizace, které potřebují a o automaticky nasdílení změn je velmi důležité tyto aktualizace.

- Správa aktualizací ve službě Azure Automation můžete použít ke správě aktualizací operačního systému pro počítače s Windows a Linuxem počítačů, které jsou nasazené v Azure, místně a v jiných poskytovatelů cloudových služeb. 
- Update Management můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat instalaci aktualizací.
- Můžete povolit správu aktualizací pro virtuální počítače přímo z účtu Azure Automation. Můžete také aktualizovat jenom jeden virtuální počítač z virtuálního počítače stránky na webu Azure Portal.
- Kromě toho virtuální počítače Azure lze registrovat pomocí nástroje System Center Configuration Manager. Může migrace úloh nástroje Configuration Manager do Azure a provádět vytváření sestav a aktualizací softwaru z jediného webového rozhraní.


![Aktualizace virtuálního počítače](./media/migrate-best-practices-security-management/updates.png)
*aktualizace*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/automation/automation-update-management) update management v Azure.
- [Zjistěte, jak](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) integrovat správu aktualizací nástroje Configuration Manager.
- [Nejčastější dotazy](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) o Configuration Manageru v Azure.


## <a name="implement-a-change-management-process"></a>Implementujte proces správy změn

Stejně jako u jakékoli produkční systém, vytváření jakéhokoli typu změna může ovlivnit prostředí. Proces správy změn, který vyžaduje požadavků na odeslání, pokud chcete změny provádět na provozní systémy jsou cenné rozšířením ve vašem migrované prostředí.

- Můžete sestavit nejlepší postup rozhraní pro zvýšení povědomí ve Správci a pracovníci podpory úroveň řízení změn.
- Pomocí Azure Automation může pomoct se správou konfigurace a sledování změn pro migrované pracovních postupů.
- Při vynucování procesu správy změn, můžete použít protokolů auditu Azure změnit propojení protokoly pravděpodobně (nebo nemusíte) existující žádosti o změnu. Takže pokud se zobrazí změny bez odpovídající žádosti o změnu, můžete prozkoumat, co došlo k chybě v procesu.

Azure nabízí řešení Change Tracking ve službě Azure automation:

- Řešení sleduje změny softwaru Windows a Linuxem a soubory, klíče registru Windows, služby Windows a Linuxové procesy démon.
- Změny na monitorované servery odesílají do služby Log Analytics v cloudu pro zpracování.
- Logika platí pro přijatá data a cloudové službě zaznamenává data.
- Na řídicím panelu řešení Change Tracking můžete snadno zobrazit změny, které byly provedeny v serverové infrastruktuře.


![Správa změn](./media/migrate-best-practices-security-management/change.png)
*Správa změn*

**Víc se uč:**

- [Další informace o](https://docs.microsoft.com/azure/automation/automation-change-tracking) Change Tracking.
- [Další informace o](https://docs.microsoft.com/azure/automation/automation-intro) možnosti Azure Automation.




## <a name="next-steps"></a>Další postup 

Přečtěte si doporučené postupy:


- [Osvědčené postupy](migrate-best-practices-networking.md) sítě po migraci.
- [Osvědčené postupy](migrate-best-practices-costs.md) cost Management po migraci.








