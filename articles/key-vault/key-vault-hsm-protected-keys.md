---
title: Vygenerování a přenos klíče chráněné HSM pro Azure Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: V tomto článku použijte při plánování, generovat a potom přeneste svůj vlastní klíče chráněné HSM pro použití s Azure Key Vault. Také označovaný jako BYOK nebo přineste si vlastní klíč.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: barclayn
ms.openlocfilehash: 44c1406c8ecd8c5ff103fed4d105ecd64d16c358
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002463"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Postup generování a přenos chráněných pomocí HSM klíčů pro Azure Key Vault

Pro jistotu při použití Azure Key Vault, můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy neopustí hranice modulu hardwarového zabezpečení. Tento scénář se často označuje jako *přineste si vlastní klíč*, nebo BYOK. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Služba Azure Key Vault využívá moduly hardwarového zabezpečení Thales nShield řady chrání vaše klíče.

Pomocí informací v tomto tématu vám pomohou plánovat, generovat a potom přeneste svůj vlastní klíče chráněné HSM pro použití s Azure Key Vault.

Tato funkce není dostupná pro Azure China.

> [!NOTE]
> Další informace o službě Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-whatis.md)  
> Úvodním kurzu, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné HSM, najdete v části [Začínáme s Azure Key Vault](key-vault-get-started.md).

Další informace o generování a přenos klíče chráněného HSM přes Internet:

* Vygenerování klíče z offline pracovní stanice, což snižuje možný útok.
* Že je klíč zašifrovaný pomocí na klíč výměny klíčů (KEK), který zůstává zašifrovaný, dokud se přenese do modulů HSM služby Azure Key Vault. Opustí jenom zašifrovaná verze vašeho klíče původní pracovní stanici.
* Sada nástrojů nastaví vlastnosti pro váš klíč tenanta, který váže svůj klíč do architektury security world služby Azure Key Vault. Proto po modulů HSM služby Azure Key Vault přijmou a dešifrují váš klíč, pouze tyto moduly hardwarového zabezpečení můžete použít. Váš klíč nedá exportovat. Tuto vazbu vynucují moduly HSM společnosti Thales.
* Klíč výměny klíčů (KEK), který slouží k šifrování tohoto klíče se generuje uvnitř modulů HSM služby Azure Key Vault a není exportovatelné. Moduly hardwarového zabezpečení vynutit, aby může existovat žádná čitelná verze klíče kek mimo tyto moduly Hsm. Kromě toho sada nástrojů obsahuje záruku od společnosti Thales, že se klíč KEK nedá exportovat a byl vygenerovaný v originálním modulu HSM vyrobeným společností Thales.
* Sada nástrojů obsahuje záruku od společnosti Thales, že architektury security world služby Azure Key Vault byla vygenerovaná na originálním modulu HSM vyrobeném společností Thales. Toto potvrzení vám poskytuje důkaz, že Microsoft používá originální hardware.
* Společnost Microsoft používá Kek a samostatné sféry zabezpečení v každé geografické oblasti. Toto oddělení zajišťuje, že klíč je možné jen v datových centrech v oblasti, ve kterém jste ho zašifrovali. Například klíč od Evropského zákazníka nelze použít v datových centrech v Severní Americe nebo Asii.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Další informace o modulech HSM Thales a služeb Microsoftu

Thales e-Security je přední globální poskytovatel šifrování dat a řešení kybernetického zabezpečení z oboru finančních služeb, špičkových technologií, výroby, government a dalších technologických oborech. S 40 let osvědčily chrání firemní i vládní informace řešení společnosti Thales používají čtyři z pěti největších společností energie a leteckém průmyslu. Jejich řešení jsou také používány 22 zemí NATO a zabezpečit více než 80 procent po celém světě platebních transakcí.

Microsoft spolupracoval se společností Thales HSM špičkovou. Těmto vylepšením získáte typické výhody hostovaných služeb, aniž byste se museli vzdát kontroly nad svými klíči. Konkrétně tato vylepšení umožňují Microsoftu spravovat moduly HSM, takže není nutné k. Jako cloudová služba Azure Key Vault škálování ve vaší organizaci nárazovým zvýšením požadavků. Ve stejnou dobu je uvnitř modulů HSM Microsoftu chráněný klíč: Je zachovat kontrolu nad životním cyklem klíče, protože generování klíče a přenést ho do modulů HSM Microsoftu.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementace funkce přineste si vlastní klíč (BYOK) pro Azure Key Vault

Použijte následující informace a postupy, pokud chcete vygenerovat si vlastní klíč chráněný HSM a přenést ho do služby Azure Key Vault – přineste vlastní klíč (BYOK) scénář.

## <a name="prerequisites-for-byok"></a>Požadavky pro BYOK

Najdete v následující tabulce najdete seznam požadavků pro funkce přineste si vlastní klíč (BYOK) pro Azure Key Vault.

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |K vytvoření služby Azure Key Vault, budete potřebovat předplatné Azure: [Zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) |
| Úroveň služby Azure Key Vault Premium k podpoře klíčů chráněných pomocí HSM |Další informace o úrovních služeb a možnostech pro Azure Key Vault najdete v tématu [cenách služby Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) webu. |
| Modulu HSM společnosti Thales, čipové karty a podpůrný software |Musíte mít přístup k modulu hardwarového zabezpečení Thales a základní provozní znalosti o modulech HSM Thales. Zobrazit [modulu hardwarového zabezpečení Thales](https://www.thales-esecurity.com/msrms/buy) seznam kompatibilních modelů nebo pokud chcete zakoupit modulu hardwarového zabezpečení, pokud nemáte jednu. |
| Níže uvedený hardware a software:<ol><li>Offline x64 pracovní stanice s minimální operační systém Windows Windows 7 a Thales nShield software, který je minimálně verze 11.50.<br/><br/>Pokud tato pracovní stanice používá Windows 7, je nutné [nainstalovat rozhraní Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Pracovní stanice, která je připojená k Internetu a má minimální operační systém Windows Windows 7 a [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.7.0) **minimální verzi 1.1.0** nainstalované.</li><li>USB Flash disk nebo jiné přenosné úložné zařízení, která obsahuje aspoň 16 MB volného místa.</li></ol> |Z bezpečnostních důvodů doporučujeme, aby první pracovní stanice nebyla připojená k síti. Nicméně toto doporučení nevynucujeme prostřednictvím kódu programu.<br/><br/>V následujících pokynech pracovní stanice označuje jako odpojené pracovní stanici.</p></blockquote><br/>Kromě toho pokud váš klíč tenanta je pro produkční síť, doporučujeme použít druhou, samostatnou pracovní stanici, která pokud chcete stáhnout sadu nástrojů a odešlete klíč tenanta. Ale pro účely testování můžete použít stejný pracovní stanice jako první z nich.<br/><br/>V následujících pokynech druhá pracovní stanice označuje jako pracovní stanici připojené k Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Vygenerování a přenos klíče do služby Azure Key Vault HSM

Následujících pět kroků budete používat pro vygenerování a přenos klíče do služby Azure Key Vault HSM:

* [Krok 1: Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Příprava odpojené pracovní stanice](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Generování klíče](#step-3-generate-your-key)
* [Krok 4: Příprava klíče na přenos](#step-4-prepare-your-key-for-transfer)
* [Krok 5: Přenos vašeho klíče do služby Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Příprava pracovní stanice připojené k Internetu

Pro tento první krok proveďte následující postupy na pracovní stanici připojené k Internetu.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Instalace prostředí Azure PowerShell

Z pracovní stanice připojené k Internetu stáhněte a nainstalujte modul Azure PowerShell, který obsahuje rutiny pro správu služby Azure Key Vault. To vyžaduje minimální verzi 0.8.13.

Pokyny k instalaci, naleznete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Získejte ID vašeho předplatného Azure

Spusťte relaci Azure Powershellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

```Powershell
   Add-AzureRMAccount
```
V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Potom použijte [Get-AzureSubscription](/powershell/module/servicemanagement/azure/get-azuresubscription?view=azuresmps-3.7.0) příkaz:

```powershell
   Get-AzureRMSubscription
```
Ve výstupu vyhledejte ID pro předplatné, které budete používat pro Azure Key Vault. Toto ID předplatného budete potřebovat později.

Nezavírejte okno Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3: Stažení sady nástrojů funkce BYOK pro Azure Key Vault

Přejděte na webu Microsoft Download Center a [stáhnete sadu nástrojů pro BYOK služby Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45345) pro geografické oblasti nebo instance Azure. Následující informace použijte k určení názvu balíčku ke stažení a jeho odpovídajícího balíčku hash SHA-256:

- - -
**Spojené státy americké:**

Trezor klíčů BYOK-Tools spojené States.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Evropa:**

Trezor klíčů BYOK-Tools-Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Asie:**

Trezor klíčů BYOK-Tools-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Latinská Amerika:**

Trezor klíčů BYOK-Tools-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japonsko:**

Trezor klíčů BYOK-Tools-Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

Trezor klíčů BYOK-Tools-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Austrálie:**

Trezor klíčů BYOK-Tools-Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

Trezor klíčů BYOK-Tools-USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Státní správy USA ministerstva obrany USA:**

Trezor klíčů BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

Trezor klíčů BYOK-Tools-Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Německo:**

Trezor klíčů BYOK-Tools-Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**Indie:**

Trezor klíčů BYOK-Tools-India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Francie:**

Trezor klíčů BYOK-Tools-France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**Spojené království:**

Trezor klíčů BYOK-Tools-UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Chcete-li ověřit integritu vašeho stažené sady nástrojů funkce BYOK, z relace prostředí Azure PowerShell, použijte [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) rutiny.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Sada nástrojů obsahuje:

* Balíček klíče výměny klíčů (KEK), jehož název začíná **BYOK-KEK - pkg-.**
* Balíček architektury Security World, jehož název začíná **BYOK-SecurityWorld - pkg-.**
* Skript v jazyce python s názvem **verifykeypackage.py.**
* Spustitelný soubor příkazového řádku, s názvem **KeyTransferRemote.exe** a přidružené knihovny DLL.
* Visual C++ Redistributable Package, s názvem **vcredist_x64.exe.**

Zkopírujte balíček na USB Flash disku nebo jiného přenosného úložiště.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Příprava odpojené pracovní stanice

Tento druhý krok proveďte následující postupy na pracovní stanici, která není připojený k síti (Internetu nebo interní sítě).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Krok 2.1: Příprava odpojené pracovní stanice pomocí modulu hardwarového zabezpečení Thales

Nainstalujte do počítače Windows podpůrný software nCipher (Thales) a připojte k tomuto počítači modul Thales HSM.

Zajistěte, aby byly nástroje Thales ve své cestě (**%nfast_home%\bin**). Například zadejte následující příkaz:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Další informace najdete v uživatelské příručce dodané s modulem HSM Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2: Instalace sady nástrojů funkce BYOK na odpojenou pracovní stanici

Zkopírujte balíček sady nástrojů funkce BYOK z USB Flash disku nebo jiného přenosného úložiště a potom postupujte takto:

1. Extrahujte soubory ze staženého balíčku do libovolné složky.
2. Z této složky spusťte vcredist_x64.exe.
3. Postupujte podle pokynů k instalaci běhových součástí Visual C++ pro Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Generování klíče

Tento třetí krok proveďte následující postupy na odpojené pracovní stanici. K dokončení tohoto kroku vašeho HSM musí být v režimu inicializace. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1: Změnit režim HSM na "I"

Pokud používáte Thales nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka Režim zvýrazněte požadovaný režim. 2. Během několika sekund stiskněte a podržte tlačítko Vymazat pár sekund. Pokud se změní režim nový režim Indikátor blikat zastaví a zůstane po. Indikátor stavu může flash nepravidelně na několik sekund a pak bliká pravidelně, když zařízení je připravené. V opačném případě lit zařízení zůstává v aktuálním režimu s odpovídající Indikátor režimu.

### <a name="step-32-create-a-security-world"></a>Krok 3.2: Vytvoření architektury security world

Spusťte příkazový řádek a spusťte nový svět program společnosti Thales.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

Tento program vytvoří **architektury Security World** soubor v adresáři % NFAST_KMDATA%\local\world, který odpovídá složce C:\ProgramData\nCipher\Key Management aplikací\Místní. Pro kvorum můžete použít různé hodnoty, ale v našem příkladu zobrazí výzva, zadejte pro každé z nich tři prázdné karty a kódy PIN. Jakékoli dvě karty potom poskytnout úplný přístup do architektury security world. Tyto karty tvoří **Administrator Card Set** pro nové architektury security world.

Potom udělejte následující:

* Zálohujte soubor world. Zabezpečení a ochraně soubor world, karty správce a jejich kódy PIN a ujistěte se, že jeden člověk měl přístup k více než jedna karta.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3: Změnit režim modulu hardwarového zabezpečení ' jednoznakový

Pokud používáte Thales nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka Režim zvýrazněte požadovaný režim. 2. Během několika sekund stiskněte a podržte tlačítko Vymazat pár sekund. Pokud se změní režim nový režim Indikátor blikat zastaví a zůstane po. Indikátor stavu může flash nepravidelně na několik sekund a pak bliká pravidelně, když zařízení je připravené. V opačném případě lit zařízení zůstává v aktuálním režimu s odpovídající Indikátor režimu.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Ověření staženého balíčku

Tento krok je volitelný, ale doporučuje se, aby mohli ověřit tyto:

* Klíč pro výměnu klíčů, který je součástí sady nástrojů, byl vygenerovaný na originálním modulu HSM společnosti Thales.
* Hodnota hash architektury Security World, který je součástí sady nástrojů, byla vygenerovaná na originálním modulu HSM společnosti Thales.
* Klíč pro výměnu klíčů je není možné exportovat.

> [!NOTE]
> Ověření staženého balíčku, modul hardwarového zabezpečení musí být připojený, zapnutý a musí mít architektury security world na něm (jako je ta, kterou jste právě vytvořili).

Ověření staženého balíčku:

1. Spusťte skript verifykeypackage.py zadáním jedné z těchto možností podle toho zeměpisné oblasti nebo instance Azure:

   * Pro Severní Ameriku:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Pro Evropa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Pro Asii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Pro Latinská Amerika:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Pro Japonsko:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Pro Koreu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Pro Austrálii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci US government Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Pro státní správu USA ministerstva obrany USA:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Pro Kanadu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Pro Německo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Indii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Pro Francii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Pro Spojené království:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Software společnosti Thales obsahuje python v %NFAST_HOME%\python\bin
     >
     >
2. Zkontrolujte, jestli vidíte následující text, který znamená úspěšné ověření: **Výsledek: ÚSPĚCH**

Tento skript ověřuje řetězec podepisujících až ke kořenovému klíči Thales. Hodnota hash tohoto kořenového klíče je vložená ve skriptu a jeho hodnota by měla být **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Tuto hodnotu můžete potvrdit samostatně přechodem [webu společnosti Thales](http://www.thalesesec.com/).

Nyní jste připraveni vytvořit nový klíč.

### <a name="step-35-create-a-new-key"></a>Krok 3.5: Vytvořit nový klíč

Vygenerujte klíč pomocí společnosti Thales **generatekey** programu.

Spusťte následující příkaz k vygenerování klíče:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Při spuštění tohoto příkazu použijte tyto pokyny:

* Parametr *chránit* musí být nastavena na hodnotu **modulu**, jak je znázorněno. Tím se vytvoří klíč chráněný modulem. Sada nástrojů pro BYOK nepodporuje klíče chráněné OCS.
* Nahraďte hodnotu *contosokey* pro **ident** a **plainname** s libovolnou hodnotou řetězce. Chcete-li minimalizovat správní režie a snížilo riziko chyby, doporučujeme použít stejnou hodnotu pro oba. **Ident** hodnota musí obsahovat pouze číslice, pomlčky a malá písmena.
* Parametr pubexp je prázdný (výchozí nastavení) v tomto příkladu, ale můžete zadat konkrétní hodnoty. Další informace najdete v dokumentaci společnosti Thales.

Tento příkaz vytvoří soubor Tokenizovaného klíče ve složce %NFAST_KMDATA%\local s názvem začínajícím textem **key_simple_** následovaný **ident** zadaný v příkazu. Příklad: **key_simple_contosokey**. Tento soubor obsahuje šifrovaný klíč.

Proveďte zálohu tohoto souboru Tokenizovaného klíče na bezpečném místě.

> [!IMPORTANT]
> Když budete chtít klíč později přenést do služby Azure Key Vault, Microsoft nelze exportovat tento klíč vrátit k tomu tak je velmi důležité, abyste klíče a soubor security world bezpečně zálohovali. Pokyny a osvědčené postupy pro zálohování klíčů získáte od společnosti Thales.
>


Nyní jste připraveni na přenos klíče do služby Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Příprava klíče na přenos

Pro tento čtvrtý krok proveďte následující postupy na odpojené pracovní stanici.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Vytvoření kopie klíče s omezenými oprávněními

Otevřete nový příkazový řádek a přejděte do umístění, kde jste rozzipoval. soubor zip BYOK. Pokud chcete snížit oprávnění pro váš klíč z příkazového řádku, spusťte jeden z těchto možností v závislosti na geografické oblasti nebo instance Azure:

* Pro Severní Ameriku:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Pro Evropa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Pro Asii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Pro Latinská Amerika:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Pro Japonsko:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Pro Koreu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Pro Austrálii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci US government Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Pro státní správu USA ministerstva obrany USA:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Pro Kanadu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Pro Německo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Indii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Pro Francii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Pro Spojené království:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

Při spuštění tohoto příkazu nahraďte *contosokey* nahraďte stejnou hodnotou, kterou jste zadali v **krok 3.5: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) kroku.

Zobrazí se výzva k připojení karet správce security world.

Jakmile se příkaz dokončí, uvidíte **výsledek: Úspěch** a kopie vašeho klíče se sníženými oprávněními jsou v souboru s názvem key_xferacId_<contosokey>.

Může zkontroluje seznamy řízení přístupu pomocí následujících příkazů pomocí nástrojů Thales:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Při spuštění těchto příkazů nahraďte contosokey stejnou hodnotu, která jste zadali v **krok 3.5: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) kroku.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2: Šifrování klíče pomocí klíč pro výměnu klíčů společnosti Microsoft

Spusťte jeden z následujících příkazů podle vaší zeměpisné oblasti nebo instance Azure:

* Pro Severní Ameriku:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Evropa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Asii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Latinská Amerika:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Japonsko:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Koreu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Austrálii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci US government Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro státní správu USA ministerstva obrany USA:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Kanadu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Indii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Francii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Spojené království:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Při spuštění tohoto příkazu použijte tyto pokyny:

* Nahraďte *contosokey* s identifikátorem, který jste použili k vygenerování klíče v **krok 3.5: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) kroku.
* Nahraďte *SubscriptionID* s ID předplatného Azure, která obsahuje váš trezor klíčů. Načíst tuto hodnotu dříve, v **krok 1.2: Získejte ID vašeho předplatného Azure** z [Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation) kroku.
* Nahraďte *ContosoFirstHSMKey* popiskem, který se používá pro název výstupního souboru.

Po dokončení tohoto postupu úspěšně, zobrazí **výsledek: Úspěch** a nový soubor v aktuální složce, která má následující název: KeyTransferPackage -*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Zkopírování balíčku pro přenos klíče na pracovní stanici připojené k Internetu

Pomocí USB Flash disku nebo jiného přenosného úložiště zkopírujte výstupní soubor z předchozího kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) do pracovní stanice připojené k Internetu.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5: Přenos vašeho klíče do služby Azure Key Vault

Pro tento závěrečný krok na pracovní stanici připojené k Internetu, použijte [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) rutinu k odeslání balíčku pro přenos klíče, který jste zkopírovali z odpojené pracovní stanice do modulu hardwarového zabezpečení Azure Key Vault:

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Pokud bude odesílání úspěšné, zobrazí zobrazí vlastnosti klíče, který jste právě přidali.

## <a name="next-steps"></a>Další postup

Teď můžete tento klíč chráněný HSM do trezoru klíčů. Další informace najdete v tématu **Pokud chcete použít modul hardwarového zabezpečení (HSM)** tématu [Začínáme se službou Azure Key Vault](key-vault-get-started.md) kurzu.
