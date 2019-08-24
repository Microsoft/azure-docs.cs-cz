---
title: Jak bezpečně spustit službu správy certifikátů trezoru OPC – Azure | Microsoft Docs
description: Popisuje, jak bezpečně spustit službu pro správu certifikátů trezoru OPC v Azure a další pokyny pro zabezpečení, které je potřeba zvážit.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: f35836f60fae11c0955c128e96a4cea188681942
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69997660"
---
# <a name="how-to-run-the-opc-vault-certificate-management-service-securely"></a>Bezpečné spuštění služby Správa certifikátů trezoru OPC

Tento článek vysvětluje, jak bezpečně spustit službu pro správu certifikátů trezoru OPC v Azure a další pokyny pro zabezpečení, které je potřeba zvážit.

## <a name="roles"></a>Role

### <a name="trusted-and-authorized-roles"></a>Důvěryhodné a autorizované role

Mikroslužba trezoru OPC je nakonfigurovaná tak, aby umožňovala jedinečným rolím přístup k různým částem služby.

> [!IMPORTANT]
> Během nasazování skript přidá pouze uživatele, který spouští skript nasazení jako uživatel pro všechny role.
> Toto přiřazení role by mělo být přezkoumáno pro produkční nasazení a znovu nakonfigurováno podle pokynů níže.
> Tato úloha vyžaduje ruční přiřazení rolí a služeb na portálu Azure AD Enterprise Applications.

### <a name="certificate-management-service-roles"></a>Role služby správy certifikátů

Mikroslužba trezoru OPC definuje následující role:

- **Čtecí modul**: Ve výchozím nastavení má každý ověřený uživatel v tenantovi oprávnění ke čtení. 
  - Přístup pro čtení aplikací a žádostí o certifikát. Může vypsat a dotazovat se na aplikace a žádosti o certifikát. K dispozici jsou také informace o zjišťování zařízení a veřejné certifikáty s přístupem pro čtení.
- **Zapisovač**: Role zapisovače je přiřazena uživateli, aby pro určité úlohy přidala oprávnění k zápisu. 
  - Přístup pro čtení a zápis pro aplikace a žádosti o certifikát. Může registrovat, aktualizovat a rušit registraci aplikací. Může vytvářet žádosti o certifikát a získávat schválené privátní klíče a certifikáty. Může také odstranit privátní klíče.
- **Schvalovatel**: Role schvalovatele je přiřazena uživateli ke schválení nebo zamítnutí žádostí o certifikát. Role neobsahuje žádné další role.
  - Kromě role schvalovatele pro přístup k rozhraní API mikroslužeb trezoru OPC musí mít uživatel také oprávnění podepisovat klíč v Key Vault, aby mohli certifikáty podepisovat.
  - Role zapisovače a schvalovatele by se měla přiřadit různým uživatelům.
  - Hlavní rolí schvalovatele je schválení generování a zamítnutí žádostí o certifikát.
- **Správce**: Role správce je přiřazena uživateli ke správě skupin certifikátů. Tato role nepodporuje roli schvalovatele, ale obsahuje roli zapisovače.
  - Správce může spravovat skupiny certifikátů, měnit konfiguraci a odvolat certifikáty aplikací vyvoláním nového seznamu CRL.
  - V ideálním případě se role zapisovače, schvalovatele a správce přiřazují různým uživatelům. Pro zvýšení zabezpečení potřebuje uživatel s rolí schvalovatele nebo správce také oprávnění podepisovat klíče v trezoru klíčů k vystavování certifikátů nebo obnovení certifikátu certifikační autority vystavitele.
  - Kromě role správy mikroslužeb zahrnuje role také, ale není omezena na:
    - Zodpovídá za správu implementace postupů zabezpečení certifikační autority.
    - Správa generování, odvolání a pozastavení certifikátů. 
    - Správa životního cyklu kryptografického klíče (například obnovení klíčů certifikační autority vystavitele).
    - Instalace, konfigurace a údržba služeb, které provozují certifikační autoritu.
    - Každodenní operace služeb. 
    - Certifikační autorita a zálohování a obnovení databáze.

### <a name="other-role-assignments"></a>Další přiřazení rolí

Při používání služby by se měly brát v úvahu taky tyto role a přiřazené:

- Obchodní vlastník smlouvy o nákupu certifikátů s externím kořenovým certifikačním úřadem (v případě, kdy vlastník koupí certifikáty z externí certifikační autority nebo provozuje certifikační autoritu podřízenou externí certifikační autoritě).
- Vývoj a ověření certifikační autority.
- Kontrola záznamů auditu.
- Zaměstnanci, kteří vám pomůžou zajistit podporu certifikační autority nebo spravovat fyzická a cloudová zařízení, ale nejsou přímo důvěryhodní k provádění operací CA, se definují jako v autorizované roli. Je také nutné zdokumentovat sadu úloh, které jsou v autorizované roli povolené.

### <a name="memberships-of-trusted-and-authorized-roles-must-be-reviewed-annually"></a>Členství v důvěryhodných a autorizovaných rolích musí být každoročně přezkoumáno.

Členství v důvěryhodných a autorizovaných rolích musí být zkontrolováno nejméně čtvrtletně, aby bylo zajištěno, že sada osob (pro manuální procesy) nebo identity služby (pro automatizované procesy) je udržována na minimum.

### <a name="certificate-issuance-process-must-enforce-role-separation-between-certificate-requester-and-approver"></a>Proces vystavování certifikátů musí vyhovět oddělení rolí mezi žadatelem a schvalovatelem certifikátů.

Proces vystavení certifikátu musí vyhovět oddělení rolí mezi žadatelem certifikátu a rolemi schvalovatele certifikátů (osoby nebo automatizované systémy). Vystavení certifikátu musí být autorizován rolí schvalovatele certifikátu, která ověřuje, jestli žadatel o certifikát má oprávnění k získání certifikátů. Osoby, které drží roli schvalovatele certifikátů, musí být formálně autorizovaní osoba.

### <a name="privileged-role-management-must-restrict-access-and-be-reviewed-quarterly"></a>Správce privilegovaných rolí musí omezit přístup a kontrolovat čtvrtletní

Přiřazení privilegovaných rolí, jako je například autorizace členství ve skupině Administrators a schvalovatelé, musí být omezeno na omezenou skupinu autorizovaných zaměstnanců. Všechny změny privilegované role musí mít přístup odvolaný do 24 hodin. V konečném případě musí být přiřazení privilegovaných rolí přezkoumána čtvrtletně a musí být odebrána všechna nepotřebná nebo vydaná přiřazení s vypršenou platností.

### <a name="privileged-roles-should-use-two-factor-authentication"></a>Privilegované role by se měly používat se dvěma faktory ověřování.

Multi-Factor Authentication (dvojúrovňové ověřování, MFA nebo TFA) se musí použít k interaktivním přihlášení schvalovatelů a správcům ke službě.

## <a name="certificate-service-operation-guidelines"></a>Pokyny pro operace certifikační služby

### <a name="operational-contacts"></a>Pracovní kontakty

Služba Certificate Service musí mít v souboru aktuální plán odpovědí na zabezpečení, který obsahuje podrobné kontakty na reakci na provozní incidenty.

### <a name="security-updates"></a>Aktualizace zabezpečení

Všechny systémy se musí průběžně monitorovat a aktualizovat s nejnovějšími aktualizacemi zabezpečení/opravami.

> [!IMPORTANT]
> Úložiště GitHub služby trezoru OPC se průběžně aktualizuje pomocí oprav zabezpečení. Aktualizace na GitHubu by se měly monitorovat a aktualizace služby se v pravidelných intervalech použijí.

### <a name="security-monitoring"></a>Monitorování zabezpečení

Přihlaste se k odběru nebo implementujte vhodné monitorování zabezpečení, třeba přihlášení k odběru řešení centrálního monitorování (například Azure Security Center, řešení pro monitorování O365) a proveďte jeho patřičnou konfiguraci, abyste zajistili, že se události zabezpečení přenáší do řešení monitorování.

> [!IMPORTANT]
> Ve výchozím nastavení je služba OPC trezor nasazená s [Azure Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/devops) jako monitorovací řešení. Důrazně doporučujeme přidat řešení zabezpečení, jako je [Azure Security Center](https://azure.microsoft.com/services/security-center/) .

### <a name="assess-security-of-open-source-software-components"></a>Vyhodnotit zabezpečení Open Source softwarových komponent

Všechny open-source komponenty, které se používají v rámci produktu nebo služby, musí být bez slabých nebo větších ohrožení zabezpečení.

> [!IMPORTANT]
> Úložiště GitHub služby OPC trezoru kontroluje všechny komponenty během průběžných integrací sestavení pro ohrožení zabezpečení. Aktualizace na GitHubu by se měly monitorovat a aktualizace služby se v pravidelných intervalech použijí.

### <a name="maintain-an-inventory"></a>Údržba inventáře

Inventář prostředků musí být udržován pro všechny provozní hostitele (včetně trvalých virtuálních počítačů), zařízení, všechny interní rozsahy IP adres, VIP a názvy veřejných domén DNS. Tento inventář se musí aktualizovat přidáním nebo odebráním systému, IP adresy zařízení, virtuální IP adresy nebo veřejné domény DNS do 30 dnů.

#### <a name="inventory-of-the-default-azure-opc-vault-microservice-production-deployment"></a>Inventář výchozího nasazení mikroslužeb Azure OPC trezoru: 

V **Azure**:
- **App Service plán**: Plán služby App Service pro hostitele služeb. Výchozí S1.
- **App Service** pro mikroslužbu: Hostitel služby úložiště OPC
- **App Service** pro ukázkovou aplikaci: Hostitel ukázkové aplikace OPC trezoru.
- **Trezor klíčů Standard**: Uložení tajných kódů a Cosmos DB klíčů pro webové služby.
- **Premium trezoru**klíčů: Hostování klíčů certifikační autority vystavitele pro podpisovou službu pro konfiguraci trezoru a ukládání privátních klíčů aplikací.
- **Cosmos DB**: Databáze pro žádosti o aplikace a certifikáty. 
- **Application Insights**: (volitelné) řešení monitorování pro webovou službu a aplikaci.
- **Registrace aplikace AzureAD**: Registrace pro ukázkovou aplikaci, službu a modul Edge.

Pro Cloud Services by se měly zdokumentovat všechny názvy hostitelů, skupiny prostředků, názvy prostředků, ID předplatného, TenantId použité k nasazení služby. 

V **IoT Edge** nebo místní **Server IoT Edge**:
- **Modul IoT Edge trezoru OPC**: Pro podporu globálního zjišťování serveru OPC UA (Factory Network). 

Pro IoT Edge zařízení by se měly názvy hostitelů a IP adresy zdokumentovat. 

### <a name="document-the-certification-authorities-cas"></a>Dokumentace certifikačních autorit (CAs)

Dokumentace k hierarchii certifikační autority musí obsahovat všechny provozované certifikační autority, včetně všech souvisejících podřízených certifikačních autorit, nadřazených certifikačních autorit a kořenových certifikačních autorit, i když je služba nespravuje. Místo formální dokumentace může být k dispozici vyčerpávající sada všech certifikátů certifikační autority, které nejsou prošly platností.

> [!NOTE]
> Ukázková aplikace OPC trezoru podporuje stažení všech certifikátů používaných a vyprodukovaných ve službě pro dokumentaci.

### <a name="document-the-issued-certificates-by-all-certification-authorities-cas"></a>Dokumentování vydaných certifikátů všemi certifikačními autoritami (CAs)

Pro dokumentaci by měla být k dispozici vyčerpávající sada všech certifikátů vydaných za posledních 12 měsíců.

> [!NOTE]
> Ukázková aplikace OPC trezoru podporuje stažení všech certifikátů používaných a vyprodukovaných ve službě pro dokumentaci.

### <a name="document-the-sop-for-securely-deleting-cryptographic-keys"></a>Dokumentuje autority pro bezpečné odstranění kryptografických klíčů.

Odstranění klíče se může během životnosti certifikační autority jenom zřídka. Důvodem je, že žádný uživatel nemá přiřazený certifikát klíče trezoru pro odstranění a proč nejsou k dispozici žádná rozhraní API pro odstranění certifikátu certifikační autority vystavitele. Ruční standardní pracovní postup pro bezpečné odstranění šifrovacích klíčů certifikační autority je k dispozici jenom přímým přístupem k trezoru klíčů v Azure Portal a odstraněním skupiny certifikátů v trezoru klíčů. Aby bylo zajištěno okamžité odstranění [obnovitelného odstranění trezoru](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) klíčů, mělo by být zakázáno.

## <a name="certificates"></a>Certifikáty

### <a name="certificates-must-comply-with-minimum-certificate-profile"></a>Certifikáty musí splňovat minimální profil certifikátu.

Služba OPC trezor je online certifikační autorita (CA), která vydává certifikát koncových entit předplatitelům.
Mikroslužba trezoru OPC se řídí těmito pokyny ve výchozí implementaci.

- Všechny certifikáty musí obsahovat následující pole X. 509, jak je uvedeno níže:
  - Obsah pole verze musí být v3. 
  - Obsah pole sériového čísla musí obsahovat nejméně 8 bajtů entropie získaného ze standardu FIPS (Federal Information Processing Standards) 140 schválen generátor náhodných čísel.<br>
    > [!IMPORTANT]
    > Sériové číslo trezoru OPC je ve výchozím nastavení 20 bajtů a získáno z generátoru kryptografického náhodného čísla operačního systému. Generátor náhodných čísel je FIPS 140 schválen na zařízeních s Windows, ale ne v systémech Linux. Tento fakt je potřeba vzít v úvahu při volbě nasazení služby, které používá virtuální počítače se systémem Linux nebo kontejnery Docker pro Linux, na kterých základní OpenSSL technologie není schválená FIPS 140.
  - Pole issuerUniqueID a subjectUniqueID nesmí být k dispozici.
  - Certifikáty koncových entit se musí identifikovat pomocí rozšíření základní omezení v souladu se specifikací IETF RFC 5280.
  - Pro certifikát vydávající certifikační autority musí být pole pathLenConstraint nastaveno na hodnotu 0. 
  - Rozšíření použití rozšířeného klíče musí být k dispozici a musí obsahovat minimální sadu identifikátorů OID rozšířeného použití klíče (OID). Identifikátor anyExtendedKeyUsage OID (2.5.29.37.0) nesmí být zadaný. 
  - V certifikátu certifikační autority vystavitele musí být rozšíření distribučního bodu seznamu CRL (CDP).<br>
    > [!IMPORTANT]
    > Rozšíření distribuční bod seznamu CRL (CDP) se nachází v certifikátech certifikační autority OPC trezoru. zařízení OPC UA ale přesto používají vlastní metody k distribuci seznamů CRL.
  - V certifikátech odběratele se musí nacházet rozšíření přístupu k informacím autority.<br>
    > [!IMPORTANT]
    > Rozšíření přístup k informacím autority je k dispozici v certifikátech předplatitelů OPC trezoru. zařízení OPC UA však k distribuci informací CA vystavitele používají vlastní metody.
- Je nutné použít schválené asymetrické algoritmy, délky klíčů, funkce algoritmu hash a režimy odsazení.
  - Jediným podporovaným algoritmům je **RSA** a **SHA-2** (*).
  - RSA se dá použít k šifrování, výměně klíčů a podpisu.
  - Šifrování RSA musí používat jenom režimy odsazení výplně OAEP, RSA-KEM nebo RSA-PSS.
  - Délka klíče > = 2048 bitů je povinná.
  - Použijte rodinu SHA-2 algoritmů hash (SHA256, SHA384 a SHA512).
  - Klíče kořenové certifikační autority RSA s typickou životností > = 20 let musí být 4096 bitů nebo větší.
  - Klíče certifikační autority vystavitele RSA musí mít aspoň 2048 bitů; Pokud je datum vypršení platnosti certifikátu certifikační autority pozdější než 2030, musí být klíč certifikační autority 4096 bitů nebo větší.
- Životnost certifikátu
  - Certifikáty kořenové certifikační autority: Maximální doba platnosti certifikátu pro kořenové certifikační autority nesmí přesáhnout 25 let.
  - Certifikáty certifikační autority pro podřízenou certifikační autoritu nebo online vydavatele: Maximální doba platnosti certifikátu pro certifikační autority, které jsou online a mají stav pouze certifikáty odběratele, nesmí překročit šest let. U těchto certifikačních autorit se nesmí použít související privátní podpisový klíč delší než tři roky k vydávání nových certifikátů.<br>
    > [!IMPORTANT]
    > Certifikát vystavitele, jak je vygenerovaný ve výchozí mikroslužbě trezoru OPC bez externí kořenové certifikační autority se považuje za online podca s příslušnými požadavky a životností. Výchozí doba života je nastavená na pět let s délkou klíče > = 2048.
  - U všech asymetrických klíčů musí být životnost delší než 5 let, Doporučená doba života v jednom roce.<br>
    > [!IMPORTANT]
    > Ve výchozím nastavení mají životnost certifikátů aplikace vydaných s trezorem OPC životnost dvou let a měly by být nahrazeny každým rokem. 
  - Při obnovení certifikátu se obnoví nový klíč.
- Rozšíření specifická pro OPC UA v certifikátech instance aplikace
  - Rozšíření subjectAltName zahrnuje identifikátory URI a názvy aplikací, které můžou zahrnovat taky plně kvalifikovaný název domény, adresy IPv4 a IPv6.
  - Použití modulu zahrnuje digitalSignature, neneodvolatelnost, keyEncipherment a dataEncipherment.
  - ExtendedKeyUsage zahrnuje serverAuth a/nebo clientAuth.
  - AuthorityKeyIdentifier je určena v podepsaných certifikátech.

### <a name="certificate-authority-ca-keys-and-certificates-must-meet-minimum-requirements"></a>Klíče certifikační autority a certifikáty musí splňovat minimální požadavky.

- **Privátní klíče**: Klíče **RSA** musí mít aspoň 2048 bitů; Pokud je datum vypršení platnosti certifikátu certifikační autority pozdější než 2030, musí být klíč certifikační autority 4096 bitů nebo větší.
- **Doba života**: Maximální doba platnosti certifikátu pro certifikační autority, které jsou online a mají stav pouze certifikáty odběratele, nesmí překročit šest let. U těchto certifikačních autorit se nesmí použít související privátní podpisový klíč delší než tři roky k vydávání nových certifikátů.

### <a name="ca-keys-are-protected-using-hardware-security-modules-hsm"></a>Klíče certifikační autority jsou chráněné pomocí modulů hardwarového zabezpečení (HSM).

- OpcVault používá službu Azure Key trezor úrovně Premium a klíče jsou chráněné moduly hardwarového zabezpečení (HSM) standardu FIPS 140-2 úrovně 2. 

Kryptografické moduly, které Key Vault používá, je-li modul HARDWAROVÉho zabezpečení nebo software, ověřovány pomocí standardu FIPS (Federal Information Processing Standards).<br>
Klíče vytvořené nebo importované jako chráněné HSM se zpracovávají v modulu HARDWAROVÉho zabezpečení, který je ověřený na úrovni FIPS 140-2 Level 2.<br>
Klíče vytvořené nebo importované jako chráněný software se zpracovávají v rámci kryptografických modulů ověřených na FIPS 140-2 úrovně 1.

## <a name="operational-practices"></a>Provozní postupy

### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-enrollment"></a>Zdokumentujte a udržujte standardní provozní postupy infrastruktury veřejných klíčů pro zápis certifikátů.

Zdokumentujte a udržujte standardní provozní postupy (shody) pro způsob, jakým certifikační autority vydávají certifikáty, včetně: 
- Způsob identifikace a ověření předplatitele 
- Jak se žádost o certifikát zpracovává a ověřuje (Pokud je k dispozici), včetně toho, jak se zpracovávají žádosti o obnovení certifikátu a opětovné použití klíčů 
- Způsob distribuce vydaných certifikátů předplatitelům 

Mikroslužba OPC trezor autority je popsaná v [přehledu](overview-opc-vault-architecture.md) a v tématu [Správa](howto-opc-vault-manage.md) dokumentů. Postupy postupují podle specifikace OPC Unified Architecture, část 12: Zjišťování a globální služby.


### <a name="document-and-maintain-standard-operational-pki-practices-for-certificate-revocation"></a>Zdokumentujte a udržujte standardní provozní postupy infrastruktury veřejných klíčů pro odvolání certifikátů.

Proces odvolaných certifikátů je popsaný v [přehledu](overview-opc-vault-architecture.md) a v tématu [Správa](howto-opc-vault-manage.md) dokumentů.
    
### <a name="document-certification-authority-ca-key-generation-ceremony"></a>Dokument pro generování klíčů certifikační autority (CA) pro procedury 

Generování klíče CA vystavitele ve mikroslužbě trezoru OPC je zjednodušené z důvodu zabezpečeného úložiště v úložišti klíčů Azure a popsaných v tématu [Správa](howto-opc-vault-manage.md) dokumentace.

Pokud se však používá externí kořenová certifikační autorita, musí procedury generování klíčů certifikační autority (CA) splňovat následující požadavky:

Procedury generování klíčů certifikační autority musí být proveden na dokumentovaném skriptu, který obsahuje alespoň následující položky: 
1. Definice rolí a odpovědností účastníků
2. Schválení pro provedení procedury generování klíčů certifikační autority
3. Kryptografický hardware a aktivační materiály vyžadované pro procedury
4. Příprava hardwaru (včetně informací o prostředcích a konfiguracích a odhlašování)
5. Instalace operačního systému
6. Konkrétní kroky provedené během generování klíče certifikační autority procedury, například: 
7. Instalace a konfigurace aplikace CA
8. Generování klíče certifikační autority
9. Zálohování klíče certifikační autority
10. Podepisování certifikátu certifikační autority
9. Import podepsaných klíčů v chráněném modulu HSM služby.
11. Vypnutí systému certifikační autority
12. Příprava materiálů pro úložiště


## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak bezpečně spravovat OPC trezor, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Zabezpečení zařízení OPC UA pomocí trezoru OPC](howto-opc-vault-secure.md)