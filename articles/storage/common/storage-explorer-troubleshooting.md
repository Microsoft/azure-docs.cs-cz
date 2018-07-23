---
title: Průvodce odstraňováním potíží Azure Storage Exploreru | Dokumentace Microsoftu
description: Přehled dvou ladění funkce Azure
services: virtual-machines
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: eeb23b52d5910c3da39d29d3a9c47f598ed5fc5a
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188810"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Průvodce odstraňováním potíží Azure Storage Exploreru

Microsoft Azure Storage Explorer je samostatná aplikace, která umožňuje jednoduchá práci s daty Azure Storage ve Windows, macOS a Linuxu. Aplikace může připojit k účtům úložiště hostované v Azure, národní Cloudy a Azure Stack.

Tato příručka obsahuje souhrn řešení běžných problémů zobrazit v Průzkumníku služby Storage.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Certifikát podepsaný svým držitelem v řetězu certifikátů (a podobné chyby)

Chyby certifikátů jsou způsobeny jednu ze dvou následujících situací:

1. Aplikace je propojena prostřednictvím "transparentní proxy server", což znamená, že server (například serveru vaší společnosti) zachycuje přenosy HTTP, dešifruje je a pak je šifruje pomocí certifikátu podepsaného svým držitelem.
2. Spustíte aplikaci, která je do zpráv protokolu HTTPS, které jste dostali vkládá certifikát SSL podepsaný svým držitelem. Příkladem aplikací, které vložení certifikátů obsahuje antivirový program a síťové přenosy kontroly software.

Když Průzkumníka služby Storage uvidí podepsán svým nebo nedůvěryhodný certifikát, můžete už nebude vědět, zda byla změněna přijatou zprávu protokolu HTTPS. Pokud máte kopii certifikátu podepsaného svým držitelem, můžete dát pokyn Průzkumníka služby Storage důvěřovat provedením následujících kroků:

1. Získat s kódováním Base-64 X.509 (.cer) kopii certifikátu
2. Klikněte na tlačítko **upravit** > **certifikáty SSL** > **importovat certifikáty**a pak pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubor .cer

Tento problém může být také výsledkem několik certifikátů (kořenové a zprostředkující). Oba certifikáty musí být přidaný do překonat chyby.

Pokud si nejste jisti odkud certifikátu, můžete zkusit těchto pokynů vyhledejte ho:

1. Nainstalujte OpenSSL.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (jakákoli z odlehčených verzí by měl být dostatečná)
    * Mac a Linux: mělo by být součástí operačního systému
2. Spusťte OpenSSL.

    * Windows: otevřete instalační adresář, klikněte na tlačítko **/bin/** a potom dvakrát klikněte na panel **openssl.exe**.
    * Mac a Linux: Spusťte **openssl** z terminálu.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, které jsou podepsané svým držitelem, hledejte kdekoli předmět `("s:")` a vystavitele `("i:")` jsou stejné.
5. Po nalezení jakékoli certifikáty podepsané svým držitelem pro každé z nich, zkopírujte a vložte všechno z a to včetně **---BEGIN CERTIFICATE---** k **---END CERTIFICATE---** do nového souboru .cer.
6. Otevřete Průzkumníka služby Storage, klikněte na tlačítko **upravit** > **certifikáty SSL** > **importovat certifikáty**a pak pomocí nástroje pro výběr souborů vyhledejte, vyberte, a Otevřete soubory .cer, které jste vytvořili.

Pokud nemůžete najít žádné certifikáty podepsané svým držitelem podle předchozích kroků, kontaktujte nás prostřednictvím nástroje pro zpětnou vazbu o další pomoc. Alternativně můžete spustit z příkazového řádku pomocí Průzkumníka služby Storage `--ignore-certificate-errors` příznak. Při spuštění s tímto příznakem, Průzkumníka služby Storage se bude ignorovat chyby certifikátů.

## <a name="sign-in-issues"></a>Problémy s přihlašováním

### <a name="reauthentication-loop-or-upn-change"></a>Opětovné ověření smyčky nebo změnit hlavní název uživatele
Pokud jste se ve smyčce opětovné ověření, nebo se změnily (UPN) jednoho z vašich účtů, zkuste následující:
1. Odeberte všechny účty a potom zavřete Průzkumníka služby Storage
2. Odstranit. IdentityService složky z vašeho počítače. Na Windows, se nachází ve složce `C:\users\<username>\AppData\Local`. Pro Mac a Linux můžete najít složku v kořenovém adresáři uživatele.
3. Pokud jste v systému Mac nebo Linux, musíte také odstranit položku Microsoft.Developer.IdentityService z úložiště klíčů pro váš operační systém. Na počítači Mac je úložiště klíčů aplikace "Gnome Keychain". Pro Linux aplikace obvykle nazývá "Klíčů", ale název mohou lišit v závislosti na vaší distribuci.

## <a name="mac-keychain-errors"></a>Chyby klíčenku Mac.
MacOS řetězce klíčů můžete získat někdy do stavu, která způsobuje problémy pro knihovnu ověřování Průzkumník úložišť. Pokud chcete získat řetězce klíčů z tohoto stavu zkuste následující kroky:
1. Zavřete Průzkumníka služby Storage.
2. Otevřít řetězce klíčů (**cmd + MEZERNÍK**, zadejte do řetězce klíčů, stiskněte enter).
3. Vyberte keychain "login".
4. Klepněte na ikonu zámku uzamknout řetězce klíčů (visacího zámku nezobrazuje se animace k poloze po jeho dokončení že může trvat několik sekund, v závislosti na tom, co aplikace, které je nutné otevřít).

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Spusťte Průzkumníka služby Storage.
6. Automaticky otevírané okno by se zobrazit o tom, že něco jako "centra služeb požaduje přístup řetězce klíčů". Když ho, zadejte své heslo účtu správce Mac a klikněte na tlačítko **vždy povolit** (nebo **povolit** Pokud **vždy povolit** není k dispozici).
7. Zkuste se přihlásit.

### <a name="general-sign-in-troubleshooting-steps"></a>Obecné přihlášení kroky řešení potíží
* Pokud jsou v systému macOS a v dialogovém okně "Čeká na ověření..." se nikdy objeví okno přihlášení, zkuste [tyto kroky](#Mac-Keychain-Errors)
* Restartování Storage Exploreru
* Pokud je prázdné okno ověřování, počkejte aspoň jednu minutu před jeho zavřením dialogu ověřování.
* Ujistěte se, že nastavení proxy serveru a certifikátů pro počítače a Průzkumníka služby Storage jsou správně nakonfigurované nastavení.
* Pokud jste na Windows a mají přístup k sadě Visual Studio 2017 na stejném počítači a přihlášení, zkuste se přihlásit do sady Visual Studio 2017. Po úspěšném přihlášení do sady Visual Studio 2017 byste měli moct otevírat Průzkumníka služby Storage a zobrazí váš účet v panel účtu. 

Pokud nefunguje žádný z těchto metod [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

### <a name="missing-subscriptions-and-broken-tenants"></a>Chybějící předplatná a nefunkční tenantů

Pokud se vám nedaří načíst vaše předplatná po úspěšném přihlášení, zkuste následující metody pro řešení potíží:

* Ověřte, že váš účet má přístup k předplatným, které očekáváte. Můžete ověřit, že máte přístup při přihlášení k portálu pro prostředí Azure, které se pokoušíte použít.
* Ujistěte se, že jste přihlášení pod správnou Azure prostředí (Azure, Azure China, Azure Germany, Azure US Government nebo vlastní prostředí).
* Pokud jste za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy Průzkumníka služby Storage.
* Zkuste odebrat a znovu přidat účet.
* Pokud je odkaz "Další informace", podívejte se a naleznete v tématu co chybové zprávy jsou hlášena pro klienty, které se nedaří. Pokud si nejste jisti co dělat s chybou zprávy najdete v tématu a potom bez obav [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Nelze odebrat připojený účet nebo úložiště prostředků

Pokud nemůžete odebrat účet připojené nebo prostředek úložiště přes uživatelské rozhraní, můžete ručně odstranit všechny připojené prostředky tak, že odstraníte následující složky:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Zavřete Průzkumníka služby Storage před odstraněním výše uvedených složek.

> [!NOTE]
>  Pokud jste dříve naimportovali jakékoli certifikáty SSL pak zálohovat obsah `certs` adresáře. Později můžete použít zálohování opětovné importování certifikátů SSL.

## <a name="proxy-issues"></a>Problémy s proxy

Nejprve se ujistěte, že jsou správně následující informace, které jste zadali:

* Adresa URL proxy serveru a číslo portu
* Uživatelské jméno a heslo v případě potřeby proxy serverem

### <a name="common-solutions"></a>Běžná řešení

Pokud stále dochází k problémům, zkuste následující metody pro řešení potíží:

* Pokud se můžete připojit k Internetu bez použití vašeho proxy serveru, ověřte, zda Průzkumníka služby Storage funguje bez nastavení proxy serveru povolené. Pokud je to tento případ, může být problém se vaše nastavení proxy serveru. Práce se správcem vaší proxy serveru a identifikovat problémy.
* Ověřte, že dalších aplikací se proxy server fungovat podle očekávání.
* Ověřte, že se můžete připojit k portálu pro prostředí Azure, které se pokoušíte použít
* Ověřte, zda se zobrazila odpovědi z vašich koncových bodů služby. Zadejte jednu z vašeho koncového bodu adresy URL do prohlížeče. Pokud se můžete připojit, byste měli obdržet k InvalidQueryParameterValue nebo podobné odpovědi ve formátu XML.
* Pokud někdo jiný používá také Průzkumníka služby Storage s proxy serverem, ověřte, že se můžou připojit. Pokud se mohou připojit, bude pravděpodobně nutné kontaktovat správce proxy serveru.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, jako je například Fiddleru pro Windows, je možné, že k diagnostice problémů následujícím způsobem:

* Pokud máte pro seznámení se základními váš proxy server, budete muset nakonfigurovat nástroj síťové připojení přes proxy server.
* Zkontrolujte číslo portu používané nástrojem vaší sítě.
* Zadejte adresu URL místního hostitele a číslo portu sítě nástroje jako nastavení proxy serveru v Průzkumníku služby Storage. Pokud jsou správně provedené, síťové nástroj spustí protokolování síťových požadavků provedených Průzkumníka služby Storage pro správu a koncových bodů služby. Zadejte například https://cawablobgrs.blob.core.windows.net/ pro koncový bod služby blob, a to v prohlížeči kde se zobrazí odpověď vypadá podobně jako následující text, který naznačuje, že prostředky existují, i když jste nejde získat přístup.

![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Obraťte se na správce serveru proxy

Pokud vaše nastavení proxy serveru jsou správné, bude pravděpodobně nutné obraťte se na správce serveru proxy, a

* Ujistěte se, že váš proxy server nedochází k blokování provozu do koncových bodů Azure management nebo prostředek.
* Zkontrolujte protokol ověřování používá proxy server. Průzkumník služby Storage v současné době nepodporuje proxy servery protokolu NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva "Nelze načíst podřízené položky"

Pokud jste připojení k Azure prostřednictvím proxy serveru, ověřte správnost nastavení serveru proxy. Pokud od vlastníka předplatného nebo účtu byly udělen přístup k prostředku, zkontrolujte, zda si přečetl(a) nebo seznamu oprávnění pro daný prostředek.

## <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS
Pokud se připojujete ke službě pomocí adresy URL SAS a dochází k této chybě:

* Ověřte, že adresa URL obsahuje potřebná oprávnění ke čtení nebo Vypíše prostředky.
* Ověřte, že nevypršela platnost adresy URL.
* Pokud zásadu přístupu podle adres URL SAS, ověřte nebyl odvolaný zásady přístupu.

Pokud jste omylem připojen pomocí neplatné adresy URL SAS a nelze odpojit, postupujte podle těchto kroků:
1.  Když spustíte Průzkumníka služby Storage, stisknutím klávesy F12 otevřete okno vývojářských nástrojů.
2.  Klikněte na kartě aplikace a pak klikněte na místní úložiště > file:// ve stromu na levé straně.
3.  Najdete klíč přidružený k typu služby problematické identifikátoru URI SAS. Například pokud chybný identifikátor URI SAS pro kontejner objektů blob, vyhledejte klíč s názvem `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Hodnota klíče musí být pole JSON. Najít objekt přidružený k chybný identifikátor URI a jeho odebrání.
5.  Stisknutím klávesy Ctrl + R znovu načíst Průzkumníka služby Storage.

## <a name="linux-dependencies"></a>Závislosti pro Linux

Pro distribuce Linuxu než Ubuntu 16.04 budete muset ručně nainstalovat některé závislosti. Obecně platí vyžadují se následující balíčky:
* [.NET core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Aktuální GCC

V závislosti na vaší distribuce může být ostatní balíčky, které je potřeba nainstalovat. Storage Explorer [zpráva k vydání verze](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) obsahují konkrétní kroky pro některé distribuce.

## <a name="next-steps"></a>Další postup

Pokud žádná z řešení pro vás nejvhodnější, pak [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues). Také rychle na GitHub, můžete získat pomocí tlačítka "Nahlásit problém do Githubu" v levém dolním rohu.

![Váš názor](./media/storage-explorer-troubleshooting/feedback-button.PNG)
