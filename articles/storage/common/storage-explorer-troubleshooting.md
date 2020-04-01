---
title: Průvodce odstraňováním potíží průzkumníka azure služby | Dokumenty společnosti Microsoft
description: Přehled technik ladění pro Průzkumníka úložiště Azure
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: db36033ea524603416f16db27f40d5eefb8bf613
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437108"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Průzkumníkem úložišť Azure

Microsoft Azure Storage Explorer je samostatná aplikace, která usnadňuje práci s daty Azure Storage na Windows, macOS a Linuxu. Aplikace se může připojit k účtům úložiště hostovaným v Azure, národních cloudech a Azure Stacku.

Tato příručka shrnuje řešení problémů, které se běžně vyskytují v Průzkumníku úložiště.

## <a name="rbac-permissions-issues"></a>Problémy s oprávněními RBAC

Řízení přístupu na základě rolí [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) umožňuje vysoce podrobnou správu přístupu prostředků Azure kombinací sad oprávnění do _rolí_. Tady jsou některé strategie, jak optimálně pracovat RBAC v Průzkumníku úložiště.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Jak se v Průzkumníku úložiště dostanu ke svým prostředkům?

Pokud máte problémy s přístupem k prostředkům úložiště prostřednictvím RBAC, pravděpodobně vám nebyly přiřazeny příslušné role. Následující části popisují oprávnění, která Průzkumník úložiště aktuálně vyžaduje pro přístup k prostředkům úložiště. Pokud si nejste jistí, že máte příslušné role nebo oprávnění, obraťte se na správce účtu Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problém s oprávněními "Číst: Seznam/Získat účty úložiště"

Musíte mít oprávnění k seznamu účtů úložiště. Chcete-li získat toto oprávnění, musíte být přiřazena role _Čtenář._

#### <a name="list-storage-account-keys"></a>Seznam klíčů účtů úložiště

Průzkumník úložiště může také použít klíče účtu k ověření požadavků. Ke klíčům účtu můžete získat prostřednictvím výkonnějších rolí, jako je například role _přispěvatele._

> [!NOTE]
> Přístupové klíče udělují neomezená oprávnění všem uživatelům, kteří je drží. Proto nedoporučujeme rozdávat tyto klíče uživatelům účtu. Pokud potřebujete odvolat přístupové klíče, můžete je znovu vygenerovat z [webu Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Datové role

Musíte přiřadit alespoň jednu roli, která uděluje přístup ke čtení dat z prostředků. Například pokud chcete vypsat nebo stáhnout objekty BLOB, budete potřebovat alespoň roli _čtečky dat objektů blob úložiště._

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Proč potřebuji roli vrstvy správy, aby se moje prostředky v Průzkumníku úložiště zoašky zoaškli?

Azure Storage má dvě vrstvy přístupu: _správu_ a _data_. Předplatná a účty úložiště jsou přístupné prostřednictvím vrstvy správy. Kontejnery, objekty BLOB a další datové prostředky jsou přístupné prostřednictvím datové vrstvy. Například pokud chcete získat seznam účtů úložiště z Azure, odešlete požadavek na koncový bod správy. Pokud chcete seznam kontejnerů objektů blob v účtu, odešlete požadavek na příslušný koncový bod služby.

Role RBAC mohou obsahovat oprávnění pro správu nebo přístup k datové vrstvě. Role Čtečka například uděluje přístup jen pro čtení k prostředkům vrstvy správy.

Přesněji řečeno, role Čtečka neposkytuje žádná oprávnění datové vrstvy a není nezbytná pro přístup k datové vrstvě.

Průzkumník úložiště usnadňuje přístup k prostředkům tím, že shromažďuje potřebné informace pro připojení k prostředkům Azure. Například chcete-li zobrazit kontejnery objektů blob, Průzkumník úložiště odešle požadavek "seznam kontejnerů" na koncový bod služby objektu blob. Chcete-li získat tento koncový bod, Průzkumník úložiště prohledá seznam předplatných a účtů úložiště, ke kterým máte přístup. K vyhledání předplatných a účtů úložiště potřebuje Průzkumník úložiště také přístup k vrstvě správy.

Pokud nemáte roli, která uděluje oprávnění vrstvy správy, Průzkumník úložiště nemůže získat informace, které potřebuje k připojení k datové vrstvě.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co když se mi od správce nepodaří získat potřebná oprávnění vrstvy pro správu?

V současné době nemáme řešení související s RBAC pro tento problém. Jako řešení můžete požádat o identifikátor URI SAS, který se [připojí k prostředku](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-built-in-rbac-roles"></a>Doporučené předdefinované role RBAC

Existuje několik předdefinovaných rolí RBAC, které mohou poskytnout oprávnění potřebná k použití Průzkumníka úložiště. Některé z těchto rolí jsou:
- [Vlastník](/azure/role-based-access-control/built-in-roles#owner): Správa všeho, včetně přístupu k prostředkům. **Poznámka:** Tato role vám poskytne přístup ke klíči.
- [Přispěvatel:](/azure/role-based-access-control/built-in-roles#contributor)Správa všeho, s výjimkou přístupu k prostředkům. **Poznámka:** Tato role vám poskytne přístup ke klíči.
- [Reader](/azure/role-based-access-control/built-in-roles#reader): Číst a seznam zdrojů.
- [Přispěvatel účtu úložiště](/azure/role-based-access-control/built-in-roles#storage-account-contributor): Úplná správa účtů úložiště. **Poznámka:** Tato role vám poskytne přístup ke klíči.
- [Vlastník dat objektu blob úložiště:](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)Úplný přístup ke kontejnerům objektů blob úložiště Azure a datům.
- [Přispěvatel dat objektu blob úložiště:](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)Čtení, zápis a odstranění kontejnerů a objektů BLOB služby Azure Storage.
- [Čtečka dat objektu blob úložiště:](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)Čtení a seznam kontejnerů azure storage a objektů BLOB.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Certifikát podepsaný svým držitelem v řetězu certifikátů (a podobné chyby)

K chybám certifikátu obvykle dochází v jedné z následujících situací:

- Aplikace je připojena prostřednictvím _transparentního proxy serveru_, což znamená, že server (například firemní server) zachycuje přenosy HTTPS, dešifruje je a poté jej šifruje pomocí certifikátu podepsaného svým držitelem.
- Používáte aplikaci, která vstřikuje certifikát TLS/SSL podepsaný svým držitelem do zpráv HTTPS, které obdržíte. Příklady aplikací, které vkládají certifikáty patří antivirový a software pro kontrolu provozu v síti.

Když Průzkumník úložiště vidí certifikát podepsaný svým držitelem nebo nedůvěryhodný certifikát, už neví, zda byla přijatá zpráva HTTPS změněna. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete průzkumníkovi úložiště dát pokyn, aby mu důvěřoval, a to takto:

1. Získejte kopii certifikátu s kódováním Base-64 X.509 (.cer).
2. Přejděte na **Upravit** > **certifikáty importu certifikátů****SSL** > a potom pomocí výběru souborů vyhledejte, vyberte a otevřete soubor CER.

K tomuto problému může dojít také v případě, že existuje více certifikátů (kořenové a zprostředkující). Chcete-li tuto chybu opravit, musí být přidány oba certifikáty.

Pokud si nejste jisti, odkud certifikát pochází, najděte ho takto:

1. Nainstalujte OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): Všechny verze světla by měly být dostatečné.
    * Mac a Linux: Měly by být součástí vašeho operačního systému.
2. Spustit OpensSL.
    * Windows: Otevřete instalační adresář, vyberte **/bin/** a poklepejte na **soubor openssl.exe**.
    * Mac a Linux: Spustit `openssl` z terminálu.
3. Spusťte `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jisti, které certifikáty jsou podepsány svým `("s:")` držitelem, poznamenejte si, kde je předmět a vystavitist `("i:")` stejný.
5. Když najdete certifikáty podepsané svým držitelem, zkopírujte a vložte vše od (včetně) `-----BEGIN CERTIFICATE-----` až `-----END CERTIFICATE-----` po nový soubor CER.
6. Otevřete Průzkumníka úložišť a přejděte na **upravit** > **certifikáty Import certifikátů****SSL** > . Potom pomocí výběru souborů vyhledejte, vyberte a otevřete vytvořené soubory CER.

Pokud pomocí těchto kroků nemůžete najít žádné certifikáty podepsané svým držitelem, kontaktujte nás prostřednictvím nástroje pro zpětnou vazbu. Průzkumníka úložiště můžete také otevřít z `--ignore-certificate-errors` příkazového řádku pomocí příznaku. Při otevření s tímto příznakem Průzkumník úložiště ignoruje chyby certifikátu.

## <a name="sign-in-issues"></a>Problémy s přihlašováním

### <a name="blank-sign-in-dialog-box"></a>Prázdné přihlašovací dialogové okno

K prázdným přihlašovacím dialogovým oknům nejčastěji dochází, když služba AD FS (Active Directory Federation Services) vyzve Průzkumníka úložiště k provedení přesměrování, které není podporováno společností Electron. Chcete-li tento problém vyřešit, můžete se pokusit použít tok kódu zařízení pro přihlášení. Postup je následující:

1. Na levém svislém panelu nástrojů otevřete **Nastavení**. V panelu Nastavení přejděte na**Přihlásit se k** **aplikaci** > . Povolit **použití přihlášení toku kódu zařízení**.
2. Otevřete dialogové okno **Připojit** (buď přes ikonu zástrčky na levém svislém pruhu, nebo výběrem **možnosti Přidat účet** na panelu účet).
3. Vyberte prostředí, ke kterým se chcete přihlásit.
4. Vyberte **Přihlásit se**.
5. Postupujte podle pokynů na dalším panelu.

Pokud se nemůžete přihlásit k účtu, který chcete použít, protože výchozí prohlížeč je již přihlášen k jinému účtu, udělejte jednu z těchto věcí:

- Ručně zkopírujte odkaz a kód do soukromé relace prohlížeče.
- Ručně zkopírujte odkaz a kód do jiného prohlížeče.

### <a name="reauthentication-loop-or-upn-change"></a>Opakování opětovného ověření nebo změna upn

Pokud jste ve smyčce pro opětovné ověřování nebo jste změnili hlavní zdroj onn jednoho ze svých účtů, postupujte takto:

1. Odeberte všechny účty a zavřete Průzkumníka úložiště.
2. Odstraňte soubor . IdentityService ze svého počítače. V systému Windows je `C:\users\<username>\AppData\Local`složka umístěna na adrese . Pro Mac a Linux, můžete najít složku v kořenovém adresáři vašeho uživatele.
3. Pokud používáte Mac nebo Linux, budete také muset odstranit položku Microsoft.Developer.IdentityService z úložiště klíčů operačního systému. Na Macu je keystore aplikace *Gnome Keychain.* V Linuxu se aplikace obvykle nazývá _Keyring_, ale název se může lišit v závislosti na vaší distribuci.

### <a name="conditional-access"></a>Podmíněný přístup

Z důvodu omezení v knihovně Azure AD používané Průzkumníka úložiště, podmíněný přístup není podporována při Storage Explorer se používá ve Windows 10, Linux, nebo macOS.

## <a name="mac-keychain-errors"></a>Chyby klíčenky Macu

Klíčenka macOS může někdy zadat stav, který způsobuje problémy pro ověřovací knihovnu Průzkumníka úložiště. Chcete-li klíčenku z tohoto stavu dostat, postupujte takto:

1. Zavřete Průzkumníka úložiště.
2. Otevřete řetězci klíčů (stiskněte command+mezerník, zadejte **klíčenku**a stiskněte Enter).
3. Vyberte "login" Klíčenka.
4. Chcete-li zámek zamknout, vyberte ikonu visacího zámku. (Visací zámek se po dokončení procesu zobrazí jako zamknutý. V závislosti na tom, jaké aplikace máte otevřené, může trvat několik sekund.

    ![Ikona zámku](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otevřete Průzkumníka služby Storage.
6. Zobrazí se výzva se zprávou jako "Centrum služeb chce získat přístup ke řetězci klíčů". Zadejte heslo účtu správce Macu a vyberte **Vždy povolit** (nebo **Povolit,** pokud není k dispozici **vždy povolit).**
7. Zkuste se přihlásit.

### <a name="general-sign-in-troubleshooting-steps"></a>Obecné kroky řešení potíží s přihlášením

* Pokud jste na macOS a přihlašovací okno se nikdy nezobrazí nad dialogovým oknem **Čekání na ověření,** vyzkoušejte [tento postup](#mac-keychain-errors).
* Restartujte Průzkumníka úložišť.
* Pokud je okno ověřování prázdné, počkejte alespoň jednu minutu před zavřením dialogového okna ověřování.
* Zkontrolujte, zda je nastavení proxy serveru a certifikátu správně nakonfigurované pro váš počítač i Průzkumníka úložiště.
* Pokud používáte Windows a máte přístup k Visual Studiu 2019 na stejném počítači a k přihlašovacím přihlašovacím údajům, zkuste se přihlásit k Visual Studiu 2019. Po úspěšném přihlášení do Visual Studia 2019 můžete otevřít Průzkumníka úložiště a zobrazit svůj účet v panelu účtu.

Pokud žádná z těchto metod nefunguje, [otevřete problém v GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Chybějící předplatná a nefunkční klienti

Pokud se vám po úspěšném přihlášení nedaří načíst předplatná, vyzkoušejte následující metody řešení potíží:

* Ověřte, zda má váš účet přístup k očekávaným předplatným. Svůj přístup můžete ověřit přihlášením k portálu pro prostředí Azure, které se pokoušíte použít.
* Ujistěte se, že jste se přihlásili prostřednictvím správného prostředí Azure (Azure, Azure China 21Vianet, Azure Germany, Azure US Government nebo Custom Environment).
* Pokud jste za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy server Průzkumníka úložiště.
* Zkuste účet odebrat a znovu přidat.
* Pokud existuje odkaz "Další informace", zkontrolujte, které chybové zprávy jsou hlášeny pro klienty, které selhávají. Pokud si nejste jisti, jak reagovat na chybové zprávy, neváhejte [otevřít problém v GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nelze odebrat připojený účet nebo prostředek úložiště.

Pokud nemůžete odebrat připojený účet nebo prostředek úložiště prostřednictvím hlavního nastavení, můžete ručně odstranit všechny připojené prostředky odstraněním následujících složek:

* Windows:`%AppData%/StorageExplorer`
* Macos:`/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux:`~/.config/StorageExplorer`

> [!NOTE]
> Před odstraněním těchto složek zavřete Průzkumníka úložiště.

> [!NOTE]
> Pokud jste někdy importovali nějaké certifikáty SSL, `certs` zálohujte obsah adresáře. Později můžete použít zálohu k opětovnému importu certifikátů SSL.

## <a name="proxy-issues"></a>Problémy s proxy serverem

Nejprve se ujistěte, že jsou správné následující informace:

* Adresa URL proxy a číslo portu
* Uživatelské jméno a heslo, pokud je proxy server vyžaduje

> [!NOTE]
> Průzkumník úložiště nepodporuje soubory s automatickými konfiguracemi proxy pro konfiguraci nastavení proxy serveru.

### <a name="common-solutions"></a>Společná řešení

Pokud potíže přetrvávají, vyzkoušejte následující metody řešení potíží:

* Pokud se můžete připojit k Internetu bez použití proxy serveru, ověřte, zda Průzkumník úložiště funguje bez povoleného nastavení proxy serveru. Pokud se jedná o tento případ, může být problém s nastavením proxy serveru. Spolupracujte se správcem na identifikaci problémů.
* Ověřte, zda ostatní aplikace, které používají proxy server, fungují podle očekávání.
* Ověřte, že se můžete připojit k portálu pro prostředí Azure, které se pokoušíte použít.
* Ověřte, zda můžete přijímat odpovědi z koncových bodů služby. Zadejte do prohlížeče jednu z adres URL koncových bodů. Pokud se můžete připojit, měli byste obdržet Hodnotu InvalidQueryParameterValue nebo podobnou odpověď XML.
* Pokud průzkumník storage explorer používá s vaším proxy serverem, ověřte, že se může připojit. Pokud ano, možná budete muset kontaktovat svého správce proxy serveru.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, například Šumař pro Windows, můžete diagnostikovat problémy následujícím způsobem:

* Pokud budete muset pracovat prostřednictvím proxy serveru, budete muset nakonfigurovat síťový nástroj pro připojení prostřednictvím proxy serveru.
* Zkontrolujte číslo portu používané síťovým nástrojem.
* V Průzkumníku úložiště zadejte jako nastavení proxy serveru adresu URL místního hostitele a číslo portu síťového nástroje. Když to uděláte správně, váš síťový nástroj spustí protokolování síťových požadavků provedených Průzkumníkem úložiště do koncových bodů pro správu a službu. Zadejte `https://cawablobgrs.blob.core.windows.net/` například koncový bod objektu blob v prohlížeči a obdržíte odpověď, která se podobá následujícímu:

  ![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Tato odpověď naznačuje, že prostředek existuje, i když k němu nemáte přístup.

### <a name="contact-proxy-server-admin"></a>Kontaktovat správce serveru proxy

Pokud je nastavení proxy serveru správné, možná budete muset kontaktovat správce proxy serveru, abyste:

* Ujistěte se, že váš proxy neblokuje provoz na Azure správu nebo koncové body prostředků.
* Ověřte ověřovací protokol používaný serverem proxy. Průzkumník úložiště aktuálně nepodporuje proxy servery NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva "Nelze načíst podřízené děti"

Pokud jste k Azure připojeni přes proxy server, ověřte, jestli je nastavení proxy serveru správné. Pokud je vám udělen přístup k prostředku od vlastníka předplatného nebo účtu, ověřte, zda máte oprávnění ke čtení nebo seznamu pro tento prostředek.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Připojovací řetězec nemá úplné nastavení konfigurace

Pokud se zobrazí tato chybová zpráva, je možné, že nemáte potřebná oprávnění k získání klíčů pro váš účet úložiště. Chcete-li ověřit, že se jedná o tento případ, přejděte na portál a vyhledejte účet úložiště. Můžete to provést kliknutím pravým tlačítkem myši na uzel pro váš účet úložiště a výběrem **možnosti Otevřít na portálu**. Potom přejděte k noži **přístupových kláves.** Pokud nemáte oprávnění k zobrazení klíčů, zobrazí se zpráva "Nemáte přístup". Chcete-li tento problém vyřešit, můžete buď získat klíč účtu od někoho jiného a připojit prostřednictvím názvu a klíče, nebo můžete požádat někoho o SAS k účtu úložiště a použít jej k připojení účtu úložiště.

Pokud se vám zobrazí klíče účtu, nasoubor problém v GitHubu, abychom vám mohli pomoci problém vyřešit.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Při přidávání nového připojení došlo k chybě: TypeError: Nelze číst vlastnost 'verze' nedefinované

Pokud se při pokusu o přidání vlastního připojení zobrazí tato chybová zpráva, mohou být poškozena data připojení uložená v místním správci pověření. Chcete-li tento problém vyřešit, zkuste odstranění poškozených místních připojení a potom je znovu přidat:

1. Spusťte Průzkumníka úložišť. V nabídce přejděte k **nápovědě** > **k nástrojům pro vývojáře**.
2. V otevřeném okně přejděte na kartě **Aplikace** na **místní úložiště** (levá strana) > **file://**.
3. V závislosti na typu připojení, se kterým máte problém, vyhledejte jeho klíč a zkopírujte jeho hodnotu do textového editoru. Hodnota je pole vlastní názvy připojení, například následující:
    * Účty úložiště
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontejnery s objekty blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Sdílené složky
        * `StorageExplorer_CustomConnections_Files_v1`
    * Fronty
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabulky
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po uložení aktuálních názvů připojení nastavte hodnotu `[]`v nástroji pro vývojáře na .

Pokud chcete zachovat připojení, která nejsou poškozena, můžete pomocí následujících kroků vyhledat poškozená připojení. Pokud vám nevadí, že ztratíte všechna existující připojení, můžete tyto kroky přeskočit a podle pokynů pro konkrétní platformu vymazat data připojení.

1. Z textového editoru znovu přidejte každý název připojení do nástrojů pro vývojáře a zkontrolujte, zda připojení stále funguje.
2. Pokud připojení funguje správně, není poškozeno a můžete ho tam bezpečně nechat. Pokud připojení nefunguje, odeberte jeho hodnotu z nástrojů pro vývojáře a zaznamenejte ho, abyste ho mohli později přidat zpět.
3. Opakujte, dokud neprozkoumáte všechna spojení.

Po procházení všech připojení, pro všechny názvy připojení, které nejsou přidány zpět, je nutné vymazat jejich poškozená data (pokud existuje) a přidat je zpět pomocí standardních kroků v Průzkumníku úložiště:

# <a name="windows"></a>[Windows](#tab/Windows)

1. V nabídce **Start** vyhledejte **Správce přihlašovacích údajů** a otevřete jej.
2. Přejděte na **pověření systému Windows**.
3. V části Obecná pověření vyhledejte **položky,** které mají `<connection_type_key>/<corrupted_connection_name>` klíč `StorageExplorer_CustomConnections_Accounts_v1/account1`(například).
4. Odstraňte tyto položky a znovu přidejte připojení.

# <a name="macos"></a>[Macos](#tab/macOS)

1. Otevřete Spotlight (Command+Spacebar) a vyhledejte **přístup ke řetězci klíčenky**.
2. Vyhledejte položky, `<connection_type_key>/<corrupted_connection_name>` které mají `StorageExplorer_CustomConnections_Accounts_v1/account1`klíč (například).
3. Odstraňte tyto položky a znovu přidejte připojení.

# <a name="linux"></a>[Linux](#tab/Linux)

Místní správa pověření se liší v závislosti na distribuci Linuxu. Pokud vaše distribuce Linuxu neposkytuje integrovaný nástroj GUI pro místní správu přihlašovacích údajů, můžete nainstalovat nástroj jiného výrobce pro správu místních přihlašovacích údajů. Můžete například použít [Seahorse](https://wiki.gnome.org/Apps/Seahorse/), open source GUI nástroj pro správu linuxových lokálních pověření.

1. Otevřete místní nástroj pro správu přihlašovacích údajů a najděte uložená pověření.
2. Vyhledejte položky, `<connection_type_key>/<corrupted_connection_name>` které mají `StorageExplorer_CustomConnections_Accounts_v1/account1`klíč (například).
3. Odstraňte tyto položky a znovu přidejte připojení.
---

Pokud se s touto chybou setkáte i po spuštění těchto kroků nebo chcete sdílet to, co máte podezření, že poškodilo připojení, [otevřete problém](https://github.com/microsoft/AzureStorageExplorer/issues) na naší stránce GitHub.

## <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS

Pokud se připojujete ke službě prostřednictvím adresy URL SAS a dochází k chybě:

* Ověřte, zda adresa URL poskytuje potřebná oprávnění ke čtení nebo seznamu prostředků.
* Ověřte, zda platnost adresy URL nevypršela.
* Pokud je adresa URL SAS založena na zásadách přístupu, ověřte, zda zásady přístupu nebyly odvolány.

Pokud jste omylem připojili pomocí neplatné adresy URL SAS a nyní nelze odpojit, postupujte takto:

1. Když používáte Průzkumníka úložiště, otevřete stisknutím klávesy F12 okno Nástroje pro vývojáře.
2. Na kartě **Aplikace** vyberte**ve** stromu vlevo file:// **místního úložiště.** > 
3. Najděte klíč přidružený k typu služby problematického identifikátoru URI SAS. Například pokud je chybný identifikátor URI SAS pro kontejner `StorageExplorer_AddStorageServiceSAS_v1_blob`objektů blob, vyhledejte klíč s názvem .
4. Hodnota klíče by měla být pole JSON. Najděte objekt přidružený k chybnému identifikátoru URI a odstraňte jej.
5. Stisknutím kláves Ctrl+R znovu načtěte Průzkumníka úložišť.

## <a name="linux-dependencies"></a>Závislosti na Linuxu

Průzkumník úložiště 1.10.0 a novější je k dispozici jako přichycení z Úložiště přichycení. Přichycení Průzkumníka úložiště nainstaluje všechny své závislosti automaticky a aktualizuje se, když je k dispozici nová verze přichycení. Doporučeným způsobem instalace je instalace přichycení Průzkumníka úložiště.

Průzkumník úložiště vyžaduje použití správce hesel, který může být nutné připojit ručně, než bude Průzkumník úložiště fungovat správně. Průzkumníka úložiště můžete připojit ke Správci hesel systému spuštěním následujícího příkazu:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Aplikaci můžete také stáhnout jako soubor .tar.gz, ale budete muset nainstalovat závislosti ručně.

> [!IMPORTANT]
> Storage Explorer, jak je uvedeno v .tar.gz download je podporován pouze pro distribuce Ubuntu. Jiné distribuce nebyly ověřeny a mohou vyžadovat alternativní nebo další balíčky.

Tyto balíčky jsou nejběžnější požadavky pro Storage Explorer na Linuxu:

* [Doba běhu .NET Core 2.2](/dotnet/core/install/dependencies?tabs=netcore22&pivots=os-linux)
* `libgconf-2-4`
* `libgnome-keyring0` nebo `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Průzkumník úložiště verze 1.7.0 a starší vyžadují rozhraní .NET Core 2.0. Pokud máte nainstalovanou novější verzi rozhraní .NET Core, budete muset [opravit Průzkumníka úložiště](#patching-storage-explorer-for-newer-versions-of-net-core). Pokud používáte Storage Explorer 1.8.0 nebo novější, měli byste být schopni použít až .NET Core 2.2. Verze nad 2.2 nebyly ověřeny pro práci v tomto okamžiku.

# <a name="ubuntu-1904"></a>[Ubuntu 19.04](#tab/1904)

1. Stáhněte si Průzkumníka úložiště.
2. Nainstalujte [rozhraní .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Stáhněte si Průzkumníka úložiště.
2. Nainstalujte [rozhraní .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Stáhněte si Průzkumníka úložiště.
2. Nainstalujte [rozhraní .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404"></a>[Ubuntu 14.04](#tab/1404)

1. Stáhněte si Průzkumníka úložiště.
2. Nainstalujte [rozhraní .NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Průzkumník úložiště pro novější verze rozhraní .NET Core

Pro Průzkumníka úložiště 1.7.0 nebo starší může být pravděpodobně potřeba opravit verzi jádra .NET používanou Průzkumníkem úložiště:

1. Stáhněte si verzi 1.5.43 StreamJsonRpc [z NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Podívejte se na odkaz "Stáhnout balíček" na pravé straně stránky.
2. Po stažení balíčku změňte jeho `.nupkg` příponu z na . `.zip`
3. Rozbalte balíček.
4. Otevřete `streamjsonrpc.1.5.43/lib/netstandard1.1/` složku.
5. Zkopírování `StreamJsonRpc.dll` do následujících umístění ve složce Průzkumník úložiště:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>"Otevřít v průzkumníku" z webu Azure Portal nefunguje

Pokud tlačítko **Otevřít v průzkumníkovi** na webu Azure Portal nefunguje, ujistěte se, že používáte kompatibilní prohlížeč. Následující prohlížeče byly testovány na kompatibilitu:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Další kroky

Pokud žádné z těchto řešení nefunguje, [otevřete problém v GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues). Můžete to také provést výběrem **tlačítka Report issue to GitHub** v levém dolním rohu.

![Váš názor](./media/storage-explorer-troubleshooting/feedback-button.PNG)
