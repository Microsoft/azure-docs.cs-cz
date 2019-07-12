---
title: Průvodce odstraňováním potíží Azure Storage Exploreru | Dokumentace Microsoftu
description: Přehled ladění techniky pro Průzkumníka služby Azure Storage
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: cf26455ce069294bd1c5a52306ed21019287a0df
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786284"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Průvodce odstraňováním potíží pro Azure Storage Exploreru

Microsoft Azure Storage Explorer je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Storage ve Windows, macOS a Linuxu. Aplikace může připojit k účtům úložiště hostované v Azure, národní Cloudy a Azure Stack.

Tato příručka obsahuje souhrn řešení běžných problémů zobrazit v Průzkumníku služby Storage.

## <a name="role-based-access-control-permission-issues"></a>Problémy oprávnění řízení přístupu na základě role

[Řízení přístupu na základě role (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) poskytuje propracovanou správu přístupu prostředků Azure díky kombinaci sady oprávnění do _role_. Tady jsou některé návrhy, můžete postupovat podle zobrazíte RBAC práci v Průzkumníku služby Storage.

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Co je třeba zobrazit Moje prostředky v Průzkumníku služby Storage?

Pokud máte potíže s přístupem k prostředkům úložiště pomocí RBAC, pravděpodobně nebyly byla přiřazena odpovídající role. Následující části popisují Průzkumníka služby Storage aktuálně vyžaduje pro přístup k prostředkům úložiště oprávnění.

Pokud si nejste jisti, že máte příslušné role nebo oprávnění, obraťte se na správce účtu Azure.

#### <a name="read-listget-storage-accounts"></a>Čtení: Vypsat nebo získat účty úložiště

Musíte mít oprávnění k seznamu účtů úložiště. Tato oprávnění můžete získat podle přiřazení role "Čtenář".

#### <a name="list-storage-account-keys"></a>Vypsat klíče účtu úložiště

Průzkumník služby Storage použít také klíče účtu k ověření požadavků. Můžete získat přístup ke klíčům výkonnější role, jako je například "Přispěvatel" role.

> [!NOTE]
> Přístupové klíče neomezená oprávnění udělit všem uživatelům, kteří je obsahuje. Proto se obecně nedoporučuje, možné předat pro účet uživatele. Pokud budete chtít odvolat přístupové klíče, můžete vygenerovat znovu z [webu Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role dat

Musí být přiřazen alespoň jednu roli, která uděluje přístup Číst data ze zdroje. Například pokud potřebujete zobrazit nebo stáhnout objekty BLOB, budete potřebovat minimálně role "Čtenář dat objektu Blob úložiště".

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Proč potřebuji roli vrstva správy zobrazíte Moje prostředky v Průzkumníku služby Storage?

Azure Storage má dvě vrstvy přístupu: _správu_ a _data_. Předplatná a účty úložiště jsou přístupné prostřednictvím vrstva správy. Kontejnery objektů BLOB a dalších dat prostředků jsou přístupné prostřednictvím datové vrstvě. Například pokud chcete získat seznam účtů úložiště z Azure, můžete odeslat požadavek na koncový bod správy. Pokud chcete seznam kontejnerů objektů blob v účtu služby, odeslat požadavek na koncový bod příslušnou službu.

Role RBAC může obsahovat oprávnění pro přístup k vrstvě správy nebo data. Role "Čtenář, například uděluje přístup jen pro čtení prostředků vrstvy správy.

Přesněji řečeno role "Čtenář" poskytuje žádná oprávnění datové vrstvy a není nutné pro přístup k datové vrstvě.

Průzkumník služby Storage usnadňuje přístup k vašim prostředkům shromáždit informace potřebné k připojení k prostředkům Azure za vás. Například pokud chcete zobrazit vaše kontejnery objektů blob, odešle Průzkumníka služby Storage žádost o seznam kontejnerů do koncového bodu služby objektů blob. Chcete-li získat tento koncový bod, Průzkumníka služby Storage vyhledá seznam předplatných a účtů úložiště, abyste měli přístup k. Ale předplatných a účtů úložiště najdete Průzkumníka služby Storage také potřebuje přístup k vrstvě správy.

Pokud nemáte k dispozici role udělení žádné management oprávnění vrstvy, Průzkumníka služby Storage nelze získat informace, které potřebuje pro připojení k datové vrstvě.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co když mám nelze získat rozhraní pro správu vrstvy oprávnění je třeba Moje správce?

Zatím nemáme řešení související RBAC v tuto chvíli. Jako alternativní řešení, můžete požádat o identifikátor URI SAS pro [připojit k prostředku](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri).

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Certifikát podepsaný svým držitelem v řetězu certifikátů (a podobné chyby)

Chyby certifikátů jsou způsobeny jednu ze dvou následujících situací:

1. Aplikace je propojena prostřednictvím "transparentní proxy server", což znamená, že server (například serveru vaší společnosti) zachycuje přenosy HTTP, dešifruje je a pak je šifruje pomocí certifikátu podepsaného svým držitelem.
2. Teď máte spuštěnou aplikaci, která je do zpráv protokolu HTTPS, které jste dostali vkládá certifikát SSL podepsaný svým držitelem. Příkladem aplikací, které vložení certifikátů obsahuje antivirový program a síťové přenosy kontroly software.

Pokud Průzkumník služby Storage se zobrazí certifikát podepsaný svým držitelem nebo nedůvěryhodné, můžete už vědět, zda byla změněna přijatou zprávu protokolu HTTPS. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete dát pokyn Průzkumníka služby Storage důvěřovat provedením následujících kroků:

1. Získat s kódováním Base-64 X.509 (.cer) kopii certifikátu
2. Klikněte na tlačítko **upravit** > **certifikáty SSL** > **importovat certifikáty**a pak pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubor .cer

Tento problém může být také výsledkem několik certifikátů (kořenové a zprostředkující). Oba certifikáty musí být přidaný do překonat chyby.

Pokud si nejste jisti odkud certifikátu, můžete vyzkoušet následující postup vás bude:

1. Nainstalujte OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (jakákoli z odlehčených verzí by měl být dostatečná)
    * Mac a Linux: mělo by být součástí operačního systému
2. Spusťte OpenSSL.
    * Windows: otevřete instalační adresář, klikněte na tlačítko **/bin/** a potom dvakrát klikněte na panel **openssl.exe**.
    * Mac a Linux: Spusťte **openssl** z terminálu.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, jaké certifikáty jsou podepsané svým držitelem, hledejte kdekoli předmět `("s:")` a vystavitele `("i:")` jsou stejné.
5. Po nalezení jakékoli certifikáty podepsané svým držitelem pro každé z nich, zkopírujte a vložte všechno z a to včetně **---BEGIN CERTIFICATE---** k **---END CERTIFICATE---** do nového souboru .cer.
6. Otevřete Průzkumníka služby Storage, klikněte na tlačítko **upravit** > **certifikáty SSL** > **importovat certifikáty**a pak pomocí nástroje pro výběr souborů vyhledejte, vyberte, a Otevřete soubory .cer, které jste vytvořili.

Pokud nemůžete najít žádné certifikáty podepsané svým držitelem podle předchozích kroků, kontaktujte nás prostřednictvím nástroje pro zpětnou vazbu o další pomoc. Můžete také spustit z příkazového řádku pomocí Průzkumníka služby Storage `--ignore-certificate-errors` příznak. Při spuštění s tímto příznakem, Průzkumníka služby Storage se bude ignorovat chyby certifikátů.

## <a name="sign-in-issues"></a>Problémy s přihlašováním

### <a name="blank-sign-in-dialog"></a>Prázdné přihlašovacího dialogového okna

Prázdný znak v dialogových oknech jsou často způsobeny služby AD FS s dotazem, Průzkumníka služby Storage provádět přesměrování, který nepodporuje elektronovým. Chcete-li tento problém obejít, pokuste se použít tok kódu zařízení pro přihlášení. Uděláte to tak, proveďte následující kroky:

1. Nabídka: Ve verzi Preview -> "Pomocí přihlášení zařízení kód".
2. Otevřete dialogové okno připojit, (buď prostřednictvím moduly ikonu na levé straně svislá čára, nebo "Přidat účet" na panel účtu).
3. Vyberte prostředí, jaké chcete k přihlášení k.
4. Klikněte na tlačítko "Sign In".
5. Postupujte podle pokynů na další panel.

Pokud se potíže při přihlašování k účtu, který chcete použít, protože váš výchozí prohlížeč se už přihlásili do jiného účtu, můžete buď:

1. Ručně zkopírujte odkaz a kód do privátní relace prohlížeče.
2. Ručně zkopírujte odkaz a kód do jiného prohlížeče.

### <a name="reauthentication-loop-or-upn-change"></a>Opětovné ověření smyčky nebo změnit hlavní název uživatele

Pokud jste ve smyčce opětovné ověření, nebo se změnily (UPN) jednoho z vašich účtů, vyzkoušejte následující kroky:

1. Odeberte všechny účty a potom zavřete Průzkumníka služby Storage
2. Odstranit. IdentityService složky z vašeho počítače. Na Windows, se nachází ve složce `C:\users\<username>\AppData\Local`. Pro Mac a Linux můžete najít složku v kořenovém adresáři uživatele.
3. Pokud jste v systému Mac nebo Linux, musíte také odstranit položku Microsoft.Developer.IdentityService z úložiště klíčů pro váš operační systém. Na počítači Mac je úložiště klíčů aplikace "Gnome Keychain". Pro Linux aplikace obvykle nazývá "Klíčů", ale název mohou lišit v závislosti na vaší distribuci.

### <a name="conditional-access"></a>Podmíněný přístup

Podmíněný přístup není podporovaný, když Průzkumníka služby Storage se používá v systému Windows 10, Linuxu nebo macOS. Toto je kvůli omezení v knihovně AAD používané Průzkumníka služby Storage.

## <a name="mac-keychain-errors"></a>Chyby klíčenku Mac.

MacOS řetězce klíčů můžete získat někdy do stavu, která způsobuje problémy pro knihovnu ověřování Průzkumník úložišť. Získat řetězec klíčů z tohoto stavu, zkuste následující kroky:

1. Zavřete Průzkumníka služby Storage.
2. Otevřít řetězce klíčů (**cmd + MEZERNÍK**, zadejte do řetězce klíčů, stiskněte enter).
3. Vyberte keychain "login".
4. Klepněte na ikonu zámku uzamknout řetězce klíčů (visacího zámku nezobrazuje se animace k poloze po jeho dokončení že může trvat několik sekund, v závislosti na tom, co aplikace, které je nutné otevřít).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Spusťte Průzkumníka služby Storage.
6. Automaticky otevírané okno by se zobrazit o tom, že něco jako "centra služeb požaduje přístup řetězce klíčů". Když ho, zadejte své heslo účtu správce Mac a klikněte na tlačítko **vždy povolit** (nebo **povolit** Pokud **vždy povolit** není k dispozici).
7. Zkuste se přihlásit.

### <a name="general-sign-in-troubleshooting-steps"></a>Obecné přihlášení kroky řešení potíží

* Pokud jste v systému macOS a selhání "čeká se na ověřování..." se nikdy objeví okno přihlášení Dialogové okno, opakujte [tyto kroky](#mac-keychain-errors)
* Restartování Storage Exploreru
* Pokud je prázdné okno ověřování, počkejte aspoň jednu minutu před jeho zavřením dialogu ověřování.
* Ujistěte se, že nastavení proxy serveru a certifikátů pro počítače a Průzkumníka služby Storage jsou správně nakonfigurované nastavení.
* Pokud jste na Windows a mají přístup k Visual Studio 2019 na stejném počítači a přihlášení, zkuste se přihlásit k aplikaci Visual Studio 2019. Po úspěšné přihlašování ve službě Visual Studio 2019 můžete otevřít Průzkumníka služby Storage a zobrazí váš účet v panel účtu.

Pokud nefunguje žádný z těchto metod [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Chybějící předplatná a nefunkční tenantů

Pokud nemůžete načíst vaše předplatná po úspěšném přihlášení, zkuste následující metody pro řešení potíží:

* Ověřte, že váš účet má přístup k předplatným, které očekáváte. Můžete ověřit váš přístup při přihlášení k portálu pro prostředí Azure, které zkoušíte použít.
* Ujistěte se, že jste přihlášení pomocí Azure správné prostředí (Azure, Azure China 21Vianet, Azure Germany, Azure US Government nebo vlastní prostředí).
* Pokud jste za proxy serverem, ujistěte se, že jste jste správně nakonfigurovali proxy Průzkumníka služby Storage.
* Zkuste odebrat a znovu přidat účet.
* Pokud je odkaz "Další informace", podívejte se a naleznete v tématu co chybové zprávy jsou hlášena pro klienty, které se nedaří. Pokud you'ren't opravdu co dělat s chybou můžete zprávy, najdete v tématu a potom bez obav [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-attached-account-or-storage-resource"></a>Nelze odebrat připojený účet nebo úložiště prostředků

Pokud nemůžete odebrat účet připojené nebo prostředek úložiště přes uživatelské rozhraní, můžete ručně odstranit všechny připojené prostředky tak, že odstraníte následující složky:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> Zavřete Průzkumníka služby Storage před odstraněním výše uvedených složek.

> [!NOTE]
> Pokud jste dříve naimportovali jakékoli certifikáty SSL pak zálohovat obsah `certs` adresáře. Později můžete použít zálohování opětovné importování certifikátů SSL.

## <a name="proxy-issues"></a>Problémy s proxy

Nejprve se ujistěte, že jsou správně následující informace, které jste zadali:

* Adresa URL proxy serveru a číslo portu
* Uživatelské jméno a heslo v případě potřeby proxy serverem

> [!NOTE]
> Průzkumník služby Storage nepodporuje soubory automatické konfigurace proxy serveru pro konfiguraci nastavení proxy serveru.

### <a name="common-solutions"></a>Běžná řešení

Pokud máte pořád problémy, zkuste následující metody pro řešení potíží:

* Pokud se můžete připojit k Internetu bez použití vašeho proxy serveru, ověřte, zda Průzkumníka služby Storage funguje bez nastavení proxy serveru povolené. Pokud je to tento případ, může být problém se vaše nastavení proxy serveru. Práce se správcem vaší proxy serveru a identifikovat problémy.
* Ověřte, že dalších aplikací se proxy server fungovat podle očekávání.
* Ověřte, že se můžete připojit k portálu pro prostředí Azure, které se snažíte použít
* Ověřte, zda se zobrazila odpovědi z vašich koncových bodů služby. Zadejte jednu z vašeho koncového bodu adresy URL do prohlížeče. Pokud se můžete připojit, byste měli obdržet k InvalidQueryParameterValue nebo podobné odpovědi ve formátu XML.
* Pokud někdo jiný používá také Průzkumníka služby Storage s proxy serverem, ověřte, že se můžou připojit. Pokud se mohou připojit, bude pravděpodobně nutné kontaktovat správce proxy serveru.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, jako je například Fiddleru pro Windows, můžete diagnostikovat problémy následujícím způsobem:

* Pokud máte pro seznámení se základními váš proxy server, budete muset nakonfigurovat nástroj síťové připojení přes proxy server.
* Zkontrolujte číslo portu používané nástrojem vaší sítě.
* Zadejte adresu URL místního hostitele a číslo portu sítě nástroje jako nastavení proxy serveru v Průzkumníku služby Storage. Pokud jsou správně provedené, síťové nástroj spustí protokolování síťových požadavků provedených Průzkumníka služby Storage pro správu a koncových bodů služby. Zadejte například https://cawablobgrs.blob.core.windows.net/ pro koncový bod služby blob, a to v prohlížeči kde se zobrazí odpověď vypadá podobně jako následující text, který naznačuje, že prostředky existují, i když jste nejde získat přístup.

![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Obraťte se na správce serveru proxy

Pokud vaše nastavení proxy serveru jsou správné, bude pravděpodobně nutné obraťte se na správce serveru proxy, a

* Ujistěte se, že váš proxy server nebrání v provozu do koncových bodů Azure management nebo prostředek.
* Zkontrolujte protokol ověřování používá proxy server. Průzkumník služby Storage v současné době nepodporuje proxy servery protokolu NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva "Nelze načíst podřízené položky"

Pokud jste připojeni k Azure prostřednictvím proxy serveru, ověřte správnost nastavení serveru proxy. Pokud jste udělen přístup k prostředku od vlastníka předplatného nebo účtu, zkontrolujte, zda si přečetl(a) nebo seznamu oprávnění pro daný prostředek.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Připojovací řetězec nebude mít úplný nastavení konfigurace

Pokud se zobrazí tato chybová zpráva, je možné, že nemáte potřebná oprávnění k získání klíčů účtu úložiště. Pokud chcete potvrdit, pokud se jedná o tento případ, přejděte na portál a vyhledejte svůj účet úložiště. Můžete rychle to provedete kliknutím pravým tlačítkem na uzel účtu úložiště a kliknutím na "Otevřít v portál". Až to uděláte, přejděte do okna "Přístupových klíčů". Pokud nemáte oprávnění k zobrazení klíče, pak se zobrazí stránka se zprávou "Nemáte přístupová oprávnění". Chcete-li tento problém obejít, můžete získat klíče účtu z někdo jiný a připojit se název a klíč, nebo můžete požádat jiného pro SAS účtu úložiště a použít ho k připojení k účtu úložiště.

Pokud se zobrazí klíče účtu, založte problém na Githubu, abychom mohli pomoct problém vyřešit.

## <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS

Pokud se připojujete ke službě pomocí adresy URL SAS a dochází k této chybě:

* Ověřte, že adresa URL obsahuje potřebná oprávnění ke čtení nebo Vypíše prostředky.
* Ověřte, že nevypršela platnost adresy URL.
* Pokud zásadu přístupu podle adres URL SAS, ověřte nebyl odvolaný zásady přístupu.

Pokud jste omylem připojen pomocí neplatné adresy URL SAS a nelze odpojit, postupujte podle těchto kroků:

1. Když spustíte Průzkumníka služby Storage, stisknutím klávesy F12 otevřete okno vývojářských nástrojů.
2. Klikněte na kartě aplikace a pak klikněte na místní úložiště > file:// ve stromu na levé straně.
3. Najdete klíč přidružený k typu služby problematické identifikátoru URI SAS. Například pokud chybný identifikátor URI SAS pro kontejner objektů blob, vyhledejte klíč s názvem `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4. Hodnota klíče musí být pole JSON. Najít objekt přidružený k chybný identifikátor URI a jeho odebrání.
5. Stisknutím klávesy Ctrl + R znovu načíst Průzkumníka služby Storage.

## <a name="linux-dependencies"></a>Závislosti pro Linux

Obecně tyto balíčky jsou potřeba ke spouštění Průzkumníka služby Storage v Linuxu:

* [.NET core 2.0 Runtime](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) Poznámka: Průzkumník služby Storage verze 1.7.0 a dříve vyžadují rozhraní .NET Core 2.0. Pokud máte novější verzi .NET Core instalaci je potřeba opravit Průzkumníka služby Storage (viz níže). Pokud používáte Průzkumníka služby Storage 1.8.0 nebo větší potom byste měli moct využít .NET Core 2.2. Systémy novější než 2.2 nebyly ověřeny pro práci v tuto chvíli.
* `libgnome-keyring-common` a `libgnome-keyring-dev`
* `libgconf-2-4`

V závislosti na vaší distribuci, mohou používat různé nebo více balíčků, je potřeba nainstalovat.

Na Ubuntu 18.04, 16.04 a 14.04 se oficiálně podporuje Průzkumník služby Storage. Postup instalace pro vyčištění počítače jsou následující:

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Stáhněte si Průzkumníka služby Storage
2. Nainstalovat modul Runtime .NET Core, je aktuální ověřené verze: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) (pokud už máte nainstalovanou novější verzi, budete muset opravit Průzkumníka služby Storage, najdete níže)
3. Spustit `sudo apt-get install libgconf-2-4`
4. Spustit `sudo apt install libgnome-keyring-common libgnome-keyring-dev`

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Stáhněte si Průzkumníka služby Storage
2. Nainstalovat modul Runtime .NET Core, je aktuální ověřené verze: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) (pokud už máte nainstalovanou novější verzi, budete muset opravit Průzkumníka služby Storage, najdete níže)
3. Spustit `sudo apt install libgnome-keyring-dev`

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Stáhněte si Průzkumníka služby Storage
2. Nainstalovat modul Runtime .NET Core, je aktuální ověřené verze: [2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) (pokud už máte nainstalovanou novější verzi, budete muset opravit Průzkumníka služby Storage, najdete níže)
3. Spustit `sudo apt install libgnome-keyring-dev`

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Opravy Průzkumníka služby Storage pro .NET Core novější verze 
Pokud máte verzi .NET Core větší než 2.0 nainstalované a použití Průzkumníka služby Storage verze 1.7.0 nebo starší, budete pravděpodobně muset oprava Průzkumníka služby Storage podle následujících kroků:
1. Stáhněte si verzi 1.5.43 StreamJsonRpc [z nugetu](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Vyhledejte "Stáhnout" odkaz na pravé straně stránky.
2. Po stažení balíčku, změňte jeho příponu souboru z `.nupkg` do `.zip`
3. Rozbalte balíček
4. Přejděte na `streamjsonrpc.1.5.43/lib/netstandard1.1/`.
5. Kopírování `StreamJsonRpc.dll` do následujícího umístění, ve složce Průzkumníka služby Storage:
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Otevřít v Průzkumníku z webu Azure portal nebude fungovat.

Pokud pro vás nebude fungovat na tlačítko "Otevřít v Průzkumníku" na webu Azure portal, ujistěte se, že používáte kompatibilní prohlížeče. Následující prohlížeče byly testovány z hlediska kompatibility.
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Další kroky

Pokud žádná z řešení pro vás nejvhodnější, pak [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues). Také rychle na GitHub, můžete získat pomocí tlačítka "Nahlásit problém do Githubu" v levém dolním rohu.

![Zpětná vazba](./media/storage-explorer-troubleshooting/feedback-button.PNG)
