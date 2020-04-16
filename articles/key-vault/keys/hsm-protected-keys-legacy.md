---
title: Jak generovat a přenášet klíče chráněné hsm pro Azure Key Vault - Azure Key Vault | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže naplánovat, generovat a pak přenést vlastní klíče chráněné hsm pro použití s Azure Key Vault. Také známý jako BYOK nebo si přineste vlastní klíč.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 28f066668d580f16d831371f2d02a5abcc0e84b3
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429730"
---
# <a name="import-hsm-protected-keys-for-key-vault-legacy"></a>Import klíčů chráněných softwarem HSM pro trezor klíčů (starší verze)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pro větší jistotu, při použití Trezoru klíčů Azure, můžete importovat nebo generovat klíče v modulech hardwarového zabezpečení (HSM), které nikdy opustit hranice modulu hardwarového zabezpečení. Tento scénář se často označuje jako *bring your own key * (použití vlastního klíče) nebo BYOK. Azure Key Vault používá nCipher nShield řady hsmů (FIPS 140-2 Úroveň 2 ověřeno) k ochraně vašich klíčů.

Informace v tomto tématu vám pomůžou naplánovat, vygenerovat a potom přenést vlastní klíče chráněné pomocí hsm chráněné ho s Azure Key Vault.

Tato funkce není k dispozici pro Azure China.

> [!NOTE]
> Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../general/overview.md)  
> Kurz začínáme, který zahrnuje vytvoření trezoru klíčů pro klíče chráněné hsm, najdete v [tématu Co je Azure Key Vault?](../general/overview.md).

Další informace o generování a přenosu klíče chráněného modulu zabezpečení modulu zabezpečení přes Internet:

* Klíč generujete z offline pracovní stanice, což snižuje prostor pro útok.
* Klíč je zašifrován pomocí klíče KEK (Key Exchange Key), který zůstane zašifrovaný, dokud se nepřenese do objektů zabezpečení úložiště klíčů Azure. Pouze šifrovaná verze klíče opouští původní pracovní stanici.
* Sada nástrojů nastaví vlastnosti vašeho klíče klienta, který váže klíč do světa zabezpečení Azure Key Vault. Takže poté, co azure key vault hsms přijímat a dešifrovat váš klíč, pouze tyto hsm můžete použít. Klíč nelze exportovat. Tato vazba je vynucena nCipher HSMs.
* Klíč KEK (Key Exchange Key), který se používá k šifrování klíče se generuje uvnitř azure key vault hsms a není exportovatelný. Moduly hardwarového zabezpečení (HSM) vynucují to, že mimo tyto moduly HSM nemůže existovat žádná čitelná verze klíče KEK. Kromě toho sada nástrojů obsahuje osvědčení od nCipher, že KEK není exportovatelný a byl vytvořen uvnitř originálního hsm, který byl vyroben nCipher.
* Sada nástrojů obsahuje osvědčení od nCipher, že azure key vault zabezpečení svět byl také generován na originální hsm vyrobené nCipher. Toto potvrzení vám prokáže, že společnost Microsoft používá originální hardware.
* Společnost Microsoft používá samostatné keky a samostatné světy zabezpečení v každé zeměpisné oblasti. Toto oddělení zajišťuje, že klíč lze použít pouze v datových centrech v oblasti, ve které jste jej zašifrovali. Například klíč od evropského zákazníka nelze použít v datových centrech v Severní Americe nebo Asii.

## <a name="more-information-about-ncipher-hsms-and-microsoft-services"></a>Další informace o programech nCipher HSM a službách společnosti Microsoft

nCipher Security, společnost Entrust Datacard, je lídrem na trhu s bezpečnostními počítači pro obecné účely a poskytuje postavení předních světových organizací tím, že poskytuje důvěru, integritu a kontrolu svým důležitým obchodním informacím a aplikacím. NCipherova kryptografická řešení zabezpečují vznikající technologie – cloud, IoT, blockchain, digitální platby – a pomáhají plnit nové mandáty pro dodržování předpisů pomocí stejné osvědčené technologie, na které jsou dnes globální organizace závislé, aby chránily před hrozbami pro jejich citlivá data, síťovou komunikaci a podnikovou infrastrukturu. nCipher poskytuje důvěru pro důležité podnikové aplikace, zajišťuje integritu dat a dává zákazníkům úplnou kontrolu – dnes, zítra, za všech okolností.

Společnost Microsoft spolupracovala s nCipher Security na zlepšení stavu techniky pro hmenové soubory. Díky těmto vylepšením získáte typické výhody hostovaných služeb, aniž byste se museli vzdát kontroly nad svými klíči. Konkrétně tato vylepšení umožňují Microsoftu spravovat moduly HSM, takže to nemusíte dělat vy sami. Jako cloudová služba azure key vault se v krátké době škáluje tak, aby splňovaly špičky využití vaší organizace. Současně je váš klíč chráněn uvnitř microsoft's HSMs: Zachováte kontrolu nad životním cyklem klíčů, protože vygenerujete klíč a přenesete jej do programu zabezpečení zabezpečení společnosti Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementace vlastních klíčů pro Azure Key Vault

Následující informace a postupy použijte, pokud vygenerujete vlastní klíč chráněný hsm a pak jej přenesete do úložiště klíčů Azure – scénář přineste si vlastní klíč (BYOK).

## <a name="prerequisites-for-byok"></a>Předpoklady pro funkci BYOK

V následující tabulce naleznete seznam předpokladů pro bring your own key (BYOK) for Azure Key Vault.

| Požadavek | Další informace |
| --- | --- |
| Předplatné Azure |K vytvoření trezoru klíčů Azure potřebujete předplatné Azure: [Zaregistrujte si bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/) |
| Úroveň služby Azure Key Vault Premium pro podporu klíčů chráněných softwarem Zabezpečení hesm |Další informace o úrovních služeb a možnostech pro Azure Key Vault najdete na webu [Azure Key Vault Pricing.](https://azure.microsoft.com/pricing/details/key-vault/) |
| nCipher nShield HSMs, čipové karty a podpůrný software |Musíte mít přístup k modulu nCipher Hardware Security Module a základním provozním znalostem modulů nCipher nShield HSM. Seznam kompatibilních modelů naleznete v [tématu nCipher nShield Hardware Security Module,](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/how-to-buy) nebo si zakoupíte modul hardwarového zabezpečení, pokud jej nemáte. |
| Následující hardware a software:<ol><li>Offline pracovní stanice x64 s minimálním operačním systémem Windows 7 a softwarem nCipher nShield, který je alespoň verze 11.50.<br/><br/>Pokud tato pracovní stanice používá Windows 7, je potřeba [nainstalovat rozhraní Microsoft .NET Framework 4.5](https://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Pracovní stanice, která je připojena k Internetu a má nainstalovaný minimální operační systém Windows 7 a [Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) **u verze 1.1.0.**</li><li>Jednotka USB nebo jiné přenosné paměťové zařízení, které má alespoň 16 MB volného místa.</li></ol> |Z bezpečnostních důvodů doporučujeme, aby první pracovní stanice nebyla připojená k síti. Toto doporučení však není programově vynuceno.<br/><br/>V následujících pokynech se tato pracovní stanice označuje jako odpojená pracovní stanice.</p></blockquote><br/>Kromě toho pokud váš klíč klienta je pro produkční síť, doporučujeme použít druhou samostatnou pracovní stanici ke stažení sady nástrojů a nahrát klíč klienta. Pro účely testování k tomu ale můžete použít první pracovní stanici.<br/><br/>V následujících pokynech se tato druhá pracovní stanice označuje jako pracovní stanice připojená k Internetu.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Vygenerujte a přeneste klíč do hsm trezoru klíčů Azure

Ke generování a přenosu klíče do objektu zabezpečení úložiště klíčů Azure vault uděláte následujících pět kroků:

* [Krok 1: Příprava pracovní stanice připojené k internetu](#step-1-prepare-your-internet-connected-workstation)
* [Krok 2: Příprava odpojené pracovní stanice](#step-2-prepare-your-disconnected-workstation)
* [Krok 3: Vygenerujte klíč](#step-3-generate-your-key)
* [Krok 4: Příprava klíče na přenos](#step-4-prepare-your-key-for-transfer)
* [Krok 5: Přenos klíče do služby Azure Key Vault](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Krok 1: Příprava pracovní stanice připojené k internetu

V tomto prvním kroku proveďte následující postupy na pracovní stanici, která je připojena k Internetu.

### <a name="step-11-install-azure-powershell"></a>Krok 1.1: Instalace Azure PowerShellu

Z pracovní stanice připojené k internetu si stáhněte a nainstalujte modul Azure PowerShell, který obsahuje rutiny pro správu azure key vaultu. Pokyny k instalaci najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Krok 1.2: Získání ID předplatného Azure

Spusťte relaci Azure PowerShellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

```Powershell
   Connect-AzAccount
```
V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Potom použijte příkaz [Get-AzSubscription:](/powershell/module/az.accounts/get-azsubscription)

```powershell
   Get-AzSubscription
```
Na výstupu vyhledejte ID pro předplatné, které budete používat pro Azure Key Vault. Toto ID předplatného budete potřebovat později.

Nezavírejte okno Azure PowerShell.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Krok 1.3: Stažení sady nástrojů BYOK pro Azure Key Vault

Přejděte do služby Stažení softwaru Microsoft a [stáhněte si sadu nástrojů Azure Key Vault BYOK](https://www.microsoft.com/download/details.aspx?id=45345) pro vaši zeměpisnou oblast nebo instanci Azure. Následující informace slouží k identifikaci názvu balíčku ke stažení a odpovídající hodu hash balíčku SHA-256:

---
**Spojené státy americké:**

KeyVault-BYOK-Tools-Spojené státy americké.zip

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

---
**Evropě:**

KeyVault-BYOK-Tools-Evropa.zip

9aaa63E2E7F20CF9BB62485868754203721D2F888D300910634A32DFA1FB19E4A

---
**Asie:**

KeyVault-BYOK-Nástroje-AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C9777388EC7121EF6B5

---
**Latinská Amerika:**

KeyVault-BYOK-Nástroje-LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

---
**Japonsko:**

KeyVault-BYOK-Nástroje-Japonsko.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

---
**Korea:**

KeyVault-BYOK-Nástroje-Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB94B8610DDDA05344ED136F

---
**Jihoafrická republika:**

KeyVault-BYOK-Nástroje-Jižní Afrika.zip

C41060C5C0170AAAAD896dA732E31433D14CB983AC3C67766F46D98620784A

---
**Sae:**

KeyVault-BYOK-Nástroje-SAE.zip

FADE80210B06962AA0913EA411DAB977929248C65F365FD953BB9F241D5FC0D3

---
**Austrálie:**

KeyVault-BYOK-Nástroje-Austrálie.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452223EEB166901C40A

---
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-Nástroje-USGovCloud.zip

F8DB2FC914A7360650922391D9A79FF030FD3048B5EC83ADC59DB018621A

---
**Americká vláda DOD:**

KeyVault-BYOK-Tools-USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

---
**Kanada:**

KeyVault-BYOK-Nástroje-Kanada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E27193463085799717C7B

---
**Německo:**

KeyVault-BYOK-Tools-Německo.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894A29ED3C71C3F844C45D6

---
**Německo Veřejná:**

KeyVault-BYOK-Tools-Německo-Public.zip

54534936D0A0C99C8117DB724C34A5E50FD204CFCBD75C78972B785865364A29

---
**Indie:**

KeyVault-BYOK-Nástroje-Indie.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E00940121C436C8

---
**Francie:**

KeyVault-BYOK-Tools-Francie.zip

5C9D1F3E4125B0C09E9F60897C9AE3A8B4CB0E7D13A14F3EDBD280128F8FE7DF

---
**Spojené království:**

KeyVault-BYOK-Tools-Česká republika

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

---
**Švýcarsko:**

KeyVault-BYOK-Nástroje-Švýcarsko.zip

88CF8D39899E26D456D4E0BC57E5C94913ABF1D73A89013FCE3BBD9599AD2FE9

---


Chcete-li ověřit integritu stažené sady nástrojů BYOK, z relace Azure PowerShell, použijte [rutinu Get-FileHash.](https://technet.microsoft.com/library/dn520872.aspx)

   ```powershell
   Get-FileHash KeyVault-BYOK-Tools-*.zip
   ```

Sada nástrojů obsahuje:

* Balíček klíče KEK (Key Exchange Key), který má název začínající **BYOK-KEK-pkg-.**
* Balíček Security World, který má název začínající **BYOK-SecurityWorld-pkg-.**
* Skript pythonu s názvem **verifykeypackage.py.**
* Spustitelný soubor příkazového řádku s názvem **KeyTransferRemote.exe** a přidružené knihovny DLL.
* Visual C++ Redistributable Package s názvem **vcredist_x64.exe.**

Zkopírujte balíček na USB flash disk nebo jiné přenosné úložiště.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Krok 2: Příprava odpojené pracovní stanice

V tomto druhém kroku proveďte následující postupy na pracovní stanici, která není připojena k síti (internet nebo interní síť).

### <a name="step-21-prepare-the-disconnected-workstation-with-ncipher-nshield-hsm"></a>Krok 2.1: Příprava odpojené pracovní stanice pomocí nCipher nShield HSM

Nainstalujte podpůrný software nCipher do počítače se systémem Windows a připojte k tomuto počítači soubor nCipher nShield HSM.

Ujistěte se, že nástroje nCipher jsou v cestě (**%nfast_home%\bin**). Zadejte například tento příkaz:

  ```cmd
  set PATH=%PATH%;"%nfast_home%\bin"
  ```

Další informace naleznete v uživatelské příručce, která je součástí programu nShield HSM.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Krok 2.2: Instalace sady nástrojů BYOK na odpojenou pracovní stanici

Zkopírujte balíček sady nástrojů funkce BYOK z USB flash disku nebo jiného přenosného úložiště a potom postupujte takto:

1. Extrahujte soubory ze staženého balíčku do libovolné složky.
2. Z této složky spusťte příkaz vcredist_x64.exe.
3. Postupujte podle pokynů k instalaci komponent y Visual C++ runtime pro Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Krok 3: Vygenerujte klíč

V tomto třetím kroku proveďte následující postupy na odpojené pracovní stanici. Chcete-li dokončit tento krok hsm musí být v inicializačním režimu. 

### <a name="step-31-change-the-hsm-mode-to-i"></a>Krok 3.1: Změna režimu HSM na "I"

Pokud používáte nCipher nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka Režim zvýrazněte požadovaný režim. 2. Během několika sekund stiskněte a podržte tlačítko Vymazat několik sekund. Pokud se režim změní, led dioda nového režimu přestane blikat a zůstane rozsvícená. Stavová LED dioda může několik sekund nepravidelně blikat a poté pravidelně bliká, když je zařízení připraveno. V opačném případě zůstane zařízení v aktuálním režimu s rozsvíceným příslušným režimem LED.

### <a name="step-32-create-a-security-world"></a>Krok 3.2: Vytvoření světa zabezpečení

Spusťte příkazový řádek a spusťte program nCipher new-world.

   ```cmd
    new-world.exe --initialize --cipher-suite=DLf3072s256mRijndael --module=1 --acs-quorum=2/3
   ```

Tento program vytvoří soubor **Security World** v adresáři %NFAST_KMDATA%\local\world, který odpovídá složce C:\ProgramData\nCipher\Key Management Data\local. Pro kvorum můžete použít různé hodnoty, ale v našem příkladu budete vyzváni k zadání tří prázdných karet a špendlíků pro každou z nich. Pak, všechny dvě karty poskytují plný přístup do světa bezpečnosti. Tyto karty tvoří sadu karet správce **Administrator Card Set** pro novou architekturu Security World.

> [!NOTE]
> Pokud váš HSM nepodporuje novější cypher suite DLf3072s256mRijndael, můžete nahradit --cipher-suite= DLf3072s256mRijndael s --cipher-suite=DLf1024s160mRijndael
> 
> Bezpečnostní svět vytvořený s new-world.exe, který je dodáván s nCipher software verze 12.50 není kompatibilní s tímto postupem BYOK. Máte dvě možnosti:
> 1) Downgrade nCipher verze softwaru na 12.40.2 vytvořit nový svět zabezpečení.
> 2) Obraťte se na podporu nCipher a požádejte je o poskytnutí opravy hotfix pro verzi softwaru 12.50, která umožňuje používat verzi 12.40.2 programu new-world.exe, která je kompatibilní s tímto postupem BYOK.

Potom udělejte následující:

* Zazálohujte si soubor World. Zabezpečte soubor World, karty správce a jejich kódy PIN a nastavte pro ně ochranu. Zajistěte také, aby jeden člověk měl přístup maximálně k jedné kartě.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Krok 3.3: Změna režimu HSM na "O"

Pokud používáte nCipher nShield Edge, chcete-li změnit režim: 1. Pomocí tlačítka Režim zvýrazněte požadovaný režim. 2. Během několika sekund stiskněte a podržte tlačítko Vymazat několik sekund. Pokud se režim změní, led dioda nového režimu přestane blikat a zůstane rozsvícená. Stavová LED dioda může několik sekund nepravidelně blikat a poté pravidelně bliká, když je zařízení připraveno. V opačném případě zůstane zařízení v aktuálním režimu s rozsvíceným příslušným režimem LED.

### <a name="step-34-validate-the-downloaded-package"></a>Krok 3.4: Ověření staženého balíčku

Tento krok není povinný, ale doporučuje se, abyste mohli ověřit tyto položky:

* Klíč výměny klíčů, který je součástí sady nástrojů, byl vygenerován z originálního souboru nCipher nShield HSM.
* Hash světa zabezpečení, který je součástí sady nástrojů, byl vygenerován v originálním souboru nCipher nShield HSM.
* Klíč pro výměnu klíčů (KEK) se nedá exportovat.

> [!NOTE]
> Chcete-li ověřit stažený balíček, musí být hsm připojen, zapnutý a musí mít svět zabezpečení na něm (například ten, který jste právě vytvořili).

Ověření staženého balíčku:

1. Spusťte skript verifykeypackage.py zadáním jedné z následujících podmínek v závislosti na vaší geografické oblasti nebo instanci Azure:

   * V Severní Americe:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * V Evropě:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Pro Asii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Pro Latinskou Ameriku:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Pro Japonsko:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Pro Koreu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Pro Jihoafrickou republiku:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SA-1 -w BYOK-SecurityWorld-pkg-SA-1
   * Pro SAE:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UAE-1 -w BYOK-SecurityWorld-pkg-UAE-1
   * Pro Austrálii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá americkou vládní instanci Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Pro vládu USA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Pro Kanadu:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Pro Německo:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Německo Veřejné:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Pro Indii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
   * Pro Francii:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-FRANCE-1 -w BYOK-SecurityWorld-pkg-FRANCE-1
   * Spojené království:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-UK-1 -w BYOK-SecurityWorld-pkg-UK-1
   * Pro Švýcarsko:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-SUI-1 -w BYOK-SecurityWorld-pkg-SUI-1

     > [!TIP]
     > Software nCipher nShield obsahuje python na %NFAST_HOME%\python\bin
     >
     >
2. Potvrďte, že se zobrazí následující, což znamená úspěšné ověření: **Výsledek: ÚSPĚCH**

Tento skript ověří řetěz autora podpisu až ke kořenovému klíči nShield. Hodnota hash tohoto kořenového klíče je vložená ve skriptu a měla by mít hodnotu **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Tuto hodnotu můžete také potvrdit samostatně na [webových stránkách nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure/validation).

Nyní jste připraveni vytvořit nový klíč.

### <a name="step-35-create-a-new-key"></a>Krok 3.5: Vytvoření nového klíče

Generovat klíč pomocí nCipher nShield **generatekey** program.

Potom spuštěním následujícího příkazu klíč vygenerujte:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Při spouštění tohoto příkazu použijte tyto pokyny:

* Parametr *protect* musí být nastavený na hodnotu **module**, jak je vidět na obrázku. Vytvoří se tím klíč chráněný modulem. Sada nástrojů funkce BYOK nepodporuje klíče chráněné OCS.
* Hodnotu *contosokey* nahraďte hodnotou **ident** a hodnotu **plainname** nahraďte jakoukoli řetězcovou hodnotou. Chcete-li minimalizovat administrativní režie a snížit riziko chyb, doporučujeme použít stejnou hodnotu pro oba. Hodnota **ident** musí obsahovat pouze čísla, pomlčky a malá písmena.
* Parametr pubexp je v tomto příkladě prázdný (výchozí nastavení), můžete ale zadat konkrétní hodnoty. Další informace naleznete v [dokumentaci nCipher.](https://www.ncipher.com/resources/solution-briefs/protect-sensitive-data-rest-and-use-across-premises-and-azure-based)

Tento příkaz vytvoří soubor tokenizovaného klíče ve vaší složce %NFAST_KMDATA%\local s názvem začínajícím **key_simple_**, **následovaným identem,** který byl zadán v příkazu. Například: **key_simple_contosokey**. Tento soubor obsahuje šifrovaný klíč.

Tento soubor tokenizovaného klíče zálohujte do bezpečného umístění.

> [!IMPORTANT]
> Když později přenesete klíč do azure key vaultu, Microsoft nemůže exportovat tento klíč zpět k vám, takže je nesmírně důležité, abyste bezpečně zálohovali svůj klíč a bezpečnostní svět. Obraťte se na [nCipher](https://www.ncipher.com/about-us/contact-us) pro pokyny a osvědčené postupy pro zálohování klíče.
>


Nyní jste připraveni k přenosu klíče do úložiště klíčů Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Krok 4: Příprava klíče na přenos

V tomto čtvrtém kroku proveďte následující postupy na odpojené pracovní stanici.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Krok 4.1: Vytvoření kopie klíče se sníženými oprávněními

Otevřete nový příkazový řádek a změňte aktuální adresář na umístění, kde jste rozbalili soubor BYOK zip. Chcete-li snížit oprávnění ke klíči, spusťte z příkazového řádku jednu z následujících možností v závislosti na vaší geografické oblasti nebo instanci Azure:

* V Severní Americe:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* V Evropě:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Pro Asii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Pro Latinskou Ameriku:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Pro Japonsko:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Pro Koreu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Pro Jihoafrickou republiku:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1
* Pro SAE:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1
* Pro Austrálii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá americkou vládní instanci Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Pro vládu USA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Pro Kanadu:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Pro Německo:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Německo Veřejné:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Pro Indii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1
* Pro Francii:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-FRANCE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-FRANCE-1
* Spojené království:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1
* Pro Švýcarsko:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1

Při spuštění tohoto příkazu nahraďte *contosokey* stejnou hodnotou, jakou jste zadali v **kroku 3.5: Vytvořte nový klíč** z kroku Generovat [klíč.](#step-3-generate-your-key)

Budete vyzváni k připojení bezpečnostních světových administrátorů.

Po dokončení příkazu se zobrazí **výsledek: ÚSPĚCH** a kopie klíče se sníženými\<oprávněními jsou v souboru s názvem key_xferacId_ contosokey>.

ACLS můžete zkontrolovat pomocí následujících příkazů pomocí nástrojů nCipher nShield:

* aclprint.py:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Při spuštění těchto příkazů nahraďte contosokey stejnou hodnotou, kterou jste zadali v **kroku 3.5: Vytvořte nový klíč** z kroku Generovat [klíč.](#step-3-generate-your-key)

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Krok 4.2: Šifrování klíče pomocí klíče Microsoft Exchange Key

Spusťte jeden z následujících příkazů v závislosti na vaší geografické oblasti nebo instanci Azure:

* V Severní Americe:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* V Evropě:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Asii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Latinskou Ameriku:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Japonsko:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Koreu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Jihoafrickou republiku:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro SAE:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UAE-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UAE-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Austrálii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro [Azure Government](https://azure.microsoft.com/features/gov/), který používá americkou vládní instanci Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro vládu USA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Kanadu:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Německo Veřejné:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Indii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Francii:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-France-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-France-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Spojené království:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-UK-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-UK-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Pro Švýcarsko:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-SUI-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-SUI-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Při spouštění tohoto příkazu použijte tyto pokyny:

* Nahraďte *contosokey* identifikátorem, který jste použili ke generování klíče v **kroku 3.5: Vytvořte nový klíč** z kroku [Vygenerovat klíč.](#step-3-generate-your-key)
* Nahraďte *SubscriptionID* ID s ID předplatného Azure, který obsahuje trezor klíčů. Tuto hodnotu jste načetli dříve v **kroku 1.2: Získejte ID předplatného Azure** z kroku Připravit pracovní [stanici připojené k internetu.](#step-1-prepare-your-internet-connected-workstation)
* Nahraďte *contosoFirstHSMKey* popiskem, který se používá pro název výstupního souboru.

Po úspěšném dokončení se zobrazí **Výsledek: ÚSPĚCH** a v aktuální složce je nový soubor, který má následující název: KeyTransferPackage-*ContosoFirstHSMkey*.byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Krok 4.3: Zkopírujte balíček pro přenos klíčů do pracovní stanice připojené k Internetu

Ke kopírování výstupního souboru z předchozího kroku (KeyTransferPackage-ContosoFirstHSMMkey.byok) do pracovní stanice připojené k Internetu použijte jednotku USB nebo jiné přenosné úložiště.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Krok 5: Přenos klíče do služby Azure Key Vault

V tomto posledním kroku na pracovní stanici připojené k Internetu použijte rutinu [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) k nahrání balíčku přenosu klíčů, který jste zkopírovali z odpojené pracovní stanice do hsm úložiště klíčů Azure:

   ```powershell
        Add-AzKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'
   ```

Pokud je nahrávání úspěšné, zobrazí se vlastnosti klíče, který jste právě přidali.

## <a name="next-steps"></a>Další kroky

Nyní můžete použít tento klíč chráněný s hsm v trezoru klíčů. Další informace naleznete v tomto [porovnání](https://azure.microsoft.com/pricing/details/key-vault/)cen a funkcí .
