---
title: Jak bezpečně spustit službu správy certifikátů OPC Vault - Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak bezpečně spustit službu správy certifikátů OPC Vault v Azure, a zkontroluje další pokyny pro zabezpečení, které je třeba zvážit.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 88f8188779c5fb6b3cd07c67e9f35a6b8f9ad97d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271704"
---
# <a name="run-the-opc-vault-certificate-management-service-securely"></a>Bezpečné spuštění služby správy certifikátů OPC Vault

Tento článek vysvětluje, jak bezpečně spustit službu správy certifikátů OPC Vault v Azure a zkontroluje další pokyny pro zabezpečení, které je třeba zvážit.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Důvěryhodné a autorizované role

Mikroslužba OPC Vault umožňuje různé role pro přístup k různým částem služby.

> [!IMPORTANT]
> Během nasazení skript přidá pouze uživatele, který spustí skript nasazení jako uživatel pro všechny role. Pro produkční nasazení byste měli zkontrolovat toto přiřazení role a odpovídajícím způsobem překonfigurovat podle následujících pokynů. Tato úloha vyžaduje ruční přiřazení rolí a služeb na portálu Enterprise Applications azure active directory (Azure AD).

### <a name="certificate-management-service-roles"></a>Role služby správy certifikátů

Mikroslužba OPC Vault definuje následující role:

- **Čtečka**: Ve výchozím nastavení má přístup pro čtení každý ověřený uživatel v klientovi. 
  - Přístup pro čtení k aplikacím a žádostem o certifikát. Může seznam a dotaz na aplikace a žádosti o certifikát. Také informace o zjišťování zařízení a veřejné certifikáty jsou přístupné s přístupem pro čtení.
- **Zapisovatel**: Role Zapisovatel je přiřazena uživateli k přidání oprávnění k zápisu pro určité úkoly. 
  - Přístup pro čtení a zápis k aplikacím a žádostem o certifikát. Může zaregistrovat, aktualizovat a zrušit registraci aplikací. Může vytvářet žádosti o certifikáty a získat schválené soukromé klíče a certifikáty. Můžete také odstranit soukromé klíče.
- **Schvalovatel**: Role Schvaltavatele je přiřazena uživateli ke schválení nebo odmítnutí žádostí o certifikát. Role neobsahuje žádnou jinou roli.
  - Kromě role schvalovatele pro přístup k rozhraní API mikroslužeb OPC Vault musí mít uživatel také oprávnění k podepisování klíčů v úložišti Azure Key Vault, aby mohl certifikáty podepsat.
  - Role Zapisovača a schvalovatele by měla být přiřazena různým uživatelům.
  - Hlavní úlohou schvalovatele je schválení generování a odmítnutí žádostí o certifikát.
- **Správce**: Role Správce je přiřazena uživateli ke správě skupin certifikátů. Role nepodporuje roli schvalovatele, ale zahrnuje roli zapisovače.
  - Správce může spravovat skupiny certifikátů, měnit konfiguraci a odvolávat certifikáty aplikací vydáním nového seznamu odvolaných certifikátů (CRL).
  - V ideálním případě jsou role Zapisovač, Schvalovatel a Správce přiřazeny různým uživatelům. Pro další zabezpečení potřebuje uživatel s rolí Schvaltár nebo Správce také oprávnění k podepisování klíčů v trezoru klíčů, k vydávání certifikátů nebo k obnovení certifikátu certifikační autority vystavitela.
  - Kromě role správy mikroslužeb role zahrnuje, ale není omezena na:
    - Odpovědnost za správu implementace bezpečnostních postupů certifikačníautority.
    - Řízení výroby, zrušení a pozastavení certifikátů. 
    - Správa životního cyklu kryptografického klíče (například obnovení klíčů certifikační autority vystavitela).
    - Instalace, konfigurace a údržba služeb, které provozují certifikační autoritu.
    - Každodenní provoz služeb. 
    - Ca a zálohování a obnovení databáze.

### <a name="other-role-assignments"></a>Další přiřazení rolí

Při spuštění služby zvažte také následující role:

- Vlastník firmy smlouvy o zadávací dokumentaci certifikátu u externího kořenového certifikačního úřadu (například když vlastník nakupuje certifikáty od externícertifikační autority nebo provozuje certifikační autoritu podřízenou externícertifikační autoritě).
- Vývoj a validace certifikační autority.
- Kontrola záznamů auditu.
- Pracovníci, kteří pomáhají podporovat certifikační autoritu nebo spravovat fyzická a cloudová zařízení, ale nejsou přímo důvěryhodní k provádění operací certifikační autority, jsou v *autorizované* roli. Soubor úkolů, které mohou osoby v autorizované roli vykonávat, musí být také zdokumentován.

### <a name="review-memberships-of-trusted-and-authorized-roles-quarterly"></a>Čtvrtletně kontroluvají členství v důvěryhodných a autorizovaných rolích

Zkontrolujte členství v důvěryhodných a autorizovaných rolích alespoň čtvrtletně. Ujistěte se, že sada osob (pro ruční procesy) nebo identity služby (pro automatizované procesy) v každé roli je omezena na minimum.

### <a name="role-separation-between-certificate-requester-and-approver"></a>Oddělení rolí mezi žádostí o certifikát a schvalovatelem

Proces vystavování certifikátů musí vynutit oddělení rolí vyžádání certifikátu a role schvalovatele certifikátů (osoby nebo automatizované systémy). Vystavování certifikátů musí být autorizováno rolí schvalovatele certifikátu, která ověří, zda je žadatel certifikátu oprávněn k získání certifikátů. Osoby, které mají roli schvalovatele certifikátu, musí být formálně oprávněnou osobou.

### <a name="restrict-assignment-of-privileged-roles"></a>Omezit přiřazení privilegovaných rolí

Přiřazení privilegovaných rolí, například autorizace členství ve skupině Administrators and Approvers, byste měli omezit na omezenou skupinu oprávněných pracovníků. Všechny změny privilegované role musí mít přístup odvolán do 24 hodin. Nakonec zkontrolujte přiřazení privilegovaných rolí čtvrtletně a odeberte všechna nepotřebná přiřazení nebo přiřazení s ukončenou platností.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegované role by měly používat dvoufaktorové ověřování

Pro interaktivní přihlášení schvalovatelů a správců ke službě použijte vícefaktorové ověřování (nazývané se také dvoufaktorové ověřování).

## <a name="certificate-service-operation-guidelines"></a>Pokyny pro provoz certifikační služby

### <a name="operational-contacts"></a>Provozní kontakty

Certifikační služba musí mít v souboru aktuální plán odezvy zabezpečení, který obsahuje podrobné kontakty reakce na provozní incidenty.

### <a name="security-updates"></a>Aktualizace zabezpečení

Všechny systémy musí být průběžně sledovány a aktualizovány nejnovějšími aktualizacemi zabezpečení.

> [!IMPORTANT]
> Úložiště GitHub u služby OPC Vault je průběžně aktualizováno opravami zabezpečení. Sledujte tyto aktualizace a aplikujte je na službu v pravidelných intervalech.

### <a name="security-monitoring"></a>Monitorování zabezpečení

Přihlaste se k odběru nebo implementujte příslušné monitorování zabezpečení. Například přihlaste se k odběru řešení centrálního monitorování (například Azure Security Center nebo Office 365 monitoring solution) a nakonfigurujte ho odpovídajícím způsobem, abyste zajistili, že události zabezpečení se přenášejí do řešení monitorování.

> [!IMPORTANT]
> Ve výchozím nastavení se služba OPC Vault nasazuje s [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako monitorovacím řešením. Přidání řešení zabezpečení, jako je [Azure Security Center,](https://azure.microsoft.com/services/security-center/) je vysoce doporučeno.

### <a name="assess-the-security-of-open-source-software-components"></a>Posouzení zabezpečení softwarových komponent s otevřeným zdrojovým kódem

Všechny součásti s otevřeným zdrojovým kódem používané v rámci produktu nebo služby musí obsahovat bez závažných nebo větších chyb zabezpečení.

> [!IMPORTANT]
> Během sestav průběžné integrace prohledá úložiště GitHub služby OPC Vault všechny součásti pro zjišťování slabých míst. Sledujte tyto aktualizace na GitHubu a aplikujte je na službu v pravidelných intervalech.

### <a name="maintain-an-inventory"></a>Udržovat zásoby

Udržujte inventář majetku pro všechny produkční hostitele (včetně trvalých virtuálních počítačů), zařízení, všechny interní rozsahy IP adres, VIP a veřejné názvy domén DNS. Kdykoli přidáte nebo odeberete systém, IP adresu zařízení, virtuální IP adresu nebo veřejnou doménu DNS, musíte inventář aktualizovat do 30 dnů.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Soupis výchozího produkčního nasazení mikroslužeb Azure OPC Vault 

V Azure:
- **Plán služby App Service:** Plán služeb aplikace pro hostitele služeb. Výchozí S1.
- **Služba App Service** pro mikroslužbu: Hostitel služby OPC Vault.
- **Služba App Service** pro ukázkovou aplikaci: Ukázkový hostitel aplikace OPC Vault.
- **Key Vault Standard:** Chcete-li ukládat tajné klíče a klíče Azure Cosmos DB pro webové služby.
- **Trezor klíčů Premium**: Chcete-li hostit klíče certifikační autority vystavitela, pro podepisování služby a pro konfiguraci trezoru a úložiště soukromých klíčů aplikace.
- **Azure Cosmos DB**: Databáze pro žádosti o aplikace a certifikáty. 
- **Application Insights:**(volitelné) Řešení monitorování pro webovou službu a aplikaci.
- **Registrace aplikace Azure AD**: Registrace pro ukázkovou aplikaci, službu a hraniční modul.

Pro cloudové služby by měly být zdokumentovány všechny názvy hostitelů, skupiny prostředků, názvy prostředků, ID předplatného a ID klienta použitá k nasazení služby. 

V Azure IoT Edge nebo na místním serveru IoT Edge:
- **Modul OPC Vault IoT Edge**: Pro podporu tovární sítě OPC UA Global Discovery Server. 

Pro zařízení IoT Edge by měly být zdokumentovány názvy hostitelů a IP adresy. 

### <a name="document-the-certification-authorities-cas"></a>Dokument certifikačních úřadů (CERTIFIKAČNÍ orgány)

Dokumentace hierarchie certifikační autority musí obsahovat všechny provozované certifikační autority. To zahrnuje všechny související podřízené certifikační autority, nadřazené certifikační autority a kořenové certifikační autority, i když nejsou spravovány službou. Namísto formální dokumentace můžete poskytnout vyčerpávající sadu všech certifikátů certifikační autority, jejichž platnost nevypršela.

> [!NOTE]
> Ukázková aplikace OPC Vault podporuje stahování všech certifikátů použitých a vyrobených ve službě pro dokumentaci.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Zdokumentujte vystavené certifikáty všemi certifikačními úřady (CS)

Poskytněte vyčerpávající sadu všech certifikátů vydaných za posledních 12 měsíců.

> [!NOTE]
> Ukázková aplikace OPC Vault podporuje stahování všech certifikátů použitých a vyrobených ve službě pro dokumentaci.

### <a name="document-the-standard-operating-procedure-for-securely-deleting-cryptographic-keys"></a>Dokumentovat standardní operační postup pro bezpečné odstranění kryptografických klíčů

Během životnosti certifikační autority může dojít k odstranění klíče jen zřídka. To je důvod, proč žádný uživatel nemá klíč Vault Certificate Delete právo přiřazena a proč nejsou k dispozici žádné rozhraní API vystaveny odstranit certifikát certifikační autority vystavitela. Ruční standardní operační postup pro bezpečné odstranění kryptografických klíčů certifikační autority je k dispozici jenom přímým přístupem k trezoru klíčů na webu Azure Portal. Skupinu certifikátů můžete také odstranit v trezoru klíčů. Chcete-li zajistit okamžité odstranění, zakažte funkci [obnovitelného odstranění trezoru klíčů.](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

## <a name="certificates"></a>Certifikáty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikáty musí splňovat minimální profil certifikátu

Služba OPC Vault je online certifikační autorita, která odběratelům vydává certifikáty koncových entit. Mikroslužba OPC Vault se řídí těmito pokyny ve výchozí implementaci.

- Všechny certifikáty musí obsahovat následující pole X.509, jak je uvedeno níže:
  - Obsah pole verze musí být v3. 
  - Obsah pole serialNumber musí obsahovat alespoň 8 bajtů entropie získané z fips (Federal Information Processing Standards) 140 schválené generátor náhodných čísel.<br>
    > [!IMPORTANT]
    > Sériové číslo OPC Vault je ve výchozím nastavení 20 bajtů a je získáno z generátoru kryptografických náhodných čísel operačního systému. Generátor náhodných čísel je FIPS 140 schválen na zařízeních se systémem Windows, ale ne na Linuxu. Zvažte to při výběru nasazení služby, která používá linuxové virtuální počítače nebo linuxové docker kontejnery, na kterých základní technologie OpenSSL není schválenfips 140.
  - Pole issuerUniqueID a subjectUniqueID nesmí být k dispozici.
  - Certifikáty koncové entity musí být identifikovány s rozšířením základních omezení v souladu s IETF RFC 5280.
  - Pole pathLenConstraint musí být nastaveno na 0 pro certifikát vystavující certifikační autority. 
  - Rozšíření Rozšířené použití klíče musí být k dispozici a musí obsahovat minimální sadu identifikátorů objektů rozšířeného použití klíče (ID). AnyExtendedKeyUsage OID (2.5.29.37.0) nesmí být zadán. 
  - Rozšíření distribučního bodu CRL (CDP) musí být k dispozici v certifikátu certifikační autority vystavittele.<br>
    > [!IMPORTANT]
    > Rozšíření CDP je k dispozici v certifikátech OPC Vault CA. Zařízení OPC UA však k distribuci seznamů CRL používají vlastní metody.
  - V certifikátech odběratele musí být k dispozici rozšíření o přístup k informacím úřadu.<br>
    > [!IMPORTANT]
    > Rozšíření Přístup k informacím úřadu je k dispozici v certifikátech předplatitelů OPC Vault. Zařízení OPC UA však používají k distribuci informací certifikační autority vystavithona vlastní metody.
- Musí být použity schválené asymetrické algoritmy, délky klíčů, hash funkce a režimy odsazení.
  - RSA a SHA-2 jsou jediné podporované algoritmy.
  - RSA lze použít pro šifrování, výměnu klíčů a podpis.
  - Šifrování RSA musí používat pouze režimy odsazení OAEP, RSA-KEM nebo RSA-PSS.
  - Jsou požadovány délky klíče větší nebo rovno 2048 bitům.
  - Použijte sha-2 řady algoritmů hash (SHA256, SHA384 a SHA512).
  - Klíče kořenové certifikační autority RSA s typickou životností větší nebo rovnou 20 let musí být 4096 bitů nebo vyšší.
  - Klíče certifikační autority vystavitela RSA musí být alespoň 2048 bitů. Pokud je datum vypršení platnosti certifikátu certifikační autority po roce 2030, musí být klíč certifikační autority 4096 bitů nebo vyšší.
- Životnost certifikátu
  - Kořenové certifikáty certifikační autority: Maximální doba platnosti certifikátu pro kořenové certifikační autority nesmí přesáhnout 25 let.
  - Podřízený certifikační autority nebo online certifikáty certifikační autority vystavitela: Maximální doba platnosti certifikátu pro certifikační autority, které jsou online a vydávají pouze certifikáty předplatitelů, nesmí přesáhnout 6 let. Pro tyto certifikační autority nesmí být související soukromý podpisový klíč používán déle než 3 roky k vydávání nových certifikátů.<br>
    > [!IMPORTANT]
    > S certifikátem vystavittele, který je generován ve výchozí mikroslužbě OPC Vault bez externí kořenové certifikační autority, se zachází jako s online podcertifikačním systémem s příslušnými požadavky a životností. Výchozí životnost je nastavena na 5 let, s délkou klíče větší nebo rovnou 2048.
  - Všechny asymetrické klíče musí mít maximální životnost 5 let a doporučenou životnost 1 rok.<br>
    > [!IMPORTANT]
    > Ve výchozím nastavení životnost certifikátů aplikací vydaných s OPC Vault mají životnost 2 roky a by měla být nahrazena každý rok. 
  - Při každém obnovení certifikátu se obnoví pomocí nového klíče.
- Rozšíření specifická pro OPC UA v certifikátech instancí aplikace
  - Rozšíření subjectAltName zahrnuje aplikaci Uri a názvy hostitelů. Mezi ně mohou patřit také adresy FQDN, IPv4 a IPv6.
  - KeyUsage zahrnuje digitalSignature, nonRepudiation, keyEncipherment a dataEncipherment.
  - ExtendedKeyUsage zahrnuje serverAuth a clientAuth.
  - Identifikátor authorityKeyIdentifier je určen v podepsaných certifikátech.

### <a name="ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klíče a certifikáty certifikační autority musí splňovat minimální požadavky

- **Soukromé klíče**: Klíče RSA musí být alespoň 2048 bitů. Pokud je datum vypršení platnosti certifikátu certifikační autority po roce 2030, musí být klíč certifikační autority 4096 bitů nebo vyšší.
- **Životnost**: Maximální doba platnosti certifikátu pro certifikační autority, které jsou online a vydávají pouze osvědčení o odběrateli, nesmí přesáhnout 6 let. Pro tyto certifikační autority nesmí být související soukromý podpisový klíč používán déle než 3 roky k vydávání nových certifikátů.

### <a name="ca-keys-are-protected-using-hardware-security-modules"></a>Klíče certifikační autority jsou chráněny pomocí modulů hardwarového zabezpečení

OpcVault používá Azure Key Vault Premium a klíče jsou chráněny moduly hardwarového zabezpečení úrovně 2 FIPS (HSM). 

Kryptografické moduly, které trezor klíčů používá, ať už modul y HSM nebo software, jsou ověřeny FIPS. Klíče vytvořené nebo importované jako chráněné pomocí hsm jsou zpracovány uvnitř objektu hsm, ověřené na FIPS 140-2 úroveň 2. Klíče vytvořené nebo importované jako softwarově chráněné jsou zpracovány uvnitř kryptografických modulů ověřených na FIPS 140-2 Level 1.

## <a name="operational-practices"></a>Provozní postupy

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Dokumentujte a udržujte standardní provozní postupy PKI pro zápis certifikátů

Dokumentovat a udržovat standardní provozní postupy (SUP) pro vydávání certifikátů certifikačními autoritami, včetně: 
- Jak je odběratel identifikován a ověřen. 
- Způsob zpracování a ověření žádosti o certifikát (pokud je to možné, uveďte také způsob zpracování žádostí o obnovení certifikátu a opětovné hospo) . 
- Jak jsou vydané certifikáty distribuovány předplatitelům. 

Mikroservis OPC Vault SOP je popsán v [architektuře OPC Vault](overview-opc-vault-architecture.md) a [v oboru Správa certifikační služby OPC Vault](howto-opc-vault-manage.md). Postupy postupujte podle "OPC Unified Architecture Specifikace část 12: Zjišťování a globální služby."


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Dokumentovat a udržovat standardní provozní postupy PKI pro odvolání certifikátu

Proces odvolání certifikátu je popsán v [architektuře OPC Vault](overview-opc-vault-architecture.md) a [v aplikaci Manage the OPC Vault certificate service](howto-opc-vault-manage.md).
    
### <a name="document-ca-key-generation-ceremony"></a>Dokumentca klíč generace obřad 

Generování klíčů certifikační autority vystavitela v mikroslužbě OPC Vault je zjednodušené díky zabezpečenému úložišti v trezoru klíčů Azure. Další informace naleznete [v tématu Správa certifikační služby OPC Vault](howto-opc-vault-manage.md).

Pokud však používáte externí certifikační autoritu root, musí obřad generování klíčů certifikační autority splňovat následující požadavky.

Obřad generování klíčů certifikační autority musí být proveden proti zdokumentovanýmu skriptu, který obsahuje alespoň následující položky: 
- Definice rolí a odpovědností účastníků.
- Schválení pro provedení obřadu generování klíčů certifikační autority.
- Kryptografický hardware a aktivační materiály potřebné pro obřad.
- Příprava hardwaru (včetně aktualizace a odhlášení informací o majetku/konfiguraci).
- Instalace operačního systému.
- Konkrétní kroky provedené během obřadu generování klíčů certifikační autority, například: 
  - Instalace a konfigurace aplikace certifikační autority.
  - Generování klíčů certifikační autority.
  - Zálohování klíčů certifikační autority.
  - Podepisování certifikátu certifikační autority.
  - Import podepsaných klíčů v chráněném hsm mj služby.
  - Vypnutí systému CA.
  - Příprava materiálů pro skladování.


## <a name="next-steps"></a>Další kroky

Nyní, když jste se naučili, jak bezpečně spravovat OPC Vault, můžete:

> [!div class="nextstepaction"]
> [Bezpečná zařízení OPC UA s OPC trezorem](howto-opc-vault-secure.md)
