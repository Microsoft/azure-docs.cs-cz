---
title: Poradce při potížích s nasazením řady StorSimple 8000 | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267622"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Poradce při potížích s nasazením zařízení StorSimple
## <a name="overview"></a>Přehled
Tento článek obsahuje užitečné pokyny pro řešení potíží pro nasazení Microsoft Azure StorSimple. Popisuje běžné problémy, možné příčiny a doporučené kroky, které vám pomohou vyřešit problémy, které mohou nastat při konfiguraci StorSimple. 

Tyto informace platí pro fyzické zařízení řady StorSimple 8000 i pro cloudové zařízení StorSimple.

> [!NOTE]
> Problémy související s konfigurací zařízení, kterým může dojít při prvním nasazení zařízení, nebo k nim může dojít později, když je zařízení funkční. Tento článek se zaměřuje na řešení problémů s prvním nasazením. Chcete-li vyřešit potíže s operačním zařízením, přejděte na [článek Použití nástroje Diagnostika k řešení potíží s operačním zařízením](storsimple-8000-diagnostics.md).

Tento článek také popisuje nástroje pro řešení potíží STOrSimple nasazení a poskytuje podrobný příklad řešení potíží.

## <a name="first-time-deployment-issues"></a>Problémy s prvním nasazením
Pokud při prvním nasazení zařízení narazíte na problém, zvažte následující skutečnosti:

* Pokud řešíte potíže s fyzickým zařízením, zkontrolujte, zda byl hardware nainstalován a nakonfigurován tak, jak je popsáno v [části Instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
* Zkontrolujte předpoklady pro nasazení. Ujistěte se, že máte všechny informace popsané v [kontrolním seznamu konfigurace nasazení](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Zkontrolujte StorSimple poznámky k verzi a zjistěte, zda je popsán problém. Poznámky k verzi obsahují řešení známých problémů s instalací. 

Během nasazení zařízení se vyskytují nejčastější problémy, kterým uživatelé čelí při spuštění průvodce instalací a při registraci zařízení prostřednictvím prostředí Windows PowerShell pro StorSimple. (Pomocí prostředí Windows PowerShell pro StorSimple zaregistrovat a nakonfigurovat zařízení StorSimple. Další informace o registraci zařízení naleznete [v tématu Krok 3: Konfigurace a registrace zařízení prostřednictvím prostředí Windows PowerShell pro StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Následující části vám mohou pomoci vyřešit problémy, se kterými se setkáte při první konfiguraci zařízení StorSimple.

## <a name="first-time-setup-wizard-process"></a>První proces průvodce instalací
Následující kroky shrnují proces průvodce instalací. Podrobné informace o nastavení najdete [v tématu Nasazení místního zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Spusťte rutinu [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) a spusťte průvodce instalací, který vás provede zbývajícími kroky. 
2. Konfigurace sítě: Průvodce instalací umožňuje konfigurovat nastavení sítě pro síťové rozhraní DATA 0 na zařízení StorSimple. Mezi tato nastavení patří následující:
   * Virtuální IP (VIP), maska podsítě a brána – Rutina [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) je spuštěna na pozadí. Konfiguruje IP adresu, masku podsítě a bránu pro síťové rozhraní DATA 0 na vašem zařízení StorSimple.
   * Primární DNS server – Rutina [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) je spuštěna na pozadí. Konfiguruje nastavení DNS pro vaše řešení StorSimple.
   * NTP server – Rutina [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) je spuštěna na pozadí. Konfiguruje nastavení serveru NTP pro vaše řešení StorSimple.
   * Volitelný webový proxy server – Rutina [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) je spuštěna na pozadí. Nastavuje a umožňuje konfiguraci webového proxy serveru pro vaše řešení StorSimple.
3. Nastavení hesla: Dalším krokem je nastavení hesla správce zařízení.
   Heslo správce zařízení se používá k přihlášení k zařízení. Výchozí heslo zařízení je **Password1**.
        
     > [!IMPORTANT]
     > Hesla jsou shromažďována před registrací, ale použita pouze po úspěšné registraci zařízení. Pokud dojde k selhání použití hesla, budete vyzváni k zadání hesla znovu, dokud nebudou shromážděna požadovaná hesla (splňující požadavky na složitost).
     
4. Registrace zařízení: Posledním krokem je registrace zařízení pomocí služby StorSimple Device Manager spuštěné v Microsoft Azure. Registrace vyžaduje, abyste [získali registrační klíč služby](storsimple-8000-manage-service.md#get-the-service-registration-key) z portálu Azure a poskytli jej v průvodci nastavením. **Po úspěšné registraci zařízení je vám poskytnut šifrovací klíč dat služby. Nezapomeňte uchovávat tento šifrovací klíč na bezpečném místě, protože bude muset zaregistrovat všechna následující zařízení se službou.**

## <a name="common-errors-during-device-deployment"></a>Běžné chyby během nasazení zařízení
V následujících tabulkách jsou uvedeny běžné chyby, se kterými se můžete setkat při:

* Nakonfigurujte požadovaná nastavení sítě.
* Nakonfigurujte volitelné nastavení webového proxy serveru.
* Nastavte heslo správce zařízení.
* Zaregistrujte zařízení.

## <a name="errors-during-the-required-network-settings"></a>Chyby při požadovaném nastavení sítě
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Tento příkaz lze spustit pouze na aktivním řadiči. |Konfigurace byla prováděna na pasivním řadiči. |Spusťte tento příkaz z aktivního řadiče. Další informace naleznete v [tématu Identifikace aktivního ovladače v zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Zařízení není připraveno. |Existují problémy s připojením k síti na DATA 0. |Zkontrolujte fyzické připojení k síti v data 0. |
| 3 |Invoke-HcsSetupWizard: Došlo ke konfliktu adresy IP s jiným systémem v síti (Výjimka z HRESULT: 0x80070263). |Ip dodaná pro DATA 0 byla již používána jiným systémem. |Zadejte novou adresu IP, která se nepoužívá. |
| 4 |Invoke-HcsSetupWizard: Prostředek clusteru se nezdařil. (Výjimka z HRESULT: 0x800713AE). |Duplicitní VIP. Dodaná adresa IP je již používána. |Zadejte novou adresu IP, která se nepoužívá. |
| 5 |Invoke-HcsSetupWizard: Neplatná adresa IPv4. |Ip adresa je poskytována v nesprávném formátu. |Zkontrolujte formát a znovu zadejte svou IP adresu. Další informace naleznete v tématu [Ipv4 Addressing][1]. |
| 6 |Invoke-HcsSetupWizard: Neplatná adresa IPv6. |Ip adresa je poskytována v nesprávném formátu. |Zkontrolujte formát a znovu zadejte svou IP adresu. Další informace naleznete v tématu [Ipv6 Addressing][2]. |
| 7 |Invoke-HcsSetupWizard: Z mapovače koncového bodu nejsou k dispozici žádné další koncové body. (Výjimka z HRESULT: 0x800706D9) |Funkce clusteru nefunguje. |Další kroky [vám poskytne podpora společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Chyby při volitelném nastavení webového proxy serveru
| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Neplatný parametr (Výjimka z HRESULT: 0x80070057) |Jeden z parametrů poskytnutých pro nastavení proxy serveru není platný. |Identifikátor URI není k dispozici ve správném formátu. Použijte následující formát: http://*\<IP adresu nebo vícestranný *název sítě www proxy serveru>:*\<číslo portu TCP>* |
| 2 |Invoke-HcsSetupWizard: Server RPC není k dispozici (Výjimka z HRESULT: 0x800706ba) |Hlavní příčinou je jedna z následujících příčin:<ol><li>Cluster není nahoře.</li><li>Pasivní řadič nemůže komunikovat s aktivním řadičem a příkaz je spuštěn z pasivního řadiče.</li></ol> |V závislosti na hlavní příčině:<ol><li>[Obraťte](storsimple-8000-contact-microsoft-support.md) se na podporu společnosti Microsoft a ujistěte se, že cluster je nahoře.</li><li>Spusťte příkaz z aktivního řadiče. Pokud chcete spustit příkaz z pasivního řadiče, budete muset zajistit, že pasivní řadič může komunikovat s aktivním řadičem. Pokud je toto připojení přerušeno, budete muset [kontaktovat podporu společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md)</li></ol> |
| 3 |Invoke-HcsSetupWizard: Volání vzdáleného volání procedur se nezdařilo (Výjimka z HRESULT: 0x800706be) |Cluster nese. |[Obraťte](storsimple-8000-contact-microsoft-support.md) se na podporu společnosti Microsoft a ujistěte se, že cluster je nahoře. |
| 4 |Invoke-HcsSetupWizard: Prostředek clusteru nebyl nalezen (Výjimka z HRESULT: 0x8007138f) |Prostředek clusteru nebyl nalezen. K tomu může dojít, když instalace nebyla správná. |Možná budete muset zařízení obnovit do výchozího továrního nastavení. [Chcete-li](storsimple-8000-contact-microsoft-support.md) vytvořit prostředek clusteru, obraťte se na podporu společnosti Microsoft. |
| 5 |Invoke-HcsSetupWizard: Prostředek clusteru není online (Výjimka z HRESULT: 0x8007138c) |Prostředky clusteru nejsou online. |Další kroky [vám poskytne podpora společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md) |

## <a name="errors-related-to-device-administrator-password"></a>Chyby související s heslem správce zařízení
Výchozí heslo správce zařízení je **Password1**. Platnost tohoto hesla vyprší po prvním přihlášení. Proto budete muset použít průvodce nastavením změnit. Při první registraci zařízení je nutné zadat nové heslo správce zařízení. 

Ujistěte se, že vaše hesla splňují následující požadavky:

* Heslo správce zařízení by mělo mít 8 až 15 znaků.
* Hesla by měla obsahovat 3 z následujících 4 typů znaků: malá, velká, číselná a speciální. 
* Heslo nemůže být stejné jako posledních 24 hesel.

Kromě toho mějte na paměti, že hesla vyprší každý rok a lze je změnit až po úspěšné registraci zařízení. Pokud se registrace z nějakého důvodu nezdaří, hesla se nezmění.

Další informace o hesle správce zařízení naleznete v [části Změna hesla StorSimple pomocí služby StorSimple](storsimple-8000-change-passwords.md)Device Manager .

Při nastavování hesel správce zařízení a správce snímků StorSimple se může vyskytnat jedna nebo více následujících chyb.

| Ne. | Chybová zpráva | Doporučená akce |
| --- | --- | --- |
| 1 |Heslo překračuje maximální délku. |Heslo správce zařízení musí mít 8 až 15 znaků. |
| 2 |Heslo nesplňuje požadovanou délku. |Heslo správce zařízení musí mít 8 až 15 znaků.|
| 3 |Heslo musí obsahovat malá písmena. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá, velká, číselná a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 4 |Heslo musí obsahovat číselné znaky. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá, velká, číselná a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 5 |Heslo musí obsahovat speciální znaky. |Hesla musí obsahovat 3 z následujících 4 typů znaků: malá, velká, číselná a speciální. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 6 |Heslo musí obsahovat 3 z následujících 4 typů znaků: velká, malá, číselná a speciální. |Heslo neobsahuje požadované typy znaků. Ujistěte se, že vaše heslo splňuje tyto požadavky. |
| 7 |Parametr neodpovídá potvrzení. |Zkontrolujte, zda heslo splňuje všechny požadavky a zda jste je zadali správně. |
| 8 |Heslo se nemůže shodovat s výchozím nastavením. |Výchozí heslo je *Password1*. Toto heslo je třeba změnit po prvním přihlášení. |
| 9 |Zadané heslo neodpovídá heslu zařízení. Zadejte heslo znovu. |Zkontrolujte heslo a zadejte jej znovu. |

Hesla jsou shromažďována před registrací zařízení, ale platí se až po úspěšné registraci. Pracovní postup obnovení hesla vyžaduje registraci zařízení.

> [!IMPORTANT]
> Obecně platí, že pokud se pokus o použití hesla nezdaří, pak se software opakovaně pokouší shromáždit heslo, dokud nebude úspěšné. Ve výjimečných případech nelze použít heslo. V takovém případě můžete zaregistrovat zařízení a pokračovat, ale hesla se nezmění. Po registraci z webu Azure Portal můžete změnit heslo správce zařízení.


Heslo můžete resetovat na webu Azure Portal prostřednictvím služby StorSimple Device Manager. Další informace naleznete v části [Změna hesla správce zařízení](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Chyby při registraci zařízení
K registraci zařízení se používá služba StorSimple Device Manager spuštěná v Microsoft Azure. Při registraci zařízení se můžete setkat s jedním nebo více z následujících problémů.

| Ne. | Chybová zpráva | Možné příčiny | Doporučená akce |
| --- | --- | --- | --- |
| 1 |Chyba 350027: Nepodařilo se zaregistrovat zařízení pomocí Správce zařízení StorSimple. | |Počkejte několik minut a akci opakujte. Pokud problém přetrvává, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Chyba 350013: Při registraci zařízení došlo k chybě. To může být způsobeno nesprávným registračním klíčem služby. | |Zaregistrujte zařízení znovu pomocí správného registračního klíče služby. Další informace naleznete [v tématu Získání registračního klíče služby.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Chyba 350063: Bylo předáno ověření službě StorSimple Device Manager, ale registrace se nezdařila. Opakujte operaci po určité době. |Tato chyba označuje, že ověření pomocí služby ACS prošlo, ale volání registru služby se nezdařilo. To by mohlo být důsledkem sporadické závady sítě. |Pokud problém přetrvává, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Chyba 350049: Službu nelze během registrace dosáhnout. |Při volání do služby je přijata webová výjimka. V některých případech to může získat pevné opakováním operace později. |Zkontrolujte adresu IP a název DNS a opakujte operaci. Pokud problém přetrvává, [obraťte se na podporu společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Chyba 350031: Zařízení již bylo zaregistrováno. | |Žádná akce není nutná. |
| 6 |Chyba 350016: Registrace zařízení se nezdařila. | |Ujistěte se, že registrační klíč je správný. |
| 7 |Invoke-HcsSetupWizard: Při registraci zařízení došlo k chybě. To může být způsobeno nesprávnou IP adresou nebo názvem DNS. Zkontrolujte nastavení sítě a akci opakujte. Pokud problém přetrvává, [obraťte se na podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md). (Chyba 350050) |Ujistěte se, že vaše zařízení může ping na vnější síť. Pokud nemáte připojení k vnější síti, registrace může selhat s touto chybou. Tato chyba může být kombinací jedné nebo více z následujících:<ul><li>Nesprávná adresa IP</li><li>Nesprávná podsíť</li><li>Nesprávná brána</li><li>Nesprávné nastavení DNS</li></ul> |Podívejte se na postup v [příkladu řešení potíží krok za krokem](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Aktuální operace se nezdařila z důvodu vnitřní chyby služby [0x1FBE2]. Opakujte operaci po nějaké mši. Pokud problém přetrvává, obraťte se na podporu společnosti Microsoft. |Toto je obecná chyba vyvolána pro všechny neviditelné chyby uživatele ze služby nebo agenta. Nejčastějším důvodem může být selhání ověřování služby ACS. Možnou příčinou selhání je, že existují problémy s konfigurací serveru NTP a čas v zařízení není správně nastaven. |Opravte čas (pokud existují problémy) a opakujte operaci registrace. Pokud použijete set-hcssystem -timezone příkaz upravit časové pásmo, velká písmena každé slovo v časovém pásmu (například "Tichomoří (standardní čas").  Pokud tento problém přetrvává, [obraťte se](storsimple-8000-contact-microsoft-support.md) na podporu společnosti Microsoft pro další kroky. |
| 9 |Upozornění: Zařízení nelze aktivovat. Hesla správce zařízení a správce snímků StorSimple nebyla změněna. |Pokud se registrace nezdaří, správce zařízení a Hesla Správce snímků StorSimple se nezmění. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Nástroje pro řešení potíží s nasazeními StorSimple
StorSimple obsahuje několik nástrojů, které můžete použít k řešení potíží s řešením StorSimple. Mezi ně patří:

* Podpora balíčků a protokolů zařízení.
* Rutiny speciálně navržené pro řešení potíží.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Balíčky podpory a protokoly zařízení, které jsou k dispozici pro řešení potíží
Balíček podpory obsahuje všechny příslušné protokoly, které mohou pomoci týmu podpory společnosti Microsoft při řešení problémů se zařízením. Prostředí Windows PowerShell for StorSimple můžete použít ke generování šifrovaného balíčku podpory, který pak můžete sdílet s pracovníky podpory.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Zobrazení protokolů nebo obsahu balíčku podpory
1. Pomocí prostředí Windows PowerShell pro StorSimple vygenerujte balíček podpory, jak je popsáno v [příkazu Vytvořit a spravovat balíček podpory](storsimple-8000-create-manage-support-package.md).
2. Stáhněte [si dešifrovací skript](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) místně v klientském počítači.
3. Pomocí tohoto [podrobného postupu](storsimple-8000-create-manage-support-package.md#edit-a-support-package) otevřete a dešifrujte balíček podpory.
4. Dešifrované protokoly balíčků podpory jsou ve formátu etw/etvx. Chcete-li zobrazit tyto soubory v prohlížeči událostí systému Windows, můžete provést následující kroky:
   
   1. Spusťte příkaz **eventvwr** v klientovi systému Windows. Tím se spustí Prohlížeč událostí.
   2. V podokně **Akce** klikněte na **Otevřít uložený protokol** a přejděte na soubory protokolu ve formátu etvx/etw (balíček podpory). Nyní můžete soubor zobrazit. Po otevření souboru můžete soubor klepnout pravým tlačítkem myši a uložit jej jako text.
      
      > [!IMPORTANT]
      > Rutinu **Get-WinEvent** můžete také použít k otevření těchto souborů v prostředí Windows PowerShell. Další informace naleznete v [tématu Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) v referenční dokumentaci rutiny prostředí Windows PowerShell.
     
5. Když se protokoly otevřou v Prohlížeči událostí, vyhledejte následující protokoly, které obsahují problémy související s konfigurací zařízení:
   
   * hcs_pfconfig/provozní protokol
   * hcs_pfconfig/konfigurace
6. V souborech protokolu vyhledejte řetězce související s rutinami volaných průvodcem nastavením. Seznam těchto rutin naleznete [v tématu První postup instalace.](#first-time-setup-wizard-process)
7. Pokud nejste schopni zjistit příčinu problému, můžete se obrátit na [podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md) pro další kroky. Postup použijte v části [Vytvoření žádosti o podporu,](storsimple-8000-contact-microsoft-support.md#create-a-support-request) když se obrátíte na podporu společnosti Microsoft a požádáte o pomoc.

## <a name="cmdlets-available-for-troubleshooting"></a>Rutiny jsou k dispozici pro řešení potíží
Pomocí následujících rutin prostředí Windows PowerShell zjišťujte chyby připojení.

* `Get-NetAdapter`: Pomocí této rutiny můžete zjistit stav síťových rozhraní.
* `Test-Connection`: Pomocí této rutiny zkontrolujte připojení k síti uvnitř i vně sítě.
* `Test-HcsmConnection`: Pomocí této rutiny zkontrolujte připojení úspěšně registrovaného zařízení.
* `Sync-HcsTime`: Tato rutina slouží k zobrazení času zařízení a vynucení synchronizace času se serverem NTP.
* `Enable-HcsPing`a `Disable-HcsPing`: Pomocí těchto rutin umožníhostitelům příkaz ping v síťových rozhraních na zařízení StorSimple. Ve výchozím nastavení síťová rozhraní StorSimple nereagují na požadavky příkazu ping.
* `Trace-HcsRoute`: Tuto rutinu použijte jako nástroj pro trasování trasy. Odešle pakety do každého směrovače na cestě do konečného cíle po určitou dobu a potom vypočítá výsledky na základě paketů vrácených z každého směrování. Vzhledem k tomu, `Trace-HcsRoute` že ukazuje stupeň ztráty paketů v daném směrovači nebo propojení, můžete určit, které směrovače nebo odkazy mohou způsobovat problémy se sítí.
* `Get-HcsRoutingTable`: Tato rutina slouží k zobrazení místní směrovací tabulky IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Poradce při potížích s rutinou Get-NetAdapter
Při konfiguraci síťových rozhraní pro první nasazení zařízení není stav hardwaru k dispozici v uživatelském rozhraní služby StorSimple Device Manager, protože zařízení ještě není registrováno ve službě. Kromě toho okno **stavu hardwaru** nemusí vždy správně odrážet stav zařízení, zejména pokud existují problémy, které ovlivňují synchronizaci služby. V těchto situacích můžete `Get-NetAdapter` rutinu použít k určení stavu a stavu síťových rozhraní.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Zobrazení seznamu všech síťových adaptérů v zařízení
1. Spusťte prostředí Windows PowerShell pro `Get-NetAdapter`StorSimple a zadejte příkaz . 
2. Pomocí výstupu rutiny `Get-NetAdapter` a následujících pokynů porozumíte stavu síťového rozhraní.
   
   * Pokud je rozhraní v pořádku a povoleno, stav **ifIndex** se zobrazí jako **Nahoru**.
   * Pokud je rozhraní v pořádku, ale není fyzicky připojen (síťovýkabel), **ifIndex** se zobrazí jako **zakázáno**.
   * Pokud je rozhraní v pořádku, ale není povoleno, stav **ifIndex** je zobrazen jako **NotPresent**.
   * Pokud rozhraní neexistuje, nezobrazí se v tomto seznamu. Uživatelské rozhraní služby StorSimple Device Manager bude stále zobrazovat toto rozhraní ve stavu selhání.

Další informace o použití této rutiny naleznete v části [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) v odkazu na rutinu prostředí Windows PowerShell.

V následujících částech jsou `Get-NetAdapter` uvedeny ukázky výstupu z rutiny.

 V těchto ukázkách byl řadič 0 pasivním řadičem a byl nakonfigurován takto:

* V zařízení existovala síťová rozhraní DATA 0, DATA 1, DATA 2 a DATA 3.
* karty síťového rozhraní DATA 4 a DATA 5 nebyly k dispozici; proto nejsou uvedeny ve výstupu.
* Data 0 byla povolena.

Řadič 1 byl aktivní řadič a byl nakonfigurován takto:

* V zařízení existovala síťová rozhraní DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 a DATA 5.
* Data 0 byla povolena.

**Ukázkový výstup – regulátor 0**

Následuje výstup z řadiče 0 (pasivní regulátor). DATA 1, DATA 2 a DATA 3 nejsou připojeny. DATA 4 a DATA 5 nejsou uvedeny, protože nejsou v zařízení k dispozici.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Ukázkový výstup – regulátor 1**

Následuje výstup z řadiče 1 (aktivní regulátor). Nakonfigurováno a funguje pouze síťové rozhraní DATA 0 v zařízení.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Poradce při potížích s rutinou Testovací připojení
Pomocí rutiny `Test-Connection` můžete určit, zda se vaše zařízení StorSimple může připojit k vnější síti. Pokud jsou všechny síťové parametry, včetně DNS, správně nakonfigurovány `Test-Connection` v průvodci instalací, můžete pomocí rutiny příkazem ping na dotaz ovat známou adresu mimo síť, například outlook.com.

Chcete-li vyřešit problémy s připojením s touto rutinou, měli byste povolit příkaz ping, pokud je příkaz ping zakázán.

Podívejte se na následující `Test-Connection` ukázky výstupu z rutiny.

> [!NOTE]
> V první ukázce je zařízení nakonfigurováno s nesprávným DNS. V druhé ukázce je služba DNS správná.

**Ukázkový výstup – nesprávné DNS**

V následující ukázce neexistuje žádný výstup pro adresy IPV4 a IPV6, což znamená, že služba DNS není vyřešena. To znamená, že neexistuje žádné připojení k vnější síti a je třeba zadávat správné DNS.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Ukázkový výstup – správné DNS**

V následující ukázce vrátí služba DNS adresu IPV4 označující, že služba DNS je správně nakonfigurována. Tím se potvrzuje, že je k dispozici připojení k vnější síti.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Poradce při potížích s rutinou Test-HcsmConnection
Rutinu `Test-HcsmConnection` použijte pro zařízení, které je již připojeno a zaregistrováno ve službě Správce zařízení StorSimple. Tato rutina vám pomůže ověřit připojení mezi registrovaným zařízením a odpovídající službou Správce zařízení StorSimple. Tento příkaz můžete spustit v prostředí Windows PowerShell pro StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Spuštění rutiny Test-HcsmConnection
1. Ujistěte se, že je zařízení zaregistrované.
2. Zkontrolujte stav zařízení. Pokud je zařízení deaktivováno, v režimu údržby nebo offline, může se zobrazit jedna z následujících chyb:
   
   * ErrorCode.CiSDeviceDecommissioned – to znamená, že zařízení je deaktivováno.
   * ErrorCode.DeviceNotReady – to znamená, že zařízení je v režimu údržby.
   * ErrorCode.DeviceNotReady – to znamená, že zařízení není online.
3. Ověřte, zda je spuštěna služba Správce zařízení StorSimple (použijte rutinu [Get-ClusterResource).](https://technet.microsoft.com/library/ee461004.aspx) Pokud služba není spuštěna, mohou se zobrazit následující chyby:
   
   * ErrorCode.cisApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – to znamená, že při spuštění get-clusterresource došlo k výjimce.
4. Zkontrolujte token služby řízení přístupu (ACS). Pokud vyvolá výjimku webu, může být výsledkem problému brány, chybějící ho ověřování proxy serveru, nesprávného DNS nebo selhání ověřování. Mohou se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceGateway – označuje výjimku HttpStatusCode.BadGateway: služba překladače názvů nemohla přeložit název hostitele.
   * ErrorCode.CiSApplianceProxy – označuje výjimku HttpStatusCode.ProxyAuthenticationRequired (stavový kód HTTP 407): klient se nemohl ověřit pomocí proxy serveru.
   * ErrorCode.CiSApplianceDNSError – označuje výjimku WebExceptionStatus.NameResolutionFailure: služba překladače názvů nemohla přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale je připojení.
     
     Pokud nevyvolá výjimku webu, zkontrolujte ErrorCode.CiSApplianceFailure. To znamená, že zařízení selhalo.
5. Zkontrolujte připojení cloudové služby. Pokud služba vyvolá webovou výjimku, mohou se zobrazit následující chyby:
   
   * ErrorCode.CiSApplianceGateway – označuje výjimku HttpStatusCode.BadGateway: zprostředkující proxy server obdržel chybný požadavek od jiného proxy serveru nebo z původního serveru.
   * ErrorCode.CiSApplianceProxy – označuje výjimku HttpStatusCode.ProxyAuthenticationRequired (stavový kód HTTP 407): klient se nemohl ověřit pomocí proxy serveru.
   * ErrorCode.CiSApplianceDNSError – označuje výjimku WebExceptionStatus.NameResolutionFailure: služba překladače názvů nemohla přeložit název hostitele.
   * ErrorCode.CiSApplianceACSError – to znamená, že služba vrátila chybu ověřování, ale je připojení.
     
     Pokud nevyvolá webovou výjimku, zkontrolujte ErrorCode.CiSApplianceSaasServiceError. To znamená problém se službou StorSimple Device Manager.
6. Zkontrolujte připojení Azure Service Bus. ErrorCode.CiSApplianceServiceBusError označuje, že se zařízení nemůže připojit ke sběrnici Service Bus.

Soubory protokolu CiSCommandletLog0Curr.errlog a CiSAgentsvc0Curr.errlog budou mít další informace, například podrobnosti o výjimce.

Další informace o použití rutiny naleznete v části [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) v referenční dokumentaci prostředí Windows PowerShell.

> [!IMPORTANT]
> Tuto rutinu můžete spustit pro aktivní i pasivní řadič.

Podívejte se na následující `Test-HcsmConnection` ukázky výstupu z rutiny.

**Ukázkový výstup – úspěšně registrované zařízení se spuštěnou aktualizací StorSimple Update 3**

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

Tato ukázka je ze zařízení, které má stav **Offline** na webu Azure Portal.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Zařízení se nemůže připojit pomocí aktuální konfigurace webového proxy serveru. Může se jedná o problém s konfigurací webového proxy serveru nebo probléms připojením k síti. V takovém případě byste se měli ujistit, že nastavení webového proxy serveru je správné a vaše webové proxy servery jsou online a dosažitelné.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Poradce při potížích s rutinou Sync-HcsTime
Tato rutina slouží k zobrazení času zařízení. Pokud má čas zařízení posun se serverem NTP, můžete pomocí této rutiny vynutit synchronizaci času se serverem NTP.
- Pokud je posun mezi zařízením a serverem NTP větší než 5 minut, zobrazí se upozornění. 
- Pokud posun přesáhne 15 minut, zařízení přejde do pouzdla. Tuto rutinu můžete stále použít k vynucení synchronizace času. 
- Pokud však posun přesáhne 15 hodin, nebude možné vynutit synchronizaci času a zobrazí se chybová zpráva.

**Ukázkový výstup – vynucená synchronizace času pomocí sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Poradce při potížích s rutinami Enable-HcsPing a Disable-HcsPing
Pomocí těchto rutin můžete zajistit, aby síťová rozhraní v zařízení reagovala na požadavky příkazu ping ICMP. Ve výchozím nastavení síťová rozhraní StorSimple nereagují na požadavky příkazu ping. Použití této rutiny je nejjednodušší způsob, jak zjistit, zda je vaše zařízení online a dostupné.

**Ukázkový výstup – Enable-HcsPing a Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Poradce při potížích s rutinou Trace-HcsRoute
Tuto rutinu použijte jako nástroj pro trasování trasy. Odešle pakety do každého směrovače na cestě do konečného cíle po určitou dobu a potom vypočítá výsledky na základě paketů vrácených z každého směrování. Vzhledem k tomu, že rutina zobrazuje stupeň ztráty paketů v daném směrovači nebo propojení, můžete určit, které směrovače nebo odkazy mohou způsobovat problémy se sítí.

**Ukázkový výstup znázorňující trasu paketu pomocí Trace-HcsRoute**

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

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Poradce při potížích s rutinou Get-HcsRoutingTable
Tato rutina slouží k zobrazení směrovací tabulky pro vaše zařízení StorSimple. Směrovací tabulka je sada pravidel, která mohou pomoci určit, kam budou směrovány datové pakety putující přes síť IP ....

Směrovací tabulka zobrazuje rozhraní a bránu, která směruje data do určených sítí. Poskytuje také metriku směrování, která je rozhodovací pravomocí pro cestu k dosažení určitého cíle. Čím nižší je metrika směrování, tím vyšší je předvolba.

Máte-li například 2 síťová rozhraní DATA 2 a DATA 3 připojená k Internetu. Pokud jsou metriky směrování pro DATA 2 a DATA 3 15 a 261, pak data 2 s nižší metrikou směrování je upřednostňovaným rozhraním používaným k dosažení Internetu.

Pokud na zařízení StorSimple používáte aktualizaci 1, má síťové rozhraní DATA 0 nejvyšší předvolbu pro provoz v cloudu. To znamená, že i v případě, že existují jiné rozhraní s podporou cloudu, cloudový provoz by být směrovány přes DATA 0.

Pokud spusťte rutinu `Get-HcsRoutingTable` bez zadání parametrů (jak ukazuje následující příklad), bude rutina výstup emitován IPv4 i IPv6. Případně můžete zadat `Get-HcsRoutingTable -IPv4` nebo `Get-HcsRoutingTable -IPv6` získat příslušnou směrovací tabulku.

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
Následující příklad ukazuje podrobné řešení potíží s nasazením StorSimple. V ukázkovém scénáři se registrace zařízení nezdaří s chybovou zprávou, že nastavení sítě nebo název DNS je nesprávný.

Vrácená chybová zpráva je:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Chyba může být způsobena některou z následujících příčin:

* Nesprávná instalace hardwaru
* Vadné síťové rozhraní
* Nesprávná adresa IP, maska podsítě, brána, primární server DNS nebo webový proxy server
* Nesprávný registrační klíč
* Nesprávné nastavení brány firewall

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Vyhledání a vyřešení problému s registrací zařízení
1. Zkontrolujte konfiguraci zařízení: na `Invoke-HcsSetupWizard`aktivním ovladači spusťte .
   
   > [!NOTE]
   > Průvodce instalací musí být spuštěn na aktivním řadiči. Chcete-li ověřit, zda jste připojeni k aktivnímu řadiči, podívejte se na banner prezentovaný v konzole sériového zařízení. Banner označuje, zda jste připojeni k řadiči 0 nebo řadiči 1 a zda je řadič aktivní nebo pasivní. Další informace naleznete v části [Identifikace aktivního ovladače v zařízení](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Zkontrolujte, zda je zařízení správně kabelové: zkontrolujte síťovou kabeláž na zadní rovině zařízení. Kabeláž je specifická pro model zařízení. Další informace naleznete v části [Instalace zařízení StorSimple 8100](storsimple-8100-hardware-installation.md) nebo [Instalace zařízení StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Pokud používáte síťové porty 10 GbE, budete muset použít dodávané adaptéry QSFP-SFP a kabely SFP. Další informace naleznete v [seznamu kabelů, přepínačů a vysílačů doporučených pro porty 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Ověřte stav síťového rozhraní:
   
   * Rutina Get-NetAdapter slouží ke zjišťování stavu síťových rozhraní pro DATA 0. 
   * Pokud propojení nefunguje, stav **ifindex** bude znamenat, že rozhraní je vypnuto. Poté budete muset zkontrolovat síťové připojení portu k zařízení a k přepínači. Budete také muset vyloučit špatné kabely. 
   * Pokud máte podezření, že port DATA 0 na aktivním řadiči selhal, můžete to potvrdit připojením k portu DATA 0 na řadiči 1. Chcete-li to potvrdit, odpojte síťový kabel ze zadní části zařízení z řadiče 0, připojte kabel k řadiči 1 a znovu spusťte rutinu Get-NetAdapter.
     Pokud se nezdaří port DATA 0 na řadiči, [obraťte se](storsimple-8000-contact-microsoft-support.md) na podporu společnosti Microsoft pro další kroky. Možná budete muset vyměnit ovladač v systému.
4. Ověřte připojení k přepínači:
   
   * Ujistěte se, že síťová rozhraní DATA 0 na řadiči 0 a řadiči 1 v primární skříni jsou ve stejné podsíti. 
   * Zkontrolujte rozbočovač nebo směrovač. Obvykle byste měli připojit oba řadiče ke stejnému rozbočovači nebo směrovači. 
   * Ujistěte se, že přepínače, které používáte pro připojení, mají data 0 pro oba řadiče ve stejné síti vLAN.
5. Eliminujte všechny uživatelské chyby:
   
   * Spusťte znovu průvodce instalací (spusťte **Invoke-HcsSetupWizard**) a znovu zadejte hodnoty, abyste se ujistili, že nedošlo k žádným chybám. 
   * Ověřte použitý registrační klíč. Stejný registrační klíč lze použít k připojení více zařízení ke službě Správce zařízení StorSimple. Pomocí postupu v [části Získání registračního klíče služby](storsimple-8000-manage-service.md#get-the-service-registration-key) se ujistěte, že používáte správný registrační klíč.
     
     > [!IMPORTANT]
     > Pokud máte spuštěno více služeb, budete muset zajistit, aby se k registraci zařízení použil registrační klíč pro příslušnou službu. Pokud jste zaregistrovali zařízení s nesprávnou službou StorSimple Device Manager, budete muset [kontaktovat podporu společnosti Microsoft](storsimple-8000-contact-microsoft-support.md) pro další kroky. Možná budete muset provést obnovení továrního nastavení zařízení (což může mít za následek ztrátu dat), aby se pak připojilo k zamýšlené službě.
     > 
     > 
6. Pomocí rutiny Test-Connection ověřte, zda máte připojení k vnější síti. Další informace naleznete v [části Poradce při potížích s rutinou Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Zkontrolujte, zda není rušení brány firewall. Pokud jste ověřili, že nastavení virtuální IP adresy (VIP), podsítě, brány a DNS jsou správná a stále se zobrazují problémy s připojením, je možné, že brána firewall blokuje komunikaci mezi zařízením a vnější sítí. Je třeba zajistit, aby porty 80 a 443 byly k dispozici na zařízení StorSimple pro odchozí komunikaci. Další informace naleznete v [tématu Požadavky na síť pro zařízení StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Podívej se na ty záznamy. Přejděte na [balíčky podpory a protokoly zařízení, které jsou k dispozici pro řešení potíží](#support-packages-and-device-logs-available-for-troubleshooting).
9. Pokud předchozí kroky problém nevyřeší, obraťte se na [podporu společnosti Microsoft.](storsimple-8000-contact-microsoft-support.md)

## <a name="next-steps"></a>Další kroky
[Přečtěte si, jak pomocí nástroje Diagnostika řešit potíže se zařízením StorSimple](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
