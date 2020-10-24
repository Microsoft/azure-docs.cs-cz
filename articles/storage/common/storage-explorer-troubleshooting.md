---
title: Průvodce odstraňováním potíží s Průzkumník služby Azure Storage | Microsoft Docs
description: Přehled technik ladění pro Průzkumník služby Azure Storage
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 83b71d46c3d6b1612728b2bd81c6acede6d0559b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488618"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Průvodce odstraňováním potíží s Průzkumníkem služby Azure Storage

Průzkumník služby Azure Storage je samostatná aplikace, která usnadňuje práci s daty ve službě Azure Storage v operačních systémech Windows, macOS a Linux. Tato aplikace se může připojit k účtům úložiště hostovaným v Azure, národních cloudech nebo ve službě Azure Stack.

Tato příručka shrnuje řešení problémů, které se běžně zobrazují v Průzkumník služby Storage.

## <a name="azure-rbac-permissions-issues"></a>Problémy s oprávněními Azure RBAC

Řízení přístupu na základě role Azure [RBAC Azure](/azure/role-based-access-control/overview) umožňuje vysoce detailní správu přístupu prostředků Azure tím, že kombinuje sady oprávnění s _rolemi_. Tady jsou některé strategie pro optimální fungování Azure RBAC v Průzkumník služby Storage.

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Návody přistupovat k prostředkům v Průzkumník služby Storage?

Pokud máte problémy s přístupem k prostředkům úložiště prostřednictvím Azure RBAC, možná nebudete mít přiřazeny příslušné role. Následující části popisují oprávnění Průzkumník služby Storage aktuálně vyžadují přístup k prostředkům úložiště. Pokud si nejste jistí, že máte příslušné role nebo oprávnění, obraťte se na správce účtu Azure.

#### <a name="read-listget-storage-accounts-permissions-issue"></a>Problém s oprávněním pro čtení: vypsat/získat účty úložiště

Musíte mít oprávnění k vypsání účtů úložiště. Chcete-li získat toto oprávnění, musíte být přiřazeni k roli _Čtenář_ .

#### <a name="list-storage-account-keys"></a>Výpis klíčů účtu úložiště

Průzkumník služby Storage můžou k ověřování požadavků používat taky klíče účtu. Přístup k klíčům účtu můžete získat pomocí výkonnějších rolí, jako je například role _Přispěvatel_ .

> [!NOTE]
> Přístupové klíče udělují neomezená oprávnění komukoli, kdo je drží. Proto nedoporučujeme tyto klíče pro uživatele účtů vysílat. Pokud potřebujete odvolat přístupové klíče, můžete je znovu vygenerovat z [Azure Portal](https://portal.azure.com/).

#### <a name="data-roles"></a>Role dat

Musíte mít přiřazenou aspoň jednu roli, která uděluje přístup ke čtení dat z prostředků. Pokud například chcete zobrazit nebo stáhnout objekty blob, budete potřebovat alespoň roli _čtečka dat objektů BLOB úložiště_ .

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Proč potřebuji roli vrstvy správy, aby se zobrazily moje prostředky v Průzkumník služby Storage?

Azure Storage má dvě vrstvy přístupu: _správu_ a _data_. K předplatným a účtům úložiště se dostanete prostřednictvím vrstvy správy. K kontejnerům, objektům blob a dalším datovým prostředkům se dostanete přes datovou vrstvu. Pokud například chcete získat seznam účtů úložiště z Azure, odešlete žádost do koncového bodu správy. Pokud chcete v účtu vytvořit seznam kontejnerů objektů blob, odešlete požadavek na příslušný koncový bod služby.

Role Azure vám můžou udělit oprávnění pro správu nebo přístup k datové vrstvě. Role čtenář například uděluje přístup jen pro čtení k prostředkům vrstvy správy.

Výhradně řečeno, role čtenářů neposkytuje žádná oprávnění k datové vrstvě a není nutná pro přístup k datové vrstvě.

Průzkumník služby Storage usnadňuje přístup k prostředkům tím, že shromáždí potřebné informace pro připojení k prostředkům Azure. Pokud například chcete zobrazit kontejnery objektů blob, Průzkumník služby Storage odešle požadavek na seznam kontejnerů do koncového bodu služby BLOB Service. Pokud chcete tento koncový bod získat, Průzkumník služby Storage prohledá seznam předplatných a účtů úložiště, ke kterým máte přístup. Chcete-li najít vaše předplatná a účty úložiště, Průzkumník služby Storage také potřebovat přístup ke vrstvě správy.

Pokud nemáte roli, která uděluje žádná oprávnění vrstvy správy, Průzkumník služby Storage nemůže získat informace potřebné pro připojení k datové vrstvě.

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>Co když mi nejde získat oprávnění pro vrstvu správy, které potřebuji od správce?

Pokud chcete získat přístup k kontejnerům nebo frontám objektů blob, můžete k těmto prostředkům připojit pomocí vašich přihlašovacích údajů Azure.

1. Otevřete dialogové okno připojit.
2. Vyberte Přidat prostředek prostřednictvím Azure Active Directory (Azure AD). Klikněte na Další.
3. Vyberte uživatelský účet a tenanta přidružený k prostředku, ke kterému se připojujete. Klikněte na Další.
4. Vyberte typ prostředku, zadejte adresu URL prostředku a zadejte jedinečný zobrazovaný název pro připojení. Klikněte na Další. Klikněte na Připojit.

U jiných typů prostředků momentálně nepoužíváme řešení související se službou Azure RBAC. Jako alternativní řešení si můžete vyžádat identifikátor URI SAS, který se [připojí k vašemu prostředku](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-shared-access-signature-uri).

### <a name="recommended-azure-built-in-roles"></a>Doporučené předdefinované role Azure

K dispozici je několik předdefinovaných rolí Azure, které mohou poskytnout oprávnění potřebná k použití Průzkumník služby Storage. Některé z těchto rolí:
- [Vlastník](/azure/role-based-access-control/built-in-roles#owner): Správa všeho, včetně přístupu k prostředkům. **Poznámka**: Tato role vám poskytne přístup k klíčům.
- [Přispěvatel](/azure/role-based-access-control/built-in-roles#contributor): umožňuje spravovat všechno, kromě přístupu k prostředkům. **Poznámka**: Tato role vám poskytne přístup k klíčům.
- [Čtenář](/azure/role-based-access-control/built-in-roles#reader): čtení a výpis prostředků
- [Přispěvatel účtu úložiště](/azure/role-based-access-control/built-in-roles#storage-account-contributor): plná Správa účtů úložiště. **Poznámka**: Tato role vám poskytne přístup k klíčům.
- [Vlastník dat objektu BLOB úložiště](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner): úplný přístup k Azure Storage kontejnerů a dat objektů BLOB.
- [Přispěvatel dat objektu BLOB služby Storage](/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor): číst, zapisovat a odstraňovat Azure Storage kontejnery a objekty blob.
- [Čtečka dat objektů BLOB služby Storage](/azure/role-based-access-control/built-in-roles#storage-blob-data-reader): čtení a výpis Azure Storage kontejnerů a objektů BLOB.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: certifikát podepsaný svým držitelem v řetězu certifikátů (a podobné chyby)

K chybám certifikátu obvykle dochází v jedné z následujících situací:

- Aplikace je propojená pomocí _transparentního proxy serveru_. To znamená, že server (například server vaší společnosti) zachycuje provoz HTTPS, dešifruje ho a pak ho šifruje pomocí certifikátu podepsaného svým držitelem.
- Máte spuštěnou aplikaci, která vkládá certifikát TLS/SSL podepsaný svým držitelem do zpráv HTTPS, které obdržíte. Mezi aplikace, které vkládají certifikáty, patří software pro kontrolu antivirového a síťového provozu.

Když Průzkumník služby Storage uvidí certifikát podepsaný svým držitelem nebo nedůvěryhodný, už nebude vědět, jestli se přijatá zpráva HTTPS změnila. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete mu dát Průzkumník služby Storage, aby mu důvěřovali pomocí následujících kroků:

1. Získejte kopii certifikátu X. 509 (. cer) s kódováním Base-64.
2. Přejít na **Upravit**  >  **certifikáty SSL**  >  **Import certifikátů**a potom pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubor. cer.

K tomuto problému může dojít také v případě, že existuje více certifikátů (root a zprostředkující). Chcete-li tuto chybu opravit, je nutné přidat oba certifikáty.

Pokud si nejste jistí, odkud certifikát pochází, postupujte podle těchto kroků a najděte ho:

1. Nainstalujte OpenSSL.
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html): všechny verze světla by měly být dostatečné.
    * Mac a Linux: měla by být součástí vašeho operačního systému.
2. Spusťte OpenSSL.
    * Windows: otevřete instalační adresář, vyberte **/bin/** a potom poklikejte na **openssl.exe**.
    * Mac a Linux: Spusťte `openssl` z terminálu.
3. Spusťte `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, které certifikáty jsou podepsané svým držitelem, poznamenejte si, jestli je předmět `("s:")` a vydavatel `("i:")` stejné.
5. Když najdete certifikáty podepsané svým držitelem, můžete pro každý z nich zkopírovat a vložit všechno z (a včetně `-----BEGIN CERTIFICATE-----` ) `-----END CERTIFICATE-----` do nového souboru. cer.
6. Otevřete Průzkumník služby Storage a pokračujte v **úpravách**  >  **certifikátů SSL**  >  **Import certifikátů**. Pak pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubory. CER, které jste vytvořili.

Pokud nemůžete najít žádné certifikáty podepsané svým držitelem pomocí následujících kroků, kontaktujte nás prostřednictvím nástroje pro zpětnou vazbu. Můžete také otevřít Průzkumník služby Storage z příkazového řádku pomocí `--ignore-certificate-errors` příznaku. Po otevření s tímto příznakem Průzkumník služby Storage ignoruje chyby certifikátu.

## <a name="sign-in-issues"></a>Problémy s přihlašováním

### <a name="blank-sign-in-dialog-box"></a>Dialogové okno prázdné přihlášení

Prázdné přihlašovací dialogová okna se nejčastěji vyskytují, když Active Directory Federation Services (AD FS) (AD FS) vyzývá Průzkumník služby Storage k provedení přesměrování, které nepodporuje elektronicky. Pokud chcete tento problém obejít, můžete zkusit použít tok kódu zařízení pro přihlášení. To můžete provést pomocí těchto kroků:

1. Na levém svislém panelu nástrojů otevřete **Nastavení**. Na panelu nastavení přejdete na přihlášení **aplikace**  >  **Sign in**. Povolit **používání přihlášení k toku kódu zařízení**
2. Otevřete dialogové okno **připojit** (buď prostřednictvím ikony plug-in na levé straně, nebo vyberte **Přidat účet** na panelu účet).
3. Vyberte prostředí, ke kterému se chcete přihlásit.
4. Vyberte **Přihlásit se**.
5. Postupujte podle pokynů na dalším panelu.

Pokud se nemůžete přihlásit k účtu, který chcete použít, protože váš výchozí prohlížeč je už přihlášený k jinému účtu, udělejte jednu z těchto akcí:

- Ručně zkopírujte odkaz a kód do privátní relace prohlížeče.
- Ručně zkopírujte odkaz a kód do jiného prohlížeče.

### <a name="reauthentication-loop-or-upn-change"></a>Smyčka opakovaného ověřování nebo změna hlavního názvu uživatele

Pokud jste ve smyčce opakovaného ověřování nebo jste změnili hlavní název uživatele (UPN) jednoho z vašich účtů, postupujte takto:

1. Odeberte všechny účty a pak Průzkumník služby Storage zavřete.
2. Odstraňte. IdentityService složku z počítače. Ve Windows se složka nachází na adrese `C:\users\<username>\AppData\Local` . V případě systémů Mac a Linux můžete složku najít v kořenovém adresáři adresáře uživatele.
3. Pokud používáte systém Mac nebo Linux, budete také muset odstranit položku Microsoft. Developer. IdentityService z úložiště klíčů operačního systému. V počítači Mac je úložiště *klíčů aplikací GNOME* . V systému Linux se aplikace obvykle nazývá _Správce klíčů_, ale název se může lišit v závislosti na vaší distribuci.

### <a name="conditional-access"></a>Podmíněný přístup

Z důvodu omezení v knihovně Azure AD, kterou používá Průzkumník služby Storage, není podmíněný přístup podporován, když se Průzkumník služby Storage používá ve Windows 10, Linux nebo macOS.

## <a name="mac-keychain-errors"></a>Chyby řetězce klíčů Mac

MacOS řetězec klíčů může někdy zadat stav, který způsobuje problémy v knihovně ověřování Průzkumník služby Storage. Chcete-li získat z tohoto stavu řetězce klíčů, postupujte následovně:

1. Zavřete Průzkumník služby Storage.
2. Otevřete řetězec klíčů (stiskněte příkaz + mezerník, zadejte **řetězec klíčů**a stiskněte klávesu ENTER).
3. Vyberte řetězec "login".
4. Vyberte ikonu visacího zámku nezobrazuje pro uzamčení řetězce klíčů. (Visacího zámku nezobrazuje se po dokončení procesu zobrazí jako zamčené. V závislosti na tom, jaké aplikace jste otevřeli, může trvat několik sekund.

    ![Ikona visacího zámku nezobrazuje](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Otevřete Průzkumníka služby Storage.
6. Zobrazí se výzva se zprávou, jako je například "centrum služeb vyžaduje přístup k řetězci klíčů." Zadejte heslo účtu správce počítače Mac a vyberte možnost **vždy povolený** (nebo **Povolte** možnost **vždy** zakázat, pokud není k dispozici).
7. Zkuste se přihlásit.

### <a name="general-sign-in-troubleshooting-steps"></a>Obecné kroky při řešení potíží s přihlašováním

* Pokud pracujete v macOS a přihlašovací okno se nikdy nezobrazuje v dialogovém okně **čekání na ověření** , zkuste [tyto kroky](#mac-keychain-errors).
* Restartujte Průzkumník služby Storage.
* Pokud je okno ověřování prázdné, před zavřením dialogového okna ověřování počkejte alespoň jednu minutu.
* Ujistěte se, že je nastavení proxy serveru a certifikátu správně nakonfigurované pro váš počítač i Průzkumník služby Storage.
* Pokud používáte systém Windows a máte přístup k aplikaci Visual Studio 2019 ve stejném počítači a přihlašovací údaje přihlášení, zkuste se přihlásit k Visual Studiu 2019. Po úspěšném přihlášení do sady Visual Studio 2019 můžete otevřít Průzkumník služby Storage a zobrazit účet na panelu účet.

Pokud žádná z těchto metod nefunguje, [otevřete problém v GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Chybějící předplatná a poškození klienti

Pokud po úspěšném přihlášení nemůžete načíst vaše předplatné, zkuste následující metody řešení potíží:

* Ověřte, že váš účet má přístup k předplatným, která očekáváte. Přístup můžete ověřit tak, že se přihlásíte k portálu pro prostředí Azure, které se pokoušíte použít.
* Ujistěte se, že jste se přihlásili přes správné prostředí Azure (Azure, Azure Čína 21Vianet, Azure Německo, Azure US státní správu nebo vlastní prostředí).
* Pokud jste za proxy server, ujistěte se, že jste správně nakonfigurovali proxy Průzkumník služby Storage.
* Zkuste účet odebrat a znovu přidat.
* Pokud existuje odkaz Další informace, zjistěte, které chybové zprávy se pro klienty, kteří selžou, nahlásí. Pokud si nejste jistí, jak reagovat na chybové zprávy, můžete [otevřít problém na GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>Nejde odebrat připojený účet nebo prostředek úložiště.

Pokud nemůžete odebrat připojený účet nebo prostředek úložiště prostřednictvím uživatelského rozhraní, můžete ručně odstranit všechny připojené prostředky odstraněním následujících složek:

* Systému `%AppData%/StorageExplorer`
* MacOS `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux `~/.config/StorageExplorer`

> [!NOTE]
> Před odstraněním těchto složek zavřete Průzkumník služby Storage.

> [!NOTE]
> Pokud jste někdy importovali nějaké certifikáty SSL, zálohujte obsah `certs` adresáře. Později můžete pomocí zálohy znovu naimportovat své certifikáty SSL.

## <a name="proxy-issues"></a>Problémy s proxy serverem

Nejprve zkontrolujte, zda jsou zadány správné následující informace:

* Adresa URL proxy serveru a číslo portu
* Uživatelské jméno a heslo, pokud je proxy vyžaduje

> [!NOTE]
> Průzkumník služby Storage nepodporuje soubory automatické konfigurace proxy serveru pro konfiguraci nastavení proxy serveru.

### <a name="common-solutions"></a>Běžná řešení

Pokud stále dochází k problémům, zkuste následující metody řešení potíží:

* Pokud se můžete připojit k Internetu bez použití proxy serveru, ověřte, že Průzkumník služby Storage funguje bez povoleného nastavení proxy serveru. V takovém případě může dojít k potížím s nastavením proxy serveru. Spolupracujte s vaším správcem a Identifikujte problémy.
* Ověřte, že další aplikace, které používají proxy server fungují podle očekávání.
* Ověřte, že se můžete připojit k portálu pro prostředí Azure, které se pokoušíte použít.
* Ověřte, že můžete přijímat odpovědi z koncových bodů služby. Do prohlížeče zadejte jednu z adres URL koncového bodu. Pokud se můžete připojit, měli byste přijmout InvalidQueryParameterValue nebo podobnou odpověď XML.
* Pokud Průzkumník služby Storage s proxy server používá i někdo jiný, ověřte, že se můžou připojit. Pokud je to možné, možná budete muset kontaktovat správce proxy server.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, například Fiddler pro Windows, můžete problémy diagnostikovat následujícím způsobem:

* Pokud potřebujete pracovat přes proxy server, bude pravděpodobně nutné nakonfigurovat síťový nástroj pro připojení prostřednictvím proxy serveru.
* Ověřte číslo portu používaného síťovým nástrojem.
* Zadejte adresu URL místního hostitele a číslo portu nástroje sítě jako nastavení proxy serveru v Průzkumník služby Storage. Když to uděláte správně, nástroj sítě spustí protokolování požadavků sítě, které provedly Průzkumník služby Storage ke správě a koncovým bodům služby. Například zadejte `https://cawablobgrs.blob.core.windows.net/` pro koncový bod objektu BLOB v prohlížeči a dostanete odpověď, která bude vypadat přibližně takto:

  ![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  Tato odpověď naznačuje, že prostředek existuje, i když k němu nemáte přístup.

### <a name="contact-proxy-server-admin"></a>Kontaktujte správce proxy server

Pokud je vaše nastavení proxy serveru správné, možná budete muset požádat správce proxy server, aby:

* Ujistěte se, že proxy neblokuje provoz do koncových bodů prostředků nebo správy Azure.
* Ověřte protokol ověřování používaný vaším proxy server. Průzkumník služby Storage v současné době nepodporuje proxy NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva Nelze načíst podřízené položky.

Pokud jste připojení k Azure prostřednictvím proxy serveru, ověřte správnost nastavení proxy serveru. Pokud jste udělili přístup k prostředku z vlastníka předplatného nebo účtu, ověřte, že máte oprávnění číst nebo zobrazit seznam pro daný prostředek.

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>Připojovací řetězec nemá kompletní konfigurační nastavení.

Pokud se zobrazí tato chybová zpráva, je možné, že nemáte potřebná oprávnění k získání klíčů pro váš účet úložiště. Pokud chcete potvrdit, že se jedná o tento případ, přejděte na portál a Najděte svůj účet úložiště. To můžete provést tak, že kliknete pravým tlačítkem na uzel svého účtu úložiště a vyberete **otevřít na portálu**. Pak přejděte do okna **přístupové klíče** . Pokud nemáte oprávnění k zobrazení klíčů, zobrazí se zpráva, že nemáte přístup. Pokud chcete tento problém obejít, můžete buď získat klíč účtu od někoho jiného a připojit se přes jméno a klíč, nebo můžete požádat někoho o SAS k účtu úložiště a použít ho k připojení účtu úložiště.

Pokud vidíte klíče účtu, založte problém do GitHubu, abychom vám mohli problém vyřešit.

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Došlo k chybě při přidávání nového připojení: TypeError: nejde číst vlastnost Version nedefinovaného.

Pokud se zobrazí tato chybová zpráva, když se pokusíte přidat vlastní připojení, může dojít k poškození dat připojení uložených v místním Správci přihlašovacích údajů. Pokud chcete tento problém obejít, zkuste odstranit vaše poškozená místní připojení a pak je znovu přidat:

1. Spusťte Průzkumník služby Storage. V nabídce přejděte na příkaz **help**  >  **Toggle vývojářské nástroje**.
2. V otevřeném okně na kartě **aplikace** přejdete do **místního úložiště** (levé straně) > **File://**.
3. V závislosti na typu připojení, se kterým máte potíže, vyhledejte jeho klíč a zkopírujte jeho hodnotu do textového editoru. Hodnota je pole vlastních názvů připojení, například následující:
    * Účty úložiště
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * Kontejnery objektů blob
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * Sdílené složky
        * `StorageExplorer_CustomConnections_Files_v1`
    * Fronty
        * `StorageExplorer_CustomConnections_Queues_v1`
    * Tabulky
        * `StorageExplorer_CustomConnections_Tables_v1`
4. Po uložení aktuálních názvů připojení nastavte hodnotu v Vývojářské nástroje na `[]` .

Pokud chcete zachovat nepoškozená připojení, můžete pomocí následujících kroků vyhledat poškozená připojení. Pokud si nejste vědomi, že ztratíte všechna existující připojení, můžete tyto kroky přeskočit a podle pokynů pro konkrétní platformu vymazat data o připojení.

1. V textovém editoru znovu přidejte název každého připojení do Vývojářské nástroje a potom zkontrolujte, zda připojení stále funguje.
2. Pokud připojení funguje správně, není poškozeno a můžete ho bezpečně opustit. Pokud připojení nefunguje, odeberte jeho hodnotu z Vývojářské nástroje a zaznamenejte si ho, abyste ho mohli později přidat zpátky.
3. Opakujte, dokud nezkontrolovali všechna připojení.

Po prozatím všech připojeních, u všech názvů připojení, která nejsou přidaná zpět, je nutné vymazat poškozená data (pokud existují) a přidat je zpět pomocí standardních kroků v Průzkumník služby Storage:

# <a name="windows"></a>[Windows](#tab/Windows)

1. V nabídce **Start** vyhledejte **Správce přihlašovacích údajů** a otevřete ho.
2. Přejít na **přihlašovací údaje systému Windows**.
3. V části **Obecné přihlašovací údaje**vyhledejte položky, které mají `<connection_type_key>/<corrupted_connection_name>` klíč (například `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
4. Odstraňte tyto položky a přidejte připojení znovu.

# <a name="macos"></a>[macOS](#tab/macOS)

1. Otevřete Spotlight (Command + mezerník) a vyhledejte **přístup k řetězci klíčů**.
2. Vyhledejte položky, které mají `<connection_type_key>/<corrupted_connection_name>` klíč (například `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Odstraňte tyto položky a přidejte připojení znovu.

# <a name="linux"></a>[Linux](#tab/Linux)

Správa místních přihlašovacích údajů se liší v závislosti na distribuci systému Linux. Pokud vaše distribuce systému Linux neposkytuje integrovaný nástroj grafického uživatelského rozhraní pro správu místních přihlašovacích údajů, můžete nainstalovat nástroj třetí strany pro správu místních přihlašovacích údajů. Můžete například použít Open source nástroj [Seahorse](https://wiki.gnome.org/Apps/Seahorse/)pro správu místních přihlašovacích údajů pro Linux.

1. Otevřete nástroj pro správu místních přihlašovacích údajů a vyhledejte uložená pověření.
2. Vyhledejte položky, které mají `<connection_type_key>/<corrupted_connection_name>` klíč (například `StorageExplorer_CustomConnections_Accounts_v1/account1` ).
3. Odstraňte tyto položky a přidejte připojení znovu.
---

Pokud k této chybě dochází i po spuštění těchto kroků, nebo pokud chcete sdílet, co máte v případě, že jsou tato připojení poškozená, [otevřete problém](https://github.com/microsoft/AzureStorageExplorer/issues) na naší stránce GitHubu.

## <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS

Pokud se připojujete ke službě prostřednictvím adresy URL SAS a dochází k chybě:

* Ověřte, že adresa URL poskytuje potřebná oprávnění ke čtení nebo vypsání prostředků.
* Ověřte, že platnost adresy URL nevypršela.
* Pokud je adresa URL SAS založená na zásadách přístupu, ověřte, že zásady přístupu nebyly odvolány.

Pokud omylem připojíte neplatnou adresu URL SAS a teď se nedá odpojit, postupujte podle těchto kroků:

1. Když spouštíte Průzkumník služby Storage, otevřete Vývojářské nástroje okně stisknutím klávesy F12.
2. Na kartě **aplikace** vyberte **místní úložiště**  >  **File://** ve stromu vlevo.
3. Vyhledejte klíč přidružený k typu služby problematického identifikátoru URI SAS. Pokud je například špatný identifikátor URI SAS pro kontejner objektů blob, vyhledejte klíč s názvem `StorageExplorer_AddStorageServiceSAS_v1_blob` .
4. Hodnota klíče by měla být pole JSON. Najděte objekt přidružený k chybnému identifikátoru URI a odstraňte ho.
5. Stisknutím kombinace kláves CTRL + R znovu načtěte Průzkumník služby Storage.

## <a name="linux-dependencies"></a>Závislosti Linux

### <a name="snap"></a>Moduly snap

Průzkumník služby Storage 1.10.0 a novější je k dispozici jako modul snap z obchodu s modulem snap-in. Modul snap-in Průzkumník služby Storage automaticky nainstaluje všechny závislosti a aktualizuje se, až bude k dispozici nová verze modulu snap-in. Instalace modulu snap-in Průzkumník služby Storage je doporučovanou metodou instalace.

Průzkumník služby Storage vyžaduje použití Správce hesel, ke kterému se možná budete muset ručně připojit, jinak bude Průzkumník služby Storage fungovat správně. Pomocí následujícího příkazu se můžete připojit Průzkumník služby Storage k vašemu systému správce hesel.

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>Soubor. tar. gz

Aplikaci můžete také stáhnout jako soubor. tar. gz, ale budete muset nainstalovat závislosti ručně.

Průzkumník služby Storage, jak je uvedeno v souborech. tar. gz, jsou podporovány pouze v následujících verzích Ubuntu. Průzkumník služby Storage mohou fungovat i na dalších distribucích systému Linux, ale nejsou oficiálně podporovány.

- Ubuntu 20,04 x64
- Ubuntu 18,04 x64
- Ubuntu 16,04 x64

Průzkumník služby Storage vyžaduje, aby bylo v systému nainstalované rozhraní .NET Core. Doporučujeme .NET Core 2,1, ale Průzkumník služby Storage budou fungovat i s 2,2.

> [!NOTE]
> Průzkumník služby Storage verze 1.7.0 a starší vyžadují .NET Core 2,0. Pokud máte nainstalovanou novější verzi .NET Core, budete muset [opravit Průzkumník služby Storage](#patching-storage-explorer-for-newer-versions-of-net-core). Pokud používáte Průzkumník služby Storage 1.8.0 nebo novější, potřebujete alespoň .NET Core 2,1.

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Stáhněte soubor Průzkumník služby Storage. tar. gz.
2. Instalace [modulu runtime .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Stáhněte soubor Průzkumník služby Storage. tar. gz.
2. Instalace [modulu runtime .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Stáhněte soubor Průzkumník služby Storage. tar. gz.
2. Instalace [modulu runtime .NET Core](/dotnet/core/install/linux):
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Mnoho knihoven potřebných v Průzkumník služby Storage přináší předinstalované standardní instalace Ubuntu v kanonickém stavu. Pro vlastní prostředí můžou chybět některé z těchto knihoven. Pokud máte problémy se spouštěním Průzkumník služby Storage, doporučujeme, abyste se ujistili, že jsou v systému nainstalované následující balíčky:

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-util

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>Oprava Průzkumník služby Storage pro novější verze .NET Core

Pro Průzkumník služby Storage 1.7.0 nebo starší možná budete muset opravit verzi rozhraní .NET Core, kterou používá Průzkumník služby Storage:

1. Stáhněte si 1.5.43 verze StreamJsonRpc [z NuGet](https://www.nuget.org/packages/StreamJsonRpc/1.5.43). Vyhledejte odkaz Stáhnout balíček na pravé straně stránky.
2. Po stažení balíčku změňte jeho příponu z `.nupkg` na `.zip` .
3. Rozbalíte balíček.
4. Otevřete `streamjsonrpc.1.5.43/lib/netstandard1.1/` složku.
5. Zkopírujte `StreamJsonRpc.dll` do následujících umístění ve složce Průzkumník služby Storage:
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Možnost "otevřít v Průzkumníkovi" z Azure Portal nefunguje

Pokud tlačítko **otevřít v Průzkumníkovi** na Azure Portal nefunguje, ujistěte se, že používáte kompatibilní prohlížeč. Následující prohlížeče byly testovány na kompatibilitu:
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>Další kroky

Pokud žádná z těchto řešení nefunguje za vás, [otevřete problém v GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues). Můžete to udělat i tak, že v levém dolním rohu vyberete tlačítko **nahlásit problém k GitHubu** .

![Váš názor](./media/storage-explorer-troubleshooting/feedback-button.PNG)
