---
title: Jak bezpečně spustit službu správy certifikátů trezoru OPC – Azure | Microsoft Docs
description: Popisuje, jak bezpečně spustit službu pro správu certifikátů trezoru OPC v Azure a jak zvážit další pokyny pro zabezpečení, které je potřeba vzít v úvahu.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 01213cd4d04783d0d877b4565493f06fa3718f8d
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399631"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Bezpečně spusťte službu Správa certifikátů trezoru OPC.

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

Tento článek vysvětluje, jak bezpečně spustit službu pro správu certifikátů trezoru OPC v Azure, a kontroluje další pokyny pro zabezpečení, které je potřeba vzít v úvahu.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Důvěryhodné a autorizované role

Mikroslužba trezoru OPC umožňuje jedinečným rolím přístup k různým částem služby.

> [!IMPORTANT]
> Během nasazování skript přidá pouze uživatele, který spouští skript nasazení jako uživatel pro všechny role. V případě nasazení v produkčním prostředí byste měli zkontrolovat přiřazení této role a patřičně je znovu nakonfigurovat podle následujících pokynů. Tato úloha vyžaduje ruční přiřazení rolí a služeb na portálu Azure Active Directory (Azure AD) podnikových aplikací.

### <a name="certificate-management-service-roles"></a>Role služby správy certifikátů

Mikroslužba trezoru OPC definuje následující role:

- **Čtecí zařízení**: ve výchozím nastavení má každý ověřený uživatel v tenantovi oprávnění ke čtení. 
  - Přístup pro čtení aplikací a žádostí o certifikát. Může vypsat a dotazovat se na aplikace a žádosti o certifikát. K dispozici jsou také informace o zjišťování zařízení a veřejné certifikáty s přístupem pro čtení.
- **Zapisovač**: role zapisovače je přiřazena uživateli, aby pro určité úlohy přidala oprávnění k zápisu. 
  - Přístup pro čtení a zápis pro aplikace a žádosti o certifikát. Může registrovat, aktualizovat a rušit registraci aplikací. Může vytvářet žádosti o certifikát a získávat schválené privátní klíče a certifikáty. Může také odstranit privátní klíče.
- **Schvalovatel**: role schvalovatele je přiřazena uživateli ke schválení nebo zamítnutí žádostí o certifikát. Role neobsahuje žádné další role.
  - Kromě role schvalovatele pro přístup k rozhraní API mikroslužeb OPC trezor musí mít uživatel také oprávnění k podepsání klíče v Azure Key Vault, aby mohli certifikáty podepisovat.
  - Role zapisovače a schvalovatele by se měla přiřadit různým uživatelům.
  - Hlavní rolí schvalovatele je schválení generování a zamítnutí žádostí o certifikát.
- **Správce**: role správce je přiřazena uživateli ke správě skupin certifikátů. Tato role nepodporuje roli schvalovatele, ale obsahuje roli zapisovače.
  - Správce může spravovat skupiny certifikátů, měnit konfiguraci a odvolávat certifikáty aplikací vyvoláním nového seznamu odvolaných certifikátů (CRL).
  - V ideálním případě jsou role zapisovače, schvalovatele a správce přiřazovány různým uživatelům. Pro zvýšení zabezpečení potřebuje uživatel s rolí schvalovatele nebo správce taky oprávnění pro podepisování klíčů v Key Vault, k vydávání certifikátů nebo obnovení certifikátu certifikační autority vystavitele.
  - Kromě role správy mikroslužeb tato role zahrnuje, ale není omezená na:
    - Zodpovědnost za správu implementace postupů zabezpečení certifikační autority.
    - Správa generování, odvolání a pozastavení certifikátů. 
    - Správa životního cyklu kryptografického klíče (například obnovení klíčů certifikační autority vystavitele).
    - Instalace, konfigurace a údržba služeb, které provozují certifikační autoritu.
    - Každodenní operace služeb. 
    - Certifikační autorita a zálohování a obnovení databáze.

### <a name="other-role-assignments"></a>Další přiřazení rolí

Při spouštění služby Zvažte také následující role:

- Obchodní vlastník smlouvy o nákupu certifikátů s externím kořenovým certifikačním úřadem (například když vlastník koupí certifikáty od externí certifikační autority nebo provozuje certifikační autoritu, která je podřízená externí certifikační autoritě).
- Vývoj a ověření certifikační autority.
- Kontrola záznamů auditu.
- Zaměstnanci, kteří vám pomůžou zajistit podporu certifikační autority nebo spravovat fyzická a cloudová zařízení, ale nejsou přímo důvěryhodní k provádění operací CA, jsou v *autorizované* roli. Je také nutné zdokumentovat sadu úloh, které jsou v autorizované roli povolené.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Čtvrtletní kontrola členství důvěryhodných a autorizovaných rolí

Přečtěte si alespoň čtvrtletní členství důvěryhodných a autorizovaných rolí. Ujistěte se, že sada osob (pro manuální procesy) nebo identitu služby (pro automatizované procesy) je v každé roli udržována minimálně.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Oddělení rolí mezi žadatelem a schvalovatelem certifikátů

Proces vystavení certifikátu musí vyhovět oddělení rolí mezi žadatelem certifikátu a rolemi schvalovatele certifikátů (osoby nebo automatizované systémy). Vystavení certifikátu musí být autorizován rolí schvalovatele certifikátu, která ověřuje, jestli žadatel o certifikát má oprávnění k získání certifikátů. Osoby, které drží roli schvalovatele certifikátů, musí být formálně autorizovaní osoba.

### <a name="restrict-assignment-of-privileged-roles"></a>Omezení přiřazení privilegovaných rolí

Přiřazení privilegovaných rolí, jako je například autorizace členství ve skupině Administrators a schvalovatelé, byste měli omezit na omezené skupiny autorizovaných zaměstnanců. Všechny změny privilegované role musí mít přístup odvolaný do 24 hodin. Nakonec zkontrolujte přiřazení privilegovaných rolí čtvrtletně a odeberte všechna nepotřebná nebo vydaná přiřazení s vypršenou platností.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegované role by se měly používat se dvěma faktory ověřování.

Použijte službu Multi-Factor Authentication (označovanou také jako dvojúrovňové ověřování) pro interaktivní přihlášení schvalovatelů a správců ke službě.

## <a name="certificate-service-operation-guidelines"></a>Pokyny pro operace certifikační služby

### <a name="operational-contacts"></a>Pracovní kontakty

Služba Certificate Service musí mít v souboru aktuální plán odpovědí na zabezpečení, který obsahuje podrobné kontakty na reakci na provozní incidenty.

### <a name="security-updates"></a>Aktualizace zabezpečení

Všechny systémy je potřeba průběžně monitorovat a aktualizovat s nejnovějšími aktualizacemi zabezpečení.

> [!IMPORTANT]
> Úložiště GitHub služby trezoru OPC se průběžně aktualizuje pomocí oprav zabezpečení. Sledujte tyto aktualizace a v pravidelných intervalech je používejte pro službu.

### <a name="security-monitoring"></a>Monitorování zabezpečení

Přihlaste se k odběru nebo implementujte příslušné monitorování zabezpečení. Například se přihlaste k odběru řešení centrálního monitorování (například Azure Security Center nebo Microsoft 365 monitorování) a nakonfigurujte ho tak, aby bylo zajištěno, že se události zabezpečení přenáší do řešení monitorování.

> [!IMPORTANT]
> Ve výchozím nastavení je služba trezoru OPC nasazená s [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako řešení monitorování. Důrazně doporučujeme přidat řešení zabezpečení, jako je [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-the-security-of-open-source-software-components"></a>Posouzení zabezpečení Open Source softwarových komponent

Všechny open-source komponenty, které se používají v rámci produktu nebo služby, musí být bez slabých nebo větších ohrožení zabezpečení.

> [!IMPORTANT]
> V průběhu průběžné integrace se v úložišti GitHubu služby OPC trezoru kontrolují ohrožení zabezpečení všech součástí. Sledujte tyto aktualizace na GitHubu a v pravidelných intervalech je používejte pro službu.

### <a name="maintain-an-inventory"></a>Údržba inventáře

Udržujte inventář assetů pro všechny provozní hostitele (včetně trvalých virtuálních počítačů), zařízení, všechny interní rozsahy IP adres, VIP a názvy veřejných domén DNS. Kdykoli přidáte nebo odeberete systém, IP adresu zařízení, VIP nebo veřejnou doménu DNS, musíte aktualizovat inventář do 30 dnů.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventář výchozího nasazení mikroslužeb Azure OPC trezoru 

V Azure:
- **App Service plán**: plán služby App Service pro hostitele služeb. Výchozí S1.
- **App Service** pro mikroslužbu: hostitel služby trezoru OPC.
- **App Service** pro ukázkovou aplikaci: hostitel ukázkové aplikace trezoru OPC.
- **Key Vault Standard**: ukládání tajných klíčů a Azure Cosmos DB klíčů pro webové služby.
- **Key Vault Premium**: k hostování klíčů certifikační autority vystavitele, pro podpisovou službu a pro konfiguraci trezoru a ukládání privátních klíčů aplikace.
- **Azure Cosmos DB**: databáze pro žádosti aplikací a certifikátů. 
- **Application Insights**: (volitelné) řešení monitorování pro webovou službu a aplikaci.
- **Registrace aplikace Azure AD**: registrace pro ukázkovou aplikaci, službu a modul Edge.

Pro cloudové služby by se měly zdokumentovat všechny názvy hostitelů, skupiny prostředků, názvy prostředků, ID předplatných a ID klientů použitá k nasazení služby. 

V Azure IoT Edge nebo místní server IoT Edge:
- **Modul IoT Edge trezoru OPC**: pro podporu serveru pro vytváření globálních serverů OPC UA. 

U IoT Edgech zařízení by se měly názvy hostitelů a IP adresy zdokumentovat. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentace certifikačních autorit (CAs)

Dokumentace k hierarchii certifikační autority musí obsahovat všechny provozované certifikační autority. To zahrnuje všechny související podřízené certifikační autority, nadřazené certifikační autority a kořenové certifikační autority, i když je služba nespravuje. Místo formální dokumentace můžete zadat vyčerpávající sadu všech certifikátů certifikační autority, které nejsou prošly.

> [!NOTE]
> Ukázková aplikace OPC trezor podporuje stažení všech používaných certifikátů a vyprodukovaných ve službě pro dokumentaci.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentování vydaných certifikátů všemi certifikačními autoritami (CAs)

Poskytněte vyčerpávající sadu všech certifikátů vydaných za posledních 12 měsíců.

> [!NOTE]
> Ukázková aplikace OPC trezor podporuje stažení všech používaných certifikátů a vyprodukovaných ve službě pro dokumentaci.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Zdokumentujte standardní provozní proceduru pro bezpečné odstranění kryptografických klíčů.

Během životnosti certifikační autority může odstraňování klíčů probíhat jenom zřídka. Důvodem je, že žádný uživatel nemá přiřazenou oprávnění k odstranění Key Vault certifikátu a proč nejsou k dispozici žádná rozhraní API k odstranění certifikátu certifikační autority vystavitele. Ruční standardní provozní proceduru pro bezpečné odstranění kryptografických klíčů certifikační autority je k dispozici jenom přímým přístupem k Key Vault v Azure Portal. Můžete také odstranit skupinu certifikátů v Key Vault. Chcete-li zajistit okamžité odstranění, zakažte funkci [Key Vaultho obnovitelného odstranění](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) .

## <a name="certificates"></a>Certifikáty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikáty musí splňovat minimální profil certifikátu.

Služba OPC trezor je online certifikační autorita, která zákazníkům dává certifikáty koncových entit. Mikroslužba trezoru OPC se řídí těmito pokyny ve výchozí implementaci.

- Všechny certifikáty musí zahrnovat následující pole X. 509, jak je uvedeno níže:
  - Obsah pole verze musí být v3. 
  - Obsah pole sériového čísla musí obsahovat nejméně 8 bajtů entropie získaného ze standardu FIPS (Federal Information Processing Standards) 140 schválen generátor náhodných čísel.<br>
    > [!IMPORTANT]
    > Sériové číslo trezoru OPC je ve výchozím nastavení 20 bajtů a získává se z generátoru kryptografického náhodného čísla operačního systému. Generátor náhodných čísel je FIPS 140 schválen na zařízeních s Windows, ale ne v systému Linux. Vezměte v úvahu, když zvolíte nasazení služby, které používá virtuální počítače se systémem Linux nebo kontejnery Docker pro Linux, na kterých základní technologie OpenSSL není schválena FIPS 140.
  - Pole issuerUniqueID a subjectUniqueID nesmí být k dispozici.
  - Certifikáty koncových entit musí být identifikované s rozšířením základní omezení v souladu se specifikací IETF RFC 5280.
  - Pro certifikát vydávající certifikační autority musí být pole pathLenConstraint nastaveno na hodnotu 0. 
  - Rozšíření použití rozšířeného klíče musí být k dispozici a musí obsahovat minimální sadu identifikátorů OID rozšířeného použití klíče (OID). Identifikátor anyExtendedKeyUsage OID (2.5.29.37.0) nesmí být zadaný. 
  - V certifikátu certifikační autority vystavitele musí být rozšíření distribučního bodu seznamu CRL (CDP).<br>
    > [!IMPORTANT]
    > Rozšíření CDP je k dispozici v certifikátech certifikační autority OPC trezoru. Zařízení OPC UA však k distribuci seznamů CRL používají vlastní metody.
  - V certifikátech odběratele se musí nacházet rozšíření přístupu k informacím autority.<br>
    > [!IMPORTANT]
    > Rozšíření přístup k informacím autority je k dispozici v certifikátech předplatitele OPC trezoru. Zařízení OPC UA ale k distribuci informací o certifikační autoritě vystavitele používá vlastní metody.
- Je nutné použít schválené asymetrické algoritmy, délky klíčů, funkce algoritmu hash a režimy odsazení.
  - Jedinými podporovanými algoritmy jsou RSA a SHA-2.
  - RSA se dá použít k šifrování, výměně klíčů a podpisu.
  - Šifrování RSA musí používat jenom režimy odsazení výplně OAEP, RSA-KEM nebo RSA-PSS.
  - Vyžaduje se délka klíče větší nebo rovna 2048 bitů.
  - Použijte rodinu SHA-2 algoritmů hash (SHA256, SHA384 a SHA512).
  - Klíče kořenové certifikační autority RSA s typickou životností větší nebo rovnou 20 let musí být 4096 bitů nebo větší.
  - Klíče certifikační autority vystavitele RSA musí mít aspoň 2048 bitů. Pokud je datum vypršení platnosti certifikátu certifikační autority pozdější než 2030, musí být klíč certifikační autority 4096 bitů nebo větší.
- Životnost certifikátu
  - Certifikáty kořenové certifikační autority: maximální doba platnosti certifikátu pro kořenové certifikační autority nesmí přesáhnout 25 let.
  - Podcertifikační autorita nebo certifikáty certifikační autority online vystavitele: maximální doba platnosti certifikátu pro certifikační autority, které jsou online a mají stav pouze certifikáty odběratele, nesmí překročit 6 let. U těchto certifikačních autorit se nesmí použít související privátní podpisový klíč delší než 3 roky k vydávání nových certifikátů.<br>
    > [!IMPORTANT]
    > Certifikát vystavitele, jak je vygenerovaný ve výchozí mikroslužbě trezoru OPC bez externí kořenové certifikační autority, se považuje za online podca s příslušnými požadavky a životností. Výchozí doba života je nastavená na 5 let s délkou klíče větší nebo rovnou 2048.
  - Všechny asymetrické klíče musí mít maximální dobu platnosti 5 let a doporučenou dobu 1 roku.<br>
    > [!IMPORTANT]
    > Ve výchozím nastavení mají životnost certifikátů aplikace vydaných s trezorem OPC dobu životnosti 2 roky a měly by být nahrazeny každým rokem. 
  - Pokaždé, když se certifikát obnoví, obnoví se s novým klíčem.
- Rozšíření specifická pro OPC UA v certifikátech instance aplikace
  - Rozšíření subjectAltName zahrnuje identifikátor URI a názvy hostitelů aplikace. Tyto můžou zahrnovat taky plně kvalifikovaný název domény, IPv4 a IPv6 adresy.
  - Použití modulu zahrnuje digitalSignature, neneodvolatelnost, keyEncipherment a dataEncipherment.
  - ExtendedKeyUsage zahrnuje serverAuth a clientAuth.
  - AuthorityKeyIdentifier je určena v podepsaných certifikátech.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klíče a certifikáty certifikační autority musí splňovat minimální požadavky.

- **Privátní klíče**: klíče RSA musí mít minimálně 2048 bitů. Pokud je datum vypršení platnosti certifikátu certifikační autority pozdější než 2030, musí být klíč certifikační autority 4096 bitů nebo větší.
- **Doba**platnosti: maximální doba platnosti certifikátu pro certifikační autority, které jsou online a mají stav pouze certifikáty odběratele, nesmí překročit 6 let. U těchto certifikačních autorit se nesmí použít související privátní podpisový klíč delší než 3 roky k vydávání nových certifikátů.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Klíče certifikační autority jsou chráněné pomocí modulů hardwarového zabezpečení.

OpcVault používá Azure Key Vault Premium a klíče jsou chráněné moduly hardwarového zabezpečení (HSM) standardu FIPS 140-2 úrovně 2. 

Kryptografické moduly, které Key Vault používá, ať už se ověřuje Standard FIPS nebo software. Klíče vytvořené nebo importované jako chráněné HSM se zpracovávají v modulu HARDWAROVÉho zabezpečení, který je ověřený na úrovni FIPS 140-2 Level 2. Klíče vytvořené nebo importované jako chráněný software se zpracovávají v rámci kryptografických modulů ověřovaných na úroveň FIPS 140-2 Level 1.

## <a name="operational-practices"></a>Provozní postupy

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Zdokumentujte a udržujte standardní provozní postupy infrastruktury veřejných klíčů pro zápis certifikátů.

Zdokumentujte a udržujte standardní provozní postupy (shody) pro způsob, jakým certifikační autority vydávají certifikáty, včetně: 
- Způsob identifikace a ověření předplatitele. 
- Způsob zpracování a ověření žádosti o certifikát (Pokud je k dispozici) včetně toho, jak se zpracovávají žádosti o obnovení certifikátu a opětovné použití klíčů. 
- Způsob distribuce vydaných certifikátů odběratelům. 

Mikroslužba OPC trezor autority je popsaná v tématu [Architektura OPC trezoru](overview-opc-vault-architecture.md) a [spravuje službu certifikátů úložiště OPC](howto-opc-vault-manage.md). Postupy následují jako OPC Unified Architecture Specification, část 12: zjišťování a globální služby.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Zdokumentujte a udržujte standardní provozní postupy infrastruktury veřejných klíčů pro odvolání certifikátů.

Proces odvolaných certifikátů je popsaný v tématu [Architektura OPC trezoru](overview-opc-vault-architecture.md) a [Správa certifikátů služby OPC trezor](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Procedury generování klíčů certifikační autority dokumentu 

Generování klíče CA vystavitele v mikroslužbě trezoru OPC je zjednodušené kvůli zabezpečenému úložišti v Azure Key Vault. Další informace najdete v tématu [Správa služby certifikátu trezoru OPC](howto-opc-vault-manage.md).

Pokud ale používáte externí kořenovou certifikační autoritu, musí procedury generování klíčů certifikační autority splňovat následující požadavky.

Procedury generování klíčů certifikační autority musí být proveden na dokumentovaném skriptu, který obsahuje alespoň následující položky: 
- Definice rolí a odpovědností účastníků.
- Schválení pro procedury generování klíčů certifikační autority.
- Kryptografický hardware a aktivační materiály vyžadované pro procedury.
- Příprava hardwaru (včetně informací o assetech/konfiguraci a jejich aktualizaci a odhlášení).
- Instalace operačního systému.
- Konkrétní kroky provedené během generování klíče certifikační autority procedury, například: 
  - Instalace a konfigurace aplikace CA.
  - Generování klíče certifikační autority
  - Záloha klíče certifikační autority
  - Podepisování certifikátu certifikační autority.
  - Import podepsaných klíčů v chráněném modulu HSM služby.
  - Vypnutí systému certifikační autority.
  - Příprava materiálů pro úložiště.


## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak bezpečně spravovat OPC trezor, můžete:

> [!div class="nextstepaction"]
> [Zabezpečení zařízení OPC UA pomocí trezoru OPC](howto-opc-vault-secure.md)
