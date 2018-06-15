---
title: Řešení problémů nasazení řady StorSimple 8000 | Microsoft Docs
description: Popisuje, jak diagnostikovat a opravit chyby, ke kterým dochází při prvním nasazení zařízení StorSimple.
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
ms.openlocfilehash: d6642231bf9d012b0015902c82d2bfde3b86285c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927856"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Řešení problémů nasazení zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek obsahuje užitečné pokyny k odstraňování potíží pro vaše nasazení Microsoft Azure StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, aby pomáhal vyřešit problémy, které se můžete setkat při konfiguraci zařízení StorSimple. 

Tato informace se vztahují na fyzické zařízení řady StorSimple 8000 a cloudu zařízení StorSimple.

> [!NOTE]
> Související s konfigurací problémy zařízení, které může být vystaven může dojít, když nasazujete zařízení poprvé, nebo k nim lze dojde později, když je zařízení provozní. Tento článek se zaměřuje na řešení potíží s prvním nasazení. Chcete-li vyřešit provozní zařízení, přejděte na [řešení potíží s provozní zařízení pomocí nástroje Diagnostika](storsimple-8000-diagnostics.md).

Tento článek také popisuje nástroje pro řešení potíží s nasazením zařízení StorSimple a poskytuje podrobný příklad řešení potíží.

## <a name="first-time-deployment-issues"></a>Problémy při prvním nasazení
Pokud narazíte na problém při nasazování zařízení poprvé, zvažte následující:

* Pokud řešíte fyzické zařízení, ujistěte se, že hardware má byla nainstalována a nakonfigurována, jak je popsáno v [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
* Zkontrolujte požadavky na nasazení. Ujistěte se, že máte všechny informace, které jsou popsané v [kontrolní seznam konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Přečtěte si poznámky k verzi StorSimple, pokud je popsána problém. Poznámky k verzi obsahovat alternativní řešení známých instalace problémy. 

Během nasazování zařízení nejběžnější problémy kterou můžou uživatelé setkávají dojde k jejich spuštění Průvodce instalací a při jejich registrace zařízení pomocí Windows Powershellu pro StorSimple. (Můžete použít prostředí Windows PowerShell pro StorSimple registrace a konfigurace zařízení StorSimple. Další informace o registraci zařízení najdete v tématu [krok 3: Konfigurace a registrace zařízení pomocí Windows Powershellu pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

V následujících částech vám může pomoct vyřešit problémy, které dojde při první konfiguraci zařízení StorSimple.

## <a name="first-time-setup-wizard-process"></a>Průvodce instalací prvního
Následující kroky shrnují Průvodce instalací. Instalační program podrobné informace najdete v tématu [nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Spustit [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) rutiny spusťte Průvodce instalací, který vás provede zbývající kroky. 
2. Konfigurace sítě: Průvodce instalací vám umožní nakonfigurovat nastavení sítě síťového rozhraní DATA 0 v zařízení StorSimple. Tato nastavení zahrnují následující:
   * Virtuální IP (VIP), maska podsítě a bránu – [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) rutina se spustí na pozadí. Nakonfiguruje adresu IP, masky podsítě a bránu pro rozhraní DATA 0 sítě na zařízení StorSimple.
   * Primární server DNS – [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) rutina se spustí na pozadí. Konfiguruje nastavení DNS pro vašeho řešení StorSimple.
   * NTP server – [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) rutina se spustí na pozadí. Konfiguruje nastavení serveru NTP pro vašeho řešení StorSimple.
   * Volitelné webový proxy server – [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) rutina se spustí na pozadí. Nastaví a umožňuje konfiguraci webového proxy serveru pro vaše řešení StorSimple.
3. Nastavit heslo: dalším krokem je nastavení hesla správce zařízení.
   Heslo správce zařízení se používá k přihlášení k zařízení. Výchozí heslo zařízení je **Password1**.
        
     > [!IMPORTANT]
     > Hesla jsou shromažďovány před zaregistrováním, ale použijí pouze po úspěšné registraci zařízení. Pokud dojde k selhání použít heslo, budete vyzváni k zadání hesla znovu, dokud se shromažďují požadované hesla (které splňují požadavky na složitost).
     
4. Registrace zařízení: posledním krokem je registrace zařízení pomocí Správce zařízení StorSimple služby běžící v Microsoft Azure. Registrace vyžaduje, abyste [získat registrační klíč služby](storsimple-8000-manage-service.md#get-the-service-registration-key) z portálu Azure a poskytnout ho v Průvodci instalací. **Po úspěšné registraci zařízení se vám poskytuje šifrovacího klíče dat služby. Ujistěte se, že zachovat tento šifrovací klíč do bezpečného umístění, protože ji budou muset všechna další zařízení zaregistrovat službu.**

## <a name="common-errors-during-device-deployment"></a>Běžné chyby během nasazování zařízení
V následujících tabulkách jsou uvedeny běžné chyby, že můžete narazit při můžete:

* Nakonfigurujte požadovaná síťová nastavení.
* Nakonfigurujte nastavení volitelné webového proxy serveru.
* Nastavení hesla správce zařízení.
* Registrace zařízení.

## <a name="errors-during-the-required-network-settings"></a>Chyby při nastavení požadovaná sítě
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Tento příkaz lze spustit pouze v aktivním řadiči. |Konfigurace se provádí na pasivní řadiče. |Tento příkaz lze spustíte z aktivní řadiče. Další informace najdete v tématu [identifikovat v zařízení aktivní řadič](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Zařízení není připraveno. |Nedochází k potížím s připojením k síti na DATA 0. |Zkontrolujte připojení k fyzické síti na DATA 0. |
| 3 |Invoke-HcsSetupWizard: Je konflikt IP adres s jiným systémem v síti (výjimky z HRESULT: 0x80070263). |IP adresa zadaná pro DATA 0 byl již používán jiným systémem. |Zadejte novou IP, které nejsou používána. |
| 4 |Invoke-HcsSetupWizard: Prostředku clusteru se nezdařilo. (Výjimka z HRESULT: 0x800713AE). |Duplicitní VIP. Zadaná IP adresa je již používán. |Zadejte novou IP, které nejsou používána. |
| 5 |Invoke-HcsSetupWizard: Neplatný IPv4 adresa. |IP adresy se poskytuje v nesprávném formátu. |Zkontrolujte formát a znovu zadejte IP adresu. Další informace najdete v tématu [adresování protokolu Ipv4][1]. |
| 6 |Invoke-HcsSetupWizard: Neplatný IPv6 adresa. |IP adresy se poskytuje v nesprávném formátu. |Zkontrolujte formát a znovu zadejte IP adresu. Další informace najdete v tématu [adresách Ipv6][2]. |
| 7 |Invoke-HcsSetupWizard: Další koncové body nejsou k dispozici z mapovač koncových bodů. (Výjimka z HRESULT: 0x800706D9) |Funkce cluster nefunguje. |[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Chyby při nastavení volitelné webového proxy serveru
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Neplatný parametr (výjimky z HRESULT: 0x80070057) |Jeden z parametrů zadaných pro nastavení proxy serveru je neplatný. |Identifikátor URI není k dispozici ve správném formátu. Použijte následující formát: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: Server RPC není k dispozici (výjimky z HRESULT: 0x800706ba) |Hlavní příčinou je jedním z těchto:<ol><li>Až clusteru není.</li><li>Pasivní řadiče nemůže komunikovat s řadičem aktivní a se příkaz spustí z pasivní řadiče.</li></ol> |V závislosti na příčiny:<ol><li>[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) a ujistěte se, zda je clusteru.</li><li>Spusťte příkaz z aktivní řadiče. Pokud chcete spustit příkaz z pasivní řadiče, musíte zajistit, že pasivní řadič může komunikovat s řadičem active. Budete muset [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) Pokud toto připojení je přerušené.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Volání vzdáleného volání Procedur se nezdařilo (výjimky z HRESULT: 0x800706be) |Clusteru je mimo provoz. |[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) a ujistěte se, zda je clusteru. |
| 4 |Invoke-HcsSetupWizard: Clusteru prostředek nebyl nalezen (výjimky z HRESULT: 0x8007138f) |Prostředek clusteru nebyl nalezen. To může nastat při instalaci není správný. |Potřebujete v zařízení resetovat výchozí tovární nastavení. [Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) vytvoření prostředku clusteru. |
| 5 |Invoke-HcsSetupWizard: Clusteru zdroj není online (výjimky z HRESULT: 0x8007138c) |Prostředky clusteru nejsou online. |[Kontaktujte Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. |

## <a name="errors-related-to-device-administrator-password"></a>Chyby související s hesla správce zařízení
Výchozí heslo správce zařízení je **Heslo1**. Toto heslo vyprší po prvním přihlášení; Proto musíte použít Průvodce instalací nástroje ho změnit. Při první registraci zařízení, je nutné zadat nové heslo správce zařízení. 

Ujistěte se, že hesla splňovat následující požadavky:

* Heslo správce zařízení musí mít délku 8 až 15 znaků.
* Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. 
* Heslo nesmí být stejný jako poslední 24 hesla.

Kromě toho mějte na paměti, že každý rok po vypršení platnosti hesla a lze změnit pouze po úspěšné registraci zařízení. Pokud se registrace z jakéhokoli důvodu nezdaří, hesla se nezmění.

Další informace o heslo správce zařízení, přejděte na [pomocí služby StorSimple Manager zařízení StorSimple heslo změnit](storsimple-8000-change-passwords.md).

Jeden nebo více z těchto chyb setkat při nastavování správce zařízení a hesla Snapshot Manageru zařízení StorSimple.

| Ne. | Chybová zpráva | Doporučená akce |
| --- | --- | --- |
| 1 |Heslo překračuje maximální délku. |Heslo správce zařízení musí být mezi 8 až 15 znaků. |
| 2 |Heslo nesplňuje požadovanou délku. |Heslo správce zařízení musí být mezi 8 až 15 znaků.|
| 3 |Heslo musí obsahovat malá písmena. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 4 |Heslo musí obsahovat znaky. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 5 |Heslo musí obsahovat speciální znaky. |Hesla musí obsahovat 3 z následujících typů znaků 4: malá písmena, velká písmena, číselné a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 6 |Heslo musí obsahovat 3 z následujících typů 4 znaků: velká písmena, malá písmena, číselné a speciální. |Heslo neobsahuje požadované typy znaků. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 7 |Parametr neodpovídá potvrzení. |Ujistěte se, že vaše heslo splňuje všechny požadavky a že jste ji zadali správně. |
| 8 |Vaše heslo se nemůže shodovat výchozí. |Výchozí heslo je *Heslo1*. Budete muset po prvním přihlášení Toto heslo změnit. |
| 9 |Heslo, které jste zadali, neodpovídá heslo zařízení. Zadejte heslo znovu. |Zkontrolujte heslo a zadejte jej znovu. |

Hesla se shromažďují před zařízení je registrované, ale použijí se jenom po úspěšné registraci. Pracovní postup obnovení hesla vyžaduje, aby zařízení k registraci.

> [!IMPORTANT]
> Obecně platí Pokud se nezdaří pokus o použití hesla, pak software pokusí se opakovaně o shromažďování heslo, dokud neproběhne úspěšně. Ve výjimečných případech nelze použít heslo. V takovém případě můžete zaregistrovat zařízení a pokračovat, ale nebudou změněny hesla. Po registraci z portálu Azure můžete změnit heslo správce zařízení.


Můžete resetovat heslo v portálu Azure pomocí služby StorSimple Manager zařízení. Další informace, přejděte na [změnit heslo správce zařízení](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Chyby při registraci zařízení
Použijete službu StorSimple Manager zařízení v Microsoft Azure k registraci zařízení. Jeden nebo více z následujících důvodů může dojít během registrace zařízení.

| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Chyba 350027: Nepodařilo se zaregistrovat zařízení pomocí Správce zařízení StorSimple. | |Počkejte několik minut a opakujte operaci znovu. Pokud potíže potrvají, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Chyba 350013: Došlo k chybě při registraci zařízení. Tuto situaci mohly způsobit nesprávné služby registrační klíč. | |Zaregistrujte prosím zařízení znovu s použitím správného registračního klíče služby. Další informace najdete v tématu [získat registrační klíč služby.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Chyba 350063: Selhalo ověřování služby StorSimple Manager zařízení předán ale registrace. Opakujte operaci po určité době. |Tato chyba znamená, že uplynutí ověřování pomocí služby ACS, ale volání registrace služby se nezdařilo. To může být výsledek chybě ojediněle sítě. |Pokud problém přetrvá, prosím [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Chyba 350049: Služba není dosažitelná během registrace. |Při volání ke službě, byl přijat webové výjimka. V některých případech to může získat stanovena opakováním operaci později. |Zkontrolujte, zda vaše IP adresa a název DNS a potom operaci opakujte. Pokud potíže potrvají, [kontaktovat Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Chyba 350031: Zařízení je již zaregistrován. | |Není potřebná žádná akce. |
| 6 |Chyba 350016: Registrace zařízení se nezdařila. | |Zkontrolujte prosím, že je správný registrační klíč. |
| 7 |Invoke-HcsSetupWizard: Došlo k chybě při registraci zařízení; Tuto situaci mohly způsobit nesprávné IP adresu nebo název DNS. Zkontrolujte síťové nastavení a zkuste to znovu. Pokud potíže potrvají, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Chyba 350050) |Ujistěte se, že zařízení může odeslat příkaz ping vnější síti. Pokud jste připojení k vnější síti, registrace se pravděpodobně nezdaří s touto chybou. Tato chyba může být kombinací jeden nebo více následujících akcí:<ul><li>Nesprávná IP</li><li>Nesprávný podsítě</li><li>Nesprávný brány</li><li>Nesprávné nastavení DNS</li></ul> |Podívejte se na postup v [podrobný příklad řešení potíží](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Aktuální operace selhala kvůli vnitřní chybě služby [0x1FBE2]. Zkuste operaci po nějaké době zopakovat. Pokud potíže potrvají, kontaktujte prosím Microsoft Support. |Toto je obecná chyba vyvolána pro všechny uživatele neviditelná chyby ze služby nebo agenta. Nejčastější příčinou může být, že se nezdařilo ověření služby ACS. Možnou příčinou chyby je, že dochází k potížím s konfigurací serveru NTP a čas v zařízení není správně nastaven. |Opravte čas (v případě problémů) a opakujte operaci registrace. Pokud použijete příkaz Set-HcsSystem - časové pásmo upravit časové pásmo, převedení na velká písmena jednotlivých slov v časovém pásmu (například "Tichomoří (běžný čas)").  Pokud potíže potrvají, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. |
| 9 |Upozornění: Nebylo možné aktivovat zařízení. Správce zařízení a hesla Snapshot Manageru zařízení StorSimple nebyly změněny. |Pokud se registrace nezdaří, Správce zařízení a hesla Snapshot Manageru zařízení StorSimple, nebudou změněny. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Nástroje pro řešení potíží s nasazením zařízení StorSimple
StorSimple obsahuje několik nástrojů, které můžete použít k řešení vašeho řešení StorSimple. Mezi ně patří:

* Podporuje balíčky a protokolů zařízení.
* Rutiny speciálně pro řešení potíží.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Podporuje balíčky a protokolů zařízení, které jsou k dispozici pro řešení potíží
Balíček pro podporu obsahuje všechny relevantní protokoly, které mohou pomoci týmem Microsoft Support s potíží zařízení. Prostředí Windows PowerShell pro StorSimple můžete použít ke generování balíček šifrované podpory, které pak můžete sdílet s pracovníky podpory.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Chcete-li zobrazit protokoly nebo obsah balíčku podpory
1. Pomocí Windows Powershellu pro StorSimple ke generování balíčku pro podporu, jak je popsáno v [vytvoření a Správa balíčku pro podporu](storsimple-8000-create-manage-support-package.md).
2. Stažení [dešifrování skriptu](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně na klientském počítači.
3. Použít [podrobný postup](storsimple-8000-create-manage-support-package.md#edit-a-support-package) otevřít a dešifrování balíček pro podporu.
4. Protokoly balíček dešifrovaný podpory jsou ve formátu trasování událostí pro Windows nebo etvx. Abyste mohli provést následující kroky k zobrazení těchto souborů v prohlížeči událostí systému Windows:
   
   1. Spustit **eventvwr** příkazů na vašeho klienta Windows. Tím se spustí prohlížeč událostí.
   2. V **akce** podokně klikněte na tlačítko **otevřít uložený protokol** a přejděte na soubory protokolu ve formátu etvx nebo trasování událostí pro Windows (podpora balíček). Teď si můžete zobrazit soubor. Po otevření souboru, kliknete pravým tlačítkem a uložte soubor jako text.
      
      > [!IMPORTANT]
      > Můžete také **Get-WinEvent** rutiny otevření těchto souborů v prostředí Windows PowerShell. Další informace najdete v tématu [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) v referenční dokumentaci rutiny prostředí Windows PowerShell.
     
5. Když otevřete protokoly v prohlížeči událostí, podívejte se na tyto protokoly, které obsahují problémy související s konfigurací zařízení:
   
   * hcs_pfconfig/Operational protokolu
   * hcs_pfconfig nebo konfigurace
6. V souborech protokolu hledat řetězce související s rutinami volá Průvodce instalací. V tématu [Průvodce instalací prvního](#first-time-setup-wizard-process) seznam tyto rutiny.
7. Pokud si nejste schopni zjistit příčinu problému, můžete [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. Postupujte podle kroků v [vytvořit žádost o podporu](storsimple-8000-contact-microsoft-support.md#create-a-support-request) při požádejte Microsoft Support o pomoc.

## <a name="cmdlets-available-for-troubleshooting"></a>Rutiny, které jsou k dispozici pro řešení potíží
Následující rutiny prostředí Windows PowerShell použijte k detekci k chybám připojení.

* `Get-NetAdapter`: Tuto rutinu použijte k detekci stavu síťových rozhraní.
* `Test-Connection`: Pomocí této rutiny zkontrolujte možnosti síťového připojení uvnitř i vně sítě.
* `Test-HcsmConnection`: Pomocí této rutiny zkontrolujte připojení úspěšně registrovaná zařízení.
* `Sync-HcsTime`: Tuto rutinu použijte k zobrazení času a vynutit synchronizace času s serveru NTP.
* `Enable-HcsPing`a `Disable-HcsPing`: použijte tyto rutiny umožňující hostitelů na příkaz ping na rozhraní sítě v zařízení StorSimple. Ve výchozím nastavení rozhraní sítě StorSimple neodpoví na žádosti příkazu ping.
* `Trace-HcsRoute`: Pomocí této rutiny jako nástroj pro sledování tras. Odesílá pakety každému směrovači na cestě do konečného cíle přes v časovém intervalu a pak vypočítá výsledky podle vrácených z každého směrování paketů. Vzhledem k tomu `Trace-HcsRoute` zobrazuje stupeň ztráta paketů v jakékoli dané směrovače nebo odkaz, lze přesně určit které směrovače nebo odkazy může být příčinou problémů se sítí.
* `Get-HcsRoutingTable`: Tuto rutinu použijte k zobrazení místní směrovací tabulky protokolu IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Poradce při potížích s rutinu Get-NetAdapter
Když konfigurujete síťová rozhraní pro nasazení prvního zařízení, stav hardwaru není k dispozici ve službě StorSimple Manager zařízení uživatelského rozhraní, protože zařízení zatím není registrovaný ve službě. Kromě toho **stavu hardwaru** okno nemusí odrážet vždy správně stavu zařízení, zejména v případě, že jsou problémy, které mají vliv synchronizace služby. V těchto situacích můžete použít `Get-NetAdapter` rutiny určit stav síťových rozhraní.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Zobrazte seznam všech síťových adaptérů na zařízení
1. Spusťte prostředí Windows PowerShell pro StorSimple a pak zadejte `Get-NetAdapter`. 
2. Použít výstup `Get-NetAdapter` rutiny a následující pokyny pro porozumění stavům síťového rozhraní.
   
   * Pokud rozhraní je v pořádku a je povolen **ifIndex** stav se zobrazuje jako **až**.
   * Pokud rozhraní je v pořádku, ale není fyzicky (podle síťový kabel), **ifIndex** se zobrazí jako **zakázané**.
   * Pokud rozhraní je v pořádku, ale není povoleno **ifIndex** stav se zobrazuje jako **NotPresent**.
   * Pokud rozhraní neexistuje, nezobrazí se v tomto seznamu. Službu StorSimple Manager zařízení uživatelského rozhraní, bude mít toto rozhraní ve stavu selhání.

Další informace o tom, jak použít tuto rutinu, přejděte na [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) v odkazu na rutiny Windows Powershellu.

Následující části vysvětlují ukázky výstupu z `Get-NetAdapter` rutiny.

 Tyto ukázky řadič 0 byl pasivní řadiče a bylo nakonfigurováno takto:

* DATA 0, 1 dat, DATA 2 a DATA 3 síťové rozhraní byly na zařízení.
* DATA 4 a 5 dat síťových karet nebyly nalezeny; proto nejsou uvedené ve výstupu.
* DATA 0 bylo povoleno.

Řadič 1 byla aktivní řadiče a bylo nakonfigurováno takto:

* DATA 0, DATA 1, 2 dat, DATA 3, DATA 4 a DATA 5 síťové rozhraní byly na zařízení.
* DATA 0 bylo povoleno.

**Ukázkový výstup – řadič 0**

Zde je výstup řadič 0 (pasivní řadič). DATA 1, DATA 2 a DATA 3 nejsou připojené. DATA 4 a 5 dat nejsou zobrazeny, protože se nenachází v zařízení.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Ukázkový výstup – řadič 1**

Zde je výstup z řadiče 1 (aktivní řadič). Pouze DATA 0 je nakonfigurován síťové rozhraní na zařízení a funkční.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Poradce při potížích s rutinu Test-Connection
Můžete použít `Test-Connection` rutiny k určení, zda zařízení StorSimple se mohou připojit k vnější síti. Pokud všechny síťové parametry, včetně DNS, jsou v Průvodci instalací správně nakonfigurovaný, můžete použít `Test-Connection` rutiny příkazem ping otestovat adresu známé mimo síť, jako je outlook.com.

Měli byste povolit příkaz ping a řešení potíží s problémy s připojením pomocí této rutiny, pokud je zakázána příkazu ping.

Viz následující ukázky výstupu z `Test-Connection` rutiny.

> [!NOTE]
> V první ukázce je zařízení nakonfigurované s nesprávnou DNS. V druhé ukázce DNS je správná.

**Ukázkový výstup – nesprávný DNS**

V následující ukázce není žádný výstup pro adresy IPV4 a IPV6, který označuje, že není vyřešen DNS. To znamená, že je k dispozici žádné připojení k vnější síti a správné DNS musí být zadán.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Ukázkový výstup – správné DNS**

V následující ukázce DNS vrátí adresu IPV4, označující, zda je správně nakonfigurována DNS. Tím potvrdí, že existuje připojení k vnější síti.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Poradce při potížích s rutinu Test-HcsmConnection
Použití `Test-HcsmConnection` rutiny pro zařízení, která je již připojena k a zaregistrovali služby StorSimple Manager zařízení. Tato rutina vám pomůže zkontrolovat připojení mezi registrované zařízení a odpovídající služby StorSimple Manager zařízení. Tento příkaz lze spustit v prostředí Windows PowerShell pro StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Spuštění rutiny Test-HcsmConnection
1. Ujistěte se, že je zařízení zaregistrované.
2. Zkontrolujte stav zařízení. Pokud je deaktivována zařízení, v režimu údržby, nebo v režimu offline, můžete sledovat některé z těchto chyb:
   
   * ErrorCode.CiSDeviceDecommissioned – to znamená, že zařízení je deaktivována.
   * ErrorCode.DeviceNotReady – to znamená, že zařízení je v režimu údržby.
   * ErrorCode.DeviceNotReady – to znamená, že zařízení není online.
3. Ověřte, zda je spuštěna služba Manager zařízení StorSimple (pomocí [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) rutiny). Pokud tato služba neběží, může se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – to znamená, že při spuštění Get-ClusterResource došlo k výjimce.
4. Zkontrolujte token služby Řízení přístupu (ACS). Pokud se vyhodí výjimku web, může to být výsledek problém brány, chybějící ověření proxy serverem, nesprávný DNS či k chybě ověřování. Může se zobrazí následující chyby:
   
   * ErrorCode.CiSApplianceGateway – to znamená výjimku HttpStatusCode.BadGateway: název překladače služby nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceProxy – to znamená výjimku HttpStatusCode.ProxyAuthenticationRequired (kód stavu HTTP 407): klient nemůže ověřit na proxy serveru.
   * ErrorCode.CiSApplianceDNSError – to znamená WebExceptionStatus.NameResolutionFailure výjimka: název překladače služby nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError –, to znamená, že služba vrátila chybu ověřování, ale je k dispozici připojení.
     
     Pokud nevyvolá výjimku webové, zkontrolujte ErrorCode.CiSApplianceFailure. To znamená, že zařízení se nezdařila.
5. Zkontrolujte připojení cloudové služby. Pokud službu vyhodí výjimku web, může se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceGateway – to znamená výjimku HttpStatusCode.BadGateway: zprostředkujícího serveru proxy přijal neplatný požadavek z jiného serveru proxy nebo z původního serveru.
   * ErrorCode.CiSApplianceProxy – to znamená výjimku HttpStatusCode.ProxyAuthenticationRequired (kód stavu HTTP 407): klient nemůže ověřit na proxy serveru.
   * ErrorCode.CiSApplianceDNSError – to znamená WebExceptionStatus.NameResolutionFailure výjimka: název překladače služby nešlo přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError –, to znamená, že služba vrátila chybu ověřování, ale je k dispozici připojení.
     
     Pokud nevyvolá výjimku webové, zkontrolujte ErrorCode.CiSApplianceSaasServiceError. To znamená problém s služby StorSimple Manager zařízení.
6. Zkontrolujte připojení k Azure Service Bus. ErrorCode.CiSApplianceServiceBusError označuje, že zařízení nemůže připojit k Service Bus.

Soubory protokolu CiSCommandletLog0Curr.errlog a CiSAgentsvc0Curr.errlog bude obsahovat další informace, jako je například Podrobnosti o výjimce.

Další informace o tom, jak pomocí rutiny, přejděte na [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) v prostředí Windows PowerShell referenční dokumentace.

> [!IMPORTANT]
> Spuštěním této rutiny pro aktivní a pasivní řadiče.

Viz následující ukázky výstupu z `Test-HcsmConnection` rutiny.

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

Tato ukázka je ze zařízení, které je ve stavu **Offline** na portálu Azure.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Zařízení se nelze připojit pomocí aktuální konfigurace webového proxy serveru. Může se jednat o problém s konfigurace webového proxy serveru nebo problém se síťovým připojením. V takovém případě by měl Ujistěte se, že vaše nastavení proxy serveru webové správné a webových proxy serverů jsou online a dostupný.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Poradce při potížích s rutinu HcsTime synchronizace
Tuto rutinu použijte k zobrazení času zařízení. Pokud zařízení čas je posun s serveru NTP, můžete pomocí této rutiny force synchronizovat čas s serveru NTP.
- Pokud posun mezi zařízením a serveru NTP je větší než 5 minut, zobrazí se upozornění. 
- Posun je větší než 15 minut, zařízení bude přejděte do režimu offline. Tato rutina stále můžete vynutit synchronizace času. 
- Ale pokud posun překračuje po dobu 15 hodin, pak nebude možné k vynucení sync, které se zobrazí čas a chybová zpráva.

**Ukázkový výstup – synchronizace vynucené času pomocí HcsTime synchronizace**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Poradce při potížích s rutiny HcsPing povolit a zakázat HcsPing
Tyto rutiny použijte k zajištění, že rozhraní sítě ve vašem zařízení reagovat na požadavky ping ICMP. Ve výchozím nastavení rozhraní sítě StorSimple neodpoví na žádosti příkazu ping. Použití této rutiny je nejjednodušší způsob, jak zjistit, pokud vaše zařízení je online a dostupný.

**Ukázkový výstup – HcsPing povolit a zakázat HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Poradce při potížích s rutinu HcsRoute trasování
Použijte tuto rutinu jako nástroj pro sledování tras. Odesílá pakety každému směrovači na cestě do konečného cíle přes v časovém intervalu a pak vypočítá výsledky podle vrácených z každého směrování paketů. Protože rutina udává podíl ztráta paketů v každém směrovači nebo odkaz, lze přesně určit které směrovače nebo odkazy může být příčinou problémů se sítí.

**Ukázkový výstup znázorňující trasu paket se HcsRoute trasování**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Poradce při potížích s rutinu Get-HcsRoutingTable
Tuto rutinu použijte k zobrazení směrovací tabulky pro zařízení StorSimple. Směrovací tabulka je sada pravidel, které vám mohou pomoci určit, kde budou přesměrováni datových paketů cestě přes síť Internet Protocol (IP).

Směrovací tabulka ukazuje rozhraní a brána, která směruje data na servery z uvedených sítí. Dává také metriky směrování, která je rozhodovací pravomocí pro trasu pro dosažení určitého cíle. Čím nižší metriky směrování, tím vyšší priorita.

Například pokud máte 2 síťová rozhraní DATA 2 a DATA 3, připojený k Internetu. Pokud směrování metriky pro DATA 2 a DATA 3 jsou 15 a 261, s nižší metriky rozhraní DATA 2 je upřednostňovaný rozhraní použité k dosažení Internetu.

Pokud používáte zařízení StorSimple Update 1, vašeho síťového rozhraní DATA 0 má nejvyšší předvolby pro přenosy dat cloudové. To znamená, že i když existují další rozhraní povolenou podporu cloudu, by přenosy dat cloudové směrován přes DATA 0.

Pokud spustíte `Get-HcsRoutingTable` rutiny bez zadání všech parametrů (jak ukazuje následující příklad), bude výstup rutiny směrovací tabulky IPv4 i IPv6. Alternativně můžete zadat `Get-HcsRoutingTable -IPv4` nebo `Get-HcsRoutingTable -IPv6` získat relevantní směrovací tabulky.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Podrobný příklad řešení potíží StorSimple
Následující příklad ukazuje, podrobné řešení potíží s nasazení zařízení StorSimple. V ukázkovém scénáři registrace zařízení nezdaří a zobrazí se chybová zpráva oznamující, že nastavení sítě nebo název DNS je nesprávná.

Chybovou zprávu vrácenou je:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Chyba může být způsobeno některé z následujících:

* Instalace nesprávný hardwaru
* Vadné síti alespoň jedno rozhraní
* Nesprávnou adresu IP, masky podsítě, brány, primární server DNS nebo webového proxy serveru
* Nesprávný registrační klíč
* Nastavení brány firewall nesprávný

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Vyhledejte a opravte problém registrace zařízení
1. Zkontrolujte konfiguraci zařízení: v aktivním řadiči, spusťte `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > V aktivním řadiči musíte spustit Průvodce instalací. Pokud chcete ověřit, že jste připojeni k aktivnímu řadiči, podívejte se na informační zprávě zobrazí v konzole sériového portu. Banner informující o označuje, zda jste připojeni k řadič 0 nebo 1 řadiče a zda je daný řadič aktivní nebo pasivní. Další informace, přejděte na [identifikovat v zařízení aktivní řadič](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Ujistěte se, že je zařízení správně zapojené: Zkontrolujte síť kabelů zpět ploché zařízení. Kabeláž je specifické pro model zařízení. Další informace, přejděte na [instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Pokud používáte 10 GbE síťové porty, musíte použít zadaný QSFP SFP adaptéry a kabely SFP. Další informace najdete v tématu [seznam kabely, přepínače a vysílače doporučeno pro 10 GbE porty](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Ověření stavu síťové rozhraní:
   
   * Použijte rutinu Get-NetAdapter ke zjištění stavu síťových rozhraní data 0. 
   * Pokud na odkaz nefunguje, **ifindex** stav bude znamenat, že rozhraní dolů. Pak bude muset zkontrolujte síťové připojení k zařízení a k přepínači portu. Musíte také vyloučit chybný kabely. 
   * Pokud máte podezření, že DATA 0 v aktivním řadiči port se nezdařila, můžete to ověřit připojením k DATŮM na řadiči 1 0 port. Chcete-li to ověřit, odpojte síťový kabel od zpět zařízení z řadič 0, připojte kabel k řadiči 1 a poté spusťte rutinu Get-NetAdapter.
     Pokud DATA 0 portu na řadiči selže, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. Může se stát, že je třeba nahradit řadičem ve vašem systému.
4. Ověření připojení k přepínači:
   
   * Ujistěte se, že DATA 0 síťová rozhraní řadič 0 a řadič 1 ve vaší primární skříň jsou ve stejné podsíti. 
   * Zkontrolujte rozbočovače nebo směrovač. Oba řadiče by měl obvykle připojit ke stejné rozbočovače nebo směrovač. 
   * Ujistěte se, že přepínače, které používáte pro připojení mají DATA 0 pro oba řadiče v stejnou síť vLAN.
5. Vyloučit všechny chyby uživatele:
   
   * Spusťte znovu Průvodce instalací (Spustit **Invoke-HcsSetupWizard**) a zadejte hodnoty znovu, abyste měli jistotu, že nejsou žádné chyby. 
   * Ověření registrace klíč používaný. Stejné registrační klíč slouží k připojení více zařízení ke službě StorSimple Manager zařízení. Pomocí postupu v [získat registrační klíč služby](storsimple-8000-manage-service.md#get-the-service-registration-key) zajistit, že používáte správné registrační klíč.
     
     > [!IMPORTANT]
     > Pokud máte více služby spuštěné, musíte zajistit, že si registrační klíč pro příslušnou službu slouží k registraci zařízení. Pokud zařízení jste zaregistrovali službu nesprávný Manager zařízení StorSimple, budete muset [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) pro další kroky. Možná budete muset provést obnovení továrního nastavení zařízení (což může vést ke ztrátě dat) a pak připojit ke službě určený.
     > 
     > 
6. Chcete-li ověřit, že máte připojení k vnější síti pomocí rutiny Test-Connection. Další informace, přejděte na [Poradce při potížích s rutinu Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Zkontrolujte, zda brána firewall narušení. Pokud můžete ověřit, zda nastavení virtuální IP (VIP), podsíť, bránu a DNS jsou všechny správné a stále vidět problémy s připojením, pak je možné, že brána firewall blokuje komunikaci mezi vaším zařízením a vnější síti. Je potřeba zajistit, že porty 80 a 443 jsou k dispozici v zařízení StorSimple pro odchozí komunikaci. Další informace najdete v tématu [požadavcích sítě pro zařízení StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Podívejte se na protokoly. Přejděte na [podporuje balíčky a protokolů zařízení, které jsou k dispozici pro řešení potíží s](#support-packages-and-device-logs-available-for-troubleshooting).
9. Pokud předchozí kroky k vyřešení problému, [kontaktovat Microsoft Support](storsimple-8000-contact-microsoft-support.md) o pomoc.

## <a name="next-steps"></a>Další kroky
[Naučte se používat nástroj diagnostiky potíží s zařízení StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
