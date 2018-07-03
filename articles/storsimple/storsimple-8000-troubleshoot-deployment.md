---
title: StorSimple 8000 series řešení problémů s nasazením | Dokumentace Microsoftu
description: Popisuje, jak diagnostikovat a opravit chyby, ke kterým dochází při prvním nasazení StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 61719d482a4db1c737bbe38277f2ac3b2d684b63
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342421"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Řešení potíží s problémy při nasazení zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek obsahuje užitečné pokyny k odstraňování problémů pro nasazení Microsoft Azure StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, které se můžete setkat při konfiguraci StorSimple. 

Tyto informace platí pro fyzická zařízení řady StorSimple 8000 i řešení StorSimple Cloud Appliance.

> [!NOTE]
> Konfigurace související problémy zařízení, které mohou nastat situace může nastat při nasazení zařízení poprvé, nebo k nim můžete dojde později, když zařízení je funkční. Tento článek se zaměřuje na řešení potíží s problémy při prvním nasazení. Řešení potíží s operational zařízení, přejděte na [pomocí diagnostického nástroje pro řešení potíží s operational zařízení](storsimple-8000-diagnostics.md).

Tento článek také popisuje nástroje pro řešení potíží s nasazeními StorSimple a poskytuje podrobný příklad řešení potíží.

## <a name="first-time-deployment-issues"></a>Problémy při prvním nasazení
Pokud narazíte problém při prvním nasazení zařízení, zvažte následující:

* Pokud řešíte fyzické zařízení, ujistěte se, že byla nainstalována a nakonfigurovali podle popisu v hardwaru [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
* Zkontrolujte požadavky na nasazení. Ujistěte se, že máte všechny informace, které jsou popsané v [kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Přečtěte si poznámky k verzi StorSimple, pokud je popsána problém. Zpráva k vydání verze zahrnují alternativní řešení známých instalace problémy. 

Během nasazování zařízení nejčastější problémy, které uživatelé pro rozpoznávání tváře dojít při spuštění Průvodce instalací a při registraci zařízení pomocí Windows Powershellu pro StorSimple. (Můžete použít Windows PowerShell pro StorSimple k registraci a konfiguraci zařízení StorSimple. Další informace o registraci zařízení najdete v tématu [krok 3: Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Následující části vám můžou pomoct vyřešit problémy, ke které dojde při první konfiguraci zařízení StorSimple.

## <a name="first-time-setup-wizard-process"></a>Průvodce instalací první
Následující kroky shrnují proces Průvodce instalací. Podrobné informace o nastavení, najdete v článku [nasazení zařízení StorSimple v místním](storsimple-8000-deployment-walkthrough-u2.md).

1. Spustit [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) rutinu spusťte Průvodce instalací, který vás provede zbývající kroky. 
2. Konfigurace sítě: Průvodce instalací vám umožní nakonfigurovat nastavení sítě síťové rozhraní DATA 0 v zařízení StorSimple. Tato nastavení patří následující:
   * Virtuální IP (VIP), maska podsítě a bránu – [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) rutiny probíhá na pozadí. Nakonfiguruje IP adresu, masku podsítě a bránu pro rozhraní DATA 0 sítě na zařízení StorSimple.
   * Primární DNS server – [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) rutiny probíhá na pozadí. Konfiguruje nastavení DNS pro vaše řešení StorSimple.
   * NTP server – [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) rutiny probíhá na pozadí. Konfiguruje nastavení serveru NTP pro vaše řešení StorSimple.
   * Volitelné webový proxy server – [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) rutiny probíhá na pozadí. Nastaví a umožňuje konfigurace webového proxy serveru pro řešení StorSimple.
3. Nastavit heslo: dalším krokem je nastavení hesla správce zařízení.
   Heslo správce zařízení se používá k přihlášení k zařízení. Výchozí heslo zařízení je **Password1**.
        
     > [!IMPORTANT]
     > Hesla jsou shromážděná před registrací, ale použít pouze po úspěšné registraci zařízení. Pokud dojde k selhání použít hesla, vyzváni k zadání hesla znovu, dokud se shromažďují požadovaná hesla, (, které splňují požadavky na složitost).
     
4. Registrace zařízení: posledním krokem je registrace zařízení ve službě Správce zařízení StorSimple běží v Microsoft Azure. Registrace vyžaduje, abyste [registrační klíč služby získáte](storsimple-8000-manage-service.md#get-the-service-registration-key) z portálu Azure portal a ho zadat v Průvodci instalací. **Po úspěšné registraci zařízení, budete mít k dispozici šifrovací klíč dat služby. Je potřeba zachovat tento šifrovací klíč na bezpečném místě, protože ji budou muset všechna další zařízení zaregistrovat do služby.**

## <a name="common-errors-during-device-deployment"></a>Běžné chyby během nasazování zařízení
Následující tabulky obsahují seznam běžných chyb, že můžete setkat, když jste:

* Nakonfigurujte potřebná nastavení sítě.
* Konfigurace nastavení volitelné webového proxy serveru.
* Nastavení hesla správce zařízení.
* Registrace zařízení.

## <a name="errors-during-the-required-network-settings"></a>Chyby při potřebná nastavení sítě
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Tento příkaz lze spustit pouze na aktivním řadiči. |Konfigurace se právě provádí na pasivním kontroleru. |Spusťte tento příkaz z aktivního kontroleru. Další informace najdete v tématu [identifikace aktivního kontroleru na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Zařízení není připraveno. |Dochází k problémům s připojením k síti na DATA 0. |Zkontrolujte připojení k fyzické síti na DATA 0. |
| 3 |Invoke-HcsSetupWizard: Existuje konflikt IP adres s jiným systémem v síti (výjimka z HRESULT: 0x80070263). |IP adresa zadaná pro DATA 0 se již používá jiný systém. |Zadejte novou IP adresu, která se nepoužívá. |
| 4 |Invoke-HcsSetupWizard: Prostředek clusteru selhal. (Výjimka z HRESULT: 0x800713AE). |Duplicitní virtuální IP adresy. Zadaná IP adresa se už používá. |Zadejte novou IP adresu, která se nepoužívá. |
| 5 |Invoke-HcsSetupWizard: Neplatná IPv4 adresa. |IP adresy se poskytuje v nesprávném formátu. |Zkontrolujte formát a znovu zadejte IP adresu. Další informace najdete v tématu [adresování protokolu Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Neplatná IPv6 adresa. |IP adresy se poskytuje v nesprávném formátu. |Zkontrolujte formát a znovu zadejte IP adresu. Další informace najdete v tématu [adresování Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Žádné další koncové body nejsou k dispozici z mapovač koncových bodů. (Výjimka z HRESULT: 0x800706D9) |Fungování clusteru není funkční. |[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Chyby při nastavení volitelné webového proxy serveru
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Neplatný parametr (výjimka z HRESULT: 0x80070057) |Jeden z parametrů stanovených nastavení proxy serveru není platný. |Není k dispozici identifikátor URI ve správném formátu. Použijte následující formát: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Server vzdáleného volání Procedur není k dispozici (výjimka z HRESULT: 0x800706ba) |Hlavní příčinou je jeden z následujících akcí:<ol><li>Až cluster není.</li><li>Pasivní kontroler nemůže komunikovat s aktivním řadiči. proto se příkaz spustí z pasivní kontroler.</li></ol> |V závislosti na hlavní příčina:<ol><li>[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) abyste měli jistotu, že cluster běží.</li><li>Spusťte příkaz z aktivního kontroleru. Pokud chcete spustit příkaz z pasivního kontroleru, je potřeba zajistit, že pasivní kontroler může komunikovat s aktivním řadiči. Budete muset [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) Pokud tyto možnosti připojení bylo přerušeno.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Volání RPC se nezdařilo (výjimka z HRESULT: 0x800706be) |Clusteru je mimo provoz. |[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) abyste měli jistotu, že cluster běží. |
| 4 |Invoke-HcsSetupWizard: Cluster prostředek se nenašel (výjimka z HRESULT: 0x8007138f) |Prostředek clusteru se nenašel. To může nastat při instalaci není správný. |Budete muset obnovit zařízení do výchozího továrního nastavení. [Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) k vytvoření prostředku clusteru. |
| 5 |Invoke-HcsSetupWizard: Cluster zdroj není online (výjimka z HRESULT: 0x8007138c) |Prostředky clusteru nejsou online. |[Obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. |

## <a name="errors-related-to-device-administrator-password"></a>Chyby související s hesla správce zařízení
Výchozí heslo správce zařízení je **Heslo1**. Toto heslo vyprší po prvním přihlášení; Proto je potřeba ji změnit pomocí Průvodce instalací. Při první registraci zařízení je nutné zadat nové heslo správce zařízení. 

Ujistěte se, že hesla splňovat následující požadavky:

* Heslo správce zařízení by měl mít délku 8 až 15 znaků.
* Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. 
* Heslo nesmí být stejný jako poslední 24 hesla.

Kromě toho mějte na paměti, hesla vyprší každý rok, který lze změnit pouze po úspěšné registraci zařízení. Pokud z nějakého důvodu selže registrace hesla se nezmění.

Další informace o heslo správce zařízení, přejděte na [použít službu Správce zařízení StorSimple ke změně hesla StorSimple](storsimple-8000-change-passwords.md).

Jeden nebo více z těchto chyb může dojít při nastavování hesla Snapshot Manageru zařízení StorSimple a Správce zařízení.

| Ne. | Chybová zpráva | Doporučená akce |
| --- | --- | --- |
| 1 |Heslo překračuje maximální délku. |Heslo správce zařízení musí být dlouhé 8 až 15 znaků. |
| 2 |Heslo nesplňuje požadovanou délku. |Heslo správce zařízení musí být dlouhé 8 až 15 znaků.|
| 3 |Heslo musí obsahovat malá písmena. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 4 |Heslo musí obsahovat znaky. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 5 |Heslo musí obsahovat speciální znaky. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 6 |Heslo musí obsahovat 3 z následujících typů 4 znaků: velká písmena, malá písmena, číselné a speciální. |Heslo neobsahuje požadované typy znaků. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 7 |Parametr neodpovídá potvrzení. |Ujistěte se, že vaše heslo splňuje všechny požadavky a že jste ho zadali správně. |
| 8 |Heslo nesmí odpovídat ve výchozím nastavení. |Výchozí heslo je *Heslo1*. Budete muset toto heslo změnit po prvním přihlášení. |
| 9 |Heslo, které jste zadali, neodpovídá heslo zařízení. Zadejte heslo znovu. |Zkontrolujte heslo a zadávat znovu. |

Hesla se shromažďují předtím, než zařízení je zaregistrované, ale se použijí pouze po úspěšném dokončení registrace. Pracovní postup obnovení hesla vyžaduje zařízení k registraci.

> [!IMPORTANT]
> Obecně platí Pokud se nezdaří pokus o použití hesla, pak software opakovaně pokusí shromáždit heslo, dokud nebude úspěšná. Ve výjimečných případech nelze použít heslo. V takovém případě můžete zaregistrovat zařízení a pokračovat, ale nebudou změněny hesla. Po registraci na webu Azure Portal můžete změnit heslo správce zařízení.


Můžete resetovat heslo na webu Azure Portal prostřednictvím služby Správce zařízení StorSimple. Další informace najdete v části [změnit heslo správce zařízení](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Chyby při registraci zařízení
Použijte ve službě Správce zařízení StorSimple běží v Microsoft Azure k registraci zařízení. Jeden nebo více z těchto problémů může dojít při registraci zařízení.

| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Chyba 350027: Nepovedlo se zaregistrovat zařízení pomocí Správce zařízení StorSimple. | |Počkejte pár minut a opakujte operaci znovu. Pokud se problém nevyřeší, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Chyba 350013: Při registraci zařízení došlo k chybě. To může být nesprávný registrační klíč služby. | |Zaregistrujte prosím zařízení znovu s použitím správného registračního klíče služby. Další informace najdete v tématu [získat registrační klíč služby.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Chyba 350063: Selhalo ověřování do služby Správce zařízení StorSimple předán, ale registrace. Zkuste prosím operaci zopakovat později. |Tato chyba označuje, že uplynutí ověřování pomocí služby ACS, ale volání registrace ke službě se nezdařila. To může být výsledkem poruchu sporadické sítě. |Pokud se problém nevyřeší, prosím [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Chyba 350049: Služba není dosažitelná během registrace. |Když při volání do služby, webové výjimce přijetí. V některých případech to může získat opraven budete pokoušet operaci později. |Zkontrolujte prosím IP adresu a název DNS a zkuste operaci zopakovat. Pokud se problém nevyřeší, [obraťte se na Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Chyba 350031: Zařízení je již zaregistrován. | |Není potřeba žádná akce. |
| 6 |Chyba 350016: Registrace zařízení se nezdařila. | |Ujistěte se prosím, že registrační klíč je správný. |
| 7 |Invoke-HcsSetupWizard: Došlo k chybě při registraci zařízení; To může být způsobeno nesprávná IP adresa nebo název DNS. Zkontrolujte nastavení sítě a zkuste to znovu. Pokud se problém nevyřeší, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Chyba 350050) |Ujistěte se, že vaše zařízení příkazem ping vnější síti. Pokud nemáte připojení k vnější síti, registrace může selhat s touto chybou. Tato chyba může být kombinací jeden nebo více z následujících akcí:<ul><li>Nesprávná IP</li><li>Nesprávný podsítě</li><li>Nesprávný brány</li><li>Nesprávné nastavení DNS</li></ul> |Podívejte se na postup v [podrobný Poradce při potížích příklad](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Aktuální operace selhala kvůli vnitřní chybě služby [0x1FBE2]. Zkuste operaci po nějaké době zopakovat. Pokud se problém nevyřeší, obraťte se na Microsoft Support. |Toto je obecná chyba vyvolána pro všechny uživatele neviditelné chyby ze služby nebo agenta. Nejběžnějším Důvodem může být, že ověřování ACS se nezdařilo. Možná příčina selhání je, že dojde k problémům s konfigurací serveru NTP a čas na zařízení není správně nastaven. |Opravte čas (Pokud dojde k problémům) a pak zkuste operaci zopakovat registraci. Pokud pomocí příkazu Set-HcsSystem - Timezone upravte časové pásmo, velké první písmeno každého slova v časovém pásmu (například "Tichomoří (běžný čas").  Pokud se problém nevyřeší, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. |
| 9 |Upozornění: Nebylo možné aktivovat zařízení. Správce zařízení a hesla Snapshot Manageru zařízení StorSimple se nezměnily. |Pokud se registrace nezdaří, Správce zařízení a hesla Snapshot Manageru zařízení StorSimple se nezmění. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Nástroje pro řešení potíží s nasazeními StorSimple
StorSimple obsahuje několik nástrojů, které můžete použít k řešení vašeho řešení StorSimple. Mezi ně patří:

* Podporuje balíčky a protokoly zařízení.
* Rutiny vytvořené speciálně pro řešení potíží.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Podporuje balíčky a zařízení nejsou k dispozici protokoly pro řešení potíží
Balíček pro podporu obsahuje všechny relevantní protokoly, které mohou pomoci týmu Microsoft Support při řešení problémů se zařízením. Prostředí Windows PowerShell pro StorSimple můžete použít k vygenerování balíčku šifrované podpory, které pak můžete sdílet s pracovníky podpory.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Chcete-li zobrazit protokoly nebo obsah balíčku pro podporu
1. Vygenerujte balíček pro podporu, jak je popsáno v pomocí Windows Powershellu pro StorSimple [vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md).
2. Stáhněte si [dešifrování skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokálně na klientském počítači.
3. Použijte tento [podrobný postup](storsimple-8000-create-manage-support-package.md#edit-a-support-package) k otevření a dešifrovat balíček pro podporu.
4. Protokoly balíček dešifrovaný podpory jsou ve formátu trasování událostí pro Windows/etvx. Můžete provést následující kroky a tyto soubory zobrazit v prohlížeči událostí pro Windows:
   
   1. Spustit **eventvwr** příkaz na klientovi Windows. Tím se spustí prohlížeč událostí.
   2. V **akce** podokně klikněte na tlačítko **otevřít uložený protokol** a odkazují na soubory protokolu ve formátu etvx/trasování událostí pro Windows (balíček pro podporu). Teď si můžete zobrazit soubor. Po otevření souboru, kliknete pravým tlačítkem a uložte soubor jako text.
      
      > [!IMPORTANT]
      > Můžete také použít **Get-WinEvent** rutiny pro otevření těchto souborů v prostředí Windows PowerShell. Další informace najdete v tématu [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) v referenční dokumentace k rutinám Windows Powershellu.
     
5. Když otevřete protokoly v prohlížeči událostí vyhledejte tyto protokoly, které obsahují problémy související s konfigurací zařízení:
   
   * hcs_pfconfig/Operational protokolu
   * hcs_pfconfig/Config
6. V souborech protokolu hledat řetězce související s rutinami, volá se, Průvodce instalací. Zobrazit [Průvodce instalací první](#first-time-setup-wizard-process) seznam těchto rutin.
7. Pokud nejste schopni zjistit příčinu problému, můžete si [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. Postupujte podle kroků v [vytvořit žádost o podporu](storsimple-8000-contact-microsoft-support.md#create-a-support-request) když požádáte o pomoc Microsoft Support.

## <a name="cmdlets-available-for-troubleshooting"></a>Rutiny, které jsou k dispozici pro řešení potíží
Následující rutiny prostředí Windows PowerShell použijte k detekci chyb připojení.

* `Get-NetAdapter`: Tuto rutinu použijte k detekci stavu síťových rozhraní.
* `Test-Connection`: Pomocí této rutiny zkontrolujte připojení k síti uvnitř i mimo síť.
* `Test-HcsmConnection`: Pomocí této rutiny aby se ověřilo připojení úspěšně registrovaná zařízení.
* `Sync-HcsTime`: Tuto rutinu použijte k zobrazení času zařízení a vynucení synchronizace času se serverem NTP.
* `Enable-HcsPing` a `Disable-HcsPing`: použijte tyto rutiny umožňující hostitele pro odeslání příkazu ping síťových rozhraní na zařízení StorSimple. Ve výchozím nastavení síťová rozhraní StorSimple není reagovat na žádosti příkazu ping.
* `Trace-HcsRoute`: Pomocí této rutiny jako nástroj pro sledování postupu. Odešle pakety každý směrovač na cestě do konečného místa určení po určitou dobu a pak vypočítá výsledky podle paketů vrácených z každého směrování. Protože `Trace-HcsRoute` ukazuje stupeň ztráta paketů v jakékoli dané směrovače nebo odkaz, můžete určit které směrovače nebo odkazy by mohly způsobovat problémy se sítí.
* `Get-HcsRoutingTable`: Tuto rutinu použijte k zobrazení místní směrovací tabulce IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Řešení potíží pomocí rutiny Get-NetAdapter
Při konfiguraci síťových rozhraní pro nasazení první zařízení, stav hardwaru není k dispozici ve službě Správce zařízení StorSimple uživatelského rozhraní, protože zařízení ještě není zaregistrované ve službě. Kromě toho **stav hardwaru** okno nemusí odrážet vždy správně stavu zařízení, zejména v případě, že dojde k problémům, které mají vliv synchronizace služby. V takových případech můžete použít `Get-NetAdapter` rutiny určit stav síťových rozhraní.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Chcete-li zobrazit seznam všech síťových adaptérů na vašem zařízení
1. Spusťte prostředí Windows PowerShell pro StorSimple a potom zadejte `Get-NetAdapter`. 
2. Použít výstup `Get-NetAdapter` rutiny a následující pokyny pro zjištění stavu síťového rozhraní.
   
   * Pokud rozhraní je v pořádku a povolený, **ifIndex** stav zobrazen **nahoru**.
   * Pokud rozhraní je v pořádku, ale není fyzicky připojené (pomocí síťového kabelu) **ifIndex** se zobrazuje jako **zakázané**.
   * Pokud rozhraní je v pořádku, ale není povoleno, **ifIndex** stav zobrazen **NotPresent**.
   * Pokud rozhraní buď neexistuje, nezobrazí se v tomto seznamu. Uživatelské rozhraní služby Správce zařízení StorSimple bude mít toto rozhraní ve stavu selhání.

Další informace o tom, jak pomocí této rutiny naleznete v části [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) v Reference k rutinám Windows Powershellu.

V následujících částech se dozvíte ukázky výstup z `Get-NetAdapter` rutiny.

 V těchto ukázek řadič 0 pasivní kontroler a bylo nakonfigurováno takto:

* DATA 0, DATA 1, DATA 2 a DATA 3 v síti byly rozhraní na zařízení.
* Nebyly k dispozici; DATA 4 a karty síťového rozhraní DATA 5 proto nejsou uvedené ve výstupu.
* Bylo povolené rozhraní DATA 0.

Řadič 1 aktivní kontroler a bylo nakonfigurováno takto:

* DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 a 5 dat v síti byly rozhraní na zařízení.
* Bylo povolené rozhraní DATA 0.

**Ukázkový výstup – řadiče 0**

Následuje výstup z řadiče 0 (pasivní kontroler). Nejste připojení dat 1, DATA 2 a DATA 3. DATA 4 a DATA 5 nejsou uvedené, protože nejsou k dispozici na zařízení.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Ukázkový výstup – řadiče 1**

Následuje výstup z řadiče 1 (aktivní kontroler). Pouze DATA 0 nakonfigurovali síťového rozhraní na zařízení a funkční.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Řešení potíží pomocí rutiny Test-Connection
Můžete použít `Test-Connection` rutiny k určení, zda zařízení StorSimple můžete připojit k vnější síti. Pokud všechny síťové parametry, včetně služby DNS jsou správně nakonfigurovány v Průvodci instalací, můžete použít `Test-Connection` rutiny pro odeslání příkazu ping známé adrese mimo síť, jako je outlook.com.

Měli byste povolit příkaz ping na řešit problémy s připojením pomocí této rutiny je zakázána. příkaz ping.

Najdete v následujících ukázkách výstup `Test-Connection` rutiny.

> [!NOTE]
> V prvním příkladu je toto zařízení nakonfigurované s nesprávné DNS. V druhém příkladu DNS je správná.

**Ukázkový výstup – nesprávné DNS**

V následujícím příkladu neexistuje žádný výstup pro adresy IPV4 a IPV6, což znamená, že DNS není přeložen. To znamená, že není k dispozici připojení k vnější síti a správné DNS musí být zadán.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Ukázkový výstup – správné DNS**

V následujícím příkladu DNS vrátí adresu IPV4, označující, jestli je správně nakonfigurovaný server DNS. Tím potvrdíte, že se připojení k vnější síti.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Řešení potíží pomocí rutiny Test-HcsmConnection
Použití `Test-HcsmConnection` rutiny pro zařízení, která je již připojen k a registraci ve službě Správce zařízení StorSimple. Tato rutina vám pomůže zkontrolovat připojení mezi registrovaných zařízení a odpovídající služby Správce zařízení StorSimple. Tento příkaz můžete spustit v prostředí Windows PowerShell pro StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Ke spuštění rutiny Test-HcsmConnection
1. Ujistěte se, že je zařízení zaregistrované.
2. Kontrola stavu zařízení. Pokud je zařízení deaktivováno, v režimu údržby nebo v režimu offline, může se zobrazit jedna z následujících chyb:
   
   * ErrorCode.CiSDeviceDecommissioned – to znamená, že je zařízení deaktivováno.
   * ErrorCode.DeviceNotReady – to znamená, že je zařízení v režimu údržby.
   * ErrorCode.DeviceNotReady – to znamená, že zařízení není online.
3. Ověřte, zda je spuštěna služba Správce zařízení StorSimple (použít [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) rutiny). Pokud služba není spuštěná, může se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – to znamená, že když jste spustili Get ClusterResource došlo k výjimce.
4. Zkontrolujte token Access Control Service (ACS). Pokud vyvolá výjimky na web, může být výsledkem problému brány, chybějící ověření proxy serverem, nesprávné DNS nebo selhání ověřování. Může se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceGateway – to znamená výjimku HttpStatusCode.BadGateway: službě překládání názvu nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceProxy – to znamená HttpStatusCode.ProxyAuthenticationRequired výjimky (kód stavu HTTP 407): klient nemůže ověřit proxy serveru.
   * ErrorCode.CiSApplianceDNSError – to znamená, s výjimkou WebExceptionStatus.NameResolutionFailure: službě překládání názvu nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale připojení.
     
     Pokud se nevyvolá výjimky na web, zkontrolujte ErrorCode.CiSApplianceFailure. To znamená, že zařízení se nezdařila.
5. Zkontrolujte připojení cloudové služby. Pokud webovou službu výjimku, může se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceGateway – to znamená výjimku HttpStatusCode.BadGateway: zprostředkující proxy serveru přijata chybná žádost z jiného serveru proxy nebo z původního serveru.
   * ErrorCode.CiSApplianceProxy – to znamená HttpStatusCode.ProxyAuthenticationRequired výjimky (kód stavu HTTP 407): klient nemůže ověřit proxy serveru.
   * ErrorCode.CiSApplianceDNSError – to znamená, s výjimkou WebExceptionStatus.NameResolutionFailure: službě překládání názvu nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale připojení.
     
     Pokud se nevyvolá výjimky na web, zkontrolujte ErrorCode.CiSApplianceSaasServiceError. To znamená problém s ve službě Správce zařízení StorSimple.
6. Zkontrolujte připojení k Azure Service Bus. ErrorCode.CiSApplianceServiceBusError označuje, jestli se zařízení nemůže připojit ke službě Service Bus.

Soubory protokolu CiSCommandletLog0Curr.errlog a CiSAgentsvc0Curr.errlog bude obsahovat další informace, jako je například Podrobnosti o výjimce.

Další informace o tom, jak používat rutinu, přejděte na [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) v prostředí Windows PowerShell referenční dokumentaci.

> [!IMPORTANT]
> Spuštěním této rutiny pro aktivní a pasivní kontroler.

Najdete v následujících ukázkách výstup `Test-HcsmConnection` rutiny.

**Ukázkový výstup – úspěšně registrovaná zařízení se systémem StorSimple Update 3**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Ukázkový výstup – offline zařízení** 

Tato ukázka je ze zařízení, která je ve stavu **Offline** na webu Azure Portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Zařízení se nemohlo připojit pomocí aktuální konfigurace webového proxy serveru. Může se jednat problém s konfigurace webového proxy serveru nebo problém se síťovým připojením. V takovém případě by měl Ujistěte se, že vaše nastavení webového proxy serveru jsou správné a webové proxy servery byly online a dostupné.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Řešení potíží pomocí rutiny Sync-HcsTime
Tuto rutinu použijte k zobrazení času zařízení. Pokud čas zařízení má posun se serverem NTP, pak můžete tuto rutinu vynucení synchronizace času se serverem NTP.
- Posun mezi zařízením a serverem NTP je větší než 5 minut, zobrazí se upozornění. 
- Posun je větší než 15 minut, zařízení bude přejdou do režimu offline. Tato rutina stále slouží k vynucení synchronizace času. 
- Nicméně pokud posun přesahuje 15 hodin, pak nebudete mít k vynucení synchronizace, čas a chybová zpráva se zobrazí.

**Ukázkový výstup – pomocí Sync-HcsTime vynucené čas synchronizace**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Řešení potíží s rutinami Enable HcsPing a Disable-HcsPing
Tyto rutiny používají k zajištění, že síťových rozhraní na zařízení s reagovat na žádosti příkazu ping ICMP. Ve výchozím nastavení síťová rozhraní StorSimple není reagovat na žádosti příkazu ping. Použití této rutiny je nejjednodušší způsob, jak vědět, jestli vaše zařízení je online a dostupný.

**Ukázkový výstup – Enable HcsPing a Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Řešení potíží pomocí rutiny HcsRoute trasování
Pomocí této rutiny jako nástroj pro sledování postupu. Odešle pakety každý směrovač na cestě do konečného místa určení po určitou dobu a pak vypočítá výsledky podle paketů vrácených z každého směrování. Protože rutina zobrazuje stupeň ztráta paketů v jakékoli dané směrovače nebo odkaz, můžete určit které směrovače nebo odkazy by mohly způsobovat problémy se sítí.

**Ukázkový výstup ukazuje, jak trasu paket s HcsRoute trasování**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Řešení potíží pomocí rutiny Get-HcsRoutingTable
Tuto rutinu použijte k zobrazení směrovací tabulky pro zařízení StorSimple. Směrovací tabulku je sada pravidel, která vám pomůže určit, kde budete přesměrováni datových paketů přenosu po síti Internet Protocol (IP).

Směrovací tabulka ukazuje rozhraní a brána, který přesměruje data do vybraných sítích. Poskytuje metriky, což je osoba, která rozhoduje pro trasu k dosažení určitého cíle. Nižší metriky, tím vyšší priorita.

Například pokud máte 2 síťová rozhraní DATA 2 a DATA 3, připojený k Internetu. Pokud směrování metriky pro DATA 2 a DATA 3 se 15 a 261, s nižší metriky rozhraní DATA 2 je upřednostňovaný rozhraní použité pro přístup k Internetu.

Pokud používáte zařízení StorSimple Update 1, vaše síťové rozhraní DATA 0 má nejvyšší priority pro přenosy v cloudu. Z toho vyplývá, že i v případě, že existují další povolenou podporu cloudu rozhraní, by cloud provoz směrován přes DATA 0.

Pokud spustíte `Get-HcsRoutingTable` rutiny bez zadání všech parametrů (jako v následujícím příkladu), bude výstup rutiny směrovací tabulky IPv4 i IPv6. Alternativně můžete zadat `Get-HcsRoutingTable -IPv4` nebo `Get-HcsRoutingTable -IPv6` zobrazíte relevantní směrovací tabulky.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Podrobný Poradce při potížích příklad StorSimple
Následující příklad ukazuje podrobný Poradce při potížích pro StorSimple nasazení. V ukázkovém scénáři neúspěšné registrace zařízení s chybová zpráva oznamující, že nastavení sítě nebo název DNS je nesprávná.

Vrácená chybová zpráva je:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Chyba může být způsobeno jedním z následujících akcí:

* Nesprávný hardware instalace
* Chybný síťová rozhraní
* Nesprávná IP adresa, maska podsítě, brány, primárního serveru DNS nebo webového proxy serveru
* Nesprávný registrační klíč
* Nastavení brány firewall nesprávný

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Účelem vypátrání a opravení problému registrace zařízení
1. Zkontrolujte konfiguraci zařízení: spusťte na aktivním řadiči `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Průvodce instalací se musí spustit na aktivním řadiči. Pokud chcete ověřit, že jste připojeni k aktivnímu řadiči, podívejte se na banner v konzole sériového portu. Hlavičky určuje, zda jste připojeni k řadič 0 a řadič 1, a určuje, zda kontroler je aktivní nebo pasivní. Další informace najdete v části [identifikace aktivního kontroleru na vašem zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Ujistěte se, že je zařízení zapojené správně: Zkontrolujte síťové kabely zpět rovině zařízení. Kabeláž je specifické pro model zařízení. Další informace najdete v části [instalaci zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalaci zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Pokud používáte 10 GbE síťové porty, je potřeba použít zadaný QSFP SFP adaptéry a kabely SFP. Další informace najdete v tématu [seznam kabely, přepínače a doporučuje pro 10 GbE portů vysílače](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Ověření stavu síťového rozhraní:
   
   * Použijte rutinu Get-NetAdapter zjišťovat stav síťová rozhraní DATA 0. 
   * Pokud odkaz nebude fungovat správně, **ifindex** stav bude znamenat, že rozhraní dolů. Musíte pak zkontrolujte síťové připojení portů do zařízení a k přepínači. Musíte také vyloučit chybný kabely. 
   * Pokud máte podezření, že na port DATA 0 aktivního kontroleru se nezdařila, můžete to ověřit připojením k DATŮM 0 port na řadiči 1. Pokud to pokud chcete potvrdit, odpojíte síťový kabel od zařízení z řadiče 0, připojte kabel do řadiče 1 a pak znovu spusťte rutinu Get-NetAdapter.
     Pokud port DATA 0 na řadiči se nezdaří, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. Může se stát, že je třeba nahradit řadiče ve vašem systému.
4. Ověření připojení k přepínači:
   
   * Ujistěte se, že DATA 0 síťových rozhraní na řadiči 0 a řadič 1 v primárním zařízení jsou ve stejné podsíti. 
   * Zkontrolujte rozbočovač nebo směrovač. Obvykle byste měli připojit oba kontrolery stejné rozbočovač nebo směrovač. 
   * Ujistěte se, že přepínače, který používáte pro připojení jsou DATA 0 pro oba řadiče ve stejné síti vLAN.
5. Vylučte všechny chyby uživatelů:
   
   * Znovu spusťte Průvodce instalací (Spustit **Invoke-HcsSetupWizard**) a zadejte hodnoty znovu a ujistit se, že zde nejsou žádné chyby. 
   * Ověřte registraci klíč se používá. Stejné registrační klíč slouží k připojení několika zařízení do služby Správce zařízení StorSimple. Použijte postup uvedený v [registrační klíč služby získáte](storsimple-8000-manage-service.md#get-the-service-registration-key) zajistit, že používáte správné registrační klíč.
     
     > [!IMPORTANT]
     > Pokud máte více služby spuštěné, je potřeba zajistit, že registrační klíč pro příslušnou službu slouží k registraci zařízení. Pokud zaregistrujete zařízení s nesprávnou službu Správce zařízení StorSimple budete muset [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) o dalších krocích. Budete muset provést obnovení do výrobního nastavení zařízení (která může způsobit ztrátu dat) a pak ho připojit ke službě určené.
     > 
     > 
6. Pomocí rutiny Test-Connection ověřte, že máte připojení k vnější síti. Další informace najdete v části [řešení potíží pomocí rutiny Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Zkontrolujte rušení brány firewall. Pokud si ověříte, že nastavení virtuálních IP (VIP), podsíť, brána a DNS jsou všechny správné a stále zobrazuje problémy s připojením, pak je možné, že brána firewall blokuje komunikaci mezi zařízením a vnější síti. Je potřeba zajistit, že porty 80 a 443 jsou dostupné v zařízení StorSimple pro odchozí komunikaci. Další informace najdete v tématu [požadavky sítě pro zařízení StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Podívejte se na protokoly. Přejděte na [podporuje balíčky a zařízení nejsou k dispozici protokoly pro řešení potíží s](#support-packages-and-device-logs-available-for-troubleshooting).
9. Pokud předchozí postup nebude vyřešen, [obraťte se na Microsoft Support](storsimple-8000-contact-microsoft-support.md) žádostí o pomoc.

## <a name="next-steps"></a>Další postup
[Zjistěte, jak pomocí diagnostického nástroje pro řešení potíží s zařízení StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
