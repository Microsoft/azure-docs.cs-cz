---
title: Průvodce odstraňováním potíží s Průzkumník služby Azure Storage | Microsoft Docs
description: Přehled technik ladění pro Průzkumník služby Azure Storage
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3a95d52f2a0aa07094f91b0653fcb94ff1f88d44
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959003"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Průzkumník služby Azure Storage

Průzkumník služby Microsoft Azure Storage je samostatná aplikace, která umožňuje snadnou práci s Azure Storagemi daty v systémech Windows, macOS a Linux. Aplikace se může připojit k účtům úložiště hostovaným v Azure, národním cloudům a Azure Stack.

Tato příručka shrnuje řešení běžných potíží, které se zobrazují v Průzkumník služby Storage.

## <a name="role-based-access-control-permission-issues"></a>Problémy s oprávněním Access Control na základě rolí

[Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) poskytuje jemně odstupňovanou správu přístupu k prostředkům Azure tím, že kombinuje sady oprávnění s _rolemi_. Tady jsou některé návrhy, které vám pomůžou při práci na Průzkumník služby Storage.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Co potřebuji k zobrazení mých prostředků v Průzkumník služby Storage?

Pokud máte problémy s přístupem k prostředkům úložiště pomocí RBAC, může to být způsobeno tím, že jste nepřiřadili příslušné role. Následující části popisují oprávnění Průzkumník služby Storage aktuálně vyžadují přístup k prostředkům úložiště.

Pokud si nejste jistí, že máte příslušné role nebo oprávnění, obraťte se na správce účtu Azure.

#### <a name="read-listget-storage-accounts"></a>Oprávnění Vypsat/získat účty úložiště

Musíte mít oprávnění k vypsání účtů úložiště. Toto oprávnění můžete získat tak, že přiřadíte roli Čtenář.

#### <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště

Průzkumník služby Storage můžou k ověřování požadavků používat taky klíče účtu. Můžete získat přístup k klíčům s výkonnějšími rolemi, jako je například role Přispěvatel.

> [!NOTE]
> Přístupové klíče udělují neomezená oprávnění komukoli, kdo je drží. Proto se obecně nedoporučuje, aby byly předávány uživatelům účtu. Pokud potřebujete odvolat přístupové klíče, můžete je znovu vygenerovat z webu [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role dat

Musíte mít přiřazenou aspoň jednu roli, která uděluje přístup ke čtení dat z prostředků. Pokud potřebujete například vypsat nebo stáhnout objekty blob, budete potřebovat alespoň roli "čtecí modul dat objektů BLOB úložiště".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Proč potřebuji roli vrstvy správy, aby se zobrazily moje prostředky v Průzkumník služby Storage?

Azure Storage má dvě vrstvy přístupu: _správu_ a _data_. K předplatným a účtům úložiště se dostanete prostřednictvím vrstvy správy. K kontejnerům, objektům blob a dalším datovým prostředkům se dostanete přes datovou vrstvu. Pokud například chcete získat seznam účtů úložiště z Azure, odešlete žádost do koncového bodu správy. Pokud chcete v účtu vytvořit seznam kontejnerů objektů blob, odešlete požadavek na příslušný koncový bod služby.

Role RBAC můžou obsahovat oprávnění pro správu nebo přístup k datové vrstvě. Role čtenář například uděluje prostředky vrstvy správy přístupu jen pro čtení.

Výhradně řečeno, role čtenář neposkytuje žádná oprávnění k datové vrstvě a není nutná pro přístup k datové vrstvě.

Průzkumník služby Storage usnadňuje přístup k prostředkům tím, že shromáždí potřebné informace pro připojení k prostředkům Azure za vás. Pokud například chcete zobrazit kontejnery objektů blob, Průzkumník služby Storage odešle požadavek na kontejnery seznamu do koncového bodu služby BLOB Service. Pokud chcete tento koncový bod získat, Průzkumník služby Storage prohledá seznam předplatných a účtů úložiště, ke kterým máte přístup. K vyhledání předplatných a účtů úložiště ale Průzkumník služby Storage také potřebovat přístup ke vrstvě správy.

Pokud nemáte roli udělující žádná oprávnění vrstvy správy, Průzkumník služby Storage nemůže získat informace, které potřebuje pro připojení k datové vrstvě.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co když mi nejde získat oprávnění pro vrstvu správy, které potřebuji od správce?

V tuto chvíli zatím nepoužíváme řešení související s RBAC. Jako alternativní řešení si můžete vyžádat identifikátor URI SAS, který se [připojí k vašemu prostředku](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Certifikát podepsaný svým držitelem v řetězu certifikátů (a podobné chyby)

Chyby certifikátu jsou způsobeny jednou ze dvou následujících situací:

1. Aplikace je připojená prostřednictvím transparentního proxy serveru, což znamená, že server (například server vaší společnosti) zachycuje provoz HTTPS, dešifruje ho a pak ho šifruje pomocí certifikátu podepsaného svým držitelem.
2. Používáte aplikaci, která vkládá certifikát SSL podepsaný svým držitelem do zpráv HTTPS, které obdržíte. Příklady aplikací, které obsahují certifikáty, zahrnují software pro kontrolu proti virům a síťovým přenosům.

Když Průzkumník služby Storage uvidí certifikát podepsaný svým držitelem nebo nedůvěryhodným certifikátem, nemůže už zjistit, jestli se přijatá zpráva HTTPS nezměnila. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete mu dát Průzkumník služby Storage důvěřovat pomocí následujících kroků:

1. Získejte kopii certifikátu X. 509 (. cer) s kódováním Base-64.
2. Klikněte na upravit **certifikáty → SSL** → **Import certifikátů**a potom pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubor. cer.

Tento problém může být také výsledkem více certifikátů (root a Intermediate). K překonání chyby je nutné přidat oba certifikáty.

Pokud si nejste jistí, odkud certifikát pochází, můžete zkusit najít tento postup:

1. Nainstalujte OpenSSL.
    * [Systém Windows](https://slproweb.com/products/Win32OpenSSL.html) (všechny verze světla by měly být dostatečné)
    * Mac a Linux: měla by být součástí vašeho operačního systému.
2. Spusťte OpenSSL.
    * Windows: otevřete instalační adresář, klikněte na **/bin/** a potom poklikejte na **OpenSSL. exe**.
    * Mac a Linux: Spusťte **OpenSSL** z terminálu.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, které certifikáty jsou podepsané svým držitelem, hledejte všude, `("s:")` kde je `("i:")` váš předmět a vydavatel stejné.
5. Po nalezení libovolných certifikátů podepsaných svým držitelem si pro každý z nich zkopírujte a vložte všechno z a včetně **-----Spustit certifikát-----** , aby se **-----koncovým certifikátem-----** do nového souboru. cer.
6. Otevřete Průzkumník služby Storage klikněte na **Upravit** **certifikáty → SSL** → **Import certifikátů**a potom pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubory. CER, které jste vytvořili.

Pokud nemůžete najít žádné certifikáty podepsané svým držitelem pomocí předchozích kroků, kontaktujte nás prostřednictvím nástroje pro zpětnou vazbu, kde najdete další informace. Můžete také zvolit spuštění Průzkumník služby Storage z příkazového řádku s `--ignore-certificate-errors` příznakem. Při spuštění s tímto příznakem Průzkumník služby Storage bude ignorovat chyby certifikátu.

## <a name="sign-in-issues"></a>Problémy s přihlašováním

### <a name="blank-sign-in-dialog"></a>Dialog prázdné přihlášení

Nepodporovaná dialogová okna jsou nejčastěji způsobená tím, že služba ADFS žádá Průzkumník služby Storage, aby provedla přesměrování, která není elektronicky podporovaná. Pokud chcete tento problém obejít, můžete se pokusit použít tok kódu zařízení pro přihlášení. Provedete to provedením následujících kroků:

1. Nabídce Preview – > použít přihlášení ke kódu zařízení.
2. Otevřete dialogové okno připojit (buď prostřednictvím ikony plug-in na levé straně panelu, nebo "Přidat účet" na panelu účet).
3. Vyberte prostředí, ke kterému se chcete přihlásit.
4. Klikněte na tlačítko Přihlásit se.
5. Postupujte podle pokynů na dalším panelu.

Pokud zjistíte, že máte potíže s přihlášením k účtu, který chcete použít, protože váš výchozí prohlížeč je už přihlášený k jinému účtu, můžete použít tyto kroky:

1. Ručně zkopírujte odkaz a kód do privátní relace prohlížeče.
2. Ručně zkopírujte odkaz a kód do jiného prohlížeče.

### <a name="reauthentication-loop-or-upn-change"></a>Smyčka opakovaného ověřování nebo změna hlavního názvu uživatele

Pokud jste ve smyčce opakovaného ověřování nebo jste změnili hlavní název uživatele (UPN) jednoho z vašich účtů, zkuste provést následující kroky:

1. Odeberte všechny účty a potom zavřete Průzkumník služby Storage
2. Odstraňte. IdentityService složku z počítače. Ve Windows se složka nachází na adrese `C:\users\<username>\AppData\Local`. V případě systémů Mac a Linux můžete složku najít v kořenovém adresáři adresáře uživatele.
3. Pokud pracujete v systému Mac nebo Linux, budete také muset odstranit položku Microsoft. Developer. IdentityService z úložiště klíčů operačního systému. V systému Mac je úložiště klíčů aplikace "GNOME řetězce klíčů". Pro Linux se aplikace obvykle nazývá "Ring", ale název se může lišit v závislosti na vaší distribuci.

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup není podporován, pokud je Průzkumník služby Storage používáno ve Windows 10, Linux nebo macOS. Důvodem je omezení knihovny AAD, kterou používá Průzkumník služby Storage.

## <a name="mac-keychain-errors"></a>Chyby řetězce klíčů Mac

MacOS řetězec klíčů se někdy může dostat do stavu, který způsobuje problémy pro knihovnu ověřování Průzkumník služby Storage. Pokud chcete z tohoto stavu získat řetězce klíčů, vyzkoušejte následující kroky:

1. Zavřete Průzkumník služby Storage.
2. Otevřete řetězec klíčů (**cmd + Space**, zadejte řetězec klíčů, stiskněte ENTER).
3. Vyberte řetězec "login".
4. Kliknutím na ikonu visacího zámku nezobrazuje uzamknete řetězec klíčů (visacího zámku nezobrazuje se po dokončení animuje na uzamčenou pozici, může to trvat několik sekund v závislosti na tom, jaké aplikace jste otevřeli).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Spusťte Průzkumník služby Storage.
6. Zobrazí se automaticky otevírané okno s informací, že centrum služeb chce získat přístup k řetězci klíčů. V takovém případě zadejte heslo účtu správce Mac a klikněte na možnost **vždy povoleno** (nebo **Povolte** možnost **vždy** zakázat, pokud není k dispozici).
7. Zkuste se přihlásit.

### <a name="general-sign-in-troubleshooting-steps"></a>Obecné kroky při řešení potíží s přihlašováním

* Pokud jste na macOS, a přihlašovací okno se nikdy nezobrazuje přes "čekání na ověření"... a potom zkuste provést [tyto kroky](#mac-keychain-errors) .
* Restartovat Průzkumník služby Storage
* Pokud je okno ověřování prázdné, před zavřením dialogového okna ověřování počkejte alespoň jednu minutu.
* Ujistěte se, že je nastavení proxy serveru a certifikátu správně nakonfigurované pro váš počítač i Průzkumník služby Storage.
* Pokud pracujete v systému Windows a máte přístup k aplikaci Visual Studio 2019 ve stejném počítači a přihlašování, zkuste se přihlásit do sady Visual Studio 2019. Po úspěšném přihlášení do sady Visual Studio 2019 můžete otevřít Průzkumník služby Storage a zobrazit účet na panelu účet.

Pokud žádná z těchto metod nefunguje, [otevřete problém na GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Chybějící předplatná a poškození klienti

Pokud nemůžete načíst vaše předplatná po úspěšném přihlášení, zkuste následující metody řešení potíží:

* Ověřte, že váš účet má přístup k předplatným, která očekáváte. Přístup můžete ověřit tak, že se přihlásíte k portálu pro prostředí Azure, které se pokoušíte použít.
* Ujistěte se, že jste se přihlásili pomocí správného prostředí Azure (Azure, Azure Čína 21Vianet, Azure Německo, Azure US státní správa a vlastní prostředí).
* Pokud jste za proxy serverem, ujistěte se, že jste správně nakonfigurovali Průzkumník služby Storage proxy.
* Zkuste účet odebrat a znovu přidat.
* Pokud je k dispozici odkaz Další informace, podívejte se a podívejte se, jaké chybové zprávy jsou hlášeny pro klienty, kteří selžou. Pokud si you'ren'ti, co dělat s chybovými zprávami, které vidíte, můžete si klidně [otevřít problém na GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Nejde odebrat připojený účet nebo prostředek úložiště.

Pokud se vám v uživatelském rozhraní nedaří odebrat připojený účet nebo prostředek úložiště, můžete ručně odstranit všechny připojené prostředky tak, že odstraníte následující složky:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Před odstraněním výše uvedených složek zavřete Průzkumník služby Storage.

> [!NOTE]
> Pokud jste někdy importovali všechny certifikáty SSL, zazálohujte obsah `certs` adresáře. Později můžete pomocí zálohy znovu naimportovat své certifikáty SSL.

## <a name="proxy-issues"></a>Problémy s proxy serverem

Nejprve se ujistěte, že jsou správné následující informace, které jste zadali:

* Adresa URL proxy serveru a číslo portu
* Uživatelské jméno a heslo v případě potřeby proxy serverem

> [!NOTE]
> Průzkumník služby Storage nepodporuje soubory automatické konfigurace proxy serveru pro konfiguraci nastavení proxy serveru.

### <a name="common-solutions"></a>Běžná řešení

Pokud stále dochází k problémům, zkuste následující metody řešení potíží:

* Pokud se můžete připojit k Internetu bez použití proxy serveru, ověřte, že Průzkumník služby Storage funguje bez povoleného nastavení proxy serveru. V takovém případě může dojít k potížím s nastavením proxy serveru. Pro identifikaci problémů Spolupracujte se správcem proxy serveru.
* Ověřte, že další aplikace, které používají proxy server, fungují podle očekávání.
* Ověřte, že se můžete připojit k portálu pro prostředí Azure, které se pokoušíte použít.
* Ověřte, že můžete přijímat odpovědi z koncových bodů služby. Do prohlížeče zadejte jednu z adres URL koncového bodu. Pokud se můžete připojit, měli byste obdržet InvalidQueryParameterValue nebo podobnou odpověď XML.
* Pokud Průzkumník služby Storage s proxy server používá i někdo jiný, ověřte, že se můžou připojit. Pokud se mohou připojit, bude pravděpodobně nutné kontaktovat správce proxy server.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, například Fiddler pro Windows, můžete problémy diagnostikovat následujícím způsobem:

* Pokud potřebujete pracovat přes proxy server, bude pravděpodobně nutné nakonfigurovat síťový nástroj pro připojení prostřednictvím proxy serveru.
* Ověřte číslo portu používaného síťovým nástrojem.
* Zadejte adresu URL místního hostitele a číslo portu nástroje sítě jako nastavení proxy serveru v Průzkumník služby Storage. Po správné práci nástroj sítě spustí protokolování požadavků sítě, které provedly Průzkumník služby Storage ke správě a koncovým bodům služby. Například zadejte https://cawablobgrs.blob.core.windows.net/ pro koncový bod objektu BLOB v prohlížeči a obdržíte odpověď podobnou následující, která naznačuje, že prostředek existuje, i když k němu nemáte přístup.

![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontaktujte správce proxy server

Pokud je vaše nastavení proxy serveru správné, možná budete muset kontaktovat správce proxy server a

* Ujistěte se, že proxy neblokuje provoz do koncových bodů prostředků nebo správy Azure.
* Ověřte protokol ověřování používaný vaším proxy server. Průzkumník služby Storage v současné době nepodporuje proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva Nelze načíst podřízené položky.

Pokud jste připojení k Azure prostřednictvím proxy serveru, ověřte správnost nastavení proxy serveru. Pokud jste udělili přístup k prostředku z vlastníka předplatného nebo účtu, ověřte, že máte oprávnění číst nebo zobrazit seznam pro daný prostředek.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Připojovací řetězec nemá kompletní konfigurační nastavení.

Pokud se zobrazí tato chybová zpráva, je možné, že nemáte potřebná oprávnění k získání klíčů pro váš účet úložiště. Pokud se chcete ujistit, jestli se jedná o tento případ, přejděte na portál a vyhledejte svůj účet úložiště. To můžete rychle provést tak, že kliknete pravým tlačítkem na uzel svého účtu úložiště a kliknete na otevřít na portálu. Až to uděláte, přejděte do okna přístupové klíče. Pokud nemáte oprávnění k zobrazení klíčů, zobrazí se stránka se zprávou "nemáte přístup". Pokud chcete tento problém obejít, můžete buď získat klíč účtu od někoho jiného a připojit se s jeho jménem a klíčem, nebo můžete požádat někoho o SAS k účtu úložiště a použít ho k připojení účtu úložiště.

Pokud se zobrazí klíče účtu, založte problém na GitHubu, abychom vám mohli pomohli tento problém vyřešit.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Při přidávání nového připojení došlo k chybě: TypeError Nejde číst vlastnost Version nedefinovaného typu.

Pokud se zobrazí tato chybová zpráva při pokusu o přidání vlastního připojení, je možné, že data připojení uložená v místním Správci přihlašovacích údajů jsou poškozená.
Pokud chcete tento problém obejít, můžete zkusit odstranit poškozená místní připojení a pak je znovu přidat.

1. Spusťte Průzkumník služby Storage. V horní nabídce přejděte na help → přepnout Vývojářské nástroje.
2. V otevřeném okně Přejít na kartu aplikace → místní úložiště (levá strana) → file://
3. V závislosti na typu připojení, se kterými máte potíže, vyhledejte svůj klíč a zkopírujte jeho hodnotu do textového editoru. Hodnota je pole vašich vlastních názvů připojení.
    * Účty úložiště
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontejnery objektů BLOB
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Sdílené složky
        * `StorageExplorer_CustomConnections_Files_v1`
    * Fronty
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabulky
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po uložení aktuálních názvů připojení nastavte hodnotu v Vývojářské nástroje `[]`.

Pokud chcete zachovat nepoškozená připojení, můžete k vyhledání poškozených připojení použít následující postup. Pokud si nejste vědomi ztrátu všech existujících připojení, můžete přeskočit následující kroky a podle pokynů pro konkrétní platformu vymazat data o připojení.

1. V textovém editoru znovu přidejte název každého připojení zpátky do Vývojářské nástroje a ověřte, zda připojení stále funguje.
2. Pokud připojení funguje správně, není poškozeno a můžete ho bezpečně opustit. Pokud připojení nefunguje, odeberte jeho hodnotu z Vývojářské nástroje a zaznamenejte si ho, abyste ho mohli později přidat zpátky.
3. Opakujte, dokud nezkontrolovali všechna připojení.

Po prozatím všech připojeních se u všech názvů připojení, které se nepřidaly, musíte vymazat poškozená data (pokud existují) a přidat je zpátky pomocí běžných kroků pomocí Průzkumník služby Storage.

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. Otevřete Správce přihlašovacích údajů tak, že otevřete nabídku Start a vyhledáte správce přihlašovacích údajů.
2. V otevřeném okně Přejít na přihlašovací údaje systému Windows.
3. V části Obecné přihlašovací údaje vyhledejte položky s klíčem `<connection_type_key>/<corrupted_connection_name>` ( `StorageExplorer_CustomConnections_Accounts_v1/account1`například).
4. Odeberte tyto položky a přidejte připojení zpátky.

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Otevřete Spotlight (panel příkazů příkazového řádku) a vyhledejte řetězec přístup k řetězci.
2. Vyhledejte položky s klíčem `<connection_type_key>/<corrupted_connection_name>` ( `StorageExplorer_CustomConnections_Accounts_v1/account1`například).
3. Odstraňte tyto položky a přidejte připojení zpátky.

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

Správa místních přihlašovacích údajů se liší v závislosti na distribuci systému Linux. Pokud vaše distribuce systému Linux neposkytuje integrovaný nástroj grafického uživatelského rozhraní pro správu místních přihlašovacích údajů, můžete nainstalovat nástroj třetí strany pro správu místních přihlašovacích údajů. Můžete například použít Open source nástroj [Seahorse](https://wiki.gnome.org/Apps/Seahorse/)pro správu místních přihlašovacích údajů pro Linux.

1. Otevřete nástroj pro správu místních přihlašovacích údajů, najděte uložené přihlašovací údaje.
2. Vyhledejte položky s klíčem `<connection_type_key>/<corrupted_connection_name>` ( `StorageExplorer_CustomConnections_Accounts_v1/account1`například).
3. Odstraňte tyto položky a přidejte připojení zpátky.
---

Pokud k této chybě dochází i po provedení těchto kroků, nebo pokud byste chtěli sdílet, co si myslíte, že jsou připojení poškozená, [otevřete problém](https://github.com/microsoft/AzureStorageExplorer/issues) na naší stránce GitHubu.

## <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS

Pokud se připojujete ke službě pomocí adresy URL SAS a dochází k této chybě:

* Ověřte, že adresa URL poskytuje potřebná oprávnění ke čtení nebo vypsání prostředků.
* Ověřte, že platnost adresy URL nevypršela.
* Pokud je adresa URL SAS založená na zásadách přístupu, ověřte, že zásady přístupu nebyly odvolány.

Pokud omylem připojíte neplatnou adresu URL SAS a nemůžete se odpojit, postupujte podle těchto kroků:

1. Při spuštění Průzkumník služby Storage otevřete okno vývojářské nástroje stisknutím klávesy F12.
2. Klikněte na kartu aplikace a potom ve stromu vlevo klikněte na místní úložiště > file://.
3. Vyhledejte klíč přidružený k typu služby problematického identifikátoru URI SAS. Pokud je například špatný identifikátor URI SAS pro kontejner objektů blob, vyhledejte klíč s názvem `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Hodnota klíče by měla být pole JSON. Najděte objekt přidružený ke špatnému identifikátoru URI a odeberte ho.
5. Stisknutím kombinace kláves CTRL + R znovu načtěte Průzkumník služby Storage.

## <a name="linux-dependencies"></a>Závislosti Linux

Průzkumník služby Storage 1.10.0 a novější je k dispozici jako modul snap z obchodu s modulem snap-in. Průzkumník služby Storage přichycení automaticky nainstaluje všechny závislosti a aktualizuje, když je k dispozici nová verze modulu snap-in. Instalace modulu snap-in Průzkumník služby Storage je doporučovanou metodou instalace.

Průzkumník služby Storage vyžaduje použití Správce hesel, který může být nutné ručně připojit, jinak bude Průzkumník služby Storage fungovat správně. Pomocí následujícího příkazu se můžete připojit Průzkumník služby Storage k správci hesel vašeho systému:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Aplikaci můžete také stáhnout jako soubor. tar. gz, ale budete muset nainstalovat závislosti ručně.

> [!IMPORTANT]
> Průzkumník služby Storage, jak je uvedeno v souborech. tar. gz, jsou podporovány pouze pro distribuce Ubuntu. Jiné distribuce nebyly ověřeny a mohou vyžadovat alternativní nebo další balíčky.

Tyto balíčky jsou nejběžnějšími požadavky na Průzkumník služby Storage v systému Linux:

* [Modul runtime .NET Core 2,0](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` Nebo `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Průzkumník služby Storage verze 1.7.0 a starší vyžadují .NET Core 2,0. Pokud máte nainstalovanou novější verzi .NET Core, budete muset [Průzkumník služby Storage opravit](#patching-storage-explorer-for-newer-versions-of-net-core). Pokud používáte Průzkumník služby Storage 1.8.0 nebo novější, měli byste být schopni použít až .NET Core 2,2. Verze nad rámec 2,2 se neověřily, aby v tuto chvíli fungovaly.

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Stáhněte si Průzkumník služby Storage.
2. Nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18,04](#tab/1804)

1. Stáhněte si Průzkumník služby Storage.
2. Nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Stáhnout Průzkumník služby Storage
2. Nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Stáhnout Průzkumník služby Storage
2. Nainstalujte [modul runtime .NET Core](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current).
3. Spusťte následující příkaz:
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Oprava Průzkumník služby Storage pro novější verze .NET Core

Pro Průzkumník služby Storage 1.7.0 nebo starší možná budete muset opravit verzi rozhraní .NET Core, kterou používá Průzkumník služby Storage.

1. Stáhněte si 1.5.43 verze StreamJsonRpc [z NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Vyhledejte odkaz Stáhnout balíček na pravé straně stránky.
2. Po stažení balíčku změňte jeho příponu z `.nupkg` na. `.zip`
3. Rozbalíte balíček.
4. Otevřít `streamjsonrpc.1.5.43/lib/netstandard1.1/` složky.
5. Zkopírujte `StreamJsonRpc.dll` do následujících umístění ve složce Průzkumník služby Storage:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Otevření v Průzkumníkovi z Azure Portal nefunguje

Pokud na Azure Portal nefunguje tlačítko otevřít v Průzkumníkovi, ujistěte se, že používáte kompatibilní prohlížeč. Následující prohlížeče byly testovány na kompatibilitu.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Další postup

Pokud žádná z řešení nefunguje za vás, [otevřete problém na GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues). K GitHubu se můžete také rychle dostat pomocí tlačítka "nahlásit problém do GitHubu" v levém dolním rohu.

![Zpětná vazba](./media/storage-explorer-troubleshooting/feedback-button.PNG)
