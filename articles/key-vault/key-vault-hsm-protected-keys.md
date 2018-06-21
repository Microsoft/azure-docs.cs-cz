---
title: Postup generování a přenos klíčů chráněných pomocí HSM pro Azure Key Vault | Microsoft Docs
description: Použijte tento článek vám pomohou plánovat, generovat a potom přeneste vlastní klíčů chráněných pomocí HSM pro použití s Azure Key Vault. Taky označovaný jako BYOK nebo přineste si vlastní klíč.
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
ms.topic: article
ms.date: 12/05/2017
ms.author: barclayn
ms.openlocfilehash: 5f7cccfe0288b547f84d5642c46c21ab4095a7bf
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287717"
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Klíče postup generování a přenos chráněných pomocí HSM pro Azure Key Vault
## <a name="introduction"></a>Úvod
Pro lepší kontrolu Pokud používáte Azure Key Vault, můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy neopustí hranice HSM. Tento scénář se často označuje jako *přineste si vlastní klíč*, nebo BYOK. Moduly hardwarového zabezpečení jsou ověřené podle standardu FIPS 140-2 Level 2. Azure Key Vault používá k ochraně klíče Thales nShield řadu moduly hardwarového zabezpečení.

Použijte informace v tomto tématu vám pomohou plánovat, generovat a potom přeneste vlastní klíčů chráněných pomocí HSM pro použití s Azure Key Vault.

Tato funkce není dostupná pro Azure China.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](key-vault-whatis.md)  
>
> Úvodní kurz, který zahrnuje vytvoření trezoru klíčů pro klíčů chráněných pomocí HSM, najdete v části [Začínáme s Azure Key Vault](key-vault-get-started.md).
>
>

Další informace o generování a přenos klíč chráněný HSM pomocí přes Internet:

* Vygenerování klíče z offline pracovní stanice, která omezuje prostor pro útok.
* Že je klíč zašifrovaný pomocí klíč výměny klíčů (KEK), který zůstává zašifrovaný, dokud se přenese do HSM Azure Key Vault. Jenom zašifrovaná verze vašeho klíče ponechá původní pracovní stanici.
* Sada nástrojů nastaví vlastnosti pro klíč klienta, která se sváže klíč do architektury security world Azure Key Vault. Proto po HSM Azure Key Vault přijmou a dešifrují váš klíč, pouze tyto moduly hardwarového zabezpečení můžete použít. Klíč se nedá exportovat. Tuto vazbu vynucují moduly HSM Thales.
* Klíč výměny klíčů (KEK) používaný k šifrování vašeho klíče se generuje uvnitř HSM Azure Key Vault a není exportovatelný. Moduly hardwarového zabezpečení vynutit, aby může existovat žádná čitelná verze klíče kek mimo tyto moduly Hsm. Kromě toho sada nástrojů obsahuje záruku od společnosti Thales, že se klíč KEK nedá exportovat a byl vygenerovaný v originálním modulu HSM vyrobeným společností Thales.
* Sada nástrojů obsahuje záruku od společnosti Thales, že architektury security world Azure Key Vault vygenerovalo také na originálním modulu HSM vyrobeném společností Thales. Toto ověření vám poskytuje důkaz, že Microsoft používá originální hardware.
* Společnost Microsoft používá jiné klíče Kek a jiné architektury Security World v každé geografické oblasti. Toto oddělení zajišťuje, že váš klíč lze použít pouze v datových centrech v oblasti, ve kterém jste ho zašifrovali. Například klíč od Evropského zákazníka nelze použít v datových centrech v Severní Americe nebo Asii.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Další informace o modulech HSM Thales a služby Microsoft
Thales e-Security je přední globální poskytovatel šifrování dat a řešení kybernetického zabezpečení finančních služeb, špičkové technologie, výroby, government a technologie. S 40-let chrání firemní i vládní informace řešení společnosti Thales využívají je čtyři z pěti největších energie a letecký společností. Svá řešení jsou také používány 22 členských zemí NATO a zabezpečení více než 80 % po celém světě platebních transakcí.

Microsoft spolupracoval se společností Thales k vylepšení stav obrázky pro moduly hardwarového zabezpečení. Tato vylepšení umožňují získat typické výhod hostované služby bez vzdát kontrolu nad klíče. Konkrétně tato vylepšení umožňují Microsoftu spravovat moduly HSM, takže není nutné. Jako cloudová služba Azure Key Vault škálování krátkodobě ke splnění nárazovým zvýšením požadavků vaší organizace. Ve stejnou dobu, vaše klíč uvnitř modulů HSM Microsoftu chráněný: ponecháte si kontrolu nad životním cyklem klíče, protože klíč vygenerujete a jeho přenesení do HSM společnosti Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementace funkce přineste si vlastní klíč (BYOK) pro Azure Key Vault
Použijte následující informace a postupy, pokud chcete vygenerovat si vlastní klíč chráněný HSM a pak ho přenést do Azure Key Vault – přineste si vlastní klíč (BYOK) scénář.

## <a name="prerequisites-for-byok"></a>Předpoklady pro funkci BYOK
Najdete v následující tabulce najdete seznam požadavků pro přineste si vlastní klíč (BYOK) pro Azure Key Vault.

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |K vytvoření Azure Key Vault, budete potřebovat předplatné Azure: [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) |
| Úroveň služby Azure Key Vault Premium pro podporu klíčů chráněných pomocí HSM |Další informace o úrovních služeb a možnosti pro Azure Key Vault najdete v tématu [Azure Key Vault ceny](https://azure.microsoft.com/pricing/details/key-vault/) webu. |
| Modulu HSM společnosti Thales, čipové karty a podpůrný software |Musíte mít přístup k modulu hardwarového zabezpečení Thales a základní provozní znalosti o modulech HSM Thales. V tématu [modulu hardwarového zabezpečení Thales](https://www.thales-esecurity.com/msrms/buy) seznam kompatibilních modelů nebo modul HSM zakoupit, pokud nemáte jeden. |
| Následující hardware a software:<ol><li>Offline x64 pracovní stanice s minimální operační systém Windows Windows 7 a Thales software nshield od, který je minimálně verze 11.50.<br/><br/>Pokud tato pracovní stanice používá Windows 7, musíte [instalace rozhraní Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Pracovní stanice, která je připojený k Internetu a má minimální operační systém Windows Windows 7 a [prostředí Azure PowerShell](/powershell/azure/overview) **minimální verzi 1.1.0** nainstalována.</li><li>USB Flash disk nebo jiné přenosné úložné zařízení, která obsahuje aspoň 16 MB volného místa.</li></ol> |Z bezpečnostních důvodů doporučujeme, aby první pracovní stanice nebyla připojená k síti. Toto doporučení se však nevynucuje prostřednictvím kódu programu.<br/><br/>Všimněte si, že v následujících pokynech, tento pracovní stanice se označuje jako odpojené pracovní stanici.</p></blockquote><br/>Kromě toho pokud váš klíč tenanta je pro produkční síť, doporučujeme použít druhou, samostatnou pracovní stanici stáhnete sadu nástrojů a odešlete klíč tenanta. Ale pro účely testování můžete použít jako první pracovní stanici.<br/><br/>Všimněte si, že v následujících pokynech, druhá pracovní stanice se označuje jako stanice připojené k Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Generování a přenos vašeho klíče do Azure Key Vault HSM
Následující kroky pět použije k vygenerování a přenos vašeho klíče do Azure Key Vault HSM:

* [Krok 1: Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Příprava odpojené pracovní stanice](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Vygenerování klíče](#step-3-generate-your-key)
* [Krok 4: Příprava klíče pro přenos](#step-4-prepare-your-key-for-transfer)
* [Krok 5: Přenos vašeho klíče do Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Příprava pracovní stanice připojené k Internetu
Pro tento první krok proveďte následující postupy na pracovní stanici, která je připojena k Internetu.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Nainstalování prostředí Azure PowerShell
Z pracovní stanice připojené k Internetu stáhněte a nainstalujte modul Azure PowerShell, který obsahuje rutiny pro správu Azure Key Vault. To vyžaduje minimální verzi 0.8.13.

Pokyny k instalaci naleznete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Získání ID předplatného Azure
Spusťte relaci prostředí Azure PowerShell a přihlaste se k účtu Azure pomocí následujícího příkazu:

```Powershell
   Add-AzureAccount
```
V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Potom použít [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) příkaz:

```powershell
   Get-AzureSubscription
```
Z výstupu vyhledejte ID pro odběr, který budete používat pro Azure Key Vault. Toto ID předplatného budete potřebovat později.

Nezavírejte okno Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3: Stažení sady nástrojů funkce BYOK pro Azure Key Vault
Přejděte na web Microsoft Download Center a [stáhnete sadu nástrojů Azure Key Vault BYOK](http://www.microsoft.com/download/details.aspx?id=45345) pro zeměpisné oblasti nebo instanci Azure. Název balíčku pro stahování a jeho odpovídající hodnotu hash SHA-256 balíčku pomocí následující informace:

- - -
**Spojené státy americké:**

KeyVault-BYOK-nástroje spojené States.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Evropa:**

KeyVault BYOK nástroje Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Asii:**

KeyVault BYOK nástroje AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**Latinská Amerika:**

KeyVault BYOK nástroje LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japonsko:**

KeyVault BYOK nástroje Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Korea:**

KeyVault BYOK nástroje Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Austrálie:**

KeyVault BYOK nástroje Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault BYOK nástroje USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**Vláda USA DOD:**

KeyVault BYOK nástroje USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Kanada:**

KeyVault BYOK nástroje Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Německo:**

KeyVault BYOK nástroje Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**Indie:**

KeyVault BYOK nástroje India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Francie:**

KeyVault BYOK nástroje France.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

- - -
**Spojené království:**

KeyVault BYOK nástroje UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Chcete-li ověřit integritu vašeho stažené sady nástrojů funkce BYOK, z relace prostředí Azure PowerShell, použijte [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) rutiny.

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Sada nástrojů obsahuje následující:

* Balíček klíče výměny klíčů (KEK), který má název začíná **BYOK-KEK - pkg-.**
* Balíček architektury Security World, jehož název začíná **BYOK-SecurityWorld - pkg-.**
* Skript v jazyce python s názvem **verifykeypackage.py.**
* Spustitelný soubor příkazového řádku, s názvem **KeyTransferRemote.exe** a přidružené knihovny DLL.
* Visual C++ Redistributable balíčku, s názvem **vcredist_x64.exe.**

Zkopírujte balíček na USB Flash disk nebo jiného přenosného úložiště.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Příprava odpojené pracovní stanice
Pro tento druhý krok proveďte následující postupy na pracovní stanici, který není připojen k síti (Internet nebo interní síti).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Krok 2.1: Příprava odpojené pracovní stanice s modulu HSM společnosti Thales
Nainstalujte na počítači s Windows podpůrný software nCipher (Thales) a pak k tomuto počítači připojte modul HSM společnosti Thales.

Zajistěte, aby byly nástroje Thales ve své cestě (**%nfast_home%\bin**). Můžete například zadejte následující:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Další informace najdete v tématu v uživatelské příručce dodané s modulem HSM Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2: Instalace sady nástrojů funkce BYOK na odpojené pracovní stanice
Zkopírujte balíček sady nástrojů funkce BYOK z USB Flash disku nebo jiného přenosného úložiště a potom postupujte takto:

1. Extrahujte soubory ze staženého balíčku do libovolné složky.
2. Z této složky spusťte program vcredist_x64.exe.
3. Postupujte podle pokynů pro instalaci běhových součástí Visual C++ pro Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Vygenerování klíče
Pro tento třetí krok proveďte následující postupy na odpojené pracovní stanici. K dokončení tohoto kroku vašeho HSM musí být v režimu inicializace. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1: Změňte režim modulu hardwarového zabezpečení na "I"
Pokud používáte Thales nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka režimu zvýrazněte požadovaný režim. 2. Během pár sekund stiskněte a podržte tlačítko Vymazat z několika sekund. Pokud se změní režim, nový režim DIODU přestane blikat a zůstane po. Indikátor stavu může nepravidelně flash na několik sekund a pak bliká pravidelně v případě, že zařízení není připraveno. Jinak zařízení zůstane v aktuálním režimu, s odpovídající režim DIODU lit.

### <a name="step-32-create-a-security-world"></a>Krok 3.2: Vytvoření architektury security world
Spusťte příkazový řádek a spusťte program společnosti Thales nové architektury Security world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3
   ```

Tento program vytvoří **architektury Security World** soubor v adresáři % NFAST_KMDATA%\local\world, který odpovídá složce aplikací\Místní C:\ProgramData\nCipher\Key správy. Pro kvorum můžete použít jiné hodnoty, ale v našem příkladu se zobrazí výzva k zadání pro každé z nich tři prázdné karty a kódy PIN. Jakékoli dvě karty potom poskytnout úplný přístup k architektury security world. Tyto karty tvoří **Administrator Card Set** pro nové architektury security world.

Potom udělejte následující:

* Zálohujte soubor world. Zabezpečení a ochraně soubor world, karty správce a jejich kódy PIN a ujistěte se, že jeden člověk měl přístup k více než jednu kartu.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3: Změnit režim HSM, o.
Pokud používáte Thales nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka režimu zvýrazněte požadovaný režim. 2. Během pár sekund stiskněte a podržte tlačítko Vymazat z několika sekund. Pokud se změní režim, nový režim DIODU přestane blikat a zůstane po. Indikátor stavu může nepravidelně flash na několik sekund a pak bliká pravidelně v případě, že zařízení není připraveno. Jinak zařízení zůstane v aktuálním režimu, s odpovídající režim DIODU lit.


### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Ověření staženého balíčku
Tento krok je volitelný, ale doporučujeme, aby mohli ověřit tyto:

* Klíč pro výměnu klíčů, který je součástí sady nástrojů, byl vygenerovaný na originálním modulu HSM společnosti Thales.
* Hodnota hash architektury Security World, který je součástí sady nástrojů, byla vygenerovaná na originálním modulu HSM společnosti Thales.
* Klíč pro výměnu klíčů je neexportovatelného.

> [!NOTE]
> Chcete-li ověření staženého balíčku, modul hardwarového zabezpečení musí být připojený, zapnutý a musí mít architektury security world v něm (třeba tu, kterou jste právě vytvořili).
>
>

Ověření staženého balíčku:

1. Spusťte skript verifykeypackage.py tak, že zadáte jednu z těchto, v závislosti na zeměpisné oblasti nebo instanci Azure:

   * Pro Severní Ameriku:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Pro Evropu:

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
   * Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci Azure US government:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Pro US Government DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Pro Kanadu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Pro Německo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Indii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Pro Francie:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Pro Spojené království:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1

     > [!TIP]
     > Software společnosti Thales obsahuje python v %NFAST_HOME%\python\bin
     >
     >
2. Zkontrolujte, jestli následující příkaz, který znamená úspěšné ověření: **výsledek: Úspěch**

Tento skript ověřuje řetězec podepisujících až ke kořenovému klíči Thales. Hodnota hash tohoto kořenového klíče je vložená ve skriptu a jeho hodnota by měla být **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Tuto hodnotu můžete potvrdit samostatně navštivte stránky [webu společnosti Thales](http://www.thalesesec.com/).

Nyní jste připraveni vytvořit nový klíč.

### <a name="step-35-create-a-new-key"></a>3.5 krok: Vytvořte nový klíč
Generování klíče pomocí společnosti Thales **generatekey** program.

Spusťte následující příkaz k vygenerování klíče:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Když spustíte tento příkaz, použijte tyto pokyny:

* Parametr *chránit* musí být nastavena na hodnotu **modulu**, jak je vidět. Tím se vytvoří klíč chráněný modulem. Sada nástrojů BYOK nepodporuje klíče chráněné OCS.
* Nahraďte hodnotu *contosokey* pro **ident** a **plainname** s libovolnou hodnotou řetězce. Chcete-li minimalizovat správní režie a snížilo riziko chyby, doporučujeme použít stejnou hodnotu pro oba. **Ident** hodnota musí obsahovat jenom čísla, pomlčky a malá písmena.
* Pubexp je v prázdné (výchozí) v tomto příkladu, ale můžete zadat konkrétní hodnoty. Další informace naleznete v dokumentaci společnosti Thales.

Tento příkaz vytvoří soubor Tokenizovaného klíče ve složce %NFAST_KMDATA%\local s názvem začínajícím textem **key_simple_**, za nímž následují **ident** zadaný v příkazu. Příklad: **key_simple_contosokey**. Tento soubor obsahuje šifrovaný klíč.

Zálohujte tento soubor Tokenizovaného klíče do bezpečného umístění.

> [!IMPORTANT]
> Když budete chtít klíč později přenést do Azure Key Vault, Microsoft nelze exportovat tento klíč vám tak bude velmi důležité, abyste zálohovali váš klíč a architekturu security world bezpečně. Pokyny a osvědčené postupy pro zálohování klíčů získáte od společnosti Thales.
>
>

Nyní jste připraveni přenos vašeho klíče do Azure Key Vault.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Příprava klíče pro přenos
Pro tento čtvrtý krok proveďte následující postupy na odpojené pracovní stanici.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Vytvoření kopie klíče se sníženými oprávněními

Otevřete nový příkazový řádek a změňte aktuální adresář na umístění, kde unzipped BYOK souboru zip. Chcete-li omezit oprávnění pro váš klíč z příkazového řádku, spusťte jeden z následujících, v závislosti na zeměpisné oblasti nebo instanci Azure:

* Pro Severní Ameriku:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Pro Evropu:

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
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci Azure US government:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Pro US Government DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Pro Kanadu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Pro Německo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Indii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Pro Francie:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Pro Spojené království:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1

Když spustíte tento příkaz, nahraďte *contosokey* nahraďte stejnou hodnotou, kterou jste zadali v **3.5 krok: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) krok.

Zobrazí se výzva k připojení karet správce zabezpečení world.

Až se příkaz dokončí, zobrazí **výsledek: Úspěch** a kopii klíče se sníženými oprávněními jsou v souboru s názvem key_xferacId_<contosokey>.

Může zkontroluje seznamy řízení přístupu pomocí následujících příkazů pomocí nástrojů Thales:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Při spuštění těchto příkazů, nahraďte contosokey stejnou hodnotu, která jste zadali v **3.5 krok: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) krok.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2: Zašifrování klíče pomocí klíč pro výměnu klíčů společnosti Microsoft
Spusťte jeden z následujících příkazů, v závislosti na zeměpisné oblasti nebo instanci Azure:

* Pro Severní Ameriku:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Evropu:

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
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá instanci Azure US government:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro US Government DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Kanadu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Indii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Francie:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Spojené království:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Když spustíte tento příkaz, použijte tyto pokyny:

* Nahraďte *contosokey* nahraďte identifikátorem, který jste použili k vygenerování klíče v **3.5 krok: Vytvořte nový klíč** z [vygenerování klíče](#step-3-generate-your-key) krok.
* Nahraďte *SubscriptionID* s ID předplatného Azure, která obsahuje váš trezor klíčů. Načíst tuto hodnotu dříve, v **krok 1.2: získání ID předplatného Azure** z [Příprava pracovní stanice připojené k Internetu](#step-1-prepare-your-internet-connected-workstation) krok.
* Nahraďte *ContosoFirstHSMKey* s popiskem, který se používá pro názvu výstupního souboru.

Po dokončení této akce úspěšně, zobrazí **výsledek: Úspěch** a nový soubor existuje v aktuální složce, která má následující název: KeyTransferPackage -*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Zkopírování balíčku pro přenos klíče na pracovní stanici připojené k Internetu
Pomocí USB Flash disk nebo jiného přenosného úložiště zkopírujte výstupní soubor z předchozího kroku (KeyTransferPackage-ContosoFirstHSMkey.byok) do pracovní stanice připojené k Internetu.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5: Přenos vašeho klíče do Azure Key Vault
Tento poslední krok, na pracovní stanici připojené k Internetu, použijte [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) rutinu k odeslání balíčku pro přenos klíče, který jste zkopírovali z odpojené pracovní stanici do Azure Key Vault HSM:

   ```powershell
        Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Pokud bude odesílání úspěšné, zobrazí zobrazí vlastnosti klíče, který jste právě přidali.

## <a name="next-steps"></a>Další postup
Teď můžete použít tento klíč chráněný HSM v trezoru klíčů. Další informace najdete v tématu **Pokud chcete použít modul hardwarového zabezpečení (HSM)** tématu [Začínáme s Azure Key Vault](key-vault-get-started.md) kurzu.
