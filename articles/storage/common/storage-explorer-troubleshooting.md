---
title: Azure Storage Explorer Průvodci odstraňováním potíží | Microsoft Docs
description: Přehled dvou ladění funkcí Azure
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
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: f58fb5090aba3c5052d1bbdec76225d0ae50e8f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer Průvodci odstraňováním potíží

Microsoft Azure Storage Explorer je samostatná aplikace, která umožňuje snadno pracovat s daty Azure Storage ve Windows, systému macOS a Linux. Aplikace můžete připojit k účtům úložiště, které jsou hostované v Azure, národních Cloudů a zásobník Azure.

Tato příručka obsahuje souhrn řešení pro běžné problémy, které jsou vidět ve Storage Exploreru.

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>Chyba: Certifikát podepsaný svým držitelem v řetězu certifikátů (a podobně jako chyby)

Certifikát chyby jsou způsobeny jednu ze dvou následujících situací:

1. Aplikace je připojený prostřednictvím "transparentní proxy", což znamená, brání komunikaci přes protokol HTTPS, dešifrování ho a pak šifrování pomocí certifikát podepsaný svým držitelem serveru (například serveru vaší společnosti).
2. Používáte aplikaci, která je certifikát podepsaný svým držitelem SSL vložení do zpráv protokolu HTTPS, které jste dostali. Příkladem aplikací, které vložit certifikáty zahrnuje antivirový a síťový provoz kontroly softwaru.

Když Storage Explorer uvidí svým podepsané nebo nedůvěryhodný certifikát, můžete již nebude vědět, zda byla změněna přijatou zprávu protokolu HTTPS. Pokud máte kopie certifikátu podepsaného svým držitelem, můžete v programu Průzkumník úložišť důvěřovat provedením následujících kroků:

1. Získat X.509 (.cer) kopii certifikátu kódováním Base-64
2. Klikněte na tlačítko **upravit** > **certifikáty SSL** > **importu certifikátů**a potom pomocí nástroje pro výběr souborů najít, vyberte a otevřete soubor .cer

Tento problém může být také výsledek několik certifikátů (kořenové a zprostředkující). Oba certifikáty je nutné přidat k překonání chyba.

Pokud si nejste jisti odkud pocházejí certifikát, zkuste ji najít těchto kroků:

1. Nainstalujte OpenSSL.

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (jakékoli světla verze by mělo být dostatečné)
    * Mac a Linux: by měly být zahrnuty s operačním systémem
2. Spusťte OpenSSL.

    * Windows: otevřete instalační adresář, klikněte na **/bin/** a potom dvakrát klikněte na **openssl.exe**.
    * Mac a Linux: Spusťte **openssl** z terminálu.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jistí, které jsou podepsané svým držitelem, Hledat kdekoli předmět `("s:")` a vystavitele `("i:")` jsou stejné.
5. Po nalezení všechny certifikáty podepsané svým držitelem pro každé z nich, zkopírujte a vložte všechno z a to včetně **---BEGIN CERTIFICATE---** k **---END CERTIFICATE---** do nového souboru .cer.
6. Otevřete Storage Explorer, klikněte na **upravit** > **certifikáty SSL** > **importu certifikátů**a potom pomocí nástroje pro výběr souborů najít, vyberte a otevřete .cer soubory, které jste vytvořili.

Pokud nenajdete žádné certifikáty podepsané svým držitelem pomocí předchozího postupu, kontaktujte nás pomocí nástroje zpětnou vazbu o další pomoc. Alternativně můžete spustit z příkazového řádku s Storage Explorer `--ignore-certificate-errors` příznak. Při spuštění se tento příznak bude ignorovat Storage Explorer chyby certifikátu.

## <a name="sign-in-issues"></a>Problémy s přihlášením

Pokud se nemůžete přihlásit, vyzkoušejte následující postupy řešení potíží:

* Restartujte Průzkumníka úložiště
* Pokud je prázdné okno ověřování, počkejte alespoň jednu minutu před zavření dialogového okna ověřování.
* Zajistěte, aby nastavení proxy serveru a certifikátů, že jsou správně nakonfigurovaná nastavení pro počítač a Storage Explorer
* Pokud jsou v systému Windows a mít přístup k Visual Studio 2017 na stejném počítači a přihlášení, zkuste se přihlásit k Visual Studio 2017

Pokud žádná z těchto metod fungovat [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="unable-to-retrieve-subscriptions"></a>Nelze načíst předplatná

Pokud jste se nepodařilo načíst vašich předplatných, po úspěšném přihlášení, vyzkoušejte následující postupy řešení potíží:

* Ověřte, zda má váš účet přístup k odběry, které očekáváte. Můžete ověřit, zda máte přístup po přihlášení k portálu pro Azure prostředí, které se pokoušíte použít.
* Ujistěte se, že jste se zaregistrovali pomocí správné Azure prostředí (Azure, Azure China, Azure v Německu, Azure US Government nebo vlastní prostředí).
* Pokud se nacházíte za proxy, ujistěte se, že jste nakonfigurovali proxy Storage Explorer správně.
* Zkuste odebrat a nové přidání účtu.
* Podívejte se na konzolu nástroje pro vývojáře (Nápověda > přepnutí Developer Tools) při načítání odběry Storage Explorer. Zprávy (červený text), nebo jakékoli zprávy obsahující text "Nepodařilo se načíst předplatná pro klienta." Pokud se zobrazí všechny zprávy týkající se [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="cannot-remove-attached-account-or-storage-resource"></a>Nelze odebrat připojené účet nebo úložiště prostředků

Pokud není možné odebrat účet připojené nebo prostředků úložiště prostřednictvím uživatelského rozhraní, můžete ručně odstranit všechny prostředky pro připojené odstraněním následující složky:

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  Storage Explorer zavřete před odstraněním výše složky.

> [!NOTE]
>  Pokud někdy importovali všech certifikátů protokolu SSL, pak zálohování obsah `certs` adresáře. Později můžete zálohování znovu importovat certifikáty SSL.

## <a name="proxy-issues"></a>Problémy s proxy

První zajistěte, aby byla následující informace, které jste zadali správně:

* Adresu URL proxy serveru a číslo portu * uživatelské jméno a heslo, pokud to vyžaduje proxy server

### <a name="common-solutions"></a>Běžná řešení

Pokud stále dochází k problémům, vyzkoušejte následující postupy řešení potíží:

* Pokud se můžete připojit k Internetu bez použití proxy, ověřte, že Storage Explorer funguje bez povolené nastavení proxy serveru. Pokud je to tento případ, může být problém se vaše nastavení proxy serveru. Práce se na správce serveru proxy a identifikovat problémy.
* Ověřte, že jiných aplikací pomocí proxy serveru fungovat podle očekávání.
* Ověřte, zda se můžete připojit k portálu pro Azure prostředí, které se pokoušíte použít
* Ověřte, zda se zobrazila odpovědí z koncových bodů služby. Zadejte jednu z váš koncový bod adresy URL do prohlížeče. Pokud se můžete připojit, měli byste obdržet InvalidQueryParameterValue nebo podobné odpovědi ve formátu XML.
* Pokud někdo jiný používá také Storage Explorer proxy serveru, ověřte, zda se můžete připojit. Pokud se můžete připojit, možná budete muset obrátit na správce serveru proxy.

### <a name="tools-for-diagnosing-issues"></a>Nástroje pro diagnostiku problémů

Pokud máte síťové nástroje, například aplikaci Fiddler pro Windows, bude pravděpodobně možné diagnostikovat problémy následujícím způsobem:

* Pokud máte fungovat prostřednictvím proxy, možná budete muset nakonfigurovat vaše síťové nástroje pro připojení prostřednictvím proxy serveru.
* Zkontrolujte číslo portu používané nástrojem pro vaší sítě.
* Zadejte adresu URL místního hostitele a číslo portu sítě nástroj jako nastavení proxy serveru v Storage Explorer. Pokud jsou správně provedené, vaše síťové nástroje spustí se protokolování síťové požadavky provedené Průzkumník úložišť pro koncové body služby a správu. Zadejte například https://cawablobgrs.blob.core.windows.net/ pro koncový bod služby blob v prohlížeči a obdrží odpověď se podobá následující text, který naznačuje prostředek existuje, i když nelze k němu přístup.

![Ukázka kódu](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Obraťte se na správce serveru proxy

Pokud vaše nastavení proxy serveru jsou správné, možná budete muset obrátit na správce serveru proxy a

* Ujistěte se, že proxy neblokovala přenosy na koncové body Azure pro správu nebo prostředků.
* Zkontrolujte protokol ověřování používá proxy server. Storage Explorer v současné době nepodporuje proxy protokolu NTLM.

## <a name="unable-to-retrieve-children-error-message"></a>Chybová zpráva "Nelze načíst, děti"

Pokud jste připojeni k Azure prostřednictvím proxy serveru, ověřte správnost nastavení proxy serveru. Pokud byly udělen přístup k prostředku z vlastník předplatného nebo účet, ověřte, zda přečetli nebo seznamu oprávnění pro tento prostředek.

### <a name="issues-with-sas-url"></a>Problémy s adresou URL SAS
Pokud se připojujete ke službě pomocí adresy URL SAS a hlásí tuto chybu:

* Ověřte, že adresa URL poskytuje potřebná oprávnění ke čtení nebo seznamu prostředků.
* Ověřte, zda nevypršela platnost adresu URL.
* Pokud SAS adresa URL je založená na zásadách přístupu, ověřte, že nebyl odvolaný zásady přístupu.

Pokud jste omylem připojené pomocí neplatná adresa URL SAS a se nepodařilo odpojit, postupujte takto:
1.  Při spuštění Průzkumníka úložiště, stiskněte klávesu F12 a otevřete okno nástroje pro vývojáře.
2.  Klikněte na kartu aplikace a pak klikněte na místní úložiště > file:// ve stromu na levé straně.
3.  Najít klíč přidružený k typu služby problematické identifikátoru URI SAS. Například pokud chybný identifikátor URI pro SAS pro kontejner objektů blob, vyhledejte klíč s názvem `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Hodnota klíče musí být pole JSON. Vyhledejte objekt přidružený chybný identifikátor URI a jeho odebrání.
5.  Stisknutím kombinace kláves Ctrl + R načtením Storage Explorer.

## <a name="linux-dependencies"></a>Linux závislosti

Pro distribucích systému Linux než Ubuntu 16.04 musíte ručně nainstalovat několik závislostí. Obecně platí vyžadují se následující balíčky:
* [.NET pro základní 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* Aktuální RSZ

V závislosti na vaší distro může být jiné balíčky, které potřebujete k instalaci. Storage Explorer [poznámky k verzi](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) obsahují konkrétní kroky pro některých distribucích.

## <a name="next-steps"></a>Další postup

Pokud žádná z řešení fungovat, pak [otevřete problém na Githubu](https://github.com/Microsoft/AzureStorageExplorer/issues). Na Githubu můžete také rychle získat pomocí tlačítko "Sestavy problém Githubu" v levém dolním rohu.

![Váš názor](./media/storage-explorer-troubleshooting/feedback-button.PNG)
